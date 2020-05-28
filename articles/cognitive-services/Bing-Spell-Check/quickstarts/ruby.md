---
title: 'Szybki Start: sprawdzanie pisowni za pomocą interfejsu API REST i języka Ruby-sprawdzanie pisowni Bing'
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z interfejsem API REST sprawdzanie pisowni Bing, aby sprawdzić pisownię i gramatykę w tym przewodniku Szybki Start.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: fae466124244f5d2b04ad6e59681011b9c5ba974
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993528"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Szybki Start: sprawdzanie pisowni przy użyciu interfejsu API REST sprawdzanie pisowni Bing i języka Ruby

Użyj tego przewodnika Szybki start, aby wykonać swoje pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing przy użyciu języka Ruby. Ta prosta aplikacja wysyła żądanie do interfejsu API i zwraca listę sugerowanych poprawek. 

Mimo że aplikacja jest zapisywana w języku Ruby, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania. Kod źródłowy dla tej aplikacji jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Wymagania wstępne

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) lub nowszy.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik Ruby w ulubionym edytorze lub w środowisku IDE, a następnie Dodaj następujące wymagania: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Utwórz zmienne dla klucza subskrypcji, identyfikatora URI punktu końcowego i ścieżki. Możesz użyć globalnego punktu końcowego w poniższym kodzie lub użyć punktu końcowego [niestandardowej domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlanego w Azure Portal dla zasobu. Utwórz parametry żądania:

   1. Przypisz kod rynkowy do `mkt` parametru `=` operatorem. Kod rynkowy to kod kraju/regionu, z którego pochodzi żądanie. 

   1. Dodaj `mode` parametr z `&` operatorem, a następnie przypisz tryb sprawdzania pisowni. Trybem może być albo `proof` (przechwycenie większości błędów pisowni/gramatyki) lub `spell` (przechwycenie większości błędów pisowni, ale nie tyle błędów gramatycznych). 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Wysyłanie żądania sprawdzania pisowni

1. Utwórz identyfikator URI na podstawie identyfikatora URI hosta, ścieżki oraz ciągu parametrów. Ustaw, aby zapytanie zawierało tekst, który ma być sprawdzany.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Utwórz żądanie przy użyciu identyfikatora URI złożonego wcześniej. Dodaj klucz do nagłówka `Ocp-Apim-Subscription-Key`.

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

## <a name="run-the-application"></a>Uruchamianie aplikacji

Skompiluj i Uruchom projekt. Jeśli używasz wiersza polecenia, użyj następującego polecenia, aby uruchomić aplikację:

   ```bash
   ruby <FILE_NAME>.rb
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
- [Dokumentacja wersji 7 interfejsu API sprawdzanie pisowni Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
