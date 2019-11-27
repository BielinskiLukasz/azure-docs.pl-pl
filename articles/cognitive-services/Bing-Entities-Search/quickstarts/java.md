---
title: 'Szybki Start: wysyłanie żądania wyszukiwania do interfejsu API REST przy użyciu języka Java-wyszukiwanie jednostek Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wysyłać żądania do interfejsu API REST wyszukiwania jednostek Bing przy użyciu języka Java i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 13e25ce0a237d34b98ec42140b86c4955f9058cb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323793"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Szybki Start: wysyłanie żądania wyszukiwania do wyszukiwanie jednostek Bing interfejsu API REST przy użyciu języka Java

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania jednostek Bing i wyświetlenie odpowiedzi JSON. Ta prosta aplikacja w języku Java wysyła zapytanie wyszukiwania wiadomości do interfejsu API i wyświetla odpowiedź.

Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

* Zestaw [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
* [Biblioteka Gson](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

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
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   ```

2. W nowej klasie utwórz zmienne dla punktu końcowego interfejsu API, klucza subskrypcji i zapytania wyszukiwania.

   ```java
   public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
   //...
    
   ```

## <a name="construct-a-search-request-string"></a>Konstruowanie ciągu żądania wyszukiwania

1. Utwórz funkcję o nazwie `search()` zwracającą `String` w formacie JSON. Zakoduj zapytanie wyszukiwania w formacie URL i dodaj je do ciągu parametrów za pomocą `&q=`. Dodaj do ciągu swój rynek za pomocą `?mkt=`.
 
2. Utwórz obiekt adresu URL zawierający Twoje ciągi hosta, ścieżki i parametrów.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Wysyłanie żądania wyszukiwania i odbieranie odpowiedzi

1. W utworzonej powyżej funkcji `search()` utwórz nowy obiekt `HttpsURLConnection` z `url.openCOnnection()`. Jako metodę żądania ustaw `GET` i dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Utwórz nowy element `StringBuilder`. Użyj nowego elementu `InputStreamReader` jako parametru podczas tworzenia wystąpienia `BufferedReader` do odczytywania odpowiedzi interfejsu API.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Utwórz obiekt `String` do przechowywania odpowiedzi z obiektu `BufferedReader`. Przechodząc przez niego iteracyjnie, dołącz do ciągu poszczególne wiersze. Następnie zamknij czytnik i zwróć odpowiedź. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>Formatowanie odpowiedzi w formacie JSON

1. Utwórz nową funkcję o nazwie `prettify`, aby sformatować odpowiedź w formacie JSON. Utwórz nowy obiekt `JsonParser` i wywołaj metodę `parse()` dla tekstu JSON, a następnie zapisz go w obiekcie JSON. 

2. Za pomocą biblioteki Gson utwórz nowy element `GsonBuilder()` i za pomocą metody `setPrettyPrinting().create()` sformatuj zawartość JSON. Następnie ją zwróć.    
  
   ```java
   //...
   public static String prettify (String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
   }
   //...
   ```

## <a name="call-the-search-function"></a>Wywoływanie funkcji wyszukiwania

1. Z poziomu metody main projektu wywołaj funkcję `search()` i za pomocą funkcji `prettify()` sformatuj tekst.
    
    ```java
        public static void main(String[] args) {
            try {
                String response = search ();
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

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../overview.md )
* [Dokumentacja interfejsu API wyszukiwania jednostek Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
