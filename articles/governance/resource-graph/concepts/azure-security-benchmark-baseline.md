---
title: Punkt odniesienia zabezpieczeń usługi Azure Resource Graph dla usługi Azure Security test
description: Linia bazowa zabezpieczeń grafu zasobów platformy Azure zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a85fdf1e1e27b1ce5abb0c7890b717d48ba3bd3f
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230453"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Punkt odniesienia zabezpieczeń usługi Azure Resource Graph dla usługi Azure Security test

Ta linia bazowa zabezpieczeń stosuje wskazówki z [testu porównawczego zabezpieczeń platformy Azure](../../../security/benchmarks/overview.md) do grafu zasobów platformy Azure. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące wykresu zasobów platformy Azure. Wykluczenia **nie mają** zastosowania do grafu zasobów platformy Azure. Aby dowiedzieć się, jak usługa Azure Resource Graph jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania podstawy zabezpieczeń usługi azure Virtual Network](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).



## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure Resource Graph zapewnia dostęp do typów zasobów i właściwości na podstawie kontroli dostępu opartej na rolach (Azure RBAC). Regularnie przeprowadzaj inspekcję i przeglądanie dostępu udzielanego do podmiotów zabezpieczeń (użytkowników, grup i kont usług), aby upewnić się, że zapytania zwracają wyniki dla odpowiednich zasobów.

* [Uprawnienia w usłudze Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../../../active-directory/governance/access-reviews-overview.md)


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Kontrola dostępu do danych i zasobów przy użyciu kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure. Aby korzystać z grafu zasobów platformy Azure, musisz mieć również odpowiedni dostęp do zasobów, które chcesz zbadać. Ten dostęp powinien być objęty zakresem tylko do odczytu i może być przyznany tylko do wymaganego personelu.

* [Uprawnienia w usłudze Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Jak skonfigurować usługę Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich obsługiwanych zasobów w ramach subskrypcji, grup zarządzania i dzierżawców. Upewnij się, że masz odpowiednie uprawnienia w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

* [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../first-query-portal.md)

* [Opis kontroli RBAC platformy Azure](../../../role-based-access-control/overview.md)


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z potrzebami organizacji. Użyj grafu zasobów platformy Azure, aby wykonać zapytanie o zatwierdzone zasoby platformy Azure i historię zmian (wersja zapoznawcza) w celu przejrzenia migawek i zobacz, co zmieniono.

* [Tworzenie zapytań dotyczących zasobów platformy Azure za pomocą usługi Azure Resource Graph](../first-query-portal.md)

* [Uzyskiwanie zmian zasobów](../how-to/get-resource-changes.md)


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania zasobów w ramach subskrypcji, grup zarządzania i dzierżawców. Upewnij się, że wszystkie zasoby platformy Azure w środowisku są zatwierdzone.

* [Tworzenie zapytań dotyczących zasobów platformy Azure za pomocą usługi Azure Resource Graph](../first-query-portal.md)

* [Przykłady: początkowe zapytania dla wykresu zasobów platformy Azure](../samples/starter.md)


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../../../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../../../security/benchmarks/security-baselines-overview.md)
