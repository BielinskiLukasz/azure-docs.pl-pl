---
title: Wyświetlanie działań dostawcy usług
description: Klienci mogą wyświetlać zarejestrowane działanie, aby zobaczyć akcje wykonywane przez dostawców usług za pomocą funkcji zarządzania zasobami delegowanymi przez platformę Azure.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 31a9e7ff80623cc59b0a2db5951dff95d3088b05
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749180"
---
# <a name="view-service-provider-activity"></a>Wyświetlanie działań dostawcy usług

Klienci, którzy mają delegowane subskrypcje do zarządzania zasobami delegowanymi przez platformę Azure, mogą [wyświetlać dane dziennika aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) , aby zobaczyć wszystkie wykonane akcje. Zapewnia to klientom pełny wgląd w operacje wykonywane przez dostawców usług za pomocą delegowania zasobów platformy Azure, a także tych wykonywanych przez użytkowników w ramach dzierżawy usługi Azure Active Directory (Azure AD) klienta.

## <a name="view-activity-log-data"></a>Wyświetl dane dziennika aktywności

[Dziennik aktywności można wyświetlić](../../azure-monitor/platform/activity-log-view.md) z menu **Monitoruj** w Azure Portal. Aby ograniczyć wyniki do określonej subskrypcji, można użyć filtrów do wybrania określonej subskrypcji. Możesz również [wyświetlać i pobierać zdarzenia dziennika aktywności](../../azure-monitor/platform/activity-log-view.md) .

> [!NOTE]
> Użytkownicy w dzierżawie dostawcy usług mogą wyświetlać wyniki dziennika aktywności dla delegowanej subskrypcji w dzierżawie klienta, jeśli otrzymali rolę [czytelnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika), gdy subskrypcja została dołączona do funkcji zarządzania zasobami delegowanymi przez platformę Azure.

W dzienniku aktywności zobaczysz nazwę operacji i jej stan wraz z datą i godziną wykonania. **Zdarzenie zainicjowane przez** kolumnę pokazuje, który użytkownik wykonał operację, niezależnie od tego, czy był on użytkownikiem w dzierżawie dostawcy usług działającym przez delegowane zarządzanie zasobami platformy Azure, czy użytkownikiem w dzierżawie klienta. Należy zauważyć, że nazwa użytkownika jest wyświetlana zamiast dzierżawy lub roli, do której użytkownik został przypisany do tej subskrypcji.

Zarejestrowane działanie jest dostępne w Azure Portal przez ostatnie 90 dni. Aby dowiedzieć się, jak przechowywać te dane przez dłużej niż 90 dni, zobacz [zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym log Analytics w Azure monitor](../../azure-monitor/platform/activity-log-collect.md)

## <a name="set-alerts-for-critical-operations"></a>Ustawianie alertów dla operacji krytycznych

Aby zachować świadomość krytycznych operacji wykonywanych przez dostawców usług (lub użytkowników w Twojej dzierżawie), zalecamy tworzenie [alertów dziennika aktywności](../../azure-monitor/platform/activity-log-alerts.md). Na przykład możesz chcieć śledzić wszystkie akcje administracyjne dla subskrypcji lub otrzymywać powiadomienia o usunięciu dowolnej maszyny wirtualnej w danej grupie zasobów. Podczas tworzenia alertów będą one obejmować akcje podejmowane przez użytkowników w dzierżawie klienta, a także w innych dzierżawach.

Aby uzyskać więcej informacji, zobacz [tworzenie alertów dziennika aktywności i zarządzanie nimi](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Tworzenie zapytań dziennika

Możesz tworzyć zapytania, aby analizować zarejestrowane działanie lub skupić się na określonych elementach. Na przykład, być może Inspekcja wymaga, aby zgłosić wszystkie akcje na poziomie administracyjnym wykonywane w ramach subskrypcji. Można utworzyć zapytanie, aby odfiltrować tylko te akcje i sortować wyniki według użytkownika, daty lub innej wartości.

Aby uzyskać więcej informacji, zobacz [Omówienie zapytań dzienników w Azure monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure monitor](../../azure-monitor/index.yml).
- Dowiedz się, jak [wyświetlać oferty dostawców usług i zarządzać nimi](view-manage-service-providers.md) w Azure Portal.