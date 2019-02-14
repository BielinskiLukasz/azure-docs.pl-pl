---
title: 'Szybki start: Wyszukiwanie wideo za pomocą interfejsu API REST wyszukiwania wideo Bing i języka Python'
titlesuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wysyłać żądania wyszukiwania wideo do interfejsu API REST wyszukiwania wideo Bing przy użyciu języka Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 1ac4a2ce5027606706367b43eea759fb953994c6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876882"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-python"></a>Szybki start: Wyszukiwanie wideo za pomocą interfejsu API REST wyszukiwania wideo Bing i języka Python

Ten przewodnik Szybki start umożliwia wykonanie pierwszego wywołania interfejsu API wyszukiwania wideo Bing i wyświetlenie wyników wyszukiwania na podstawie odpowiedzi JSON. Ta prosta aplikacja w języku Python wysyła zapytanie HTTP wyszukiwania wideo do interfejsu API i wyświetla odpowiedź. Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy dla tego przykładu jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingVideoSearchv7.py) wraz z dodatkową obsługą błędów i adnotacjami kodu.

Ten przykład można uruchomić jako notes programu Jupyter w witrynie [MyBinder](https://mybinder.org). W tym celu należy kliknąć ikonę Launch Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko Python [2.x lub 3.x](https://python.org)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik kodu Python w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

    ```python
    import requests
    from IPython.display import HTML
    ```
2.  Utwórz zmienne dla klucza subskrypcji, punktu końcowego wyszukiwania i terminu wyszukiwania.
    
    ```python
    subscription_key = None
    assert subscription_key
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
    search_term = "kittens"
    ```

3. Dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key` przez utworzenie nowego słownika w celu skojarzenia ciągu nagłówka z kluczem.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="send-your-request"></a>Wysyłanie żądania

1. Dodaj parametry do żądania, tworząc słownik o nazwie `params`. Dodaj termin wyszukiwania do parametru `q`, licznik wideo równy 5, wartość `free` jako cenę zwróconych wideo i wartość `short` jako długość wideo.

    ```python
    params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
    ```

2. Użyj biblioteki `requests` języka Python do wywoływania interfejsu API wyszukiwania wideo Bing. Przekaż parametry klucza interfejsu API i wyszukiwania za pomocą parametru `headers` i słownika `params`.
    
    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

3. Aby wyświetlić jedno ze zwróconych wideo, pobierz wynik wyszukiwania z obiektu `search_results`. Wstaw właściwość `embedHtml` wyniku do elementu `IFrame`.  
    
    ```python
    HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
    ```


## <a name="json-response"></a>Odpowiedź w formacie JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

 [Co to jest interfejs API wyszukiwania wideo Bing?](../overview.md)
