---
title: Liczba znaków — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jak interfejs API tłumaczenia tekstu w usłudze Translator liczy znaki.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e3a16d9272e75f9a94f5381c1681c036d177e0f6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595988"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak interfejs API tłumaczenia tekstu w usłudze Translator liczy znaki

Interfejs API tłumaczenia tekstu w usłudze Translator liczy każdy punkt kodu Unicode tekstu wejściowego jako znak. Każde tłumaczenie tekstu na język jest traktowane jako oddzielne tłumaczenie, nawet jeśli żądanie zostało wykonane w jednym wywołaniu interfejsu API translacji w wielu językach. Długość odpowiedzi nie ma znaczenia.

Jakie są następujące zliczenia:

* Tekst przesłany do interfejs API tłumaczenia tekstu w usłudze Translator w treści żądania
   * `Text`w przypadku korzystania z metod przeszukiwania, transliteracji i słownika
   * `Text`i `Translation` w przypadku korzystania z przykładów słownika
* Wszystkie znaczniki: HTML, tagi XML itp. w polu tekstowym treści żądania. Nie zliczane jest notacja JSON użyta do skompilowania żądania (na przykład "Text:").
* Pojedyncza litera
* Znaki interpunkcyjne
* Spacja, tabulator, znacznik i dowolny znak znaku odstępu
* Każdy punkt kodowy zdefiniowany w kodzie Unicode
* Wielokrotne tłumaczenie, nawet jeśli wcześniej przetłumaczy ten sam tekst

W przypadku skryptów opartych na ideograms, takich jak chiński i japoński Kanji, interfejs API tłumaczenia tekstu w usłudze Translator będzie nadal liczyć liczbę punktów kodowych Unicode, jeden znak na ideogram. Wyjątek: Surogaty Unicode liczą się jako dwa znaki.

Liczba żądań, słów, bajtów lub zdań jest nieistotna w liczbie znaków.

Wywołania metod wykrywania i BreakSentence nie są zliczane w zużyciu znaków. Jednak oczekujemy, że wywołania metod wykrywania i BreakSentence są w rozsądny sposób proporcjonalne do korzystania z innych funkcji, które są zliczane. Jeśli liczba wykonywanych wywołań wykrywania lub BreakSentence przekracza liczbę innych zliczonych metod o 100 razy, firma Microsoft zastrzega sobie prawo do ograniczenia użycia metod wykrywania i BreakSentence.


Więcej informacji o liczbie znaków znajduje się w [sekcji często zadawane pytania dotyczące usługi Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
