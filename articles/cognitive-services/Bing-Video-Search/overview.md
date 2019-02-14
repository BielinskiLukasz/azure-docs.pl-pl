---
title: Co to jest interfejs API wyszukiwania wideo Bing?
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak wyszukiwać wideo w Internecie przy użyciu interfejsu API wyszukiwania wideo Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: a7fbd9eb70c50f3bbd154726bdaa807c2bc41818
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863962"
---
# <a name="what-is-the-bing-video-search-api"></a>Co to jest interfejs API wyszukiwania wideo Bing?

Interfejs API wyszukiwania wideo Bing ułatwia dodawanie funkcji wyszukiwania wideo w usługach i aplikacjach. Wysyłając zapytania wyszukiwania użytkownika za pomocą interfejsu API, możesz pobierać i wyświetlać odpowiednie wideo wysokiej jakości podobne do [wideo Bing](https://www.bing.com/video). Użyj tego interfejsu API w przypadku wyników wyszukiwania, które zawierają tylko wideo. [Interfejs API wyszukiwania Bing](../bing-web-search/search-the-web.md) może zwrócić inne rodzaje zawartości internetowej, w tym strony internetowe, wideo, wiadomości i obrazy.

## <a name="bing-video-search-api-features"></a>Funkcje interfejsu API wyszukiwania wideo Bing

| Cecha                                                                                                                                                                                 | Opis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerowanie terminów wyszukiwania w czasie rzeczywistym](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Ulepsz działanie aplikacji przy użyciu [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md), aby wyświetlać sugerowane terminy wyszukiwania w miarę ich wpisywania. |
| [Filtrowanie i ograniczanie wyników dotyczących wideo](concepts/get-videos.md#filtering-videos)                      | Zwracane wideo możesz filtrować, edytując parametry zapytania.                                                                                                       |
| [Przycinanie, zmienianie rozmiaru i wyświetlania miniatur](resize-and-crop-thumbnails.md)                                                | Edytuj i wyświetlaj podgląd miniatur wideo zwróconych przez interfejs API wyszukiwania wideo Bing.                                                                                      |
| [Pobieranie popularnych wideo](trending-videos.md) | Wyszukaj popularne wideo z całego świata.                                                                                                          |
| [Pobieranie szczegółowych informacji o wideo](video-insights.md) | Dostosuj wyszukiwanie popularnych obrazów z całego świata.                                                                                                          |

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania wideo Bing jest usługą internetową zgodną z wzorcem REST, łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON. Możesz użyć tej usługi za pomocą [interfejsu API REST](csharp.md) lub [zestawu SDK](video-search-sdk-quickstart.md).

1. Utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z dostępem do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz bezpłatnie utworzyć [konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Wyślij żądanie do interfejsu API przy użyciu prawidłowego zapytania wyszukiwania.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.


## <a name="next-steps"></a>Następne kroki

W [interaktywnej demonstracji](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) dotyczącej interfejsu API wyszukiwania wideo Bing przedstawiono, jak dostosować zapytanie wyszukiwania i wyszukać wideo w Internecie.

Gdy wszystko jest gotowe do wywołania interfejsu API, utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Jeśli nie masz subskrypcji platformy Azure, możesz bezpłatnie utworzyć [konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Skorzystaj z przewodnika [Szybki start](csharp.md), aby szybko rozpocząć pracę z pierwszym żądaniem interfejsu API.

## <a name="see-also"></a>Zobacz też

* Strona z dokumentacją [interfejsu API wyszukiwania wideo Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach wyników wyszukiwania.

* [Wymagania dotyczące użycia i wyświetlania Bing](./useanddisplayrequirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.