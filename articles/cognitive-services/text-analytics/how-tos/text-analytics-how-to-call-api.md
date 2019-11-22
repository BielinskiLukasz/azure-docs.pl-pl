---
title: Wywoływanie interfejsu API usługi Text Analytics
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak można wywołać usługę Azure Cognitive Services analiza tekstu interfejsu API REST i programu Poster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286601"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Jak wywołać interfejs API REST analiza tekstu

Wywołania **interfejs API analizy tekstu** to wywołania http post/Get, które można sformułować w dowolnym języku. W tym artykule używamy REST i programu [Poster](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) , aby zademonstrować kluczowe pojęcia.

Każde żądanie musi zawierać klucz dostępu i punkt końcowy HTTP. Punkt końcowy określa region wybrany podczas rejestracji, adres URL usługi i zasób używany na żądanie: `sentiment`, `keyphrases`, `languages`i `entities`. 

Odzyskanie tego analiza tekstu jest bezstanowe, aby nie było żadnych zasobów danych do zarządzania. Tekst zostanie przekazany, przeanalizowany po odebraniu, a wyniki są zwracane natychmiast do aplikacji wywołującej.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definicja schematu JSON

Dane wejściowe muszą być w formacie JSON w nieprzetworzonym tekście bez struktury. KOD XML nie jest obsługiwany. Schemat jest prosty, składający się z elementów opisanych na poniższej liście. 

Obecnie można przesłać te same dokumenty do wszystkich operacji analiza tekstu: tonacji, frazy kluczowej, wykrywania języka i identyfikacji jednostek. (Schemat jest prawdopodobnie różny dla każdej analizy w przyszłości).

| Element | Prawidłowe wartości | Wymagana? | Sposób użycia |
|---------|--------------|-----------|-------|
|`id` |Typ danych to ciąg, ale w temacie identyfikatory dokumentów w programie mają być liczbami całkowitymi. | Wymagane | System używa identyfikatorów dostarczanych do struktury danych wyjściowych. Kody języka, kluczowe frazy i oceny tonacji są generowane dla każdego identyfikatora w żądaniu.|
|`text` | Nieprzetworzony tekst nieprzebudowany, do 5 120 znaków. | Wymagane | W przypadku wykrywania języka tekst może być wyrażony w dowolnym języku. W przypadku analizy tonacji, wyodrębniania kluczowych fraz i identyfikacji jednostek tekst musi być w [obsługiwanym języku](../text-analytics-supported-languages.md). |
|`language` | 2-znakowy kod [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) dla [obsługiwanego języka](../text-analytics-supported-languages.md) | Różna | Wymagane do analizy tonacji, wyodrębniania kluczowych fraz i konsolidacji jednostek; opcjonalne do wykrywania języka. Jeśli wyłączysz go, nie ma żadnego błędu, ale analiza zostanie nieprzerwana. Kod języka powinien odpowiadać podanej `text`. |

Aby uzyskać więcej informacji na temat limitów, zobacz [Analiza tekstu omówienie > limitów danych](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Skonfiguruj żądanie w programie Poster

Usługa akceptuje żądania o rozmiarze do 1 MB. Jeśli używasz programu Poster (lub innego narzędzia testowego interfejsu API sieci Web), Skonfiguruj punkt końcowy do uwzględnienia zasobu, którego chcesz użyć, a następnie podaj klucz dostępu w nagłówku żądania. Każda operacja wymaga dołączenia odpowiedniego zasobu do punktu końcowego. 

1. W programie Poster:

   + Wybierz pozycję **post** jako typ żądania.
   + Wklej w punkcie końcowym skopiowanym ze strony portalu.
   + Dołącz zasób.

   Punkty końcowe zasobów są następujące (region może się różnić):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Ustaw trzy nagłówki żądania:

   + `Ocp-Apim-Subscription-Key`: Twój klucz dostępu uzyskany z Azure Portal.
   + `Content-Type`: Application/JSON.
   + `Accept`: Application/JSON.

   Twoje żądanie powinno wyglądać podobnie do poniższego zrzutu ekranu, przy założeniu zasobu **/keyPhrases** .

   ![Zażądaj zrzutu ekranu z punktem końcowym i nagłówkami](../media/postman-request-keyphrase-1.png)

4. Kliknij pozycję **treść** , a następnie wybierz pozycję **RAW** dla formatu.

   ![Żądaj zrzutu ekranu z ustawieniami treści](../media/postman-request-body-raw.png)

5. Wklej w niektórych dokumentach JSON w formacie, który jest prawidłowy dla zamierzonej analizy. Aby uzyskać więcej informacji na temat konkretnej analizy, zobacz poniższe tematy:

  + [Wykrywanie języka](text-analytics-how-to-language-detection.md)  
  + [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md)  
  + [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md)  
  + [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md)  


6. Kliknij przycisk **Wyślij** , aby przesłać żądanie. Zobacz sekcję [limity danych](../overview.md#data-limits) w temacie Omówienie, aby uzyskać informacje dotyczące liczby żądań wysyłanych na minutę i sekundę.

   W programie Poster odpowiedź jest wyświetlana w następnym oknie w dół, jako pojedynczy dokument JSON, z elementem dla każdego identyfikatora dokumentu podanego w żądaniu.

## <a name="see-also"></a>Zobacz także 

 [Przegląd analizy tekstu](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wykryj język](text-analytics-how-to-language-detection.md)
