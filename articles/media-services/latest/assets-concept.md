---
title: Zasoby w usłudze Media Services — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis zasoby są i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/19/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ec2ddbac5d0368aaf1b46208c9ebb44bf12a622
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447314"
---
# <a name="assets"></a>Elementy zawartości

W usłudze Azure Media Services [zasobów](https://docs.microsoft.com/rest/api/media/assets) zawiera pliki cyfrowe (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów) oraz metadane dotyczące tych plików. Przekazane pliki cyfrowe do elementu zawartości używać w usłudze Media Services, kodowanie, przesyłanie strumieniowe, analizowanie zawartości przepływów pracy. Aby uzyskać więcej informacji, zobacz [przekazać pliki cyfrowe do zasobów](#upload-digital-files-into-assets) poniższej sekcji.

Element zawartości jest mapowany na kontener obiektów blob w [konta usługi Azure Storage](storage-account-concept.md) i pliki w elemencie zawartości są przechowywane jako blokowe obiekty BLOB w kontenerze. Usługa Media Services obsługuje warstwy obiektu Blob, gdy konto korzysta z ogólnego przeznaczenia w wersji 2 (GPv2) magazynu. Konta GPv2, umożliwia przeniesienie plików [chłodna lub archiwum](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archiwum** magazynu nadaje się do archiwizacji plików źródłowych, gdy nie będą już potrzebne (na przykład po został zakodowany).

**Archiwum** warstwy magazynowania jest zalecane tylko dla plików źródłowych bardzo duże, które już zaszyfrowana i kodowania dane wyjściowe zadania został umieszczony w kontenerze obiektów blob danych wyjściowych. Obiekty BLOB w kontenerze danych wyjściowych, który chcesz skojarzyć z zasobu i użycia, przesłać strumieniowo lub analizować zawartość, musi istnieć w **gorąca** lub **chłodna** warstwy magazynowania.

> [!NOTE]
> Właściwości zasobu typu Data/Godzina, zawsze znajdują się w formacie UTC.

## <a name="upload-digital-files-into-assets"></a>Przekazać pliki cyfrowe do zasobów

Jednym z typowych przepływów pracy usługi Media Services jest przekazywanie, kodowanie i przesyłanie strumieniowe plików. W tej sekcji opisano ogólne kroki.

1. Użyj interfejsu API usługi Media Services v3, aby utworzyć nowy zasób "danych wejściowych". Ta operacja tworzy kontener w ramach konta magazynu skojarzone z kontem usługi Media Services. Interfejs API zwraca nazwę kontenera (na przykład `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Jeśli masz już kontener obiektów blob, który chcesz skojarzyć z zasobem, należy określić nazwę kontenera, podczas tworzenia elementu zawartości. Usługa Media Services aktualnie obsługuje tylko obiekty BLOB w katalogu głównym kontenera, a nie przy użyciu ścieżki w nazwie pliku. W związku z tym kontener o nazwie "input.mp4" będą działać. Kontener o nazwie "videos/inputs/input.mp4", nie będzie działać.

    Za pomocą wiersza polecenia platformy Azure można przekazać bezpośrednio do dowolnego konta magazynu i kontener, w którym masz uprawnienia do subskrypcji. <br/>Nazwa kontenera musi być unikatowa i postępuj zgodnie z magazynu wskazówki dotyczące nazewnictwa. Nazwa nie musi postępuj zgodnie z elementu zawartości multimediów usługi nazwa kontenera (identyfikator GUID zasobu), formatowanie. 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Pobierz adres URL sygnatury dostępu Współdzielonego z uprawnieniami odczytu / zapisu, używane można przekazać pliki cyfrowe do kontenera zasobów. Można użyć interfejsu API Media Services do [listy adresów URL kontenera zasobów](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Za pomocą interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md), [zestawu JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), lub [zestawu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) przekazywanie plików do kontenera zasobów. 
4. Użyj interfejsów API w wersji 3 usługa Media Services, aby utworzyć przekształcenie i zadania przetwarzania "danych wejściowych" element zawartości. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md).
5. Stream zawartość z zasobu "Wyjście".

Aby uzyskać pełny przykład .NET, który pokazuje, jak: tworzenia zasobu, Pobierz adres URL zapisu sygnatury dostępu Współdzielonego do zasobu kontenera w magazynie, przekazać plik do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu Współdzielonego, zobacz [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Utwórz nowy zasób

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Na przykład REST, zobacz [utworzenie elementu zawartości z użyciem usług REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) przykład.

W przykładzie pokazano sposób tworzenia **treść żądania** której można określić przydatne informacje, takie jak opis, nazwa kontenera, konto magazynu oraz inne informacje.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Aby uzyskać pełny przykład, zobacz [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md). W wersji 3 usługa Media Services, dane wejściowe zadania można również utworzyć z adresów URL protokołu HTTPS (zobacz [tworzenie dane wejściowe zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w usłudze Media Services:

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usługi Media Services|AES-256 szyfrowania kluczy zarządzanych przez usługę Media Services|Obsługiwane<sup>(1)</sup>|Nieobsługiwane<sup>(2)</sup>|
|[Szyfrowanie usługi Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera, oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta, oferowane przez usługę Azure storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> a Media Services obsługuje obsługi zawartości, bez zabezpieczeń/bez jakiejkolwiek formy szyfrowania, to nie jest to zalecane.

<sup>2</sup> Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest tylko obsługiwane dla zapewnienia zgodności gdy Twoje zasoby zostały utworzone za pomocą usługi Media Services v2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

## <a name="next-steps"></a>Kolejne kroki

* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Za pomocą funkcji DVR w chmurze](live-event-cloud-dvr.md)
* [Różnice między Media Services v2 i v3](migrate-from-v2-to-v3.md)
