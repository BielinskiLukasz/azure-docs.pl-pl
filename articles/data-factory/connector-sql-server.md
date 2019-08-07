---
title: Kopiowanie danych do i z SQL Server przy użyciu Azure Data Factory | Microsoft Docs
description: Dowiedz się więcej na temat przenoszenia danych do i z bazy danych SQL Server, która jest lokalna lub na maszynie wirtualnej platformy Azure przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 5dcbb2c25511277eaf46d6c9f4afc007a180f8a6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827876"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopiowanie danych do i z SQL Server przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję Azure Data Factory:"]
> * [Wersja 1](v1/data-factory-sqlserver-connector.md)
> * [Bieżąca wersja](connector-sql-server.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do bazy danych SQL Server. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane z i do bazy danych SQL Server można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Lub można skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do bazy danych SQL Server. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W SQL Server ten łącznik obsługuje:

- SQL Server wersje 2016, 2014, 2012, 2008 R2, 2008 i 2005.
- Kopiowanie danych przy użyciu uwierzytelniania SQL lub systemu Windows.
- Jako źródło, pobieranie danych przy użyciu zapytania SQL lub procedury składowanej.
- Jako ujścia, dołączanie danych do tabeli docelowej lub wywoływanie procedury składowanej z logiką niestandardową podczas kopiowania.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) nie jest obsługiwana.

