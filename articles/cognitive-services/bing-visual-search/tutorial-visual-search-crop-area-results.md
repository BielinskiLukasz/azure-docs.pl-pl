---
title: 'Samouczek: Przytnij obraz z zestawem SDK wyszukiwania wizualnego Bing'
description: Bing wyszukiwania zestawu SDK programu Visual umożliwia uzyskiwanie szczegółowych informacji z określonych arach na obrazie.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 2ec4810b6ac01eca17186f4b8d2e8c2959292b8d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187899"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Samouczek: Przytnij obraz za pomocą usługi Bing wyszukiwania zestawu SDK programu Visual dlaC#

Zestaw SDK usługi Bing Visual Search umożliwia przyciąć obraz przed wyszukiwanie obrazów w trybie online, które są podobne. Ta aplikacja przycina jednej osobie z obrazu, zawierającą kilka osób, a następnie zwraca wyniki wyszukiwania zawierające podobne obrazy, znalezione w trybie online.

Pełny kod źródłowy dla tej aplikacji jest dostępna z poziomu obsługi dodatkowych błędów i adnotacje na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

W tym samouczku przedstawiono sposób:

> [!div class="checklist"]
> * Wyślij żądanie przy użyciu zestawu SDK usługi Bing Visual Search
> * Przytnij obszar obrazu do wyszukiwania z wyszukiwania wizualnego Bing
> * Otrzymywanie i obsługę odpowiedzi
> * Znajdź adresy URL czynności do wykonania w resposne

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](http://www.mono-project.com/).
- Zainstalowany pakiet [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - W Eksploratorze rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy swój projekt i wybierz polecenie `Manage NuGet Packages` z menu. Zainstaluj pakiet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Zainstalowanie pakietu NuGet Custom Search powoduje również zainstalowanie następujących zestawów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Określ obszar przycinanie obrazu

Ta aplikacja przycina obszar ten obraz szczebla zespół firmy Microsoft. Ten obszar przycięcia jest zdefiniowana za pomocą lewego górnego i prawego dolnego współrzędne, reprezentowane jako wartość procentowa całego obrazu.  

![Zespół liderów wyższego szczebla firmy Microsoft](./media/MS_SrLeaders.jpg)

Ten obraz jest przycięty, tworząc `ImageInfo` obiektu z obszaru kadrowania i ładowanie `ImageInfo` do obiektu `VisualSearchRequest`. `ImageInfo` Obiektu zawiera również adres URL obrazu.

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Wyszukuj obrazy, podobne do obszaru w oknie przycięcia

Zmienna `VisualSearchRequest` zawiera informacje o obszarze przycinanie obrazu i jego adres url. Metoda `VisualSearchMethodAsync()` pobiera wyniki.

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pobieranie danych adresu URL z elementu ImageModuleAction

Wyniki wyszukiwania wizualnego Bing są `ImageTag` obiektów.  Każdy tag zawiera listę obiektów `ImageAction`.  Każdy `ImageAction` zawiera `Data` pola, które znajduje się lista wartości, które są zależne od typu akcji.

Możesz wydrukować różnych typów, z następującym kodem:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

Kompletna aplikacja zwraca:


|ActionType  |Adres URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |         
|MoreSizes WebSearchURL     |         |         
|VisualSearch WebSearchURL    |         |         
|ImageById WebSearchURL     |         |         
|RelatedSearches WebSearchURL     |         |         
|Jednostki -> WebSearchUrl     | https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |         
|TopicResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |         
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |         

Jak wspomniano powyżej, `Entity` ActionType zawiera zapytanie wyszukiwania Bing, które zwraca informacje na temat rozpoznawalnych osoby, miejsca lub rzeczy.  Typy `TopicResults` i `ImageResults` zawierają zapytania dotyczące powiązanych obrazów. Adresy URL na liście to linki do wyników wyszukiwania Bing.


## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Uzyskiwanie adresów URL dla obiektu PagesIncluding ActionType obrazów

Uzyskanie rzeczywistych adresów URL obrazów wymaga rzutowania, które odczytuje typ akcji `ActionType` jako `ImageModuleAction` zawierający element `Data` z listą wartości.  Każda wartość to adres URL obrazu.  Następujący kod rzutuje typ akcji `PagesIncluding` na obiekt `ImageModuleAction` i odczytuje wartości.

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-visual-search-single-page-app.md)

[Odpowiedź wyszukiwania wizualnego](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
