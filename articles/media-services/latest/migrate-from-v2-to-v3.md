---
title: Migrowanie z usługi Azure Media Services v2 do v3 | Dokumentacja firmy Microsoft
description: W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services v3 i przedstawiono różnice między dwoma wersjami.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 11/07/2018
ms.author: juliako
ms.openlocfilehash: 1c8bacf4c6cb22240609fb9f5dc0c3c456bc4531
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287544"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Wskazówki dotyczące migracji do przenoszenia z usługi Media Services v2 do v3

W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services v3, przedstawiono różnice między dwoma wersjami i zawiera wskazówki dotyczące migracji.

Jeśli masz już dziś opracowanych w górnej części usługi wideo [starszej wersji usługi Media Services v2 API](../previous/media-services-overview.md), należy przejrzeć następujące wskazówki i uwagi dotyczące przed przeprowadzeniem migracji do interfejsów API w wersji 3. Istnieje wiele korzyści i nowe funkcje w wersji 3 interfejsu API, które zwiększają Środowisko deweloperskie i możliwości usługi Media Services. Jednak jako o nazwie w [znane problemy](#known-issues) sekcji tego artykułu, również istnieją pewne ograniczenia z powodu zmian między wersjami interfejsu API. Ta strona zostanie zachowana, jak zespół usługi Media Services umożliwia dalsze ulepszenia do interfejsów API w wersji 3 i luki między wersjami. 

## <a name="benefits-of-media-services-v3"></a>Zalety Media Services v3

### <a name="api-is-more-approachable"></a>Interfejs API jest bardziej przystępne.

*  Wersja 3 opiera się na ujednoliconej powierzchni interfejsu API, która udostępnia zarówno funkcje zarządzania, jak i operacji oparte na usłudze Azure Resource Manager. Szablony usługi Azure Resource Manager może służyć do tworzenia i wdrażania przekształceń, punkty końcowe przesyłania strumieniowego, LiveEvents i nie tylko.
* [Otwórz interfejs API (zwane również struktury Swagger) specyfikacji](https://aka.ms/ams-v3-rest-sdk) dokumentu.
    Przedstawia schematu dla wszystkich składników usługi, w tym kodowanie oparte na plikach.
* Zestawy SDK dostępne dla [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Przejdź](https://aka.ms/ams-v3-go-ref)i Ruby.
* [Interfejs wiersza polecenia Azure](https://aka.ms/ams-v3-cli-ref) integracji na potrzeby prostą obsługę skryptów.

### <a name="new-features"></a>Nowe funkcje

* Przetwarzanie zadania oparte na plikach można użyć adresu URL HTTP (S) jako dane wejściowe.
    Nie musisz mieć zawartości już przechowywane na platformie Azure i nie potrzebujesz do tworzenia zasobów.
* Pojęcia związane z [przekształca](transforms-jobs-concept.md) dla przetwarzanie zadań opartych na plikach. Przekształcenie może służyć do tworzenia konfiguracji wielokrotnego użytku, do tworzenia szablonów usługi Azure Resource Manager i izolowania przetwarzania ustawień między wieloma klientami lub dzierżawcy.
* Element zawartości może mieć [wielu StreamingLocators](streaming-locators-concept.md) każdego z różnymi ustawieniami funkcji dynamicznego tworzenia pakietów i szyfrowania dynamicznego.
* [Content protection](content-key-policy-concept.md) obsługuje wiele kluczowych funkcji.
* Można przesyłać strumieniowo wydarzenia na żywo, które są do 24 godzin długo po za pomocą usługi Media Services w celu przetranskodowania jej wkład pojedyncza szybkość transmisji bitów źródła danych do strumienia wyjściowego, który ma wielokrotnych.
* Nowe Niskie opóźnienie obsługę przesyłania strumieniowego na żywo na LiveEvents.
* Element LiveEvent (wersja zapoznawcza) obsługuje funkcję dynamicznego tworzenia pakietów i szyfrowania dynamicznego. Dzięki temu ochrony zawartości w wersji zapoznawczej, a także DASH i HLS pakowania.
* LiveOutput jest łatwiejszy w obsłudze niż jednostka Program w interfejsach API w wersji 2. 
* Masz kontroli dostępu opartej na rolach (RBAC) za pośrednictwem jednostek. 

## <a name="changes-from-v2"></a>Zmiany w wersji 2

* Dla zasoby utworzone w wersji 3, Media Services obsługuje tylko [szyfrowania magazynu po stronie serwera usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Za pomocą interfejsów API w wersji 3 zasoby utworzone za pomocą interfejsów API w wersji 2, który miał [szyfrowania magazynu](../previous/media-services-rest-storage-encryption.md) (AES 256) udostępnianego przez usługi multimediów.
    * Nie można tworzyć nowe zasoby za pomocą starszej wersji AES 256 [szyfrowania magazynu](../previous/media-services-rest-storage-encryption.md) przy użyciu interfejsów API w wersji 3.
* Zestawy SDK w wersji 3 są teraz całkowicie niezależni od zestawu SDK usługi Storage, co daje większą kontrolę nad wersję zestawu SDK usługi Storage ma być używany i eliminuje problemy z wersjonowaniem. 
* W interfejsach API w wersji 3 są wszystkie kodowania szybkości transmisji bitów w bitach na sekundę. Stanowi to odmianę v2, które ustawienia wstępne usługi Media Encoder Standard. Na przykład szybkości transmisji bitów w wersji 2 będzie można określić jako 128 (KB/s), ale w wersji 3 byłoby 128000 (bity/sekundę). 
* AssetFiles jednostek, AccessPolicies i IngestManifests nie istnieją w wersji 3.
* Kluczy zawartości nie jest już jednostki, jest teraz właściwość StreamingLocator.
* Obsługa siatki zdarzeń zastępuje NotificationEndpoints.
* Następujące jednostki zostały zmienione.
    * JobOutput zadań zastępuje i jest teraz częścią zadania.
    * StreamingLocator zastępuje lokalizatora.
    * Element LiveEvent zastępuje kanału.
        
        Rozliczenia LiveEvents opiera się na licznikach kanału na żywo. Aby uzyskać więcej informacji, zobacz [na żywo, przesyłanie strumieniowe Przegląd](live-streaming-overview.md#billing) i [ceny](https://azure.microsoft.com/pricing/details/media-services/).
    * LiveOutput zastępuje Program.
* LiveOutputs nie musiały zostać uruchomione w sposób jawny, start przy tworzeniu i Zatrzymaj po usunięciu. Programy działały inaczej w interfejsach API w wersji 2, mieli oni ma zostać uruchomiony po utworzeniu.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Funkcja luki w odniesieniu do interfejsów API w wersji 2

Interfejs API w wersji 3 ma następujące luki funkcji w odniesieniu do interfejsu API w wersji 2. Do wypełniania luk jest w toku.

* [Koder w warstwie Premium](../previous/media-services-premium-workflow-encoder-formats.md) i starszego [media analytics procesorów](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 — wersja zapoznawcza, usługi Face Redactor itp.) nie są dostępne za pośrednictwem v3.

    Klienci, którzy chcą przeprowadzić migrację z 1 indeksatora multimediów lub 2 (wersja zapoznawcza) można użyć od razu AudioAnalyzer wstępnie ustawione w interfejsie API w wersji 3.  To nowe ustawienie wstępne zawiera więcej funkcji niż starsze 1 indeksatora multimediów lub 2. 

* Wiele zaawansowanych funkcji usługi Media Encoder Standard w interfejsach API w wersji 2 nie są obecnie dostępne w wersji 3, takich jak:
    * Przycinanie (dla scenariuszy, na żądanie i na żywo)
    * Łączenie zasobów
    * Nakładki
    * Przycinanie
    * Miniatury ikony
* LiveEvents z transkodowanie obecnie obsługuje strumienia środku wstawiania Plansz, niestandardowych ustawień wstępnych ani wstawiania znaczników reklamy, za pośrednictwem wywołania interfejsu API. 

> [!NOTE]
> Zakładki w tym artykule i zachować sprawdzania dostępności aktualizacji.

## <a name="code-differences"></a>Różnice w kodzie

W poniższej tabeli przedstawiono różnice kodu między v2 i v3 dla typowych scenariuszy.

|Scenariusz|INTERFEJSY API WERSJI 2|W WERSJI 3 INTERFEJSU API|
|---|---|---|
|Utworzenie elementu zawartości i przekaż plik |[przykład dla środowiska .NET w wersji 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[przykład dla środowiska .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Przesyłanie zadania|[przykład dla środowiska .NET w wersji 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[przykład dla środowiska .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Pokazuje, jak utworzyć przekształcenie, a następnie przesyłać zadania.|
|Publikowanie elementu zawartości przy użyciu szyfrowania AES |1. Utwórz ContentKeyAuthorizationPolicyOption<br/>2. Utwórz ContentKeyAuthorizationPolicy<br/>3. Utwórz AssetDeliveryPolicy<br/>4. Tworzenie elementu zawartości i przekazywać zawartość lub Prześlij zadanie i korzystają z danych wyjściowych elementu zawartości<br/>5. Kojarzenie AssetDeliveryPolicy z elementem zawartości<br/>6. Utwórz ContentKey<br/>7. Dołącz ContentKey do zasobu<br/>8. Utwórz AccessPolicy<br/>9. Tworzenie lokalizatora<br/><br/>[przykład dla środowiska .NET w wersji 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Tworzenie zasad kluczy zawartości<br/>2. Utwórz zasób<br/>3. Przekazywanie zawartości lub Użyj zasobów jako JobOutput<br/>4. Utwórz StreamingLocator<br/><br/>[przykład dla środowiska .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|

## <a name="known-issues"></a>Znane problemy

* Obecnie nie można użyć witryny Azure portal do zarządzania zasobami v3. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-sdk), interfejsu wiersza polecenia lub w jednym z obsługiwanych zestawów SDK.
* Obecnie jednostek zarezerwowanych multimediów może zarządzać tylko przy użyciu interfejsu API w wersji 2 usługi Media Services. Aby uzyskać więcej informacji, zobacz [skalowanie przetwarzania multimediów](../previous/media-services-scale-media-processing-overview.md).
* Jednostki usługi Media Services, utworzone w wersji 3, interfejs API nie mogą być zarządzane przez interfejsy API wersji 2.  
* Nie zaleca się Zarządzanie jednostkami, które zostały utworzone za pomocą interfejsów API w wersji 2, za pośrednictwem interfejsów API w wersji 3. Poniżej przedstawiono przykłady różnic, wchodzące jednostkami w dwóch wersjach niezgodne:   
    * Zadania i zadania utworzone w wersji 2 nie są wyświetlane w wersji 3, ponieważ nie są one powiązane z zadaniami transformacji. Zalecane jest, aby przełączyć się do wersji 3 transformacje i zadania. Będzie konieczności monitor porządkowych w wersji 2 zadania podczas przełączenie okres względnie krótkim czasie.
    * Kanały i programy utworzone za pomocą wersji 2, (które są mapowane na LiveEvents i LiveOutputs w wersji 3) nie może kontynuować, zarządzane w wersji 3. Zalecane jest, aby przełączyć się do v3 LiveEvents i LiveOutputs na wygodne Zatrzymaj kanał.
    
        Obecnie nie można migrować, stale uruchomione kanały.  
> [!NOTE]
> Zakładki w tym artykule i zachować sprawdzania dostępności aktualizacji.

## <a name="next-steps"></a>Kolejne kroki

Aby sprawdzić, jak łatwo rozpocząć kodowanie i strumieniowe przesyłanie plików wideo, zobacz [Strumieniowe przesyłanie plików](stream-files-dotnet-quickstart.md). 