>[!NOTE]
>[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) SQL Server nie jest teraz obsługiwana przez ten łącznik. Aby obejść ten sposób, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownika SQL Server ODBC. Postępuj zgodnie z [tymi wskazówkami](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) przy użyciu opcji pobierania sterowników ODBC i parametrów połączenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć funkcji kopiowania danych z bazy danych SQL Server, która nie jest publicznie dostępna, należy skonfigurować własne środowisko Integration Runtime. Aby uzyskać więcej informacji, zobacz [samodzielne środowisko Integration Runtime](create-self-hosted-integration-runtime.md). Środowisko Integration Runtime zapewnia wbudowany sterownik bazy danych SQL Server. Nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z lub do bazy danych SQL Server.

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika SQL Server Database.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla SQL Server połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **SqlServer**. | Tak |
| connectionString |Określ informacje o **ConnectionString** , które są konieczne do nawiązania połączenia z bazą danych SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. Zapoznaj się z poniższymi przykładami.<br/>Oznacz to pole jako **SecureString** , aby bezpiecznie przechowywać je w Azure Data Factory. Można również umieścić hasło w Azure Key Vault. Jeśli jest to uwierzytelnianie SQL, należy ściągnąć `password` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz przykład JSON po zalogowaniu do tabeli i [przechowywania w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| userName |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykładem jest **\\nazwa domeny username**. |Nie |
| password |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako **SecureString** , aby bezpiecznie przechowywać je w Azure Data Factory. Lub można odwołać [się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Nie |
| connectVia | To [środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Jeśli Twój magazyn danych jest publicznie dostępny, możesz użyć własnego środowiska Integration Runtime lub Azure Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna usługa Azure Integration Runtime. |Nie |

>[!TIP]
>Jeśli wystąpi błąd z kodem błędu "UserErrorFailedToConnectToSqlServer" i komunikatem "limit sesji dla bazy danych to XXX i został osiągnięty," Dodaj `Pooling=false` do parametrów połączenia i spróbuj ponownie.

**Przykład 1: Użyj uwierzytelniania SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: Użyj uwierzytelniania SQL z hasłem w Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 3: Użyj uwierzytelniania systemu Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SQL Server.

Aby skopiować dane z i do bazy danych SQL Server, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość sqlservercollection. | Tak |
| tableName |Ta właściwość jest nazwą tabeli lub widoku w wystąpieniu bazy danych SQL Server, do której odwołuje się połączona usługa. | Brak źródła tak dla ujścia |

**Przykład**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby zapoznać się z pełną listą sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server jako źródło

Aby skopiować dane z SQL Server, ustaw typ źródła w działaniu Copy na **sqlsource**. W sekcji Źródło działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość sqlsource. | Yes |
| sqlReaderQuery |Umożliwia odczytywanie danych niestandardowe zapytania SQL. Może to być na przykład `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Ta właściwość jest nazwą procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnią instrukcję SQL musi być instrukcja SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Te parametry dotyczą procedury składowanej.<br/>Dozwolone wartości to pary nazw ani wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

**Punkty do uwagi:**

- Jeśli **sqlReaderQuery** jest określony dlaelementu sqlsource, działanie Copy uruchamia to zapytanie względem źródła SQL Server, aby uzyskać dane. Można również określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** , jeśli procedura składowana pobiera parametry.
- Jeśli nie określisz opcji **sqlReaderQuery** ani **sqlReaderStoredProcedureName**, kolumny zdefiniowane w sekcji "Structure" w kodzie JSON zestawu danych są używane do konstruowania zapytania. Zapytanie `select column1, column2 from mytable` jest uruchamiane względem SQL Server. Jeśli definicja zestawu danych nie ma "struktury", wszystkie kolumny są wybierane z tabeli.

**Przykład: Użyj zapytania SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Przykład: Użyj procedury składowanej**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definicja procedury składowanej**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server jako ujścia

> [!TIP]
> Dowiedz się więcej o obsługiwanych zachowaniach zapisu, konfiguracjach i najlepszych rozwiązaniach od [najlepszych rozwiązań dotyczących ładowania danych do SQL Server](#best-practice-for-loading-data-into-sql-server).

Aby skopiować dane do SQL Server, ustaw typ ujścia w działaniu Copy na **sqlsink**. W sekcji ujścia działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość sqlsink. | Tak |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/>Dozwolone wartości to liczby całkowite dla liczby wierszy. Domyślnie Azure Data Factory dynamicznie określa odpowiedni rozmiar wsadu na podstawie rozmiaru wiersza. |Nie |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania na zakończenie operacji wstawiania wsadowego przed upływem limitu czasu.<br/>Dozwolone wartości są dla przedziału czasu. Przykładem jest "00:30:00" w ciągu 30 minut. |Nie |
| preCopyScript |Ta właściwość określa zapytanie SQL dla działania kopiowania, które ma zostać uruchomione przed zapisaniem danych w SQL Server. Jest on wywoływany tylko raz dla każdego przebiegu kopiowania. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |
| sqlWriterStoredProcedureName | Nazwa procedury składowanej, która definiuje sposób zastosowania danych źródłowych do tabeli docelowej. <br/>Ta procedura składowana jest *wywoływana na partię*. W przypadku operacji, które są uruchamiane tylko raz i nie mają niczego do wykonania z danymi źródłowymi, na przykład Usuń lub Obetnij `preCopyScript` , użyj właściwości. | Nie |
| storedProcedureTableTypeParameterName |Nazwa parametru typu tabeli określona w procedurze składowanej.  |Nie |
| sqlWriterTableType |Nazwa typu tabeli, która ma zostać użyta w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tym typem tabeli. Kod procedury składowanej może następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. | Nie |

**Przykład 1: Dołącz dane**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Przykład 2: Wywołaj procedurę przechowywaną podczas kopiowania**

Dowiedz się więcej szczegółowo od [wywołania procedury składowanej z ujścia bazy danych SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Najlepsze rozwiązanie w zakresie ładowania danych do SQL Server

Podczas kopiowania danych do SQL Server może być wymagane inne zachowanie zapisu:

- [Dołącz](#append-data): Moje dane źródłowe mają tylko nowe rekordy.
- [Upsert](#upsert-data): Moje dane źródłowe są wstawiane i aktualizowane.
- [Zastąp](#overwrite-the-entire-table): Chcę ponownie załadować całą tabelę wymiarów za każdym razem.
- [Zapisz z logiką](#write-data-with-custom-logic)niestandardową: Potrzebuję dodatkowego przetwarzania przed ostatnim wstawieniem do tabeli docelowej.

Zapoznaj się z odpowiednimi sekcjami dotyczącymi konfigurowania programu w Azure Data Factory i najlepszych rozwiązaniach.

### <a name="append-data"></a>Dołącz dane

Dołączanie danych jest domyślnym zachowaniem tego łącznika SQL Server sink. Azure Data Factory wykonuje zbiorcze Wstawianie w celu wydajnego zapisu w tabeli. Można odpowiednio skonfigurować źródło i ujścia w działaniu kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Opcja 1:** Jeśli masz dużą ilość danych do skopiowania, użyj następującego podejścia, aby wykonać upsert: 

- Najpierw należy użyć [tabeli tymczasowej](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) do zbiorczego ładowania wszystkich rekordów przy użyciu działania kopiowania. Ponieważ operacje związane z tabelami tymczasowymi nie są rejestrowane, można ładować miliony rekordów w kilka sekund.
- Uruchom działanie procedury składowanej w Azure Data Factory, aby zastosować instrukcję [merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) lub Insert/Update. Użyj tabeli tymczasowej jako źródła, aby wykonać wszystkie aktualizacje lub wstawioną jako pojedynczą transakcję. W ten sposób liczba operacji błądzenia i dzienników jest ograniczona. Na końcu działania procedury składowanej tabela tymczasowa może zostać obcięta, aby była gotowa do następnego cyklu upsert.

Przykładowo w Azure Data Factory można utworzyć potok z **działaniem kopiowania** łańcucha z **działaniem procedury składowanej**. Dawniej kopiuje dane z magazynu źródłowego do tabeli tymczasowej bazy danych, na przykład **# #UpsertTempTable**, jako nazwę tabeli w zestawie danych. Następnie drugi wywołuje procedurę przechowywaną, aby scalić dane źródłowe z tabeli tymczasowej do tabeli docelowej i oczyścić tabelę tymczasową.

![UPSERT](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

W bazie danych Zdefiniuj procedurę składowaną z logiką scalania, taką jak Poniższy przykład, który jest wskazywany przez poprzednią aktywność procedury składowanej. Załóżmy, że element docelowy jest tabelą marketingową z trzema kolumnami: **ProfileID**, **stan**i **Kategoria**. Wykonaj upsert na podstawie kolumny **ProfileID** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opcja 2:** Możesz również wybrać opcję [wywołania procedury składowanej w ramach działania kopiowania](#invoke-a-stored-procedure-from-a-sql-sink). To podejście uruchamia każdy wiersz w tabeli źródłowej zamiast używać instrukcji BULK INSERT jako podejścia domyślnego w działaniu kopiowania, które nie jest odpowiednie dla upsert w dużej skali.

### <a name="overwrite-the-entire-table"></a>Zastąp całą tabelę

Właściwość **preCopyScript** można skonfigurować w ujścia działania kopiowania. W tym przypadku dla każdego działania kopiowania, które działa, Azure Data Factory uruchamia najpierw skrypt. Następnie uruchamia kopię, aby wstawić dane. Na przykład aby zastąpić całą tabelę najnowszymi danymi, należy określić skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym załadowaniem nowych danych ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapisz dane za pomocą logiki niestandardowej

Kroki zapisu danych za pomocą logiki niestandardowej są podobne do tych opisanych w sekcji [dane upsert](#upsert-data) . Jeśli musisz zastosować dodatkowe przetwarzanie przed ostatnim wstawieniem danych źródłowych do tabeli docelowej, możesz wykonać jedną z dwóch czynności: 

- Załaduj do tabeli tymczasowej, a następnie Wywołaj procedurę składowaną. 
- Wywołaj procedurę przechowywaną podczas kopiowania.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Wywoływanie procedury składowanej z ujścia SQL

Podczas kopiowania danych do bazy danych SQL Server, można także skonfigurować i wywołać procedurę składowaną określoną przez użytkownika z dodatkowymi parametrami. Funkcja procedury składowanej wykorzystuje parametry z [wartościami przechowywanymi w tabeli](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Wywołanie procedury składowanej przetwarza wiersz danych według wiersza zamiast przy użyciu operacji zbiorczej, której nie zalecamy w przypadku kopiowania na dużą skalę. Dowiedz się więcej z [najlepszych rozwiązań dotyczących ładowania danych do SQL Server](#best-practice-for-loading-data-into-sql-server).

Można użyć procedury składowanej, gdy wbudowane mechanizmy kopiowania nie służą do tego celu. Przykładem jest to, że chcesz zastosować dodatkowe przetwarzanie przed ostatnim wstawieniem danych źródłowych do tabeli docelowej. Niektóre dodatkowe przykłady przetwarzania są potrzebne do scalania kolumn, wyszukiwania dodatkowych wartości i wstawiania danych do więcej niż jednej tabeli.

Poniższy przykład przedstawia sposób użycia procedury składowanej do wykonania upsert w tabeli w bazie danych SQL Server. Załóżmy, że dane wejściowe i tabela **marketingu** ujścia mają trzy kolumny: **ProfileID**, **stan**i **Kategoria**. Zrób upsert na podstawie kolumny **ProfileID** i Zastosuj ją tylko dla określonej kategorii o nazwie "Product".

1. W swojej bazie danych Zdefiniuj typ tabeli o tej samej nazwie co **sqlWriterTableType**. Schemat typu tabeli jest taki sam jak schemat zwrócony przez dane wejściowe.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. W bazie danych Zdefiniuj procedurę składowaną o takiej samej nazwie jak **SqlWriterStoredProcedureName**. Obsługuje dane wejściowe z określonego źródła i scala do tabeli danych wyjściowych. Nazwa parametru typu tabeli w procedurze składowanej jest taka sama jak tabelaname zdefiniowana w zestawie danych.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. W Azure Data Factory Zdefiniuj sekcję **ujścia SQL** w działaniu kopiowania w następujący sposób:

    ```json
    "sink": {
        "type": "SqlSink",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "SqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapowanie typu danych dla SQL Server

Podczas kopiowania danych z i do SQL Server, następujące mapowania są używane z SQL Server typów danych do Azure Data Factory pośrednich typów danych. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych SQL Server | Azure Data Factory typ danych pośrednich |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> W przypadku typów danych, które są mapowane na typ pośredni dziesiętny, obecnie Azure Data Factory obsługuje precyzję do 28. Jeśli masz dane wymagające dokładności większej niż 28, Rozważ przekonwertowanie na ciąg w zapytaniu SQL.

## <a name="troubleshoot-connection-issues"></a>Rozwiązywanie problemów z połączeniem

1. Skonfiguruj wystąpienie SQL Server, aby akceptowało połączenia zdalne. Rozpocznij **SQL Server Management Studio**, kliknij prawym przyciskiem myszy pozycję **serwer**, a następnie wybierz pozycję **Właściwości**. Wybierz z listy pozycję **połączenia** , a następnie zaznacz pole wyboru **Zezwalaj na połączenia zdalne z tym serwerem** .

    ![Włącz połączenia zdalne](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie opcji konfiguracji serwera dostępu zdalnego](https://msdn.microsoft.com/library/ms191464.aspx).

2. Rozpocznij **SQL Server Configuration Manager**. Rozwiń **SQL Server konfigurację sieci** dla żądanego wystąpienia, a następnie wybierz pozycję **Protokoły dla MSSQLSERVER**. Protokoły są wyświetlane w okienku po prawej stronie. Aby włączyć protokół TCP/IP, kliknij prawym przyciskiem myszy pozycję **TCP/IP** , a następnie wybierz pozycję **Włącz**.

    ![Włącz protokół TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Aby uzyskać więcej informacji i alternatywne sposoby włączania protokołu TCP/IP, zobacz [Włączanie lub wyłączanie protokołu sieciowego serwera](https://msdn.microsoft.com/library/ms191294.aspx).

3. W tym samym oknie kliknij dwukrotnie pozycję **TCP/IP** , aby uruchomić okno **właściwości protokołu TCP/IP** .
4. Przejdź do karty **adresy IP** . Przewiń w dół, aby wyświetlić sekcję **IPAll** . Zapisz **port TCP**. Wartość domyślna to **1433**.
5. Utwórz **regułę dla zapory systemu Windows** na komputerze, aby zezwolić na ruch przychodzący przez ten port. 
6. **Sprawdź połączenie**: Aby nawiązać połączenie z SQL Server przy użyciu w pełni kwalifikowanej nazwy, użyj SQL Server Management Studio z innego komputera. Może to być na przykład `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
