---
title: Obsługa języka — interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: 'Lista języków naturalnych obsługiwanych przez interfejs API analizy tekstu. W tym artykule wyjaśniono, które języki są obsługiwane dla każdej operacji: Analiza tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka i rozpoznawanie jednostek.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942656"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Obsługa języka i regionu dla interfejs API analizy tekstu

W tym artykule wyjaśniono, które języki są obsługiwane dla każdej operacji: analizy tonacji, wyodrębniania kluczowych fraz, wykrywania języka i nazwanego rozpoznawania jednostek.

## <a name="language-detection"></a>Wykrywanie języka

Interfejs API analizy tekstu może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych.  Wykrywanie języka zwraca "skrypt" języka. Na przykład dla frazy "mam pies" zwróci `en`, a nie `en-US`. Jedynym szczególnym przypadkiem jest chiński, w którym funkcja wykrywania języka zwróci `zh_CHS` lub `zh_CHT`, jeśli może ustalić skrypt, w którym znajduje się dostarczony tekst. W sytuacjach, gdy nie można zidentyfikować określonego skryptu dla dokumentu w języku chińskim, zostanie on zwrócony po prostu `zh`.

Nie publikujemy dokładnej listy języków dla tej funkcji, ale może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych. 

Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz spróbować wykrywanie języka, aby sprawdzić, czy zwróci kod. Odpowiedź dla języków, których nie można wykryć, jest `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analiza tonacji, wyodrębnianie kluczowych fraz i nazwanego rozpoznawania jednostek

W przypadku analizy tonacji, wyodrębniania kluczowych fraz i rozpoznawania jednostek Lista obsługiwanych języków jest bardziej selektywna, ponieważ analizatory są udoskonalane w celu uwzględnienia reguł lingwistycznych dodatkowych języków. W przypadku nazwanego rozpoznawania jednostek v2 obsługa pełnego zestawu [typów jednostek](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) jest obecnie ograniczona do następujących języków: 
* Polski
* Chiński (uproszczony)
* Francuski
* Niemiecki
* Hiszpański

Tylko `Person`, `Location` i `Organization` nazwane jednostki są zwracane dla innych języków.

## <a name="language-list-and-status"></a>Lista i stan języka

Obsługa języka jest początkowo wdrażana w wersji zapoznawczej, z stopniowaniem do ogólnie dostępnego stanu (GA), niezależnie od siebie i ogólnej usługi analiza tekstu. Istnieje możliwość, że języki pozostają w wersji zapoznawczej, nawet gdy interfejs API analizy tekstu przejścia są ogólnie dostępne.

> [!NOTE]
> Aby uzyskać szczegółowy opis obsługi języka nazwanego rozpoznawania jednostek (NER) v3 (publiczna wersja zapoznawcza), zobacz [nazwane typy jednostek](named-entity-types.md).

| Język              | Kod języka | Opinia | Kluczowe frazy | Rozpoznawanie jednostek nazwanych | Łączenie jednostek |       Uwagi        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| arabski                |     `ar`      |           |             |           ✔ \*           |                |                    |
| Czeski                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| Chiński (uproszczony)    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                | `zh` również zaakceptowana                   |
| Chiński (tradycyjny)   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| Duński                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| holenderski                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Polski               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| Fiński               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Francuski                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Niemiecki                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Grecki                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| Węgierski             |     `hu`      |           |             |           ✔ \*           |                |                    |
| Włoski               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Japoński              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Koreański                |     `ko`      |   ✔ \*\*  |      ✔      |           ✔ \*           |                |                    |
| Norweski (bokmål)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                | `nb` również zaakceptowana                   |
| Polski                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Portugalski (Portugalia) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` również zaakceptowana |
| Portugalski (Brazylia)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| Rosyjski               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Hiszpański               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| Szwedzki               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Turecki               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

Obsługa języków \* jest w wersji zapoznawczej

\** również dostępna w [Analiza tonacji v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) i/lub [nazwane wersje zapoznawcze programu rozpoznawania jednostek](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) w wersji 3.

## <a name="see-also"></a>Zobacz też

[Strona dokumentacji Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
