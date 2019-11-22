---
title: Przykładowe scenariusze użytkowników dla interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono typowe scenariusze integracji interfejs API analizy tekstu z usługami i procesami.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: f11d23207a21af5c73831d0edd5ffc9eaa59c119
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280671"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Przykładowe scenariusze użytkowników dla interfejs API analizy tekstu

Interfejs API analizy tekstu jest usługą opartą na chmurze, która zapewnia zaawansowane przetwarzanie języka naturalnego dla tekstu. W tym artykule opisano przykładowe przypadki użycia związane z integracją interfejsu API z rozwiązaniami i procesami biznesowymi. 

## <a name="analyze-survey-results"></a>Analizowanie wyników ankiety

Uzyskiwanie szczegółowych informacji z wyników ankiety klienta i pracownika przez przetwarzanie nieprzetworzonych odpowiedzi tekstowych przy użyciu analiza tonacji. Agreguj wyniki analizy, zaobserwuj się i Zwiększaj zaangażowanie.

![Obraz opisujący sposób wykonywania analizy tonacji na potrzeby badań klientów i pracowników.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analizowanie zarejestrowanych przychodzących wywołań klienta

Wyodrębnij szczegółowe informacje z wywołań usług Customer Services przy użyciu zamiana tekstu na mowę, analiza tonacji i wyodrębnianie kluczowych fraz. Wyświetl wyniki w Power BI pulpicie nawigacyjnym lub w portalu, aby lepiej zrozumieć klientów, wyróżnić trendy usług klienta i zwiększyć zaangażowanie klientów. Wysyłaj żądania interfejsu API jako partię do raportowania lub w czasie rzeczywistym na potrzeby interwencji. Zobacz [przykładowy kod](https://github.com/rlagh2/callcenteranalytics).

![Obraz przedstawiający sposób automatyzowania uzyskiwania szczegółowych informacji z wywołań obsługi klienta przy użyciu analizy tonacji](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Przetwarzaj i Klasyfikuj zdarzenia pomocy technicznej

Użyj wyodrębnianie kluczowych fraz i rozpoznawania jednostek, aby przetwarzać żądania obsługi przesłane w formacie tekstowym bez struktury. Użyj wyodrębnionych fraz i jednostek do kategoryzacji żądań dotyczących planowania zasobów i analizy trendów.

![Obraz opisujący sposób użycia wyodrębniania kluczowych fraz i rozpoznawania jednostek do kategoryzowania raportów i trendów zdarzeń](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitoruj źródła danych mediów społecznościowych produktu

Monitoruj Opinie użytkowników o produkcie na stronie serwisu Twitter lub w serwisie Facebook. Za pomocą danych można analizować tonacji klientów w kierunku nowych produktów, wyodrębniać kluczowe frazy dotyczące funkcji i żądań funkcji lub rozwiązywać reklamacje klientów w miarę ich występowania. Zapoznaj się z przykładowym [szablonem Microsoft Flow](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Obraz opisujący sposób monitorowania produktu i opinii firmy na nośnikach społecznościowych przy użyciu wyodrębniania kluczowych fraz](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API analizy tekstu?](overview.md)
* [Wyślij żądanie do interfejs API analizy tekstu przy użyciu biblioteki klienckiej](quickstarts/text-analytics-sdk.md)
