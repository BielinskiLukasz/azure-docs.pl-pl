---
title: Załóż konto interfejsu API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Instrukcje dotyczące tworzenia konta na potrzeby analizy tekstu i działania w ramach limitów.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: a369d6028cc2957113de01dab0371ad5305a0c68
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605537"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Jak zarejestrować się do interfejsu API analizy tekstu

Zasoby analizy tekstu są dostępne w chmurze 24 godziny na dobę przez 7 dni w tygodniu. Aby można było przekazać zawartość na potrzeby analizy, musisz utworzyć konto w celu uzyskania klucza dostępu. Każde wywołanie interfejsu API wymaga klucza dostępu dla żądania.

+ Rozpocznij od utworzenia subskrypcji platformy Azure. Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/), aby eksperymentować bez opłat.

+ Utwórz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), wybierając **interfejs API analizy tekstu**. Klucz jest generowany podczas tworzenia konta.

Na potrzeby analizy tekstu przygotowano warstwę Bezpłatna umożliwiającą badanie i ocenę oraz płatne warstwy dla obciążeń produkcyjnych. W każdej subskrypcji możesz mieć wiele kont: jedno bezpłatne, inne płatne i tak dalej. Jeśli Twój wolumin żądań zwiększy się, możesz przełączyć się do warstwy oferującej więcej transakcji.

W przypadku usług dostępnych w wersji zapoznawczej lub oferowanych w warstwie bezpłatnej nie jest zawierana umowa dotycząca poziomu usług. Aby uzyskać więcej informacji, zobacz [Usługi poznawcze — umowa SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

## <a name="how-to-change-tiers"></a>Jak zmienić warstwy

Rozpocznij od warstwy Bezpłatna, a następnie przejdź do warstwy płatnej dla obciążeń produkcyjnych. Standardowe opłaty są naliczane w zależności od różnych poziomów. Po przełączeniu warstw można nadal korzystać z tego samego punktu końcowego i kluczy dostępu.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) i [znajdź swoją usługę](text-analytics-how-to-access-key.md).

2. Kliknij pozycję **Warstwa cenowa**.

   ![Polecenie dotyczące warstwy cenowej w menu nawigacji po lewej stronie](../media/portal-pricing-tier.png)

3. Wybierz warstwę i kliknij przycisk **Wybierz**.  Nowe limity zaczynają obowiązywać natychmiast po przetworzeniu zaznaczenia. 

   ![Kafelki i przycisk Wybierz na stronie wybierania warstwy](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>Sposób działania rozliczeń

Rozliczenia są dokonywane na podstawie liczby transakcji. Blok transakcji można kupić dla konkretnej warstwy w ramach miesięcznego cyklu rozliczeniowego, a następnie, jeśli zostanie przekroczony limit, za nadwyżkowe użycie naliczana jest niewielka opłata za każdą transakcję. Jeśli często przekraczasz maksymalny limit, rozważ przełączenie do wyższej warstwy.

Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>Jak definiuje się transakcję w przypadku interfejsu API analizy tekstu?
Każda adnotacja do dokumentu jest uznawana za transakcję. Wywołania wsadowego oceniania będą uwzględniane na podstawie liczby dokumentów, które mają zostać ocenione w ramach transakcji. Na przykład wysłanie 1000 dokumentów do analizy tonacji w jednym wywołaniu interfejsu API zostanie uznane za 1000 transakcji.

## <a name="see-also"></a>Zobacz także 

 [Przegląd analizy tekstu](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie klucza dostępu](text-analytics-how-to-access-key.md)
