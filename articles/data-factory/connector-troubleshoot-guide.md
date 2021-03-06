---
title: Rozwiązywanie problemów z łącznikami usługi Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z łącznikiem w Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 2e54c0b09c3dbe398b0522d0ad9ad2314e29ed26
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638146"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Rozwiązywanie problemów z łącznikami usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z łącznikami w Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Kod błędu: AzureBlobOperationFailed

- **Komunikat** : `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Przyczyna** : problem z trafieniem magazynu obiektów BLOB.

- **Zalecenie** : Sprawdź błąd w obszarze Szczegóły. Zapoznaj się z dokumentem pomocy obiektu BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Skontaktuj się z zespołem ds. magazynu, jeśli potrzebujesz pomocy.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Kod błędu: AzureBlobServiceNotReturnExpectedDataLength

- **Komunikat** : `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Kod błędu: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Komunikat** : `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Kod błędu: AzureStorageOperationFailedConcurrentWrite

- **Komunikat** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Komunikat o błędzie: rozmiar żądania jest zbyt duży

- **Objawy** : dane są kopiowane do Azure Cosmos dB przy użyciu domyślnego rozmiaru wsadu zapisu i wystąpił błąd *" **rozmiar żądania jest zbyt duży** "* .

- **Przyczyna** : Cosmos DB ogranicza jeden rozmiar pojedynczego żądania do 2 MB. Formuła jest, rozmiar żądania = rozmiar pojedynczego dokumentu * Zapisz rozmiar partii. Jeśli rozmiar dokumentu jest duży, zachowanie domyślne spowoduje zbyt duży rozmiar żądania. Można dostosować rozmiar partii zapisu.

- **Rozwiązanie** : w ujścia działania kopiowania Zmniejsz wartość w polu "Zapisz rozmiar partii" (wartość domyślna to 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Komunikat o błędzie: naruszenie ograniczenia indeksu unikatowego

- **Objawy** : podczas kopiowania danych do Cosmos DB można napotkać następujący błąd:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Przyczyna** : Istnieją dwie możliwe przyczyny:

    - Jeśli używasz funkcji **Wstaw** jako zachowanie zapisu, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty o takim samym identyfikatorze.

    - Jeśli używasz **upsert** jako zachowania zapisu i ustawisz kolejny unikatowy klucz do kontenera, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty z różnymi identyfikatorami, ale te same wartości zdefiniowanego unikatowego klucza.

- **Rozwiązanie** : 

    - W przypadku cause1 Ustaw **upsert** jako zachowanie zapisu.
    - W przypadku przyczyny 2 Upewnij się, że każdy dokument ma inną wartość dla zdefiniowanego unikatowego klucza.

### <a name="error-message-request-rate-is-large"></a>Komunikat o błędzie: częstotliwość żądań jest duża

- **Objawy** : podczas kopiowania danych do Cosmos DB można napotkać następujący błąd:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Przyczyna** : użyte jednostki żądania są większe niż dostępne ru skonfigurowane w Cosmos DB. Dowiedz się, jak Cosmos DB oblicza RU z tego [miejsca](../cosmos-db/request-units.md#request-unit-considerations).

- **Rozwiązanie** : Oto dwa rozwiązania:

    1. **Zwiększ wartość kontenera ru** do większej w Cosmos DB, co poprawi wydajność działania kopiowania, ale powiąże się z dodatkowym kosztem Cosmos DB. 

    2. Zmniejsz **writeBatchSize** do mniejszej wartości (na przykład 1000), a następnie ustaw **parallelCopies** na mniejszą wartość, taką jak 1, co spowoduje, że wydajność działania kopii będzie gorsza niż bieżąca, ale nie spowoduje to ponoszenia dodatkowych kosztów w Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Brak kolumny w mapowaniu kolumny

- **Objawy** : podczas importowania schematu dla Cosmos DB na potrzeby mapowania kolumn brakuje niektórych kolumn. 

- **Przyczyna** : ADF zawnioskuje schemat z pierwszych 10 Cosmos DB dokumentów. Jeśli niektóre kolumny/właściwości nie mają wartości w tych dokumentach, nie będą one wykrywane przez ADF, dlatego nie będą wyświetlane.

- **Rozwiązanie** : można dostosować zapytanie jak poniżej, aby wymusić wyświetlenie kolumny w zestawie wyników z wartością pustą: (Załóżmy, że w pierwszych 10 dokumentach brakuje kolumny "niemożliwe"). Alternatywnie można ręcznie dodać kolumnę do mapowania.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Komunikat o błędzie: GuidRepresentation dla czytnika to CSharpLegacy

- **Objawy** : podczas kopiowania danych z Cosmos DB MongoAPI/MongoDB z użyciem pola UUID wystąpił następujący błąd:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Przyczyna** : Istnieją dwa sposoby reprezentowania identyfikatora UUID w BSON-UuidStardard i UuidLegacy. Domyślnie UuidLegacy jest używany do odczytywania danych. Zostanie trafiony błąd, jeśli dane UUID w MongoDB jest UuidStandard.

- **Rozwiązanie** : w parametrach połączenia MongoDB Dodaj opcję " **uuidRepresentation = Standard** ". Aby uzyskać więcej informacji, zobacz [MongoDB parametry połączenia](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

### <a name="error-code--adlsgen2operationfailed"></a>Kod błędu: AdlsGen2OperationFailed

- **Komunikat** : `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Przyczyna** : ADLS Gen2 zgłasza błąd wskazujący, że operacja nie powiodła się.

