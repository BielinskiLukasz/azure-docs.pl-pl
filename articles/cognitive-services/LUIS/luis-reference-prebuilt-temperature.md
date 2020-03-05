---
title: Wstępnie skonstruowana Jednostka temperatury — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera temperatury informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 7e2b48c6353f56ab2269a8718146cb765797adba
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270369"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka temperatury dla aplikacji LUIS
Temperatura wyodrębnia różnych typów temperatury. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający temperatury do aplikacji. Jednostka temperatury jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>Typy temperatury
Temperatura jest zarządzana przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) repozytorium GitHub

## <a name="resolution-for-prebuilt-temperature-entity"></a>Rozpoznawanie temperatury wstępnie utworzone jednostki

Następujące obiekty jednostki są zwracane dla zapytania:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/V2)

Poniższy przykład przedstawia rozdzielczość **wbudowanej jednostki temperatury** .

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o jednostkach [wartości procentowych](luis-reference-prebuilt-percentage.md), [liczbowych](luis-reference-prebuilt-number.md)i [wieku](luis-reference-prebuilt-age.md) .
