---
title: Punkt końcowy interfejs API wyszukiwania jednostek Bing
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania jednostek Bing ma jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie zapytania. Te wyniki wyszukiwania są zwracane w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 1d9b7c79919569830834915fc609b849e717dce8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365843"
---
# <a name="bing-entity-search-api-endpoint"></a>interfejs API wyszukiwania jednostek Bing punkt końcowy

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).


Interfejs API wyszukiwania jednostek Bing ma jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie zapytania. Te wyniki wyszukiwania są zwracane w formacie JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Pobierz wyniki jednostki z punktu końcowego

Aby uzyskać wyniki jednostki przy użyciu **interfejsu API Bing** , Wyślij `GET` żądanie do poniższego punktu końcowego. Użyj [nagłówków](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) i [parametrów zapytania](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) , aby dostosować żądanie wyszukiwania. Żądania wyszukiwania można wysyłać przy użyciu `?q=` parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Czym jest interfejs API wyszukiwania jednostek Bing?](overview.md)

## <a name="see-also"></a>Zobacz też 

Aby uzyskać więcej informacji na temat nagłówków, parametrów, kodów rynkowych, obiektów odpowiedzi, błędów i innych, zobacz artykuł dotyczący [interfejs API wyszukiwania jednostek Bing wersji 7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .