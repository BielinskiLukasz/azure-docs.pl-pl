---
title: Przewodnik Szybki Start — Wyślij zapytanie do lokalnych firm interfejs API wyszukiwania Bing w C# | Dokumentacja firmy Microsoft
titleSuffix: Azure Cognitive Services
description: Użyj w tym artykule, aby rozpocząć korzystanie z lokalnych firm interfejs API wyszukiwania Bing w C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 99a9ab2ae1ed102459d2e13f060eebd08bb0ec75
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853578"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-c"></a>Szybki Start: Wysłanie zapytania do lokalnych firm interfejs API wyszukiwania Bing wC#

Użyj tego przewodnika Szybki Start, aby rozpocząć wysyłanie żądań lokalnych firm interfejs API wyszukiwania Bing, czyli usługi Azure Cognitive Service. Gdy ta prosta aplikacja została napisana C#, interfejs API jest zgodny z dowolnego języka programowania możliwością wysyłania żądań HTTP i analizowania danych JSON Usługa sieci Web typu RESTful.

Ta przykładowa aplikacja pobiera dane lokalne odpowiedzi z interfejsu API dla zapytania wyszukiwania `hotel in Bellevue`.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](http://www.mono-project.com/).

Wymagane jest [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsów API wyszukiwania Bing. [Bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) jest wystarczająca na potrzeby tego przewodnika Szybki start. Konieczne będzie klucz dostępu podany przy wywołaniu metody Aktywuj bezpłatną wersję próbną.

## <a name="create-the-request"></a>Utwórz żądanie 

Poniższy kod tworzy `WebRequest`, ustawia nagłówek klucza dostępu i dodaje ciąg zapytania dla "restauracji w Bellevue".  Następnie wysyła żądanie i przypisuje odpowiedź do ciągu zawierającego tekst w formacie JSON.

````
    // Replace the accessKey string value with your valid access key.
    const string accessKey = "enter key here";

    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/search";   

    const string searchTerm = "restaurant in Bellevue";
    // Construct the URI of the search request
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "appid=" + accessKey;

    // Run the Web request and get response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    //request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
````

## <a name="run-the-complete-application"></a>Uruchom kompletnej aplikacji

Lokalnych firm interfejsu API wyszukiwania Bing zwraca wyniki wyszukiwania zlokalizowanych z wyszukiwarki Bing.
1. Utwórz nowe rozwiązanie konsolowe w programie Visual Studio (wersja Community Edition jest wystarczająca).
2. Zastąp plik Program.cs kodem przedstawionym poniżej.
3. Zastąp wartość symbolu accessKey prawidłowy klucz dostępu dla Twojej subskrypcji.
4. Uruchom program.

````
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace localSearch
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/search";   

        const string searchTerm = "restaurant in Bellevue";

        // Returns search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Local business search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + 
                                "&appid=" + accessKey + "&market=en-us";

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            //request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}

````

## <a name="next-steps"></a>Kolejne kroki
- [Lokalnych firm wyszukiwania Java Szybki Start](local-search-java-quickstart.md)
- [Lokalny węzeł wyszukiwania firm Szybki Start](local-search-node-quickstart.md)
- [Lokalnych firm wyszukiwania Python Szybki Start](local-search-python-quickstart.md)
