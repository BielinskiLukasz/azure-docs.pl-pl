---
title: Typ jednostki wyrażenia regularnego — LUIS
description: Wyrażenie regularne jest najlepsze dla nieprzetworzonego tekstu wypowiedź. Ignoruje wielkość liter i ignoruje odmianę kulturową.  Dopasowanie wyrażenia regularnego jest stosowane po zmianach sprawdzania pisowni na poziomie znaku, a nie na poziomie tokenu.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 90260fca10fc087225f6b1286e9fa2dd6d17c836
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585610"
---
# <a name="regular-expression-entity"></a>Jednostka wyrażenia regularnego

Jednostka wyrażenia regularnego wyodrębnia jednostkę na podstawie podania wzorca wyrażenia regularnego.

Wyrażenie regularne jest najlepsze dla nieprzetworzonego tekstu wypowiedź. Ignoruje wielkość liter i ignoruje odmianę kulturową.  Dopasowanie wyrażenia regularnego jest stosowane po zmianach sprawdzania pisowni na poziomie znaku, a nie na poziomie tokenu. Jeśli wyrażenie regularne jest zbyt złożone, na przykład przy użyciu wielu nawiasów, nie można dodać wyrażenia do modelu. Używa części, ale nie całej biblioteki [wyrażeń regularnych programu .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) .

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Dane są spójne z uwzględnieniem spójnych zmian.
* Wyrażenie regularne nie wymaga więcej niż 2 poziomów zagnieżdżenia.

![Jednostka wyrażenia regularnego](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Zagadnienia dotyczące użycia

Wyrażenia regularne mogą odpowiadać więcej niż oczekiwano. Przykładem jest numeryczne Dopasowywanie wyrazów, takie jak `one` i `two` . Przykładem jest następujące wyrażenie regularne, które pasuje do liczby `one` wraz z innymi liczbami:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

To wyrażenie regularne również dopasowuje wszystkie wyrazy kończące się tymi liczbami, na przykład `phone` . Aby rozwiązać te problemy, należy się upewnić, że dopasowuje wyrażenie regularne uwzględnia granice wyrazu. Wyrażenie regularne do używania granic wyrazów dla tego przykładu jest używane w następujących wyrażeniach regularnych:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Przykładowy kod JSON

W przypadku użycia `kb[0-9]{6}` , jako definicji jednostki wyrażenia regularnego następująca odpowiedź JSON to przykład wypowiedź z zwracanymi jednostkami wyrażenia regularnego dla zapytania:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)


Jest to kod JSON, jeśli `verbose=false` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Jest to kod JSON, jeśli `verbose=true` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o jednostkach:

* [Pojęcia](luis-concept-entity-types.md)
* [Jak utworzyć](luis-how-to-add-entities.md)