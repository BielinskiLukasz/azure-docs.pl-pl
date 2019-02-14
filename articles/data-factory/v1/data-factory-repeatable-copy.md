---
title: Powtarzalne kopiowania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uniknąć duplikaty, nawet jeśli wycinek, który kopiuje dane zostanie uruchomione więcej niż raz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fa08e6da6b87de5878f099709563fb90e8669e77
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237607"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Powtarzalne kopiowania w usłudze Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany.  
 
> [!NOTE]
> Poniższe przykłady dla usługi Azure SQL, ale mają zastosowanie do wszelkich magazyn danych, który obsługuje prostokątnych zestawów danych. Może być konieczne dostosowanie **typu** źródła i **zapytania** właściwości (na przykład: zapytanie, zamiast sqlReaderQuery) dane można przechowywać.   

Zazwyczaj podczas odczytywania z magazynów relacyjnych, chcesz odczytać tylko dane odpowiadające tego wycinka. Sposób, w tym celu będzie przy użyciu WindowStart i WindowEnd zmienne systemu dostępne w usłudze Azure Data Factory. Informacje na temat zmiennych i funkcji w usłudze Azure Data Factory w tym miejscu w [usługi Azure Data Factory — funkcje i zmienne systemowe](data-factory-functions-variables.md) artykułu. Przykład: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
To zapytanie odczytuje dane znajdujące się w zakresie czasu trwania wycinek (WindowStart -> WindowEnd) z tabeli MyTable. Uruchom ponownie tego wycinka będzie również są zawsze upewnij się, że te same dane jest do odczytu. 

W innych przypadkach mogą chcieć odczytać całej tabeli i mogą definiować sqlReaderQuery w następujący sposób:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Powtarzalne zapisu SqlSink
Podczas kopiowania danych do **serwera SQL i SQL Azure** od innych magazynów danych, należy zachować powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. 

Podczas kopiowania danych do bazy danych Azure SQL i SQL Server, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Powiedz, kopiowane są dane z pliku CSV (wartości rozdzielane przecinkami) zawierającego dwa rekordy w poniższej tabeli w bazie danych Azure SQL i SQL Server. Gdy wycinek jest uruchamiany, dwa rekordy są kopiowane do tabeli SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Załóżmy, że znaleziono błędy w pliku źródłowym i zaktualizować ilość rury w dół od 2 do 4. Jeśli uruchomisz wycinka danych dla tego okresu ręcznie, można znaleźć dwa nowe rekordy dołączany do bazy danych Azure SQL i SQL Server. W tym przykładzie przyjęto założenie, że żadna z tych kolumn w tabeli ma ograniczenia klucza podstawowego.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Aby uniknąć tego zachowania, należy określić semantyki UPSERT przy użyciu jednej z dwóch następujących mechanizmów:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanizm 1: przy użyciu sqlWriterCleanupScript
Możesz użyć **sqlWriterCleanupScript** właściwości, aby wyczyścić dane z tabeli ujścia przed wstawieniem danych, gdy wycinek jest uruchamiana. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Po uruchomieniu wycinek skrypt czyszczący jest najpierw uruchom, aby usunąć dane, które odpowiada wycinek z tabeli SQL. Działanie kopiowania następnie wstawia dane do tabeli SQL. Jeśli wycinek jest przeprowadzany ponownie, ilość jest aktualizowana zgodnie z potrzebami.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Załóżmy, że rekord prostego podkładka zostanie usunięty z oryginalnego pliku csv. Następnie ponownemu uruchamianiu wycinek dałby w efekcie następujący wynik: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Działanie kopiowania uruchomiono skrypt czyszczący do usunięcia odpowiednich danych dla tego wycinka. A następnie go odczytać dane wejściowe z pliku csv, (które następnie zawiera tylko jeden rekord) i dodaje je do tabeli. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanizm 2: za pomocą sliceIdentifierColumnName
> [!IMPORTANT]
> Obecnie sliceIdentifierColumnName nie jest obsługiwana dla usługi Azure SQL Data Warehouse. 

Drugi mechanizm do osiągnięcia powtarzalności jest posiadanie dedykowanego kolumny (sliceIdentifierColumnName) w tabeli docelowej. Ta kolumna może służyć przez usługę Azure Data Factory do upewnij się, że źródłowe i docelowe pozostają zsynchronizowane. Ta metoda działa, gdy pewien stopień elastyczności, zmienianie lub definiowania schematu SQL tabeli docelowej. 

W tej kolumnie jest używana przez usługę Azure Data Factory do celów powtarzalność i procesu usługi Azure Data Factory nie powoduje zmiany schematu do tabeli. Sposób korzystania z tej metody:

1. Definiowanie kolumny typu **plik binarny (32)** w docelowej tabeli SQL. Powinna istnieć bez ograniczeń w tej kolumnie. Nadajmy nazwę tej kolumny jako AdfSliceIdentifier, w tym przykładzie.


Tabela źródłowa:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

Tabela docelowa: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

2. Należy użyć go w działaniu kopiowania w następujący sposób:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Usługa Azure Data Factory umożliwia wypełnienie tej kolumny, zgodnie z konieczności upewnij się, że źródłowe i docelowe pozostają zsynchronizowane. Nie można używać wartości tej kolumny poza tym kontekstem. 

Podobnie jak mechanizm 1, działanie kopiowania automatycznie oczyszcza danych dla danego wycinka z docelowej tabeli SQL. Następnie wstawia dane ze źródła w tabeli docelowej. 

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj poniższe łącznika artykuły, aby uzyskać kompletny przykład JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
