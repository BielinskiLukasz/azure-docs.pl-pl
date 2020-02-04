---
title: Pobierz zamierzenia z wywołaniem REST w języku Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 35bcf822b97d5e4028ec99c417ec1f54952350a4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966742"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python 3.6](https://www.python.org/downloads/) lub nowsze.
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Utwórz klucz środowiska uruchomieniowego LUIS dla prognoz

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Kliknij przycisk [utwórz **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Wprowadź wszystkie wymagane ustawienia dla klucza czasu wykonywania:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wymagana nazwa (2-64 znaków)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i dostępną|
    |Warstwa cenowa|`F0` — minimalna warstwa cenowa|
    |Grupa zasobów|Wybierz dostępną grupę zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobów.
1. Zbierz skonfigurowane `endpoint` i `key`.

## <a name="get-intent-from-the-prediction-endpoint"></a>Pobierz intencję z punktu końcowego przewidywania

Użyj języka Python, aby wykonać zapytanie dotyczące [punktu końcowego przewidywania](https://aka.ms/luis-apim-v3-prediction) i uzyskać wynik przewidywania.

1. Skopiuj ten fragment kodu do pliku o nazwie `predict.py`:

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY'
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'

        headers = {
        }

        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }

        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())

    except Exception as e:
        print(f'{e}')
    ```

1. Zastąp wartości `YOUR-KEY` i `YOUR-ENDPOINT` własnym kluczem predykcyjnym i punktem końcowym.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Klucz predykcyjny dla 32 znaków.|
    |`YOUR-ENDPOINT`| Punkt końcowy adresu URL przewidywania. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Zainstaluj zależność `requests`. Służy do wprowadzania żądań HTTP:

    ```console
    pip install requests
    ```

1. Uruchom skrypt za pomocą tego polecenia konsoli:

    ```console
    python predict.py
    ```

1. Przejrzyj odpowiedź przewidywania zwracaną jako kod JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Oto opis odpowiedzi JSON sformatowanych pod kątem czytelności:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń plik z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wyrażenia długości i uczenie](../get-started-get-model-rest-apis.md)