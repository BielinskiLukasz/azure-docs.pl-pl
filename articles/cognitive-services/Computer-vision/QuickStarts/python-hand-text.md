---
title: 'Szybki start: wyodrębnianie tekstu odręcznego — REST, Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wyodrębnisz z obrazu tekst odręczny przy użyciu interfejsu API przetwarzania obrazów i języka Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7b3fe280ced4bfe397bcf168e529600417c62e57
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311431"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-python-in-computer-vision"></a>Szybki start: wyodrębnianie tekstu odręcznego przy użyciu interfejsu API REST i języka Python w przetwarzaniu obrazów

W tym przewodniku Szybki start wyodrębnisz tekst odręczny z obrazu przy użyciu interfejsu API REST przetwarzania obrazów. Metody [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) i [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) umożliwiają wykrywanie tekstu odręcznego na obrazie i wyodrębnianie rozpoznanych znaków do strumienia znaków, który może być używany przez maszyny.

> [!IMPORTANT]
> W odróżnieniu od metody [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) metoda [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) jest uruchamiana asynchronicznie. Ta metoda nie zwraca żadnych informacji w treści pomyślnej odpowiedzi. Zamiast tego metoda Recognize Text zwraca identyfikator URI w wartości pola nagłówka odpowiedzi `Operation-Content`. Następnie możesz wywołać ten identyfikator URI, który reprezentuje metodę [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201), aby sprawdzić stan i zwrócić wyniki wywołania metody Recognize Text.

