---
title: Jak są tworzone zalecenia dotyczące rezerwacji platformy Azure
description: Dowiedz się, jak są tworzone zalecenia dotyczące rezerwacji platformy Azure dla maszyn wirtualnych.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991052"
---
# <a name="how-reservation-recommendations-are-created"></a>Jak są tworzone zalecenia dotyczące rezerwacji
Zalecenia dotyczące zakupu wystąpień zarezerwowanych na platformie Azure są udostępniane za pomocą [interfejsu API zaleceń dotyczących rezerwacji](/rest/api/consumption/reservationrecommendations) użycia platformy Azure, usługi [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) i środowiska zakupów rezerwacji w witrynie Azure Portal.

Poniższe kroki definiują sposób obliczania zaleceń: 
1. Aparat zaleceń oblicza godzinowe użycie zasobów w danym zakresie w ciągu ostatnich siedmiu, 30 i 60 dni.
2. W oparciu o dane użycia aparat symuluje koszty z rezerwacjami i bez nich.
3. Koszty są symulowane dla różnych ilości i zalecana jest ilość, która maksymalizuje oszczędności.
4. Jeśli Twoje zasoby są regularnie zamykane, symulacja nie znajdzie żadnych oszczędności i nie poda żadnych zaleceń dotyczących zakupu.

## <a name="recommendations-in-azure-advisor"></a>Zalecenia w usłudze Azure Advisor
Zalecenia dotyczące zakupu rezerwacji dla maszyn wirtualnych są dostępne w usłudze Azure Advisor. Należy pamiętać o następujących kwestiach: 
- Usługa Advisor zapewnia zalecenia wyłącznie w zakresie jednej subskrypcji.
- Zalecenia obliczane na podstawie ostatnich 30 dni są dostępne w usłudze Advisor.
- W przypadku zakupienia rezerwacji dotyczącej zakresu współdzielonego może minąć do 30 dni, zanim zalecenia zakupu rezerwacji usługi Advisor przestaną być wyświetlane.

## <a name="other-expected-api-behavior"></a>Inne oczekiwane zachowanie interfejsu API
- W przypadku korzystania z okresu ostatnich siedmiu dni zalecenia mogą nie zostać przedstawione, gdy maszyny wirtualne były zamknięte przez więcej niż jeden dzień.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, [jak rabat na rezerwację platformy Azure jest stosowany do maszyn wirtualnych](../manage/understand-vm-reservation-charges.md).
