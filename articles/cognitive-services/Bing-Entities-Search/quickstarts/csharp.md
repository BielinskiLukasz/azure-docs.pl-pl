---
title: 'Szybki start: wysyłanie żądania wyszukiwania do interfejsu API REST wyszukiwania jednostek Bing przy użyciu języka C#'
titlesuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejsu API REST wyszukiwania wiadomości Bing przy użyciu języka C# i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 211f33d5b217714b26dc39ad63f9d1427950589a
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595779"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Szybki start: wysyłanie żądania wyszukiwania do interfejsu API REST wyszukiwania jednostek Bing przy użyciu języka C#

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania jednostek Bing i wyświetlenie odpowiedzi JSON. Ta prosta aplikacja w języku C# wysyła zapytanie wyszukiwania wiadomości do interfejsu API i wyświetla odpowiedź. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.


## <a name="prerequisites"></a>Wymagania wstępne

- Dowolnej wersji programu [programu Visual Studio 2017 r. lub nowszej](https://www.visualstudio.com/downloads/).

- Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet. Aby zainstalować pakiet NuGet w programie Visual Studio:

   1. Kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań**.
   2. Wybierz **Zarządzaj pakietami NuGet**.
   3. Wyszukaj *Newtonsoft.Json* i zainstalować pakiet.

- Jeśli używasz systemu Linux/MacOS tę aplikację można uruchomić za pomocą [Mono](https://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowe rozwiązanie konsolowe dla języka C# w programie Visual Studio. Dodaj następujące przestrzenie nazw do głównego pliku kodu.
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Utwórz nową klasę i dodaj zmienne dla punktu końcowego interfejsu API, swój klucz subskrypcji i zapytania, które chcesz przeszukać.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Wysyłanie żądania i odbieranie odpowiedzi interfejsu API

1. W klasie utwórz funkcję o nazwie `Search()`. Utwórz nowy obiekt `HttpClient` i dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

   1. Łącząc hosta i ścieżkę, utwórz identyfikator URI Twojego żądania. Następnie dodaj swój rynek i zakoduj adres URL zapytania.
   2. Poczekaj `client.GetAsync()`, aby uzyskać odpowiedź HTTP, a następnie zapisz odpowiedź w formacie JSON, oczekując na `ReadAsStringAsync()`.
   3. Format ciągu JSON za pomocą `JsonConvert.DeserializeObject()` i wydrukuj go w konsoli.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

2. W metodzie głównej aplikacji wywołaj funkcję `Search()`.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../overview.md )
* [Dokumentacja interfejsu API wyszukiwania jednostek Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
