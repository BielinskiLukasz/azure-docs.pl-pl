---
title: Filtrowanie, zamawianie i stronicowanie encji usługi Media Services
titleSuffix: Azure Media Services
description: Dowiedz się więcej o filtrowaniu, zamawianiu i stronicowaniu jednostek usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: bc5c983bc98c3b62df977c6765978cd45cd3c93b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500036"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtrowanie, zamawianie i stronicowanie encji usługi Media Services

W tym temacie omówiono opcje kwerendy OData i obsługę podziałów na strony dostępne podczas wystawiania jednostek usługi Azure Media Services w wersji 3.

## <a name="considerations"></a>Zagadnienia do rozważenia

* Właściwości jednostek, które są `Datetime` typu są zawsze w formacie UTC.
* Biały znak w ciągu zapytania powinien być zakodowany w adresie URL przed wysłaniem żądania.

## <a name="comparison-operators"></a>Operatory porównania

Do porównania pola ze stałą wartością można użyć następujących operatorów:

Operatory równości:

- `eq`: Sprawdź, czy pole jest *równe* wartości stałej.
- `ne`: Sprawdź, czy pole nie jest *równe* wartości stałej.

Operatorzy asortymentu:

- `gt`: Sprawdź, czy pole jest *większe niż* wartość stała.
- `lt`: Sprawdź, czy pole jest *mniejsze niż* wartość stała.
- `ge`: Sprawdź, czy pole jest *większe lub równe* wartości stałej.
- `le`: Sprawdź, czy pole jest *mniejsze lub równe* wartości stałej.

## <a name="filter"></a>Filtr

Służy `$filter` do dostarczania parametru filtru OData, aby znaleźć tylko obiekty, które Cię interesują.

Poniższy przykład REST filtruje `alternateId` wartość środka trwałego:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Następujący przykład języka C# filtruje datę utworzenia zasobu:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Zamów według

Służy `$orderby` do sortowania zwróconych obiektów według określonego parametru. Przykład:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Aby posortować wyniki w kolejności rosnącej `asc` lub `desc` malejącej, należy dołączyć albo do nazwy pola, oddzielone spacją. Na przykład: `$orderby properties/created desc`.

## <a name="skip-token"></a>Pomiń token

Jeśli odpowiedź na kwerendę zawiera wiele `$skiptoken` `@odata.nextLink`elementów, usługa zwraca ( ) wartość, której używasz, aby uzyskać następną stronę wyników. Użyj go, aby przeglądać cały zestaw wyników.

W programie Media Services w wersji 3 nie można skonfigurować rozmiaru strony. Rozmiar strony zależy od typu encji. Przeczytaj poszczególne sekcje, które należy wykonać, aby uzyskać szczegółowe informacje.

Jeśli jednostki są tworzone lub usuwane podczas stronicowania za pośrednictwem kolekcji, zmiany są odzwierciedlane w zwróconych wyników (jeśli te zmiany znajdują się w części kolekcji, która nie została pobrana).

> [!TIP]
> Zawsze `nextLink` służy do wyliczanie kolekcji i nie zależą od określonego rozmiaru strony.
>
> Wartość `nextLink` będzie obecna tylko wtedy, gdy istnieje więcej niż jedna strona encji.

Rozważmy poniższy `$skiptoken` przykład, gdzie jest używany. Upewnij się, że zamienisz *konto amstestaccount* nazwą swojego konta i ustaw wartość *wersji interfejsu API* na najnowszą wersję.

Jeśli poprosisz o listę zasobów w ten sposób:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Otrzymasz odpowiedź podobną do tej:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Następnie należy poprosić o następną stronę, wysyłając żądanie otrzymasz:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

W poniższym przykładzie języka C# pokazano, jak wyliczyć za pośrednictwem wszystkich lokalizatorów przesyłania strumieniowego na koncie.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Łączenie opcji kwerendy za pomocą operatorów logicznych

Usługi Media Services w wersji 3 obsługuje operatory logiczne **OR** **i AND.** 

Poniższy przykład REST sprawdza stan zadania:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Konstruujesz tę samą kwerendę w języku C# w ten sposób: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opcje filtrowania i zamawiania encji

W poniższej tabeli przedstawiono, jak można zastosować opcje filtrowania i zamawiania do różnych encji:

|Nazwa jednostki|Nazwa właściwości|Filtr|Zamówienie|
|---|---|---|---|
|[Elementy zawartości](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` i `desc`|
||właściwości.alternateId |`eq`||
||właściwości.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` i `desc`|
|[Zasady kluczy zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||właściwości.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||właściwości.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||właściwości.policyId|`eq`, `ne`||
|[Stanowiska](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` i `desc`|
||właściwości.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|
||właściwości.lastModified | `gt`, `ge`, `lt`, `le` | `asc` i `desc`| 
|[Lokalizatory przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` i `desc`|
||właściwości.endCzas    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
|[Zasady przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
|[Przekształcenia](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` i `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|
|| właściwości.lastModified | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|

## <a name="next-steps"></a>Następne kroki

* [Lista zasobów](https://docs.microsoft.com/rest/api/media/assets/list)
* [Lista zasad klucza zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Lista zadań](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Lista zasad przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Lista lokalizatorów przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
