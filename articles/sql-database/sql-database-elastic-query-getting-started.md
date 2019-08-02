---
title: Raportowanie w skalowanych bazach danych w chmurze (partycjonowanie poziome) | Microsoft Docs
description: Zapytania bazy danych między bazami danych umożliwiają raportowanie wielu baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: cc59d7cb1ce09aad834130818e5af533719e04c1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568607"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Raportowanie w skalowanych bazach danych w chmurze (wersja zapoznawcza)

Możesz tworzyć raporty z wielu baz danych usługi Azure SQL z jednego punktu połączenia przy użyciu [zapytania elastycznego](sql-database-elastic-query-overview.md). Bazy danych muszą być dzielone na partycje w poziomie (znanym także jako "podzielonej na fragmenty").

Jeśli masz istniejącą bazę danych, zobacz [Migrowanie istniejących baz danych do skalowalnych baz danych](sql-database-elastic-convert-to-use-elastic-tools.md).

Aby zrozumieć obiekty SQL, które są konieczne do wykonywania zapytań, zobacz [zapytania w bazach danych w poziomie partycjonowanym](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz i uruchom [przykład wprowadzenie do narzędzi Elastic Database](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Tworzenie Menedżera mapy fragmentu za pomocą przykładowej aplikacji
W tym miejscu utworzysz Menedżera mapy fragmentu wraz z kilkoma fragmentów, a następnie wstawiasz dane do fragmentów. Jeśli masz już Instalatora fragmentów z danymi podzielonej na fragmenty, możesz pominąć poniższe kroki i przejść do następnej sekcji.

1. Kompiluj i uruchamiaj przykładową aplikację **wprowadzenie do Elastic Database Tools** . Postępuj zgodnie z instrukcjami do kroku 7 w sekcji [pobieranie i uruchamianie przykładowej aplikacji](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Na końcu kroku 7 zostanie wyświetlony następujący wiersz polecenia:

    ![wiersz polecenia][1]
2. W oknie wiersza polecenia wpisz "1" i naciśnij klawisz **Enter**. Spowoduje to utworzenie Menedżera mapy fragmentu i dodanie dwóch fragmentów do serwera. Następnie wpisz "3" i naciśnij klawisz **Enter**; Powtarzaj akcję cztery razy. Spowoduje to wstawienie przykładowych wierszy danych w fragmentów.
3. [Azure Portal](https://portal.azure.com) powinny zawierać trzy nowe bazy danych na serwerze:

   ![Potwierdzenie programu Visual Studio][2]

   W tym momencie zapytania między bazami danych są obsługiwane za pomocą bibliotek klienckich Elastic Database. Na przykład użyj opcji 4 w oknie poleceń. Wyniki zapytania fragmentu są zawsze **Union wszystkie** wyniki ze wszystkich fragmentów.

   W następnej sekcji tworzymy przykładowy punkt końcowy bazy danych, który obsługuje bogatsze zapytania dotyczące danych w fragmentów.

## <a name="create-an-elastic-query-database"></a>Tworzenie elastycznej bazy danych zapytań
1. Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się.
2. Utwórz nową bazę danych usługi Azure SQL na tym samym serwerze, na którym znajduje się konfiguracja fragmentu. Nazwij bazę danych "ElasticDBQuery".

    ![Azure Portal i warstwa cenowa][3]

    > [!NOTE]
    > Możesz użyć istniejącej bazy danych. Jeśli to możliwe, nie może być jednym z fragmentów, w którym chcesz wykonywać zapytania. Ta baza danych będzie używana do tworzenia obiektów metadanych dla zapytania Elastic Database.
    >

## <a name="create-database-objects"></a>Tworzenie obiektów bazy danych
### <a name="database-scoped-master-key-and-credentials"></a>Klucz główny i poświadczenia w zakresie bazy danych
Są one używane do nawiązywania połączenia z menedżerem map fragmentu i fragmentów:

1. Otwórz SQL Server Management Studio lub SQL Server narzędzia danych w programie Visual Studio.
2. Nawiąż połączenie z bazą danych ElasticDBQuery i wykonaj następujące polecenia T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    nazwy "username" i "Password" powinny być takie same jak informacje logowania używane w kroku 6 [pobierania i uruchamiania przykładowej aplikacji](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) w temacie [wprowadzenie do narzędzi elastycznych baz danych](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Zewnętrzne źródła danych
Aby utworzyć zewnętrzne źródło danych, wykonaj następujące polecenie w bazie danych ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" jest nazwą mapy fragmentu, jeśli utworzono mapę fragmentu i fragmentu mapowanie przy użyciu przykładowych narzędzi Elastic Database. Jeśli jednak użyto konfiguracji niestandardowej dla tego przykładu, powinna to być nazwa mapy fragmentu wybrana w aplikacji.

### <a name="external-tables"></a>Tabele zewnętrzne
Utwórz tabelę zewnętrzną zgodną z tabelą Customers w fragmentów, wykonując następujące polecenie w bazie danych ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowego zapytania T-SQL Elastic Database
Po zdefiniowaniu zewnętrznego źródła danych i tabel zewnętrznych można teraz używać pełnego języka T-SQL dla tabel zewnętrznych.

Wykonaj to zapytanie w bazie danych ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Zobaczysz, że zapytanie agreguje wyniki ze wszystkich fragmentów i daje następujące dane wyjściowe:

![Szczegóły danych wyjściowych][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importuj wyniki zapytania Elastic Database do programu Excel
 Wyniki z zapytania można zaimportować do pliku programu Excel.

1. Launch Excel 2013.
2. Przejdź do wstążki **dane** .
3. Kliknij **z innych źródeł** i kliknij pozycję **z SQL Server**.

   ![Import programu Excel z innych źródeł][5]
4. W **Kreatorze połączenia danych** wpisz nazwę serwera i poświadczenia logowania. Następnie kliknij przycisk **Next** (Dalej).
5. W oknie dialogowym **Wybierz bazę danych zawierającą dane, których chcesz użyć**, a następnie wybierz bazę danych **ElasticDBQuery** .
6. Wybierz tabelę **Customers** w widoku listy, a następnie kliknij przycisk **dalej**. Następnie kliknij przycisk **Zakończ**.
7. W formularzu **Importuj dane** w obszarze **Wybierz sposób wyświetlania tych danych w skoroszycie**wybierz pozycję **tabela** , a następnie kliknij przycisk **OK**.

Wszystkie wiersze z tabeli **Customers** , przechowywane w różnych fragmentówach wypełniają arkusz programu Excel.

Teraz można korzystać z zaawansowanych funkcji wizualizacji danych programu Excel. Możesz użyć parametrów połączenia z nazwą serwera, nazwą bazy danych i poświadczeniami, aby połączyć narzędzia do analizy biznesowej i danych z bazą danych zapytań elastycznych. Upewnij się, że SQL Server jest obsługiwane jako źródło danych dla narzędzia. Można odwołać się do bazy danych zapytań elastycznych i tabel zewnętrznych, podobnie jak każda inna baza danych SQL Server i SQL Server tabele, z którymi można nawiązać połączenie za pomocą narzędzia.

### <a name="cost"></a>Koszt
Za korzystanie z funkcji zapytania Elastic Database nie ma dodatkowych opłat.

Aby uzyskać informacje o cenach, zobacz [szczegóły cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z omówieniem zapytania elastycznego, zobacz [Omówienie zapytania elastycznego](sql-database-elastic-query-overview.md).
* Aby zapoznać się z pionowym samouczkiem partycjonowania, zobacz [Rozpoczynanie pracy z kwerendą między bazami danych (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Aby poznać składnię i przykładowe zapytania dotyczące danych partycjonowanych pionowo, zobacz [wykonywanie zapytań dotyczących partycjonowanych danych w pionie.](sql-database-elastic-query-vertical-partitioning.md)
* Aby poznać składnię i przykładowe zapytania dla danych z podziałem na partycje, zobacz [wykonywanie zapytań o dane partycjonowane w poziomie.](sql-database-elastic-query-horizontal-partitioning.md)
* Zapoznaj się z artykułem [\_Sp Execute \_Remote](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję języka Transact-SQL w ramach jednego zdalnego Azure SQL Database lub zestawu baz danych służących jako fragmentów w poziomym schemacie partycjonowania.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
