---
title: 'Szybki Start: analizowanie obrazu zdalnego za pomocą interfejsu API REST i środowiska Node. js'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start przeprowadzisz analizę obrazu za pomocą interfejsu API przetwarzania obrazów w języku Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18, seo-javascript-september2018, seo-javascript-october2019
ms.openlocfilehash: 98fb7eab52c710d9008aafefc50c2d00979d3350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973836"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Szybki Start: analizowanie obrazu zdalnego za pomocą interfejsu API REST przetwarzanie obrazów przy użyciu środowiska Node. js

W tym przewodniku szybki start przeprowadzisz analizę zdalnie przechowywanego obrazu w celu wyodrębnienia funkcji wizualnych przy użyciu interfejsu API REST przetwarzanie obrazów przy użyciu środowiska Node. js. Metoda [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) umożliwia wyodrębnienie elementów wizualnych na podstawie zawartości obrazu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć zainstalowany język [Node.js](https://nodejs.org) w wersji 4.x lub nowszej.
- Musisz mieć zainstalowany program [npm](https://www.npmjs.com/).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz wersji próbnej z usługi [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować przetwarzanie obrazów i uzyskać klucz. Następnie [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu punktu końcowego klucza i usługi, odpowiednio nazwane `COMPUTER_VISION_SUBSCRIPTION_KEY` i `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie przykładowego kodu

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Zainstaluj pakiet [`request`](https://www.npmjs.com/package/request) npm.
   1. Otwórz okno wiersza polecenia jako administrator.
   1. Uruchom następujące polecenie:

      ```console
      npm install request
      ```

   1. Po pomyślnym zainstalowaniu pakietu zamknij okno wiersza polecenia.

1. Skopiuj następujący kod do edytora tekstów.
1. Opcjonalnie zastąp wartość `imageUrl` adresem URL innego obrazu, który chcesz analizować.
1. Opcjonalnie zastąp wartość parametru żądania `language` innym językiem.
1. Zapisz kod jako plik z rozszerzeniem `.js`. Na przykład `analyze-image.js`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `node`, aby uruchomić plik. Na przykład `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.1/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Próbka analizuje i wyświetla pomyślną odpowiedź w oknie wiersza polecenia, podobnie jak w poniższym przykładzie:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli plik nie jest już potrzebny, usuń to, a następnie odinstaluj pakiet `request` npm. Aby odinstalować pakiet, wykonaj następujące czynności:

1. Otwórz okno wiersza polecenia jako administrator.
2. Uruchom następujące polecenie:

   ```console
   npm uninstall request
   ```

3. Po pomyślnym odinstalowaniu pakietu zamknij okno wiersza polecenia.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API przetwarzania obrazów używanymi do analizy obrazu, wykrywania osobistości i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
