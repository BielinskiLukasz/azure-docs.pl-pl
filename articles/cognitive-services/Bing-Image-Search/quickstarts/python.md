---
title: 'Szybki start: wyszukiwanie obrazów REST API i Python - Bing Image Search'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania wyszukiwania obrazów do interfejsu API wyszukiwania obrazów Bing przy użyciu języka Python i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f3d00688feb0f9d42e80cdbb51753483e53c388d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930726"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Szybki start: wyszukiwanie obrazów za pomocą interfejsu API REST wyszukiwania obrazów Bing i języka Python

Użyj tego przewodnika Szybki start, aby rozpocząć wysyłanie żądań wyszukiwania do interfejsu API wyszukiwania obrazów Bing. Ta aplikacja języka Python wysyła zapytanie dotyczące wyszukiwania do interfejsu API i wyświetla adres URL pierwszego obrazu w wynikach. Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Ten przykład można uruchomić jako notes programu Jupyter w witrynie [MyBinder](https://mybinder.org). W tym celu należy kliknąć ikonę Launch Binder:

[![Spinacza](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Kod źródłowy dla tego przykładu jest dostępny [w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) z dodatkową obsługą błędów i adnotacjami.


## <a name="prerequisites"></a>Wymagania wstępne

* [Python 2.x lub 3.x](https://www.python.org/)
* [Biblioteka PIL (Python Imaging Library)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik kodu Python w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe moduły. Utwórz zmienne dla klucza subskrypcji, punktu końcowego wyszukiwania i terminu wyszukiwania. `search_url`może to być globalny punkt końcowy poniżej lub niestandardowy punkt końcowy [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlany w witrynie Azure portal dla zasobu.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`, tworząc słownik i dodając klucz jako wartość. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Tworzenie i wysyłanie żądania wyszukiwania

1. Utwórz słownik na potrzeby parametrów żądania wyszukiwania. Dodaj termin wyszukiwania do parametru `q`. Użyj wartości „public” dla parametru `license` w celu wyszukiwania obrazów w domenie publicznej. Użyj wartości „photo” dla parametru `imageType` w celu wyszukiwania tylko zdjęć.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Użyj biblioteki `requests` w celu wywołania interfejsu API wyszukiwania obrazów Bing. Dodaj nagłówek i parametry do żądania, a następnie zwróć odpowiedź jako obiekt JSON. Pobierz adresy URL do kilku obrazów `thumbnailUrl` miniatur z pola odpowiedzi.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Wyświetlanie odpowiedzi

1. Utwórz nowy rysunek z czterema kolumnami i czterema wierszami przy użyciu biblioteki matplotlib. 

2. Iteruj kolumny i wiersze na rysunku, a następnie użyj metody `Image.open()` biblioteki PIL w celu dodania miniatury obrazu do każdej przestrzeni. 

3. Użyj metody `plt.show()` w celu narysowania rysunku i wyświetlenia obrazów.

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Odpowiedzi z interfejsu API wyszukiwania obrazów Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź została obcięta w celu pokazania pojedynczego wyniku.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](../tutorial-bing-image-search-single-page-app.md)

* [Co to jest interfejs API wyszukiwania obrazów Bing?](../overview.md)  
* [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) interfejsów API wyszukiwania Bing. 
* [Pobieranie bezpłatnego klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentacja usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
