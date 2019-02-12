---
title: Za pomocą transakcji w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące wykonywania transakcji w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 02/10/2019
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: ac2bc9fc05a7014cd575ec531ef0a6ba08b2609e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100146"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>W usłudze SQL Data Warehouse za pomocą transakcji
Porady dotyczące wykonywania transakcji w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.

## <a name="what-to-expect"></a>Czego oczekiwać
Jak można oczekiwać, usługa SQL Data Warehouse obsługuje transakcje jako część obciążenia magazynu danych. Jednak aby upewnić się, że wydajność usługi SQL Data Warehouse opiera się na dużą skalę niektóre funkcje są ograniczone w porównaniu do programu SQL Server. W tym artykule wyróżniono różnice i wyświetla listę innych. 

## <a name="transaction-isolation-levels"></a>Poziomy izolacji transakcji
Usługa SQL Data Warehouse implementuje transakcje ACID. Poziom izolacji transakcji pomocy technicznej są jednak ograniczone do READ UNCOMMITTED; Nie można zmienić na tym poziomie. Jeśli READ UNCOMMITTED jest istotna, można zaimplementować wiele metod, aby zapobiec odczytów danych kodowania. Najbardziej popularnych metod Użyj CTAS i przełączanie partycji tabeli (często nazywany przewijania wzorca okna), aby uniemożliwić użytkownikom wykonywanie zapytań o dane, które nadal przygotowywany jest. Widoki, które są wstępnie przefiltrować dane są także popularne podejście.  

## <a name="transaction-size"></a>Rozmiar transakcji
Transakcja modyfikacji danych jednego ma ograniczony rozmiar. Ten limit dotyczy poszczególnych dystrybucji. W związku z tym łączny przydział może obliczana poprzez pomnożenie limit według liczby dystrybucji. Aby przybliżony maksymalna liczba wierszy w ramach transakcji dzielnikiem zakończenia dystrybucji całkowity rozmiar każdego wiersza. W przypadku kolumn o zmiennej długości należy wziąć pod uwagę biorąc długość kolumny, średnia, a nie o maksymalnym rozmiarze.

Zostały wprowadzone w tabeli poniżej następujące założenia:

* Wystąpiło równomiernego rozłożenia danych 
* Średnia długość wiersza to 250 bajtów

| [DWU](sql-data-warehouse-overview-what-is.md) | Limit na dystrybucję (GiB) | Liczba dystrybucji | Maksymalny rozmiar transakcji (GiB) | Liczba wierszy na dystrybucję | Maksymalna liczba wierszy na transakcję |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6 000 000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

Limit rozmiaru transakcji jest stosowane osobno do każdej transakcji lub operacji. Nie jest ona stosowana we wszystkich równoczesnych transakcji. W związku z tym każda transakcja może zapisywać w dzienniku takiej ilości danych. 

Optymalizuj i zminimalizować ilość danych zapisanych w dzienniku, można znaleźć w temacie [transakcji najlepsze praktyki](sql-data-warehouse-develop-best-practices-transactions.md) artykułu.

> [!WARNING]
> Rozmiar maksymalny transakcji może odbyć się tylko do wyznaczania wartości skrótu, a nawet tabel ROUND_ROBIN rozproszonego, gdzie jest rozprzestrzenianie się dane. Jeśli transakcja jest zapisuje dane w sposób niesymetryczne dystrybucje limit jest prawdopodobnie zostanie osiągnięty przed rozmiar maksymalny transakcji.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Stan transakcji
Usługa SQL Data Warehouse funkcja XACT_STATE() raportu nie powiodło się transakcji przy użyciu wartości od -2. Ta wartość oznacza, że transakcji nie powiodło się i jest oznaczony do tylko wycofywania.

