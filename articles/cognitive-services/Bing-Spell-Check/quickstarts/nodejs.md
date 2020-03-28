---
title: 'Szybki start: sprawdzanie pisowni za pomocą interfejsu API REST i pliku Node.js — sprawdzanie pisowni bing'
titleSuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z interfejsu API REST sprawdzania pisowni bing, aby sprawdzić pisownię i gramatykę za pomocą tego przewodnika Szybki start.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 69c391e6c3f93a998ade7c5721a528d895f8df76
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382862"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Szybki start: sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni bing i pliku Node.js

Użyj tego przewodnika Szybki start, aby wykonać pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing. Ta prosta aplikacja Node wysyła żądanie do interfejsu API i zwraca listę słów, których nie rozpoznaje, a następnie sugerowane poprawki. Podczas gdy ta aplikacja jest napisana w node.js, interfejs API jest usługą sieci Web RESTful kompatybilną z większością języków programowania. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Node.js 6](https://nodejs.org/en/download/) lub nowsze.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowy plik JavaScript w ulubionym środowisku IDE lub edytorze. Ustaw surowość i `https`wymagaj . Następnie utwórz zmienne dla hosta punktu końcowego interfejsu API, ścieżki i klucza subskrypcji. Można użyć globalnego punktu końcowego poniżej lub niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w witrynie Azure portal dla zasobu.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Utwórz zmienne dla parametrów wyszukiwania i tekstu, który chcesz sprawdzić. Dołącz kod rynkowy `mkt=`po pliku . Kod rynku to kraj, z którego składasz wniosek. Ponadto po dorobić tryb `&mode=`sprawdzania pisowni po pliku . Tryb jest `proof` albo (połowy większość błędów pisowni `spell` / gramatyki) lub (połowy większość pisowni, ale nie tyle błędów gramatycznych).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Tworzenie parametrów żądania

Utwórz parametry żądania przez utworzenie nowego obiektu przy użyciu metody `POST`. Dodaj ścieżkę utworzoną przez połączenie ścieżki do punktu końcowego i ciągu zapytania. Dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Tworzenie procedury obsługi odpowiedzi

Utwórz funkcję o nazwie `response_handler`, która będzie przyjmować odpowiedź JSON z interfejsu API i wyświetlać ją. Utwórz zmienną na potrzeby treści odpowiedzi. Dołącz odpowiedź w przypadku odebrania flagi `data` za pomocą funkcji `response.on()`. Po odebraniu flagi `end` treść odpowiedzi JSON powinna zostać wyświetlona w konsoli.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>Wysyłanie żądania

Wywołaj interfejs API przy użyciu funkcji `https.request()` z parametrami żądania i procedurą obsługi odpowiedzi. Wpisz tekst do interfejsu API, a następnie zakończ żądanie.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Uruchamianie aplikacji

Skompiluj i uruchom swój projekt.

Jeśli używasz wiersza polecenia, użyj następujących poleceń, aby utworzyć i uruchomić aplikację.

```bash
node <FILE_NAME>.js
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
- [Dokumentacja interfejsu API sprawdzania pisowni Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
