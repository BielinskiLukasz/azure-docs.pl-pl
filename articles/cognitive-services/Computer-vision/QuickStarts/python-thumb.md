---
title: 'Szybki start: generowanie miniatury — REST, Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu języka Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 21d8659e13506777f6045272f68c69dd63f09cf5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80244685"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Szybki start: generowanie miniatury przy użyciu interfejsu API REST i języka Python

W tym przewodniku Szybki start wygenerujesz miniaturę z obrazu przy użyciu interfejsu API REST przetwarzania wizji komputerowej. W przypadku metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) możesz określać żądaną wysokość i szerokość, a interfejs API przetwarzania obrazów wykorzystuje inteligentne przycinanie, aby określić obszar zainteresowania i na jego podstawie wygenerować współrzędne przycinania.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz próbny z [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Możesz też postępować zgodnie z instrukcjami w aplikacji [Utwórz konto usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby zasubskrybować usługę Computer Vision i uzyskać klucz. Następnie [należy utworzyć zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu `COMPUTER_VISION_SUBSCRIPTION_KEY` punktu `COMPUTER_VISION_ENDPOINT`końcowego klucza i usługi, odpowiednio o nazwie i ,.
- Edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie próbki

Aby utworzyć i uruchomić przykład, skopiuj poniższy kod do edytora kodu. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v2.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Następnie wykonaj poniższe czynności:
1. Opcjonalnie zastąp wartość `image_url` adresem URL innego obrazu, dla którego chcesz wygenerować miniaturę.
1. Zapisz kod jako plik z rozszerzeniem `.py`. Na przykład `get-thumbnail.py`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź jest zwracana jako dane binarne, które reprezentują dane obrazu miniatury. W przykładzie powinien zostać wyświetlony ten obraz. Jeśli żądanie zakończy się niepowodzeniem, odpowiedź zostanie wyświetlona w oknie wiersza polecenia. Powinna ona zawierać kod błędu.

## <a name="run-in-jupyter-optional"></a>Uruchamianie w aplikacji Jupyter (opcjonalnie)

Opcjonalnie możesz pracować z tym przewodnikiem Szybki start krok po kroku, korzystając z aplikacji Jupyter Notebook w usłudze [MyBinder](https://mybinder.org). Aby uruchomić usługę Binder, wybierz poniższy przycisk:

[![Spinacza](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Następne kroki

Następnie zapoznaj się z bardziej szczegółowymi informacjami o funkcji generowania miniatur.

> [!div class="nextstepaction"]
> [Generowanie miniatur](../concept-generating-thumbnails.md)