> [!NOTE]
> Użyj-2 przy użyciu funkcji XACT_STATE do oznaczania transakcji nie powiodło się reprezentuje różne zachowanie programu SQL Server. Program SQL Server używa wartość -1, który reprezentuje transakcję niemożliwą do zatwierdzenia. Program SQL Server może tolerować błędy wewnątrz transakcji bez konieczności można oznaczyć jako niemożliwą do zatwierdzenia. Na przykład `SELECT 1/0` może spowodować wystąpienie błędu, ale nie wymusić transakcji w stanie niemożliwą do zatwierdzenia. Program SQL Server pozwala również odczyty w transakcję niemożliwą do zatwierdzenia. Jednak usługa SQL Data Warehouse pozwala na to zrobić. Jeśli wystąpi błąd w obrębie transakcji SQL Data Warehouse automatycznie przejdzie w stan-2 i nie można wprowadzić dowolne dodatkowo wybrać instrukcji, dopóki instrukcja została wycofana. W związku z tym jest ważne sprawdzić, czy kodu aplikacji, aby sprawdzić, czy używa XACT_STATE() jako użytkownik może zaistnieć potrzeba modyfikacji kodu.
> 
> 

Na przykład w programie SQL Server, może zostać wyświetlony transakcji, która wygląda podobnie do poniższego:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Powyższy kod daje następujący komunikat o błędzie:

Msg 111233, poziom 16, stan 1, wiersz 1 111233; Bieżąca transakcja została przerwana, a wszystkie oczekujące zmiany mają została wycofana. Przyczyna: Transakcji w stanie tylko do wycofania nie została jawnie wycofana przed DDL, DML lub instrukcji SELECT.

Nie otrzymasz dane wyjściowe funkcji ERROR_ *.

W usłudze SQL Data Warehouse, kod musi być nieco zmienić:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Obserwuje się oczekiwane zachowanie. Błąd w transakcji jest zarządzane i funkcje ERROR_ * Podaj wartości, zgodnie z oczekiwaniami.

To wszystkie, które uległy zmianie, wycofywanie transakcji musieli się odbyć przed odczytu informacji o błędzie w bloku CATCH.

## <a name="errorline-function"></a>ERROR_LINE() — funkcja
Warto również zauważyć, że usługa SQL Data Warehouse wdrożenia lub nie obsługuje ERROR_LINE() — funkcja. Jeśli ma to w kodzie, należy usunąć go, aby były zgodne z usługą SQL Data Warehouse. Zamiast tego użyj etykiety zapytanie w swoim kodzie zaimplementować podobne funkcje. Aby uzyskać więcej informacji, zobacz [etykiety](sql-data-warehouse-develop-label.md) artykułu.

## <a name="using-throw-and-raiserror"></a>Przy użyciu THROW i RAISERROR
THROW jest nowoczesną implementację występowanie wyjątków w usłudze SQL Data Warehouse, ale obsługiwane jest również RAISERROR. Istnieje kilka różnic, które są warte jednak zwracając uwagę.

* Liczby komunikatów o błędach zdefiniowane przez użytkownika nie może być w zakresie 150 000 100 000 THROW
* Komunikaty o błędach RAISERROR są ustalone na poziomie 50 000
* Korzystanie z sys.messages nie jest obsługiwane.

## <a name="limitations"></a>Ograniczenia
Usługa SQL Data Warehouse ma kilka ograniczeń, które odnoszą się do transakcji.

Są one w następujący sposób:

* Nie transakcji rozproszonych
* Nie transakcji zagnieżdżonych dozwolone
* Bez zapisywania punktów dozwolone
* Brak transakcji o nazwie
* Nie zaznaczonych transakcji
* Brak obsługi języka DDL, takie jak CREATE TABLE wewnątrz transakcji użytkownika

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat optymalizowania transakcji, zobacz [transakcji najlepsze praktyki](sql-data-warehouse-develop-best-practices-transactions.md). Aby poznać najlepsze rozwiązania SQL Data Warehouse, zobacz temat [najlepsze rozwiązania SQL Data Warehouse](sql-data-warehouse-best-practices.md).

