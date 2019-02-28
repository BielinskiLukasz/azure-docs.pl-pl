---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 2317e0b8bfe01f94989412db7c0c4560b2ca728f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741608"
---
Przykładowy plik wypowiedzi **utterances.json** ma określony format. 

Pole `text` zawiera tekst przykładowej wypowiedzi. Pole `intentName` musi odpowiadać nazwie istniejącej wypowiedzi w aplikacji LUIS. Pole `entityLabels` jest wymagane. Jeśli nie chcesz dodawać etykiet do żadnych jednostek, podaj pustą tablicę.

Jeśli tablica entityLabels nie jest pusta, elementy `startCharIndex` i `endCharIndex` muszą oznaczać jednostkę określoną w polu `entityName`. Indeks jest liczony od zera, co oznacza, że wartość 6 w pierwszym przykładzie odwołuje się do litery „S” wyrazu Seattle, a nie do spacji przed wielką literą S. Jeśli etykieta w tekście będzie rozpoczynać się lub kończyć spacją, wywołanie interfejsu API w celu dodania wypowiedzi nie powiedzie się.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
