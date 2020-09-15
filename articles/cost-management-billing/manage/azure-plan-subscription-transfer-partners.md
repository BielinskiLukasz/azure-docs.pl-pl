---
title: Przenoszenie subskrypcji platformy Azure między partnerami (wersja zapoznawcza)
description: W tym artykule zawarto informacje dotyczące przenoszenia własności rozliczeń subskrypcji platformy Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554206"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>Przenoszenie subskrypcji platformy Azure między partnerami (wersja zapoznawcza)

W tym artykule zawarto informacje dotyczące przenoszenia własności rozliczeń subskrypcji platformy Azure. Aby rozpocząć przenoszenie subskrypcji platformy Azure ujętej w planie platformy Azure pomiędzy partnerami firmy Microsoft, musisz skontaktować się z partnerem. Od partnera dostaniesz instrukcje dotyczące rozpoczęcia tego procesu. Po zakończeniu procesu przenoszenia własność rozliczeń subskrypcji zostanie zmieniona.

## <a name="user-access"></a>Dostęp użytkowników

Przejście nie wpływa na dostęp do istniejących użytkowników, grup lub jednostek usługi, które zostały przypisane przy użyciu kontroli dostępu na podstawie ról (RBAC) na platformie Azure. [Kontrola dostępu na podstawie ról na platformie Azure](../../role-based-access-control/overview.md) ułatwia zarządzanie osobami mającymi dostęp do zasobów platformy Azure, czynnościami, jakie mogą wykonywać, oraz obszarami, do których mają dostęp. Przeniesienie subskrypcji nie oznacza, że nowy partner otrzymuje dostęp RBAC do Twoich zasobów. Poprzedni partner zatrzymuje dostęp RBAC.

W związku z tym musisz usunąć dostęp RBAC starego partnera do platformy Azure i nadać dostęp nowemu partnerowi. Aby uzyskać więcej informacji na temat nadawania dostępu nowemu partnerowi, zobacz [Co to jest kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)?](../../role-based-access-control/overview.md) Aby uzyskać więcej informacji na temat usuwania dostępu RBAC poprzedniego partnera, zobacz [Usuwanie przypisania roli](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment).

Ponadto nowy partner nie otrzymuje automatycznie dostępu do subskrypcji w ramach funkcji [Administruj w imieniu (Admin on Behalf Of, AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO). Funkcja AOBO jest niezbędna, aby nowy partner mógł zarządzać subskrypcjami platformy Azure w Twoim imieniu. Aby uzyskać więcej informacji na temat nadawania nowemu partnerowi dostępu do funkcji AOBO, zobacz [Przenoszenie własności rozliczeń konta subskrypcji platformy Azure na inne konto](billing-subscription-transfer.md).

## <a name="stop-a-transfer"></a>Zatrzymywanie przenoszenia

Po otrzymaniu potwierdzenia, że żądanie przeniesienia zostało złożone, możesz użyć jednej z następujących opcji, **jeśli nie chcesz, aby przenoszenie było kontynuowane**.

- Jeśli żądanie nie zostało jeszcze zaakceptowane przez Twojego bieżącego partnera, możesz poprosić nowego partnera o anulowanie rozpoczętego żądania przeniesienia (gdy ma stan „oczekujące”).
- Poproś bieżącego partnera, aby nie reagował po otrzymaniu żądania przeniesienia. Przeniesienie nie może być kontynuowane bez zgody bieżącego partnera. Żądanie wygaśnie.
- Możesz _usunąć relację odsprzedawcy_ ze swoim nowym partnerem. To działanie usuwa możliwość przenoszenia subskrypcji. W rezultacie powoduje anulowania żądania.

Możesz również poszukać pomocy, a także zgłosić nieprawidłowe lub podejrzane działanie za pomocą jednej z opcji opisanych w witrynie [Microsoft Legal](https://www.microsoft.com/legal/). Opcję zgłoszenia problemu można znaleźć w sekcji Compliance & ethics (Zgodność i etyka).

## <a name="next-steps"></a>Następne kroki

- Aby nadać dostęp RBAC nowemu partnerowi, zobacz [Co to jest kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- Aby nadać nowemu partnerowi dostępu do funkcji AOBO, zobacz [Przenoszenie własności rozliczeń konta subskrypcji platformy Azure na inne konto](billing-subscription-transfer.md).