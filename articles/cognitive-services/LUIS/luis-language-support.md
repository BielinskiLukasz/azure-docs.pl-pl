---
title: Obsługa języków
titleSuffix: Azure Cognitive Services
description: Usługa LUIS ma wiele funkcji w ramach usługi. Nie wszystkie funkcje są w tej samej parzystości języka. Upewnij się, że funkcje, których jesteś zainteresowany są obsługiwane w kulturze języka, które są przeznaczone dla. Aplikacją usługi LUIS jest specyficzne dla kultury i nie można zmienić po jej ustawieniu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: 452700593d7145e828155c3bd999ad90e2437ba9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099005"
---
# <a name="language-and-region-support-for-luis"></a>Obsługa języka i regionu dla usługi LUIS

Usługa LUIS ma wiele funkcji w ramach usługi. Nie wszystkie funkcje są w tej samej parzystości języka. Upewnij się, że funkcje, których jesteś zainteresowany są obsługiwane w kulturze języka, które są przeznaczone dla. Aplikacją usługi LUIS jest specyficzne dla kultury i nie można zmienić po jej ustawieniu.

## <a name="multi-language-luis-apps"></a>Usługa LUIS wielojęzycznych aplikacji

Jeśli potrzebujesz aplikacji klienckiej usługi LUIS wielu języków, takich jak czatbota, masz kilka opcji. Jeśli usługa LUIS obsługuje wszystkie języki, możesz tworzyć aplikacją usługi LUIS dla każdego języka. Każda aplikacja usługi LUIS ma identyfikator unikatowy aplikacji i punkt końcowy dziennika. Jeśli konieczne jest zapewnienie zrozumienia dla języka nie obsługuje usługi LUIS, można użyć języka [interfejsu API usługi Microsoft Translator](../Translator/translator-info-overview.md) tłumaczenie wypowiedź w obsługiwanym języku, Prześlij wypowiedź do punktu końcowego usługi LUIS i odbierania Wynikowy wyniki.

## <a name="languages-supported"></a>Obsługiwane języki

Usługa LUIS rozumie wypowiedzi w następujących językach:

| Język |Ustawienia regionalne  |  Wstępnie utworzone domeny | Wstępnie utworzone jednostki | Fraza sugestie | **[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Tonacji i<br>Słowa kluczowe)|
|--|--|:--:|:--:|:--:|:--:|
| Angielski |`en-US` | ✔ | ✔  |✔|✔|
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holenderski |`nl-NL` |-|  -   |-|✔|
| Francuski (Francja) |`fr-FR` |-| ✔ |✔ |✔|
| Francuski (Kanada) |`fr-CA` |-|   -   |-|✔|
| Niemiecki |`de-DE` |-| ✔ |✔ |✔|
| Włoski |`it-IT` |-| ✔ |✔|✔|
| *[Japoński](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Tylko frazy kluczowe|
| Koreański |`ko-KR` |-|   -   |-|Tylko frazy kluczowe|
| Portugalski (Brazylia) |`pt-BR` |-| ✔ |✔ |nie wszystkie podrzędne kultur|
| Hiszpański (Hiszpania) |`es-ES` |-| ✔ |✔|✔|
| Hiszpański (Meksyk)|`es-MX` |-|  -   |✔|✔|


Obsługa języka jest różny dla [ze wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md) i [ze wstępnie utworzonych domen](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>* Informacje o pomocy technicznej chiński

 - W `zh-cn` kultury, LUIS oczekuje zestawu zamiast zestawu znaków tradycyjnych uproszczone znaków chińskich.
 - Nazwy intencji, jednostki, funkcje i wyrażenia regularne mogą być znakami języka chińskiego lub litery rzymskie.
 - Zobacz [odwołania ze wstępnie utworzonych domen ](luis-reference-prebuilt-domains.md) informacje obsługiwanych ze wstępnie utworzonych domen w `zh-cn` kultury.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Informacje o pomocy technicznej japoński

 - LUIS nie zapewnia analizy składni i nie będzie zrozumieć różnicę między Keigo i japoński nieformalnej, dlatego należy zastosować różne poziomy formalności jako przykłady szkolenia dla aplikacji.
     - でございます nie jest taka sama jak です.
     - です nie jest taka sama jak だ.

### <a name="text-analytics-support-notes"></a>** Analiza tekstu obsługuje uwagi
Analiza tekstu zawiera keyPhrase wstępnie utworzone jednostki i wskaźniki nastrojów klientów analizy. Tylko portugalski jest obsługiwana w przypadku podhodowli: `pt-PT` i `pt-BR`. Wszystkie inne kultury są obsługiwane na poziomie podstawowym kultury. Dowiedz się więcej na temat analizy tekstu [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Języki obsługiwane interfejsu API mowy
Zobacz mowy [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) dla języków tryb dyktowania mowy.

### <a name="bing-spell-check-supported-languages"></a>Sprawdzanie pisowni Bing obsługiwane języki
Zobacz, sprawdzanie pisowni Bing [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) Aby uzyskać listę obsługiwanych języków i stan.

## <a name="rare-or-foreign-words-in-an-application"></a>Rzadkie lub obce słowa w aplikacji
W `en-us` kultury, uzyskuje informacje o odróżnić najbardziej wyrazami, w tym żargonu usługi LUIS. W `zh-cn` kultury, uzyskuje informacje o odróżnić większości znaków chińskich usługi LUIS. Jeśli używasz rzadkich wyrazu w `en-us` lub znaków w pliku `zh-cn`, zobaczyć LUIS jest prawdopodobnie do odróżnienia tego wyrazu lub znaków, Dodaj ten wyraz lub znak do [funkcji listy fraz](luis-how-to-add-features.md). Na przykład słowa poza kulturę aplikacji — czyli obce słowa — należy dodać do funkcji listy fraz. Ta lista frazy powinna być oznaczona jako innego niż wymienne, aby wskazać, że zbiór słów rzadkich formularzy klasę, która LUIS powinien Naucz się rozpoznawać, ale nie są one synonimy lub wymienne ze sobą.

### <a name="hybrid-languages"></a>Języki hybrydowe
Języki hybrydowego łączyć wyrazów dwie kultury, takie jak angielski i chińskim. Te języki nie są obsługiwane usługi LUIS, ponieważ aplikacja jest oparta na jednej kulturze.

## <a name="tokenization"></a>Tokenizacji
Do przeprowadzenia uczenia maszynowego, usługa LUIS dzieli wypowiedź na [tokenów](luis-glossary.md#token) na podstawie kultury.

|Język|  Każdy spacji lub znaku specjalnego | poziom znaków|wyrazy złożone|[Jednostka tokenami zwracana](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chiński||✔||✔|
|Holenderski|||✔|✔|
|Angielski (en-us)|✔ ||||
|Francuski (fr-FR)|✔||||
|Francuski (fr-CA)|✔||||
|Niemiecki|||✔|✔|
|Włoski|✔||||
|Japoński||||✔|
|Koreański||✔||✔|
|Portugalski (Brazylia)|✔||||
|Hiszpański (es-ES)|✔||||
|Hiszpański (es-MX)|✔||||
