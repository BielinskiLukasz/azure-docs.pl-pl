---
title: 'Szybki start: Zaproponuj zapytania wyszukiwania przy użyciu interfejsu API REST automatycznego sugerowania Bing i języka Java'
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak szybko rozpocząć sugerowanie terminy wyszukiwania w czasie rzeczywistym za pomocą interfejsu API automatycznego sugerowania Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: f41e81d948bfa482a016e3770a705344f9c26b0c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390431"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-java"></a>Szybki start: Zaproponuj zapytania wyszukiwania przy użyciu interfejsu API REST automatycznego sugerowania Bing i języka Java


Użyj tego przewodnika Szybki Start, aby rozpocząć wprowadzanie wywołania interfejsu API automatycznego sugerowania Bing i uzyskiwania odpowiedzi JSON. Ta prosta aplikacja Java wysyła zapytanie częściowe do interfejsu API i zwraca sugestie dotyczące wyszukiwania. Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy, w tym przykładzie jest dostępny na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingAutosuggestv7.java)

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
* [Biblioteka Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

    ```java
    import java.io.*;
    import java.net.*;
    import java.util.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Tworzenie zmiennych dla swój klucz subskrypcji, interfejsu API hosta i ścieżkę, Twoje [rynek kodu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes)i zapytania wyszukiwania.
    
    ```java
    static String subscriptionKey = "enter key here";
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/Suggestions";
    static String mkt = "en-US";
    static String query = "sail";
    ```


## <a name="format-the-response"></a>Formatowanie odpowiedzi

Utwórz metodę o nazwie `prettify()` w celu sformatowania odpowiedzi z interfejsu API wideo Bing. Użyj biblioteki Gson `JsonParser`, która przyjmuje ciąg JSON i konwertuje go na obiekt. Następnie użyj funkcji `GsonBuilder()` i `toJson()`, aby utworzyć sformatowany ciąg.

```java
// pretty-printer for JSON; uses GSON parser to parse and re-serialize
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="construct-and-send-the-search-request"></a>Tworzenie i wysyłanie żądania wyszukiwania

1. Utwórz nową metodę o nazwie `get_suggestions()` i wykonaj następujące czynności:

   1. Skonstruuj adres URL na potrzeby żądania, łącząc host i ścieżkę interfejsu API i kodując zapytania wyszukiwania. Pamiętaj, aby adres url — kodowanie zapytanie przed dołączeniem go. Utwórz parametry ciągu zapytania, dodając kod na rynku, aby `mkt=` parametr i zapytania `q=` parametru.
    
      ```java
  
      public static String get_suggestions () throws Exception {
         String encoded_query = URLEncoder.encode (query, "UTF-8");
         String params = "?mkt=" + mkt + "&q=" + encoded_query;
         //...
      }
      ```
    
   2. Utwórz nowy adres URL żądania z hosta interfejsu API, ścieżki oraz parametrów utworzonego powyżej. 
    
       ```java
       //...
       URL url = new URL (host + path + params);
       //...
       ```
    
   3. Tworzenie `HttpsURLConnection` obiektu, a następnie użyj `openConnection()` do utworzenia połączenia. Jako metodę żądania ustaw `GET` i dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

      ```java
       //...
       HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
       connection.setRequestMethod("GET");
       connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
       connection.setDoOutput(true);
       //...
      ```

   4. Przeczytaj w odpowiedzi interfejsu API `StringBuilder`. Po przechwyceniu odpowiedzi Zamknij `InputStreamReader` strumienia, a następnie zwraca odpowiedź.

       ```java
       //...
       StringBuilder response = new StringBuilder ();
       BufferedReader in = new BufferedReader(
       new InputStreamReader(connection.getInputStream()));
       String line;
       while ((line = in.readLine()) != null) {
         response.append(line);
       }
       in.close();
    
       return response.toString();
       ```

2. W funkcji main aplikacji, wywołaj `get_suggestions()`i drukowania, przy użyciu odpowiedzi `prettify()`.
    
    ```java
    public static void main(String[] args) {
      try {
        String response = get_suggestions ();
        System.out.println (prettify (response));
      }
      catch (Exception e) {
        System.out.println (e);
      }
    }
    ```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorials/autosuggest.md)

- [Czym jest funkcja automatycznego sugerowania Bing?](../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 reference (Dokumentacja dotycząca automatycznego sugerowania Bing w wersji 7)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
