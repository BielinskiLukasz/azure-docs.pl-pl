---
title: 'Quickstart: Send a search request to the REST API using Ruby - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejs API REST wyszukiwania jednostek Bing przy użyciu języka Ruby i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 08/26/2019
ms.author: aahi
ms.openlocfilehash: 076fad8c1fc6fdd4bccf07f5eb3223800eddd4a0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323556"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Przewodnik Szybki start dotyczący korzystania z interfejsu API wyszukiwania jednostek Bing za pomocą języka Ruby

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania jednostek Bing i wyświetlenie odpowiedzi JSON. Ta prosta aplikacja w języku Ruby wysyła zapytanie wyszukiwania wiadomości do interfejsu API i wyświetla odpowiedź. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Chociaż ta aplikacja jest napisana w języku Ruby, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) lub nowsza wersja.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. W Twoim ulubionym środowisku IDE lub edytorze kodu utwórz plik wiadomości języka Ruby i zaimportuj następujące pakiety.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Utwórz zmienne dla swojego punktu końcowego interfejsu API, adresu URL wyszukiwania wiadomości, swojego klucza subskrypcji i zapytania wyszukiwania.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formatowanie i wykonywanie żądania interfejsu API

1. Utwórz ciąg parametrów dla swojego żądania, dołączając do zmiennej na rynku parametr `?mkt=`. Zakoduj swoje zapytanie, a następnie dołącz je do parametru `&q=`. Połącz hosta interfejsu API, ścieżkę i parametry żądania, a następnie rzutuj je jako obiekt URI.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Użyj zmiennych z ostatniego kroku, aby utworzyć żądanie. Dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. Send the request, and print the response

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing](../search-the-web.md)
* [Dokumentacja interfejsu API wyszukiwania jednostek Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
