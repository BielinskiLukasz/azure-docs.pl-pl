---
title: 'Szybki start: laboratorium Project Answer Search, Java'
titlesuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z laboratorium Project Answer Search w języku Java.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 1e10ecf685aaf5b60b94a5e9e0d8e118f45e5168
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867039"
---
# <a name="quickstart-project-answer-search-query-in-java"></a>Szybki start: laboratorium Project Answer Search — zapytanie w języku Java
W tym artykule użyto języka Java w celu zademonstrowania możliwości interfejsu API wyszukiwania odpowiedzi Bing, który jest częścią usług Microsoft Cognitive Services na platformie Azure. Ten interfejs API jest usługą internetową w architekturze REST zgodną z dowolnym językiem programowania, który może wykonywać żądania HTTP i analizować format JSON.
 
Przykładowy kod używa języka Java z minimalnym zależnościami zewnętrznymi.  Można go również uruchomić w systemie Linux lub Mac OS X przy użyciu oprogramowania Mono.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej zestawu [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

## <a name="request"></a>Żądanie 

Poniższy kod tworzy obiekt `WebRequest`, ustawia nagłówek klucza dostępu i dodaje ciąg zapytania „Gibraltar”.  Następnie wysyła żądanie i przypisuje odpowiedź do ciągu zawierającego tekst w formacie JSON.

````
    static String host = "https://api.labs.cognitive.microsoft.com";
    static String path = "/answerSearch/v7.0/search";

    // construct URL of search request (endpoint + query string)

    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
````

## <a name="complete-code"></a>Kompletny kod

Interfejs API wyszukiwania odpowiedzi Bing zwraca wyniki z wyszukiwarki Bing.
1. Pobierz i zainstaluj bibliotekę gson.
2. Utwórz nowy projekt języka Java w ulubionym środowisku IDE lub edytorze.
3. Dodaj kod przedstawiony poniżej.
4. Zastąp wartość subscriptionKey kluczem dostępu właściwym dla Twojej subskrypcji.
5. Uruchom program.

````
package knowledgeAPI;
import java.io.InputStream;
import java.net.*;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class KnowledgeSrch {

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.labs.cognitive.microsoft.com";
        static String path = "/answerSearch/v7.0/search";

        static String searchTerm = "Gibraltar";

        public static SearchResults SearchKnowledge (String searchQuery) throws Exception {

            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
            
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }

            stream.close();
            return results;
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchKnowledge(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
}

````

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka C#](c-sharp-quickstart.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)