Możesz pracować z tym przewodnikiem Szybki start krok po kroku, korzystając z aplikacji Jupyter Notebook w usłudze [MyBinder](https://mybinder.org). Aby uruchomić usługę Binder, wybierz poniższy przycisk:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz uruchomić przykładowy kod lokalnie, musisz mieć zainstalowany język [Python](https://www.python.org/downloads/).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Aby uzyskać klucz subskrypcji, zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie przykładowego kodu

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujący kod do edytora tekstów.
1. W razie potrzeby wprowadź następujące zmiany w kodzie:
    1. Zastąp wartość `subscription_key` kluczem subskrypcji.
    1. W razie potrzeby zastąp wartość `vision_base_url` adresem URL punktu końcowego dla zasobu przetwarzania obrazów z regionu platformy Azure, z którego uzyskano klucze subskrypcji.
    1. Opcjonalnie zastąp wartość `image_url` adresem URL innego obrazu, z którego chcesz wyodrębnić tekst odręczny.
1. Zapisz kod jako plik z rozszerzeniem `.py`. Na przykład `get-handwritten-text.py`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python get-handwritten-text.py`.

```python
import requests
import time
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

text_recognition_url = vision_base_url + "recognizeText"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" + \
    "Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
# Note: The request parameter changed for APIv2.
# For APIv1, it is 'handwriting': 'true'.
params  = {'mode': 'Handwritten'}
data    = {'url': image_url}
response = requests.post(
    text_recognition_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Extracting handwritten text requires two API calls: One call to submit the
# image for processing, the other to retrieve the text found in the image.

# Holds the URI used to retrieve the recognized text.
operation_url = response.headers["Operation-Location"]

# The recognized text isn't immediately available, so poll to wait for completion.
analysis = {}
poll = True
while (poll):
    response_final = requests.get(
        response.headers["Operation-Location"], headers=headers)
    analysis = response_final.json()
    time.sleep(1)
    if ("recognitionResult" in analysis):
        poll= False 
    if ("status" in analysis and analysis['status'] == 'Failed'):
        poll= False

polygons=[]
if ("recognitionResult" in analysis):
    # Extract the recognized text, with bounding boxes.
    polygons = [(line["boundingBox"], line["text"])
        for line in analysis["recognitionResult"]["lines"]]

# Display the image and overlay it with the extracted text.
plt.figure(figsize=(15, 15))
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image)
for polygon in polygons:
    vertices = [(polygon[0][i], polygon[0][i+1])
        for i in range(0, len(polygon[0]), 2)]
    text     = polygon[1]
    patch    = Polygon(vertices, closed=True, fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(vertices[0][0], vertices[0][1], text, fontsize=20, va="top")
_ = plt.axis("off")
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa strona internetowa analizuje i wyświetla pomyślną odpowiedź w oknie wiersza polecenia, podobnie jak w poniższym przykładzie:

```json
{
  "status": "Succeeded",
  "recognitionResult": {
    "lines": [
      {
        "boundingBox": [
          2,
          52,
          65,
          46,
          69,
          89,
          7,
          95
        ],
        "text": "dog",
        "words": [
          {
            "boundingBox": [
              0,
              59,
              63,
              43,
              77,
              86,
              3,
              102
            ],
            "text": "dog"
          }
        ]
      },
      {
        "boundingBox": [
          6,
          2,
          771,
          13,
          770,
          75,
          5,
          64
        ],
        "text": "The quick brown fox jumps over the lazy",
        "words": [
          {
            "boundingBox": [
              0,
              4,
              92,
              5,
              77,
              71,
              0,
              71
            ],
            "text": "The"
          },
          {
            "boundingBox": [
              74,
              4,
              189,
              5,
              174,
              72,
              60,
              71
            ],
            "text": "quick"
          },
          {
            "boundingBox": [
              176,
              5,
              321,
              6,
              306,
              73,
              161,
              72
            ],
            "text": "brown"
          },
          {
            "boundingBox": [
              308,
              6,
              387,
              6,
              372,
              73,
              293,
              73
            ],
            "text": "fox"
          },
          {
            "boundingBox": [
              382,
              6,
              506,
              7,
              491,
              74,
              368,
              73
            ],
            "text": "jumps"
          },
          {
            "boundingBox": [
              492,
              7,
              607,
              8,
              592,
              75,
              478,
              74
            ],
            "text": "over"
          },
          {
            "boundingBox": [
              589,
              8,
              673,
              8,
              658,
              75,
              575,
              75
            ],
            "text": "the"
          },
          {
            "boundingBox": [
              660,
              8,
              783,
              9,
              768,
              76,
              645,
              75
            ],
            "text": "lazy"
          }
        ]
      },
      {
        "boundingBox": [
          2,
          84,
          783,
          96,
          782,
          154,
          1,
          148
        ],
        "text": "Pack my box with five dozen liquor jugs",
        "words": [
          {
            "boundingBox": [
              0,
              86,
              94,
              87,
              72,
              151,
              0,
              149
            ],
            "text": "Pack"
          },
          {
            "boundingBox": [
              76,
              87,
              164,
              88,
              142,
              152,
              54,
              150
            ],
            "text": "my"
          },
          {
            "boundingBox": [
              155,
              88,
              243,
              89,
              222,
              152,
              134,
              151
            ],
            "text": "box"
          },
          {
            "boundingBox": [
              226,
              89,
              344,
              90,
              323,
              154,
              204,
              152
            ],
            "text": "with"
          },
          {
            "boundingBox": [
              336,
              90,
              432,
              91,
              411,
              154,
              314,
              154
            ],
            "text": "five"
          },
          {
            "boundingBox": [
              419,
              91,
              538,
              92,
              516,
              154,
              398,
              154
            ],
            "text": "dozen"
          },
          {
            "boundingBox": [
              547,
              92,
              701,
              94,
              679,
              154,
              525,
              154
            ],
            "text": "liquor"
          },
          {
            "boundingBox": [
              696,
              94,
              800,
              95,
              780,
              154,
              675,
              154
            ],
            "text": "jugs"
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z aplikacją w języku Python, w której zastosowano interfejs API przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API Python Tutorial (Samouczek dla języka Python dotyczący interfejsu API przetwarzania obrazów)](../Tutorials/PythonTutorial.md)
