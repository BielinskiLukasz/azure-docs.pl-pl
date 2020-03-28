---
title: Szybki start biblioteki klienta wyszukiwania wideo w u usługi Bing w języku Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 7a9fab8ba8bb9d21c9284cbf14bc67226d2ef9d3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289742"
---
Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą biblioteki klienta wyszukiwania wideo Bing dla języka Python. Usługa Bing Video Search ma interfejs API REST zgodny z większością języków programowania, ale biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy dla tego przykładu można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) z dodatkowymi adnotacjami i funkcjami.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Python](https://www.python.org/) 2.x lub 3.x
- Biblioteka klienta wyszukiwania wideo Bing dla języka Python

Zaleca się użycie środowiska [wirtualnego](https://docs.python.org/3/tutorial/venv.html)języka Python . Za pomocą [modułu venv](https://pypi.python.org/pypi/virtualenv)można zainstalować i zainicjować środowisko wirtualne. Zainstaluj moduł virtualenv dla środowiska Python w wersji 2.7 za pomocą następującego polecenia:

```console
python -m venv mytestenv
```

Zainstaluj bibliotekę klienta wyszukiwania wideo Bing za pomocą:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje importu. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchClient
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Utwórz zmienną dla klucza subskrypcji. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    ```

## <a name="create-the-search-client"></a>Tworzenie klienta wyszukiwania

Utwórz wystąpienie obiektu `CognitiveServicesCredentials` i utwórz wystąpienie klienta:

```python
client = VideoSearchAPI(endpoint, CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Wysyłanie żądania wyszukiwania i odbieranie odpowiedzi

1. Użyj funkcji `client.videos.search()` na potrzeby zapytania wyszukiwania, aby wysłać żądanie do interfejsu API wyszukiwania wideo Bing i uzyskać odpowiedź.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Jeśli odpowiedź zawiera wyniki wyszukiwania, pobierz pierwszy z nich i wyświetl jego identyfikator, nazwę i adres URL.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

- [Co to jest interfejs API wyszukiwania wideo Bing?](../../overview.md)
- [Przykłady zestawów SDK usług cognitive.NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
