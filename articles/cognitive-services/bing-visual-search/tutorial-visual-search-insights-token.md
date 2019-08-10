---
title: Znajdź podobne obrazy z poprzednich wyszukiwań przy użyciu ImageInsightsToken-wyszukiwanie wizualne Bing
titleSuffix: Azure Cognitive Services
description: Użyj zestawu SDK wyszukiwanie wizualne Bing, aby uzyskać adresy URL obrazów określonych przez ImageInsightsToken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rosh
ms.openlocfilehash: 5f4faa290fe4ed02ab1ed75d23755af5dc20f215
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880610"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Znajdź podobne obrazy z poprzednich wyszukiwań przy użyciu ImageInsightsToken

Zestaw SDK wyszukiwanie wizualne umożliwia wyszukiwanie obrazów w trybie online z poprzednich wyszukiwań, które `ImageInsightsToken`zwracają. Ta aplikacja pobiera `ImageInsightsToken` i używa tokenu podczas kolejnego wyszukiwania. Następnie wysyła `ImageInsightsToken` do usługi Bing i zwraca wyniki, które zawierają adresy URL wyszukiwanie Bing i adresy URL podobnych obrazów, które znajdują się w trybie online.

Pełny kod źródłowy dla tego samouczka można znaleźć w dodatkowej obsłudze błędów i adnotacjach w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu narzędzia [mono](https://www.mono-project.com/).
* Pakiety wyszukiwanie wizualne i wyszukiwanie obrazów NuGet.
    - Na Eksplorator rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet** z menu. `Microsoft.Azure.CognitiveServices.Search.CustomSearch` Zainstaluj pakiet`Microsoft.Azure.CognitiveServices.Search.ImageSearch` i pakiet. Zainstalowanie pakietu NuGet powoduje także zainstalowanie następujących elementów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Pobierz ImageInsightsToken z zestawu SDK wyszukiwanie obrazów Bing

Ta aplikacja używa `ImageInsightsToken` uzyskanego za pośrednictwem [zestawu SDK wyszukiwanie obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). W nowej C# aplikacji konsolowej Utwórz klienta do wywoływania interfejsu API za pomocą polecenia `ImageSearchClient()`. Następnie użyj `SearchAsync()` zapytania:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Zapisz pierwszy wynik wyszukiwania przy użyciu `imageResults.Value.First()`, a następnie Zapisz obraz. `ImageInsightsToken`

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Ta `ImageInsightsToken` wartość jest wysyłana do Wyszukiwanie wizualne Bing w żądaniu.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Dodawanie ImageInsightsToken do żądania wyszukiwanie wizualne

Określ żądanie Wyszukiwanie wizualne przez `ImageInfo` utworzenie obiektu na podstawie `ImageInsightsToken` odpowiedzi z Wyszukiwanie wizualne Bing. `ImageInsightsToken`

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Użyj wyszukiwanie wizualne Bing, aby znaleźć obrazy z ImageInsightsToken

Obiekt zawiera informacje o `ImageInfo` obrazie, który ma zostać przeszukany. `VisualSearchRequest` Metoda `VisualSearchMethodAsync()` pobiera wyniki. Nie musisz podawać danych binarnych obrazu, ponieważ obraz jest reprezentowany przez token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Wykonaj iterację w wyniku wyszukiwanie wizualne

Wyniki wyszukiwania wizualnego to obiekty `ImageTag`. Każdy tag zawiera listę obiektów `ImageAction`. Każdy `ImageAction` z nich `Data` zawiera pole, które jest listą wartości, które są zależne od typu akcji. Można wykonać iterację `ImageTag` obiektów w `visualSearchResults.Tags`, na `ImageAction` przykład, i pobrać tag w nim. Poniższy przykład drukuje szczegóły `PagesIncluding` akcji:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Pobieranie rzeczywistych adresów URL obrazów z typów akcji wymaga rzutowania, który odczytuje `ActionType` jako `ImageModuleAction`, który zawiera `Data` element z listą wartości. Każda wartość to adres URL obrazu.  Poniższy rzutuje `PagesIncluding` typ akcji na `ImageModuleAction` i odczytuje wartości:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Aby uzyskać więcej informacji o tych typach danych, zobacz [Images - Visual Search (Obrazy — wyszukiwanie wizualne)](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Zwrócone adresy URL

Kompletna aplikacja zwraca następujące adresy URL:

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes — > WebSearchUrl     |         |
|VisualSearch — > WebSearchUrl     |         |
|ImageById — > WebSearchUrl    |         |
|RelatedSearches > WebSearchUrl:    |         |
|DocumentLevelSuggestions > WebSearchUrl:     |         |
|TopicResults — > WebSearchUrl    | https:\//www.Bing.com/CR?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&RD=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3A%2F%2Fwww.Bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults — > WebSearchUrl    |  https:\//www.Bing.com/CR?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&RD=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3A%2F%2Fwww.Bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Jak pokazano powyżej, `TopicResults` typy i `ImageResults` zawierają zapytania dotyczące pokrewnych obrazów. Adresy URL łączą się z wynikami wyszukiwania Bing.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie wyszukiwanie wizualne jednostronicowej aplikacji sieci Web](tutorial-bing-visual-search-single-page-app.md)