- **Zalecenie** : Sprawdź szczegółowy komunikat o błędzie zgłoszony przez ADLS Gen2. Jeśli jest to spowodowane błędem przejściowym, spróbuj ponownie. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.

- **Przyczyna** : Jeśli komunikat o błędzie zawiera wartość "zabronione", nazwa główna usługi lub tożsamość zarządzana może nie mieć wystarczających uprawnień, aby uzyskać dostęp do ADLS Gen2.

- **Zalecenie** : Zapoznaj się z dokumentem pomocy: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Przyczyna** : Jeśli komunikat o błędzie zawiera wartość "InternalServerError", błąd jest zwracany przez ADLS Gen2.

- **Zalecenie** : może to być spowodowane błędem przejściowym, spróbuj ponownie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.


### <a name="error-code--adlsgen2invalidurl"></a>Kod błędu: AdlsGen2InvalidUrl

- **Komunikat** : `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Kod błędu: AdlsGen2InvalidFolderPath

- **Komunikat** : `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Kod błędu: AdlsGen2OperationFailedConcurrentWrite

- **Komunikat** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>Kod błędu: AdlsGen2TimeoutError

- **Komunikat** : `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Komunikat o błędzie: Połączenie podstawowe zostało zamknięte: nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS.

- **Objawy** : działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu: 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **Przyczyna** : Walidacja certyfikatu nie powiodła się podczas UZGADNIANIA protokołu TLS.

- **Rozwiązanie** : obejście: Użyj kopii etapowej, aby pominąć weryfikację TLS dla ADLS Gen1. Trzeba odtworzyć ten problem i zebrać ślad netmon, a następnie skontaktować się z zespołem sieci, aby sprawdzić konfigurację sieci lokalnej.

    ![Rozwiązywanie problemów ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Komunikat o błędzie: serwer zdalny zwrócił błąd: (403) zabroniony

- **Objawy** : działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Przyczyna** : jedną z możliwych przyczyn jest to, że główna lub zarządzana tożsamość usługi nie ma uprawnień dostępu do określonego folderu/pliku.

- **Rozwiązanie** : Przyznaj odpowiednie uprawnienia do wszystkich folderów i podfolderów, które chcesz skopiować. Zapoznaj się z [tym dokumentem](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Komunikat o błędzie: nie można uzyskać tokenu dostępu przy użyciu nazwy głównej usługi. Błąd ADAL: service_unavailable

- **Objawy** : działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Przyczyna** : gdy serwer tokenów usług (STS) należący do Azure Active Directory nie jest niedostępny, czyli zbyt zajęty, aby obsługiwać żądania, zwraca błąd HTTP 503. 

- **Rozwiązanie** : ponownie uruchom działanie kopiowania po kilku minutach.
                  

## <a name="azure-synapse-analytics-formerly-sql-data-warehouseazure-sql-databasesql-server"></a>Azure Synapse Analytics (dawniej SQL Data Warehouse)/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Kod błędu: SqlFailedToConnect

- **Komunikat** : `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Przyczyna** : Jeśli komunikat o błędzie zawiera wartość "SqlException", SQL Database zgłasza błąd wskazujący, że określona operacja nie powiodła się.

