---
title: Wywoływanie interfejsu API usługi Text Analytics
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak wywołać interfejs API REST analizy tekstu.
services: cognitive-services
author: ashmaka
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 60aa51e4dac796e034b7b0506be0e205951226de
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870779"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Jak wywołać interfejs API REST analizy tekstu

Wywołania **interfejsu API analizy tekstu** wywołań HTTP POST/GET, które można sformułować w dowolnym języku. W tym artykule używamy REST i [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) celu zademonstrowania kluczowych pojęć.

Każde żądanie musi zawierać klucz dostępu i punktu końcowego HTTP. Punkt końcowy określa region wybrany podczas logowania się, adres URL usługi i zasobów na żądanie: `sentiment`, `keyphrases`, `languages`, i `entities`. 

Odwołaj, analizy tekstu jest bezstanowy, więc żadne zasoby danych do zarządzania. Tekst zostanie przekazany, analizowane po otrzymaniu, a wyniki są natychmiast zwracane do aplikacji wywołującej.

> [!Tip]
> W przypadku jednorazowe wywołań zobaczyć, jak działa interfejs API, można wysłać żądania POST z poziomu wbudowanego **konsoli testowania interfejsu API**, dostępne na każdym [strony dokumentacji interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Nie skonfigurowano, i jedynymi wymogami są Aby wkleić klucz dostępu i dokumentów JSON w żądaniu. 

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsem API analizy tekstu**. 

Konieczne jest posiadanie [punktu końcowego i klucza dostępu](text-analytics-how-to-access-key.md) , zostanie wygenerowany po utworzeniu konta usług Cognitive Services. 

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definicja schematu JSON

Dane wejściowe muszą być JSON w nieprzetworzony tekst bez struktury. XML nie jest obsługiwane. Schemat jest proste, składający się z elementy opisane w poniższej liście. 

Obecnie można przesyłać tych samych dokumentów dla wszystkich operacji analizy tekstu: tonacji, kluczowych fraz, wykrywanie języka i identyfikator jednostki. (Schemat jest mogą się różnić dla każdej analizy w przyszłości).

| Element | Prawidłowe wartości | Wymagana? | Sposób użycia |
|---------|--------------|-----------|-------|
|`id` |Typ danych jest ciągiem, ale w praktyce identyfikatory dokumentów mają tendencję do być liczbami całkowitymi. | Wymagane | System używa identyfikatorów zapewnienie struktury danych wyjściowych. Wyniki tonacji kodów języków i wyrażenia kluczowe są generowane dla każdego Identyfikatora w żądaniu.|
|`text` | Bez określonej struktury nieprzetworzony tekst, maksymalnie 5000 znaków. | Wymagane | Do wykrywania języka tekst może być wyrażona w dowolnym języku. Analiza tonacji, wyodrębnianie kluczowych fraz i identyfikacji jednostki, tekst musi znajdować na [obsługiwanym językiem](../text-analytics-supported-languages.md). |
|`language` | 2-znakowy [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) kod [obsługiwanym językiem](../text-analytics-supported-languages.md) | Różna | Wymagane do analizy tonacji, wyodrębnianie kluczowych fraz i łączenie podmiotów; Opcjonalnie na potrzeby wykrywania języka. Nie ma żadnych błędów, jeśli go wykluczyć, ale analiza jest obniżony bez niego. Kod języka powinna odpowiadać `text` należy podać. |

Aby uzyskać więcej informacji na temat limitów, zobacz [Text Analytics — Przegląd > limity danych](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Konfiguruj zapytanie w narzędziu Postman

Usługa akceptuje żądania w rozmiarze do 1 MB. Jeśli używasz narzędzia Postman (lub innego narzędzia test internetowy interfejs API), konfigurowania punktu końcowego, aby uwzględnić zasób, którego chcesz użyć, a następnie podaj klucz dostępu w nagłówku żądania. Każda operacja wymaga dołączenia odpowiedniego zasobu do punktu końcowego. 

1. W narzędziu Postman:

   + Wybierz **wpis** jako typ żądania.
   + Wklej w punkcie końcowym, skopiowane ze strony portalu.
   + Dołącz zasób.

  Punktów końcowych zasobów są w następujący sposób (regionu mogą się różnić):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

2. Ustaw trzy nagłówki żądania:

   + `Ocp-Apim-Subscription-Key`: klucz dostępu uzyskany z witryny Azure portal.
   + `Content-Type`: application/json.
   + `Accept`: application/json.

  Twoje żądanie powinno wyglądać podobnie jak poniższy zrzut ekranu, zakładając, że **/keyPhrases** zasobów.

   ![Zrzut ekranu z punktu końcowego i nagłówków żądania](../media/postman-request-keyphrase-1.png)

4. Kliknij przycisk **treści** i wybierz polecenie **pierwotne** dla formatu.

   ![Żądanie zrzut ekranu z ustawieniami treści](../media/postman-request-body-raw.png)

5. Wklej wybrane dokumenty JSON w formacie, który nadaje się do zamierzonego analizy. Aby uzyskać więcej informacji na temat konkretnej analizy zobacz poniższe tematy:

  + [Wykrywanie języka](text-analytics-how-to-language-detection.md)  
  + [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md)  
  + [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md)  
  + [Rozpoznawanie jednostek (wersja zapoznawcza)](text-analytics-how-to-entity-linking.md)  


6. Kliknij przycisk **wysyłania** można przesłać żądania. Możesz przesłać do 100 żądań na minutę. 

  W narzędziu Postman odpowiedź zostanie wyświetlona w oknie dalej w dół, jako pojedynczy dokument JSON, za pomocą elementu dla każdego Identyfikatora dokumentu, podany w żądaniu.

## <a name="see-also"></a>Zobacz także 

 [Przegląd analizy tekstu](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wykryj język](text-analytics-how-to-language-detection.md)
