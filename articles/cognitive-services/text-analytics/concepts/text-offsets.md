---
title: Przesunięcia tekstu w interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat przesunięć spowodowanych przez kodowania wielojęzyczne i Emoji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: c587bb042601b947b71658bf790e9acdfbdbf742
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363786"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Przesunięcie tekstu w danych wyjściowych interfejs API analizy tekstu

Obsługa wielojęzycznych i Emoji prowadzi do kodowania Unicode, które używają więcej niż jednego [punktu kodu](https://wikipedia.org/wiki/Code_point) do reprezentowania pojedynczego wyświetlanego znaku o nazwie Grapheme. Na przykład znaki emoji, takie jak 🌷 i 👍 mogą używać kilku znaków do redagowania kształtu z dodatkowymi znakami dla atrybutów wizualnych, takich jak odcienie skórki. Podobnie słowo w języku hindi `अनुच्छेद` jest kodowane jako pięć liter i trzy znaki łączące.

Ze względu na różne długości możliwych kodowań wielojęzycznych i emoji, interfejs API analizy tekstu mogą zwracać przesunięcia w odpowiedzi.

## <a name="offsets-in-the-api-response"></a>Przesunięcia w odpowiedzi interfejsu API. 

Za każdym razem, gdy przesunięcia są zwracane odpowiedzi interfejsu API, takie jak [rozpoznawanie jednostek nazwanych](../how-tos/text-analytics-how-to-entity-linking.md) lub [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md), należy pamiętać o następujących kwestiach:

* Elementy w odpowiedzi mogą być specyficzne dla punktu końcowego, który został wywołany. 
* Ładunki POST/GET protokołu HTTP są kodowane w [formacie UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), co może być domyślne lub nie może być domyślnym kodowaniem znaków w kompilatorze po stronie klienta lub systemie operacyjnym.
* Przesunięcia odnoszą się do liczby Grapheme na podstawie standardu [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) , a nie liczby znaków.

## <a name="extracting-substrings-from-text-with-offsets"></a>Wyodrębnianie podciągów z tekstu z przesunięciami

Przesunięcia mogą spowodować problemy podczas korzystania z metod podciągów opartych na znakach, na przykład w przypadku metody [podciągu .NET ()](/dotnet/api/system.string.substring?view=netframework-4.8) . Jeden z problemów polega na tym, że przesunięcie może spowodować zakończenie metody podciągu w środku wieloznakowego kodowania Grapheme zamiast na końcu.

W programie .NET Rozważ użycie klasy [StringInfo](/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) , która umożliwia współpracę z ciągiem jako serią elementów tekstowych, a nie pojedynczymi obiektami znaków. Możesz również wyszukać biblioteki rozdzielacza Grapheme w preferowanym środowisku oprogramowania. 

Interfejs API analizy tekstu zwraca te elementy tekstowe również dla wygody.

## <a name="offsets-in-api-version-31-preview"></a>Przesunięcia w interfejsie API w wersji 3,1 — wersja zapoznawcza

Począwszy od interfejsu API w wersji 3,1-Preview. 1, wszystkie interfejs API analizy tekstu punkty końcowe, które zwracają przesunięcie, będą obsługiwały `stringIndexType` parametr. Ten parametr dostosowuje `offset` atrybuty i `length` w danych wyjściowych interfejsu API zgodnie z żądanym schematem iteracji ciągu. Obecnie obsługujemy trzy typy:

1. `textElement_v8`(domyślnie): wykonuje iterację przez graphemes zgodnie z definicją w standardzie [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)
2. `unicodeCodePoint`: wykonuje iterację w [punktach kodowych Unicode](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), domyślny schemat dla języka Python 3
3. `utf16CodeUnit`: wykonuje iterację w [jednostkach kodu UTF-16](https://unicode.org/faq/utf_bom.html#UTF16), domyślnym schemacie dla języków JavaScript, Java i .NET.

Jeśli `stringIndexType` żądane dopasowanie do wybranego środowiska programistycznego, wyodrębnianie podciągów można wykonać przy użyciu standardowych podciągów lub metod wycinka. 

## <a name="see-also"></a>Zobacz też

* [Przegląd analizy tekstu](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
* [Wykrywanie języka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)