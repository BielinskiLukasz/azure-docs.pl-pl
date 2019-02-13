---
title: 'Szybki start: Wyszukiwanie wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka C#'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wysyłać żądania wyszukiwania wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka C#.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: 820c6a57a4a8266b68c5a245c2c2cf316aa071a9
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569145"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Szybki start: Wyszukiwanie wideo za pomocą zestawu SDK wyszukiwania wideo Bing dla języka C#

Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwania wiadomości Bing dla języka C#. Chociaż wyszukiwanie wideo Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch). Zawiera on więcej adnotacji i funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
* Struktura [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) dostępna jako pakiet NuGet.

Aby dodać zestaw SDK wyszukiwania wideo Bing do projektu, przejdź do opcji `Manage NuGet Packages` w Eksploratorze rozwiązań w programie Visual Studio.  Dodaj pakiet `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Zainstalowanie [[zestawu NuGet Video Search SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)spowoduje również zainstalowanie następujących zależności:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowe rozwiązanie konsolowe dla języka C# w programie Visual Studio. Dodaj następujący kod do głównego pliku kodu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Utwórz wystąpienie klienta, tworząc nowy obiekt `ApiKeyServiceClientCredentials` za pomocą klucza subskrypcji, a następnie wywołaj konstruktor.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Wyślij żądanie wyszukiwania i przetwórz wyniki

1. Użyj klienta do wysłania żądania wyszukiwania. Użyj elementu „SwiftKey” na potrzeby zapytania wyszukiwania.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Jeśli zostały zwrócone jakiekolwiek wyniki, pobierz pierwszy z nich przy użyciu funkcji `videoResults.Value[0]`. Następnie wyświetl identyfikator, tytuł i adres URL wideo.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../overview.md)
* [Cognitive services .NET SDK samples (Przykłady zestawów SKD .NET usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
