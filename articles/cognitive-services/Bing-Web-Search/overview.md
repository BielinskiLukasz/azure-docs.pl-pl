---
title: Co to jest interfejs API wyszukiwania w sieci Web Bing?
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania w sieci Web Bing to usługa RESTful, która zapewnia błyskawiczne odpowiedzi na zapytania wyszukiwania w sieci Web. Skonfiguruj wyniki w celu uwzględnienia stron sieci Web, obrazów, filmów wideo, wiadomości i innych. Wyniki są podawane w formacie JSON oraz oparte na istotności wyszukiwania i subskrypcjach wyszukiwania w Internecie Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 5ea98a4ef759937c5962dc86f0384051b8fa0bb3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80477467"
---
# <a name="what-is-the-bing-web-search-api"></a>Co to jest interfejs API wyszukiwania w sieci Web Bing?

Interfejs API wyszukiwania w Internecie Bing jest usługą RESTful, która zapewnia błyskawiczne odpowiedzi na zapytania użytkowników. Wyniki wyszukiwania można łatwo konfigurować tak, aby obejmowały strony internetowe, obrazy, wideo, wiadomości, tłumaczenia i więcej. Wyszukiwanie w sieci Web Bing zapewnia wyniki w formacie JSON na podstawie znaczenia wyszukiwania i subskrypcji wyszukiwanie w sieci Web Bing.

Ten interfejs API jest optymalny dla aplikacji, które muszą mieć dostęp do całej zawartości odpowiedniej dla zapytania wyszukiwania użytkownika. Jeśli tworzysz aplikację, która wymaga określonego typu wyniku, rozważ użycie [interfejsu API wyszukiwania obrazów Bing](../Bing-Image-Search/overview.md), [interfejsu API wyszukiwania wideo Bing](../Bing-Video-Search/search-the-web.md) lub [interfejsu API wyszukiwania wiadomości Bing](../Bing-News-Search/search-the-web.md). Aby uzyskać pełny wykaz interfejsów API wyszukiwania Bing, zobacz [Interfejsy API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services).

Chcesz zobaczyć, jak to działa? Wypróbuj [Pokaz interfejsu API wyszukiwania w Internecie Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Funkcje  

Wyszukiwanie w sieci Web Bing nie tylko zapewnia dostęp do natychmiastowych odpowiedzi. Udostępnia także dodatkowe funkcje i funkcje, które umożliwiają dostosowywanie wyników wyszukiwania dla użytkowników.

| Funkcja | Opis |
|---------|-------------|
| [Sugerowanie terminów wyszukiwania w czasie rzeczywistym](../bing-autosuggest/get-suggested-search-terms.md) | Ulepsz działanie aplikacji przy użyciu interfejsu API automatycznego sugerowania Bing, aby wyświetlać sugerowane terminy wyszukiwania w miarę ich wpisywania. |
| [Filtrowanie i ograniczanie wyników według typu zawartości](filter-answers.md) | Dostosuj i zawęź wyniki wyszukiwania za pomocą filtrów i parametrów zapytania dla stron internetowych, obrazów, wideo, bezpiecznego wyszukiwania itd. |
| [Wyróżnianie trafień dla znaków Unicode](hit-highlighting.md) | Identyfikuj i usuwaj niepożądane znaki Unicode z wyników wyszukiwania przed wyświetleniem ich dla użytkowników za pomocą wyróżniania trafień. |
| [Lokalizowanie wyników wyszukiwania według kraju, regionu i/lub rynku](supported-countries-markets.md) | Wyszukiwanie w Internecie Bing obsługuje ponad trzydzieści krajów lub regionów. Użyj tej funkcji, aby zawęzić wyniki wyszukiwania dla określonego kraju/regionu lub rynku. |
| [Analizowanie metryk wyszukiwania za pomocą statystyk Bing](bing-web-stats.md) | Statystyka Bing jest płatną subskrypcją, która zapewnia analizę woluminu wywołań, najważniejszych ciągów zapytań, rozmieszczenia geograficznego itd. |

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania w Internecie Bing jest łatwo wywołać z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować odpowiedzi w formacie JSON. Usługa jest dostępna przy użyciu [interfejsu API REST](quickstarts/python.md) lub [bibliotek klienckich wyszukiwanie w sieci Web Bing](./quickstarts/client-libraries.md).

1. [Utwórz zasób platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) dla interfejsy API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).  
2. Wyślij [żądanie do interfejs API wyszukiwania w sieci Web Bing](quickstarts/python.md).
3. Przeanalizuj odpowiedź w formacie JSON.

## <a name="next-steps"></a>Następne kroki

* Użyj naszego artykułu [Szybki start dla języka Python](./quickstarts/client-libraries.md?pivots=programming-language-python), aby wykonać swoje pierwsze wywołanie interfejsu API wyszukiwania w Internecie Bing.  
* [Utwórz jednostronicową aplikację sieci Web](tutorial-bing-web-search-single-page-app.md).
* Przejrzyj dokumentację [interfejsu API wyszukiwania w Internecie w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Dowiedz się więcej na temat [wymagań dotyczących użycia i wyświetlania](UseAndDisplayRequirements.md) dla wyszukiwania w Internecie Bing.  
