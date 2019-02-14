---
title: 'Szybki start: wysyłanie żądania wyszukiwania do interfejsu API REST wyszukiwania jednostek Bing przy użyciu języka Python'
titlesuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejsu API REST wyszukiwania jednostek Bing przy użyciu języka Python i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 73b666116a23ab8d861d38af4dc9fa5e19d5d1bd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857162"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Szybki start: wysyłanie żądania wyszukiwania do interfejsu API REST wyszukiwania jednostek Bing przy użyciu języka Python

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania jednostek Bing i wyświetlenie odpowiedzi JSON. Ta prosta aplikacja w języku Python wysyła zapytanie wyszukiwania wiadomości do interfejsu API i wyświetla odpowiedź. Kod źródłowy tego przykładu jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python](https://www.python.org/downloads/) 2.x lub 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące importy. Utwórz zmienne dla swojego klucza subskrypcji, punktu końcowego, rynku i zapytania wyszukiwania. Punkt końcowy usługi można znaleźć na pulpicie nawigacyjnym platformy Azure.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Utwórz adres URL żądania, dodając swoją zmienną rynku do parametru `?mkt=`. Zakoduj adres URL swojego zapytania, a następnie dołącz je do parametru `&q=`. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Wysyłanie żądania i odbieranie odpowiedzi

1. Utwórz funkcję o nazwie `get_suggestions()`. Następnie wykonaj poniższe czynności.
    1. Dodaj klucz subskrypcji do słownika przy użyciu `Ocp-Apim-Subscription-Key` jako klucza.
    2. Użyj `http.client.HTTPSConnection()`, aby utworzyć obiekt klienta HTTPS. Wyślij żądanie `GET` przy użyciu `request()` z informacjami o ścieżce, parametrach i nagłówku.
    3. Zapisz odpowiedź za pomocą `getresponse()` i zwróć `response.read()`.

    ```python
    def get_suggestions ():
        headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
        conn = http.client.HTTPSConnection (host)
        conn.request ("GET", path + params, None, headers)
        response = conn.getresponse ()
        return response.read()
    ```

2. Wywołaj `get_suggestions()` i wydrukuj odpowiedź w formacie JSON.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
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
        "url": "http://www.princi.com/",
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
* [Dokumentacja interfejsu API wyszukiwania jednostek Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
