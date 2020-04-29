---
title: LUIS wstępnie utworzonych jednostek — dokumentacja poczty e-mail
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostce poczty e-mail w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f7aa2a341a82d30f7022af49e626a3cd358854b7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273479"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka poczty e-mail dla aplikacji LUIS
Wyodrębnianie wiadomości e-mail obejmuje cały adres e-mail z wypowiedź. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej wiadomości e-mail do intencji aplikacji. Jednostka poczty e-mail jest obsługiwana `en-us` tylko w kulturze.

## <a name="resolution-for-prebuilt-email"></a>Rozwiązanie dla wstępnie skompilowanej poczty e-mail

Następujące obiekty jednostki są zwracane dla zapytania:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z `verbose` parametrem ustawionym `false`na:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)

Poniższy kod JSON jest z `verbose` parametrem ustawionym `true`na:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
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

W poniższym przykładzie przedstawiono rozdzielczość **wbudowanej jednostki poczty e-mail** .

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o [liczbie](luis-reference-prebuilt-number.md), liczbie [porządkowej](luis-reference-prebuilt-ordinal.md)i [wartości procentowej](luis-reference-prebuilt-percentage.md).
