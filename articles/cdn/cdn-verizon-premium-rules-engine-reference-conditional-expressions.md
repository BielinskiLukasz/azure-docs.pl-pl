---
title: Wyrażenia warunkowe dla aparatu reguł Azure CDN Verizon Premium
description: Dokumentacja referencyjna Azure CDN z aparatu reguł Verizon Premium dopasowuje warunki i funkcje.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082968"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN z wyrażeń warunkowych aparatu reguł Verizon Premium

W tym artykule przedstawiono szczegółowe opisy wyrażeń warunkowych dla [aparatu reguł](cdn-verizon-premium-rules-engine.md)usługi Azure Content Delivery Network (CDN).

Pierwszą częścią reguły jest wyrażenie warunkowe.

Wyrażenie warunkowe | Opis
-----------------------|-------------
PRZYPADKU | Wyrażenie IF jest zawsze częścią pierwszej instrukcji w regule. Podobnie jak wszystkie inne wyrażenia warunkowe, ta instrukcja IF musi być skojarzona z dopasowaniem. Jeśli nie są zdefiniowane żadne dodatkowe wyrażenia warunkowe, to dopasowanie określa kryterium, które musi zostać spełnione, zanim zestaw funkcji może zostać zastosowany do żądania.
I JEŚLI | Wyrażenie AND IF może być dodane tylko po następujących typach wyrażeń warunkowych: Jeśli i. Wskazuje, że istnieje inny warunek, który musi być spełniony dla początkowej instrukcji IF.
ELSE IF| Wyrażenie ELSE IF określa alternatywny warunek, który musi zostać spełniony przed zestaw funkcji specyficznych dla tego ELSE, jeśli instrukcja zostanie wykonana. Obecność instrukcji ELSE IF wskazuje koniec poprzedniej instrukcji. Jedyne wyrażenie warunkowe, które może być umieszczone po instrukcji ELSE IF, jest kolejną inną instrukcją IF. Oznacza to, że Instrukcja ELSE IF może być używana tylko w celu określenia pojedynczego warunku, który musi zostać spełniony.

**Przykład**: ![warunek dopasowania sieci CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Kolejna reguła może zastąpić akcje określone przez poprzednią regułę.
   > Przykład: reguła "catch-all" zabezpiecza wszystkie żądania za pośrednictwem uwierzytelniania opartego na tokenach. Inną regułę można utworzyć bezpośrednio poniżej, aby wykonać wyjątek dla niektórych typów żądań.

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)