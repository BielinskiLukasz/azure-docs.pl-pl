---
title: 'Szybki start: pozyskiwanie danych przy użyciu zestawu Azure Data Explorer .NET Standard SDK (wersja zapoznawcza)'
description: Z tego przewodnika Szybki start dowiesz się, jak pozyskiwać (ładować) dane do usługi Azure Data Explorer za pomocą zestawu .NET Standard SDK.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/18/2018
ms.openlocfilehash: b0e8c4dabea6aeae8d93d64d97b598ec97b2d18a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52277097"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Szybki start: pozyskiwanie danych przy użyciu zestawu Azure Data Explorer .NET Standard SDK (wersja zapoznawcza)

Azure Data Explorer (ADX) to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa ADX udostępnia dwie biblioteki klienckie dla platformy .NET Standard: [bibliotekę pozyskiwania](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) i [bibliotekę danych](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Te biblioteki umożliwiają pozyskiwanie (ładowanie) danych do klastra i wykonywanie zapytań o dane z kodu. W tym przewodniku Szybki start najpierw utworzysz tabelę i mapowanie danych w klastrze testowym. Następnie umieścisz pozyskiwanie w kolejce do klastra i sprawdzisz poprawność wyników.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* [Klaster testowy i baza danych](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Instalowanie biblioteki pozyskiwania

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

Usługa Azure Data Explorer korzysta z identyfikatora dzierżawy usługi AAD w celu uwierzytelnienia aplikacji. Aby odnaleźć identyfikator dzierżawy, użyj następującego adresu URL, zastępując ciąg *YourDomain* nazwą domeny.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Jeśli na przykład Twoja domena to *contoso.com*, adres URL to [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknij ten adres URL, aby wyświetlić wyniki. Pierwszy wiersz wygląda w następujący sposób. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Identyfikator dzierżawy w tym przypadku to `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

W tym przykładzie na potrzeby uwierzytelniania w celu uzyskiwania dostępu do klastra używana jest nazwa użytkownika i hasło usługi AAD. Możesz również użyć certyfikatu aplikacji usługi AAD i klucza aplikacji usługi ADD. Zanim uruchomisz ten kod, ustaw własne wartości dla parametrów `tenantId`, `user` i `password`.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Tworzenie parametrów połączenia
Teraz możesz utworzyć parametry połączenia. W kolejnym kroku utworzysz tabelę docelową i mapowanie.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Ustawianie informacji o pliku źródłowym

Ustaw stałe dla pliku źródła danych. W tym przykładzie używany jest przykładowy plik hostowany w usłudze Azure Blob Storage. Przykładowy zestaw danych **StormEvents** zawiera dane dotyczące pogody pochodzące z organizacji [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Tworzenie tabeli w klastrze testowym
Utwórz tabelę, która będzie zgodna ze schematem danych w pliku `StormEvents.csv`. Uruchomiony kod zwraca komunikat podobny do następującego: *To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F3W4VWZDM to authenticate* (Aby się zalogować, użyj przeglądarki internetowej, aby otworzyć stronę https://microsoft.com/devicelogin, i wprowadź kod F3W4VWZDM w celu uwierzytelnienia). Postępuj zgodnie z instrukcjami, aby się zalogować, a następnie wróć w celu uruchomienia kolejnego bloku kodu. Kolejne bloki kodu umożliwiające nawiązanie połączenia wymagają ponownego zalogowania.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Definiowanie mapowania pozyskiwania

Zmapuj przychodzące dane CSV na nazwy kolumn i typy danych używane podczas tworzenia tabeli.
Aprowizacja [obiektu mapowania kolumn CSV](/azure/kusto/management/tables#create-ingestion-mapping) w tej tabeli

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 21 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 22 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Wysyłanie komunikatu do kolejki w celu pozyskiwania

Wyślij komunikat do kolejki, aby ściągnąć dane z magazynu obiektów blob i pozyskać dane do usługi ADX.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Weryfikowanie, czy dane zostały pozyskane do tabeli

Poczekaj od pięciu do dziesięciu minut, aby umieszczone w kolejce pozyskiwanie zostało zaplanowane do pozyskiwania i załadowania danych do usługi ADX. Następnie uruchom następujący kod, aby uzyskać liczbę rekordów w tabeli StormEvents.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = "StormEvents | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Uruchamianie zapytań dotyczących rozwiązywania problemów

Zaloguj się do portalu [https://dataexplorer.azure.com](https://dataexplorer.azure.com) i nawiąż połączenie z klastrem. Uruchom następujące polecenie w bazie danych, aby sprawdzić, czy wystąpiły jakieś niepowodzenia pozyskiwania w ciągu ostatnich czterech godzin. Przed uruchomieniem zastąp nazwę bazy danych.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Uruchom następujące polecenie, aby wyświetlić stan wszystkich operacji pozyskiwania z ostatnich czterech godzin. Przed uruchomieniem zastąp nazwę bazy danych.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz skorzystać z naszych pozostałych przewodników Szybki start i samouczków, zachowaj utworzone zasoby. W przeciwnym razie uruchom następujące polecenie w bazie danych, aby wyczyścić tabelę StormEvents.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pisanie zapytań](write-queries.md)