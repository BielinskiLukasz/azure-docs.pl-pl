---
title: Kategorie obsługiwane w przypadku rozpoznawania jednostek nazwanych
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o obsługiwanych kategoriach jednostek w interfejs API analizy tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 77b75b1134bbc8366478b1f9f4d14e86e9684f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709540"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Obsługiwane kategorie jednostek w interfejs API analizy tekstu v3

Skorzystaj z tego artykułu, aby znaleźć kategorie jednostek, które mogą być zwracane przez [nazwanego rozpoznawania jednostek](how-tos/text-analytics-how-to-entity-linking.md) (ner). Dostępna jest również wersja zapoznawcza NER v 3.1, która obejmuje możliwość wykrywania osobistych ( `PII` ) i informacji o kondycji ( `PHI` ). Ponadto kliknij kartę **kondycja** , aby wyświetlić listę obsługiwanych kategorii w analiza tekstu na potrzeby kondycji.

## <a name="entity-categories"></a>Kategorie jednostek

#### <a name="general"></a>[Ogólne](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[DANE](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Służba zdrowia](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Następne kroki

* [Jak używać rozpoznawania jednostek nazwanych w analiza tekstu](how-tos/text-analytics-how-to-entity-linking.md)
