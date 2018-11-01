---
title: 'Szybki start: wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing w języku Java'
description: Ten przewodnik Szybki start umożliwia wyszukanie pierwszego obrazu przy użyciu zestawu SDK wyszukiwania obrazów Bing, który jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku Java wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 42fc3dfb48c041ba187e2034eed81183dba02cb4
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413881"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-java"></a>Szybki start: wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing i języka Java

Ten przewodnik Szybki start umożliwia wyszukanie pierwszego obrazu przy użyciu zestawu SDK wyszukiwania obrazów Bing, który jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku Java wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.

Kod źródłowy dla tego przykładu jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) wraz z dodatkową obsługą błędów i adnotacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Najnowsza wersja zestawu [Java Development Kit](https://aka.ms/azure-jdks) (JDK)

Zainstaluj zależności zestawu SDK wyszukiwania obrazów Bing przy użyciu oprogramowania Maven, Gradle lub innego systemu zarządzania zależnościami. Plik POM systemu Maven wymaga następującej deklaracji:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje import do implementacji klasy:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. W metodzie main utwórz zmienne dla klucza subskrypcji i szukanego terminu. Następnie utwórz wystąpienie klienta wyszukiwania obrazów Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-bing-image-search-api"></a>Wysyłanie żądania wyszukiwania do interfejsu API wyszukiwania obrazów Bing

1. Za pomocą metody `bingImages().search()` wyślij żądanie HTTP zawierające zapytanie wyszukiwania. Zapisz odpowiedź jako `ImagesModel`.
    ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analiza i wyświetlanie wyników

Analizuj wyniki obrazów zwróconych w odpowiedzi.
Jeśli odpowiedź zawiera wyniki wyszukiwania, zapisz pierwszy wynik i wyświetl jego szczegóły, takie jak adres URL miniatury i oryginalny adres URL, wraz z całkowitą liczbą zwróconych obrazów.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
    }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zobacz też

* [Czym jest funkcja wyszukiwania obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj interaktywny pokaz online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Pobieranie bezpłatnego klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Przykłady dla zestawu Azure Cognitive Services SDK w języku Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Dokumentacja usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
