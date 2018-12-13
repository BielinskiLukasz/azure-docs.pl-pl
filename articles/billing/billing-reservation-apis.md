---
title: Interfejsy API dla rezerwacji usługi Azure automation | Dokumentacja firmy Microsoft
description: Informacje o interfejsów API platformy Azure, w której można programowo uzyskać informacje o rezerwacji.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: cwatson
ms.openlocfilehash: 01af1249039f8bfa1238cbbc12a77074e9347a39
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53264049"
---
# <a name="apis-for-azure-reservation-automation"></a>Interfejsy API dla rezerwacji usługi Azure automation

Użyj interfejsów API platformy Azure, programowo uzyskać informacji o organizacji dotyczących platformy Azure, usługi lub oprogramowania rezerwacji.

## <a name="find-reservation-plans-to-buy"></a>Znajdź rezerwacji plany zakupu

Za pomocą rezerwacji zalecenie dotyczące interfejsu API można pobrać zaleceń, na których planujesz zakup rezerwacji na podstawie Twojej organizacji użycia. Aby uzyskać więcej informacji, zobacz [Otrzymuj rekomendacje z rezerwacji](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Użycie zasobów można analizować za pomocą szczegółów użycia interfejsu API zużycie. Aby uzyskać więcej informacji, zobacz [szczegóły użycia — listy dla rozliczeń okres przez konta rozliczeniowego](/rest/api/consumption/usagedetails/listforbillingperiodbybillingaccount). Stale wykorzystane zasoby platformy Azure są zazwyczaj najlepszym Release candidate dla rezerwacji.

## <a name="buy-a-reservation"></a>Kupowanie rezerwacji

Nie można obecnie programowo kupić rezerwację. Aby kupić rezerwację, zobacz następujące artykuły:

Plany usługi:
- [Maszyna wirtualna](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Plany oprogramowania:
- [Oprogramowanie SUSE Linux](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Pobierz rezerwacji

Jeśli jesteś klientem Azure z umową Enterprise Agreement (EA klienta), możesz uzyskać rezerwacje organizacji zakupionych przy użyciu [transakcji pobrać wystąpienia zarezerwowanego opłaty za interfejs API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). W przypadku innych subskrypcji, Pobierz listę rezerwacje zakupiono i masz uprawnień do wyświetlania, korzystając z interfejsu API [zamówienie rezerwacji — lista](/rest/api/reserved-vm-instances/reservationorder/list). Domyślnie właściciel konta lub osobie, która zakupiona Rezerwacja ma uprawnienia do wyświetlania rezerwacji.

## <a name="see-reservation-usage"></a>Zobacz użycie rezerwacji

Jeśli jesteś klientem z umową EA, można programowo wyświetlać, jak są używane zastrzeżenia w Twojej organizacji. Aby uzyskać więcej informacji, zobacz [wystąpienia zarezerwowanego Pobierz użycia dla klientów korporacyjnych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). W przypadku innych subskrypcji za pomocą interfejsu API [rezerwacje podsumowania — lista przez rezerwacji kolejności i rezerwacji](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Jeśli okaże się, jest rezerwacje w organizacji używane w ramach:

- Upewnij się, że maszyn wirtualnych, które tworzy organizacji dopasowania rozmiaru maszyny Wirtualnej, która znajduje się na rezerwacji.
- Upewnij się, że elastyczność rozmiar wystąpienia jest włączony. Aby uzyskać więcej informacji, zobacz [Zarządzanie zastrzeżeniami — zmiana optymalizacji ustawienie zarezerwowanych wystąpień maszyn wirtualnych](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Zmień zakres rezerwacji współużytkowane, tak aby dotyczyła szerzej. Aby uzyskać więcej informacji, zobacz [Zarządzanie zastrzeżeniami — Zmienianie zakresu dla rezerwacji](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).
- Wymiana niewykorzystanej ilości. Aby uzyskać więcej informacji, zobacz [Zarządzanie zastrzeżenia - anulowania i wymiany](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Udzielić dostępu do rezerwacji

Pobierz listę wszystkie rezerwacje, czy użytkownik ma dostęp do za pomocą [API listy rezerwacji — operacja -](/rest/api/reserved-vm-instances/reservationorder/list). Pozwala uzyskiwać dostęp do rezerwacji programowo, zobacz jeden z następujących artykułów:

- [Zarządzanie dostępem przy użyciu RBAC i interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
- [Zarządzanie dostępem przy użyciu RBAC i programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Zarządzanie dostępem przy użyciu RBAC i wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dzielenie i scalanie rezerwacji

Po możesz kupić więcej niż jedno wystąpienie zasobów w ramach rezerwacji, możesz przypisać wystąpienia w ramach tej rezerwacji do różnych subskrypcji. Można zmienić zakres rezerwacji, tak aby dotyczyła wszystkich subskrypcji w tym samym kontekście rozliczeń. Jednak do celów zarządzania lub budżetowania koszt, możesz chcieć zachowanie zakresu jako "subskrypcja pojedyncza" i przypisz rezerwacji wystąpień do określonej subskrypcji. 

Aby podzielić rezerwacji, za pomocą interfejsu API [podziału rezerwacji -](/rest/api/reserved-vm-instances/reservation/split). Możesz również podzielić rezerwacji przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacje - podziału rezerwacji do dwóch rezerwacji](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Aby scalić dwóch rezerwacji do jedną rezerwację, za pomocą interfejsu API [rezerwacji - Merge](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Zmiana zakresu dla rezerwacji

Zakres rezerwacji może być pojedynczej subskrypcji, czy wszystkie subskrypcje w kontekstu rozliczeń. Jeśli zakres jest ustawiona na jedną subskrypcję, rezerwacja jest dopasowany do uruchomionego zasobów w wybranej subskrypcji. Jeśli ustawiono zakres udostępniony, Azure pasuje rezerwacji do zasobów, które są uruchamiane w przypadku wszystkich subskrypcji w ramach kontekstu rozliczeń. Kontekstu rozliczeń zależy od subskrypcji, którego użyto do zakupu rezerwacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie zastrzeżeniami — Zmienianie zakresu](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).

Aby programowo zmienić zakres, należy użyć interfejsu API [rezerwacji — aktualizacja](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Dowiedz się więcej

- [Co to są rezerwacji dla platformy Azure](billing-save-compute-costs-reservations.md)
- [Zrozumienie, jak jest stosowany rabat związany z rezerwacją maszyny Wirtualnej](billing-understand-vm-reservation-charges.md)
- [Zrozumienie, jak jest stosowany rabat plan oprogramowania SUSE Linux Enterprise](billing-understand-suse-reservation-charges.md)
- [Zrozumienie sposobu stosowania rabatów rezerwacji](billing-understand-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)
- [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)