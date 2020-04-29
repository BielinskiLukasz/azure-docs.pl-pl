---
title: Punkty końcowe funkcji wyszukiwania wiadomości Bing
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera podsumowanie punktów końcowych interfejsu API wyszukiwania wiadomości. wiadomości, najpopularniejsze wiadomości i trendy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74111487"
---
# <a name="bing-news-search-api-endpoints"></a>interfejs API wyszukiwania wiadomości Bing punkty końcowe

**Interfejs API wyszukiwanie wiadomości** zwraca artykuły z wiadomościami, strony sieci Web, obrazy, filmy wideo i [jednostki](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki zawierają informacje podsumowujące dotyczące osoby, miejsca lub tematu.

## <a name="endpoints"></a>Punkty końcowe

Aby uzyskać wyniki wyszukiwania wiadomości za pomocą interfejs API wyszukiwania wiadomości Bing, Wyślij `GET` żądanie do jednego z następujących punktów końcowych. Nagłówki i parametry URL definiują dalsze specyfikacje.

### <a name="news-items-by-search-query"></a>Elementy wiadomości według zapytania wyszukiwania

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Zwraca elementy wiadomości na podstawie zapytania wyszukiwania. Jeśli zapytanie wyszukiwania jest puste, interfejs API zwróci najpopularniejsze artykuły z różnych kategorii. Wyślij zapytanie według adresu URL, aby zakodować termin wyszukiwania i dołączyć je`q=""` do parametru. Aby uzyskać dostęp, zobacz temat [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Najważniejsze elementy wiadomości według kategorii

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Zwraca najważniejsze elementy wiadomości według kategorii. Możesz zażądać najważniejszych artykułów firmy, sportu lub rozrywki, korzystając `category=business`z, `category=sports`lub. `category=entertainment` `category` Parametru można używać tylko z `/news` adresem URL. Istnieją pewne formalne wymagania dotyczące określania kategorii; Zapoznaj `category` się z dokumentacją dotyczącą [parametrów zapytania](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) . Wyślij zapytanie według adresu URL, aby zakodować termin wyszukiwania i dołączyć je`q=""` do parametru. Aby uzyskać dostęp, zobacz temat [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Informacje o trendach tematów 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Zwraca tematy dotyczące nowości w sieciach społecznościowych. Gdy ta `/trendingtopics` opcja jest uwzględniona, wyszukiwanie w usłudze Bing ignoruje kilka innych `freshness` parametrów `?q=""`, takich jak i. Aby uzyskać dostęp, zobacz temat [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach itp., zobacz informacje o [interfejsie API wyszukiwania wiadomości Bing wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) .

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez poszczególne punkty końcowe, zobacz strony referencyjne dla każdego typu.
Przykłady podstawowych żądań za pomocą interfejsu API wyszukiwania wiadomości można znaleźć w temacie [Wyszukiwanie wiadomości Bing Quick-Start](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
