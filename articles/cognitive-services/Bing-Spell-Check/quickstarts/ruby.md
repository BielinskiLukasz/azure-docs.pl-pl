---
title: 'Szybki start: Sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka Ruby'
titlesuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z interfejsu API REST sprawdzania pisowni Bing, aby sprawdzać pisownię i poprawność gramatyczną.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d488923f38a9c65cb117b4535b50bb9fdff2dbfc
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888852"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Szybki start: Sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka Ruby

Użyj tego przewodnika Szybki start, aby wykonać swoje pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing przy użyciu języka Ruby. Ta prosta aplikacja wysyła żądanie do interfejsu API i zwraca listę nierozpoznanych słów oraz sugerowane poprawki. Chociaż ta aplikacja jest napisana w języku Ruby, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Wymagania wstępne

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) lub nowsza wersja.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Ruby w ulubionym edytorze lub środowisku IDE i dodaj następujące wymagania. 

    ```javascript
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Utwórz zmienne dla klucza subskrypcji, identyfikatora URI punktu końcowego i ścieżki. Utwórz parametry żądania, dodając parametr `mkt=` do rynku i parametr `&mode` do trybu `proof`.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Wysyłanie żądania sprawdzania pisowni

1. Utwórz identyfikator URI na podstawie identyfikatora URI hosta, ścieżki oraz ciągu parametrów. Skonfiguruj zapytanie, aby zawierało tekst, dla którego ma być sprawdzana pisownia.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Utwórz żądanie przy użyciu skonstruowanego powyżej identyfikatora URI. Dodaj klucz do nagłówka `Ocp-Apim-Subscription-Key`.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Wyślij żądanie.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Pobierz odpowiedź JSON i wyświetl ją w konsoli. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorials/spellcheck.md)

- [Czym jest interfejs API sprawdzania pisowni Bing?](../overview.md)
- [Dokumentacja interfejsu API sprawdzania pisowni Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
