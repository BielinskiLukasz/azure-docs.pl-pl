---
title: 'Szybki start: Sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka C#'
titlesuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z interfejsu API REST sprawdzania pisowni Bing, aby sprawdzać pisownię i poprawność gramatyczną.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 7a17c695482f2e9c8158c437c9c40c0abcb07e67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066241"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Szybki start: Sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka C#

Użyj tego przewodnika Szybki start, aby wykonać pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing. Ta prosta aplikacja w języku C# wysyła żądanie do interfejsu API i zwraca listę sugerowanych poprawek. Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Aby zainstalować `Newtonsoft.Json` jako pakiet NuGet w programie Visual studio:
    1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy plik rozwiązania.
    1. Wybierz **Zarządzaj pakietami NuGet dla rozwiązania**.
    1. Wyszukaj `Newtonsoft.Json` i zainstalować pakiet.
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowe rozwiązanie konsoli o nazwie `SpellCheckSample` w programie Visual Studio. Dodaj następujące przestrzenie nazw do głównego pliku kodu.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, swojego klucza subskrypcji i tekstu, dla którego ma być sprawdzana pisownia.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Utwórz zmienną dla parametrów wyszukiwania. Dołączanie kodu rynku po `mkt=`. Kod na rynku jest wprowadzone żądanie od kraju. Ponadto dołączyć swoje tryb sprawdzania pisowni, po `&mode=`. Tryb jest albo `proof` (przechwytuje większość błędów Pisownia i gramatyka) lub `spell` (przechwytuje większość pisowni, ale nie tyle błędy gramatyczne).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Tworzenie i wysyłanie żądania sprawdzania pisowni

1. Utwórz asynchroniczną funkcję o nazwie `SpellCheck()`, aby wysłać żądanie do interfejsu API. Utwórz obiekt `HttpClient` i dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`. Następnie wykonaj następujące czynności w ramach tej funkcji.

    ```csharp
    async static void SpellCheck()
    {
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = new HttpResponseMessage();
        // add the rest of the code snippets here (except for main())...
    }

2. Create the URI for your request by appending your host, path, and parameters.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Za pomocą obiektu `KeyValuePair` utwórz listę zawierającą tekst, a następnie użyj jej do utworzenia obiektu `FormUrlEncodedContent`. Ustaw informacje nagłówka i użyj funkcji `PostAsync()` w celu wysłania żądania.

    ```csharp
    List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
    values.Add(new KeyValuePair<string, string>("text", text));
    
    using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
    {
        content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
        response = await client.PostAsync(uri, content);
    }
    ```

## <a name="get-and-print-the-api-response"></a>Pobieranie i wyświetlanie odpowiedzi interfejsu API

### <a name="get-the-client-id-header"></a>Pobieranie nagłówka Identyfikatora klienta

Jeśli odpowiedź zawiera nagłówek `X-MSEdge-ClientID`, pobierz jego wartość i wyświetl ją.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Pobieranie odpowiedzi

Pobierz odpowiedź z interfejsu API. Deserializuj obiekt JSON i wyświetl go w konsoli.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Wywoływanie funkcji sprawdzania pisowni

W funkcji Main projektu wywołaj funkcję `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorials/spellcheck.md)

- [Czym jest interfejs API sprawdzania pisowni Bing?](../overview.md)
- [Dokumentacja interfejsu API sprawdzania pisowni Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
