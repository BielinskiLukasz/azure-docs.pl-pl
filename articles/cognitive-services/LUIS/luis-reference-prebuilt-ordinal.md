---
title: Wstępnie skompilowany obiekt Entity-LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje porządkowe wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273444"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka dla aplikacji LUIS
Numer porządkowy jest reprezentacją liczbową obiektu wewnątrz zestawu: `first`, `second`, `third`. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający porządkowego do intencji aplikacji. Jednostka porządkowa jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-ordinal"></a>Typy numer
Numer porządkowy jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) repozytorium GitHub

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Rozwiązania dla wstępnie utworzone jednostki porządkowe

Następujące obiekty jednostki są zwracane dla zapytania:

`Order the second option`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. porządkowej** .

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej na temat [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [numeru telefonu](luis-reference-prebuilt-phonenumber.md)i jednostek [temperatury](luis-reference-prebuilt-temperature.md) .