- **Zalecenie** : Wyszukaj według kodu błędu SQL w tym dokumencie odwołania, aby uzyskać więcej szczegółów: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna** : Jeśli komunikat o błędzie zawiera wartość "klient z adresem IP"... " nie ma dostępu do serwera "i próbujesz nawiązać połączenie z Azure SQL Database, zwykle jest to spowodowane przez Azure SQL Database problem z zaporą.

- **Zalecenie** : w logicznej konfiguracji zapory programu SQL Server Włącz opcję "Zezwalaj na dostęp do tego serwera dla usług i zasobów platformy Azure". Doc Reference: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Kod błędu: SqlOperationFailed

- **Komunikat** : `A database operation failed. Please search error to get more details.`

- **Przyczyna** : Jeśli komunikat o błędzie zawiera wartość "SqlException", SQL Database zgłasza błąd wskazujący, że określona operacja nie powiodła się.

- **Zalecenie** : Jeśli błąd SQL nie jest wyczyszczony, spróbuj zmienić bazę danych na najnowszy poziom zgodności "150". Może zgłosić najnowsze wersje programu SQL Server. Zapoznaj się z [dokumentem szczegółowym](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Aby rozwiązać problemy związane z programem SQL, wyszukaj kod błędu SQL w tym dokumencie odwołania, aby uzyskać więcej szczegółów: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna** : Jeśli komunikat o błędzie zawiera "PdwManagedToNativeInteropException", zazwyczaj jest spowodowany niezgodnością rozmiarów kolumn źródłowych i ujścia.

- **Zalecenie** : Sprawdź rozmiar kolumn źródłowych i ujścia. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna** : Jeśli komunikat o błędzie zawiera wartość "InvalidOperationException", zazwyczaj jest to spowodowane przez nieprawidłowe dane wejściowe.

- **Zalecenie** : aby ustalić, który wiersz napotyka problem, Włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu przeprowadzenia dalszej analizy. Doc Reference: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .



### <a name="error-code--sqlunauthorizedaccess"></a>Kod błędu: SqlUnauthorizedAccess

- **Komunikat** : `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Przyczyna** : poświadczenie jest niepoprawne lub konto logowania nie może uzyskać dostępu do SQL Database.

- **Zalecenie** : Sprawdź, czy konto logowania ma wystarczające uprawnienia dostępu do SQL Database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Kod błędu: SqlOpenConnectionTimeout

- **Komunikat** : `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Przyczyna** : może być SQL Database błąd przejściowy.

- **Zalecenie** : spróbuj ponownie zaktualizować parametry połączenia połączonej usługi z większą wartością limitu czasu połączenia.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Kod błędu: SqlAutoCreateTableTypeMapFailed

- **Komunikat** : `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Przyczyna** : tabela autotworzenia nie może spełnić wymagań źródłowych.

- **Zalecenie** : zaktualizuj typ kolumny w polu "mapowania" lub ręcznie Utwórz tabelę ujścia na serwerze docelowym.


### <a name="error-code--sqldatatypenotsupported"></a>Kod błędu: SqlDataTypeNotSupported

- **Komunikat** : `A database operation failed. Check the SQL errors.`

- **Przyczyna** : Jeśli problem występuje w źródle SQL, a błąd jest związany z przepełnieniem SqlDateTime, wartość danych jest poza zakresem typów logiki (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Zalecenie** : rzutowanie typu na ciąg w źródłowym zapytaniu SQL lub w mapowaniu kolumny działania kopiowania Zmień typ kolumny na "String".

- **Przyczyna** : Jeśli problem występuje w usłudze SQL sink i błąd jest związany z przepełnieniem elementu SqlDateTime, wartość danych jest poza dozwolonym zakresem w tabeli ujścia.

- **Zalecenie** : zaktualizuj odpowiedni typ kolumny na typ "datetime2" w tabeli sink.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Kod błędu: SqlInvalidDbStoredProcedure

- **Komunikat** : `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Przyczyna** : określona procedura składowana jest nieprawidłowa. Może to być spowodowane tym, że procedura składowana nie zwraca żadnych danych.

- **Zalecenie** : Sprawdź poprawność procedury składowanej przez narzędzia SQL. Upewnij się, że procedura składowana może zwracać dane.


### <a name="error-code--sqlinvaliddbquerystring"></a>Kod błędu: SqlInvalidDbQueryString

- **Komunikat** : `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Przyczyna** : określone zapytanie SQL jest nieprawidłowe. Przyczyną może być to, że zapytanie nie zwraca żadnych danych

- **Zalecenie** : Sprawdź poprawność zapytania SQL przez narzędzia SQL. Upewnij się, że zapytanie może zwracać dane.


### <a name="error-code--sqlinvalidcolumnname"></a>Kod błędu: SqlInvalidColumnName

- **Komunikat** : `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Przyczyna** : nie można odnaleźć kolumny. Nieprawidłowa konfiguracja.

- **Zalecenie** : Sprawdź kolumnę w kwerendzie, "Structure" w zestawie danych i w działaniu.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Kod błędu: SqlColumnNameMismatchByCaseSensitive

- **Komunikat** : `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Kod błędu: SqlBatchWriteTimeout

- **Komunikat** : `Timeouts in SQL write operation.`

- **Przyczyna** : może być SQL Database błąd przejściowy.

- **Zalecenie** : ponów próbę. Jeśli problem Odtwórz, skontaktuj się z pomocą techniczną Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Kod błędu: SqlBatchWriteTransactionFailed

- **Komunikat** : `SQL transaction commits failed`

- **Przyczyna** : Jeśli szczegóły wyjątku stale informują o limicie czasu transakcji, opóźnienie sieci między środowiskiem Integration Runtime i bazą danych jest większe niż domyślna wartość progowa co 30 sekund.

- **Zalecenie** : zaktualizuj parametry połączenia połączonej usługi SQL z wartością "limit czasu połączenia" równą 120 lub wyższej i ponownie uruchom działanie.

- **Przyczyna** : Jeśli szczegóły wyjątku sporadycznie informują o przerwaniu usługi SqlConnection, może to być tylko przejściowy błąd sieci lub SQL Database problemu ze stroną

- **Zalecenie** : spróbuj ponownie wykonać działanie i przejrzyj metryki po stronie SQL Database.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Kod błędu: SqlBulkCopyInvalidColumnLength

- **Komunikat** : `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Przyczyna** : Kopiowanie zbiorcze SQL nie powiodło się z powodu odebrania nieprawidłowej długości kolumny od klienta bcp.

- **Zalecenie** : aby ustalić, który wiersz napotyka problem, Włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu przeprowadzenia dalszej analizy. Doc Reference: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Kod błędu: SqlConnectionIsClosed

- **Komunikat** : `The connection is closed by SQL Database.`

- **Przyczyna** : połączenie SQL jest zamknięte przez SQL Database w przypadku dużego połączenia współbieżnego przebiegu i przerwania serwera.

- **Zalecenie** : serwer zdalny zamknął połączenie SQL. Spróbuj ponownie wykonać. Jeśli problem Odtwórz, skontaktuj się z pomocą techniczną Azure SQL.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Kod błędu: SqlCreateTableFailedUnsupportedType

- **Komunikat** : `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Komunikat o błędzie: konwersja nie powiodła się podczas konwertowania ciągu znaków na unikatowy identyfikator

- **Objawy** : w przypadku kopiowania danych ze źródła danych tabelarycznych (takich jak SQL Server) do usługi Azure Synapse Analytics przy użyciu kopiowania etapowego i wielopodstawa, wystąpił następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Przyczyna** : baza narzędziowa usługi Azure Synapse Analytics nie może przekonwertować pustego ciągu na identyfikator GUID.

- **Rozwiązanie** : w ujścia działania kopiowania w obszarze Ustawienia bazy danych ustaw wartość false dla opcji **Użyj typu domyślnego** .

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Komunikat o błędzie: Oczekiwano typu danych: DECIMAL (x, x), wartość powodująca problemy

- **Objawy** : w przypadku kopiowania danych ze źródła danych tabelarycznych (takich jak SQL Server) do usługi Azure Synapse Analytics przy użyciu kopiowania etapowego i wielopodstawa, wystąpił następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Przyczyna** : Baza wiedzy Azure Synapse Analytics Base nie może wstawić pustego ciągu (wartość null) do kolumny dziesiętnej.

- **Rozwiązanie** : w ujścia działania kopiowania w obszarze Ustawienia bazy danych ustaw wartość false dla opcji **Użyj typu domyślnego** .

### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Komunikat o błędzie: komunikat o wyjątku Java: HdfsBridge:: CreateRecordReader

- **Objawy** : skopiujesz dane do usługi Azure Synapse Analytics przy użyciu bazy danych Base i wystąpił następujący błąd:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Przyczyna** : możliwe przyczyny jest to, że schemat (całkowita szerokość kolumny) jest zbyt duży (większy niż 1 MB). Sprawdź schemat docelowej tabeli usługi Azure Synapse Analytics przez dodanie rozmiaru wszystkich kolumn:

    - Int-> 4 bajty
    - Bigint-> 8 bajtów
    - Varchar (n), char (n), Binary (n), varbinary (n)-> n bajtów
    - Nvarchar (n), nchar (n)-> n * 2 bajty
    - Data — > 6 bajtów
    - DateTime/(2), smalldatetime-> 16 bajtów
    - DateTimeOffset — > 20 bajtów
    - Liczba dziesiętna — > 19 bajtów
    - Float-> 8 bajtów
    - Money-> 8 bajtów
    - Smallmoney — > 4 bajty
    - Real-> 4 bajty
    - Smallint — > 2 bajty
    - Czas — > 12 bajtów
    - Tinyint — > 1 bajt

- **Rozdzielczość** : Zmniejsz szerokość kolumny, aby była mniejsza niż 1 MB

- Lub użyj metody wstawiania zbiorczego, wyłączając bazę

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Komunikat o błędzie: warunek określony przy użyciu nagłówków warunkowych HTTP nie jest spełniony

- **Objawy** : używasz zapytania SQL do ściągania danych z usługi Azure Synapse Analytics i wystąpił następujący błąd:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Przyczyna** : problem z analizą usługi Azure Synapse Analytics w tabeli zewnętrznej w usłudze Azure Storage.

- **Rozwiązanie** : Uruchom to samo zapytanie w programie SSMS i sprawdź, czy widzisz ten sam wynik. Jeśli tak, Otwórz bilet pomocy technicznej w usłudze Azure Synapse Analytics i podaj nazwę serwera usługi Azure Synapse Analytics oraz bazę danych, aby kontynuować rozwiązywanie problemów.
            

## <a name="delimited-text-format"></a>Format tekstu rozdzielanego

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Kod błędu: DelimitedTextColumnNameNotAllowNull

- **Komunikat** : `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Przyczyna** : w przypadku ustawienia opcji "użycia" w działaniu pierwszy wiersz będzie używany jako nazwa kolumny. Ten błąd oznacza, że pierwszy wiersz zawiera wartość pustą. Na przykład: "ColumnName,, ColumnB".

- **Zalecenie** : zaznacz pierwszy wiersz i popraw wartość, jeśli wartość jest pusta.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Kod błędu: DelimitedTextMoreColumnsThanDefined

- **Komunikat** : `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Przyczyna** : liczba kolumn problematycznych wierszy jest duża niż liczba kolumn w pierwszym wierszu. Może to być spowodowane problemem z danymi lub nieprawidłowym ogranicznikiem kolumny/ustawieniami znaku cudzysłowu.

- **Zalecenie** : Pobierz liczbę wierszy w komunikacie o błędzie, Sprawdź kolumnę wiersza i popraw dane.

- **Przyczyna** : jeśli oczekiwana liczba kolumn jest równa "1" w komunikacie o błędzie, istnieje możliwość określenia nieprawidłowych ustawień kompresji lub formatu, które spowodowały niewłaściwe przeanalizowanie plików przez ADF.

- **Zalecenie** : Sprawdź ustawienia formatu, aby upewnić się, że jest ono zgodne z plikami źródłowymi.

- **Przyczyna** : Jeśli źródło jest folderem, istnieje możliwość, że pliki w określonym folderze mają inny schemat.

- **Zalecenie** : Upewnij się, że pliki w danym folderze mają identyczny schemat.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Kod błędu: DelimitedTextIncorrectRowDelimiter

- **Komunikat** : `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Kod błędu: DelimitedTextTooLargeColumnCount

- **Komunikat** : `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Kod błędu: DelimitedTextInvalidSettings

- **Komunikat** : `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Kod błędu: DynamicsCreateServiceClientError

- **Komunikat** : `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Przyczyna** : jest to przejściowy problem po stronie serwera Dynamics.

- **Zalecenie** : Uruchom ponownie potok. Jeśli zachowanie nie powiedzie się, spróbuj zmniejszyć liczbę równoległości. Jeśli nadal nie powiedzie się, skontaktuj się z pomocą techniczną systemu Dynamics.



## <a name="json-format"></a>Format JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Kod błędu: JsonInvalidArrayPathDefinition

- **Komunikat** : `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Kod błędu: JsonEmptyJObjectData

- **Komunikat** : `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Kod błędu: JsonNullValueInPathDefinition

- **Komunikat** : `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Kod błędu: JsonUnsupportedHierarchicalComplexValue

- **Komunikat** : `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Kod błędu: JsonConflictPartitionDiscoverySchema

- **Komunikat** : `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Kod błędu: JsonInvalidDataFormat

- **Komunikat** : `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Kod błędu: JsonInvalidDataMixedArrayAndObject

- **Komunikat** : `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Format Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Kod błędu: ParquetJavaInvocationException

- **Komunikat** : `An error occurred when invoking java, message: %javaException;.`

- **Przyczyna** : Jeśli komunikat o błędzie zawiera "Java. lang. OutOfMemory", "Space sterty Java" i "doubleCapacity", zazwyczaj jest to problem z zarządzaniem pamięcią w starej wersji środowiska Integration Runtime.

- **Zalecenie** : Jeśli korzystasz z samodzielnej Integration Runtime, a wersja jest wcześniejsza niż 3.20.7159.1, zaleca się uaktualnienie do najnowszej wersji.

- **Przyczyna** : Jeśli komunikat o błędzie zawiera plik "Java. lang. OutOfMemory", środowisko Integration Runtime nie ma wystarczającej ilości zasobów, aby przetworzyć pliki.

- **Zalecenie** : Ogranicz współbieżne uruchomienia w środowisku Integration Runtime. W przypadku samodzielnej Integration Runtime skalowanie w górę do zaawansowanej maszyny z pamięcią równą lub większą niż 8 GB.

- **Przyczyna** : Jeśli komunikat o błędzie zawiera wartość "NullPointerReference", możliwe, że jest to błąd przejściowy.

- **Zalecenie** : ponów próbę. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.


### <a name="error-code--parquetinvalidfile"></a>Kod błędu: ParquetInvalidFile

- **Komunikat** : `File is not a valid parquet file.`

- **Przyczyna** : problem z plikiem Parquet.

- **Zalecenie** : Sprawdź, czy dane wejściowe są prawidłowym plikiem Parquet.


### <a name="error-code--parquetnotsupportedtype"></a>Kod błędu: ParquetNotSupportedType

- **Komunikat** : `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Przyczyna** : format Parquet nie jest obsługiwany w Azure Data Factory.

- **Zalecenie** : dokładnie sprawdź dane źródłowe. Zapoznaj się z dokumentem: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Kod błędu: ParquetMissedDecimalPrecisionScale

- **Komunikat** : `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Przyczyna** : Spróbuj przeanalizować dokładność i skalę liczby, ale nie ma takich informacji.

- **Zalecenie** : element "source" nie zwraca prawidłowych dokładności i skali. Sprawdź precyzję i skalę kolumny problemu.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Kod błędu: ParquetInvalidDecimalPrecisionScale

- **Komunikat** : `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Przyczyna** : schemat jest nieprawidłowy.

- **Zalecenie** : Sprawdź precyzję i skalę kolumny problemu.


### <a name="error-code--parquetcolumnnotfound"></a>Kod błędu: ParquetColumnNotFound

- **Komunikat** : `Column %column; does not exist in Parquet file.`

- **Przyczyna** : schemat źródłowy jest niezgodny ze schematem ujścia.

- **Zalecenie** : Sprawdź the'mappings "w" Activity ". Upewnij się, że kolumna źródłowa może być zmapowana do prawej kolumny ujścia.


### <a name="error-code--parquetinvaliddataformat"></a>Kod błędu: ParquetInvalidDataFormat

- **Komunikat** : `Incorrect format of %srcValue; for converting to %dstType;.`

- **Przyczyna** : nie można przekonwertować danych na typ określony w mapowaniu. Źródło

- **Zalecenie** : należy dokładnie sprawdzić dane źródłowe lub określić poprawny typ danych dla tej kolumny w mapowaniu kolumny działania kopiowania. Zapoznaj się z dokumentem: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Kod błędu: ParquetDataCountNotMatchColumnCount

- **Komunikat** : `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Przyczyna** : niezgodność liczby kolumn źródłowych i liczby kolumn ujścia

- **Zalecenie** : liczba kolumn źródłowych sprawdzania podwójnego jest taka sama jak liczba kolumn ujścia w elemencie "mapowanie".


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Kod błędu: ParquetDataTypeNotMatchColumnType

- **Komunikat** : typ danych% srcType; nie jest zgodny z podanym typem kolumny% dstType;. w kolumnie "% columnIndex;".

- **Przyczyna** : dane ze źródła nie mogą zostać skonwertowane na typ zdefiniowany w ujścia

- **Zalecenie** : Określ poprawny typ w mapowaniu. sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Kod błędu: ParquetBridgeInvalidData

- **Komunikat** : `%message;`

- **Przyczyna** : wartość danych przekracza ograniczenie

- **Zalecenie** : ponów próbę. Jeśli problem będzie się powtarzać, skontaktuj się z nami.


### <a name="error-code--parquetunsupportedinterpretation"></a>Kod błędu: ParquetUnsupportedInterpretation

- **Komunikat** : `The given interpretation '%interpretation;' of parquet format is not supported.`

- **Przyczyna** : nieobsługiwany scenariusz

- **Zalecenie** : element "ParquetInterpretFor" nie może być "sparkSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Kod błędu: ParquetUnsupportFileLevelCompressionOption

- **Komunikat** : `File level compression is not supported for Parquet.`

- **Przyczyna** : nieobsługiwany scenariusz

- **Zalecenie** : Usuń element "CompressionType" w ładunku.



## <a name="general-copy-activity-error"></a>Ogólny błąd działania kopiowania

### <a name="error-code--jrenotfound"></a>Kod błędu: JreNotFound

- **Komunikat** : `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Przyczyna** : własne środowisko Integration Runtime nie może znaleźć środowiska uruchomieniowego języka Java. Środowisko uruchomieniowe Java jest wymagane do odczytywania określonego źródła.

- **Zalecenie** : Sprawdź środowisko Integration Runtime, doc Reference: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Kod błędu: WildcardPathSinkNotSupported

- **Komunikat** : `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Przyczyna** : zestaw danych ujścia nie obsługuje symboli wieloznacznych.

- **Zalecenie** : Sprawdź zestaw danych ujścia i popraw ścieżkę bez symbolu wieloznacznego.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Kod błędu: MappingInvalidPropertyWithEmptyValue

- **Komunikat** : `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Kod błędu: MappingInvalidPropertyWithNamePathAndOrdinal

- **Komunikat** : `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Kod błędu: MappingDuplicatedOrdinal

- **Komunikat** : `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Kod błędu: MappingInvalidOrdinalForSinkColumn

- **Komunikat** : `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
