---
title: Uprawnienia w Azure Advisor
description: Uprawnienia usługi Advisor oraz sposób, w jaki mogą blokować możliwość konfigurowania subskrypcji lub odroczenia lub odrzucania zaleceń.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422313"
---
# <a name="permissions-in-azure-advisor"></a>Uprawnienia w Azure Advisor

Azure Advisor zapewnia zalecenia na podstawie użycia i konfiguracji zasobów i subskrypcji platformy Azure. W usłudze Advisor są używane [wbudowane role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) udostępniane przez [Access Control oparte na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) w celu zarządzania dostępem do zaleceń i funkcji usługi Advisor. 

## <a name="roles-and-their-access"></a>Role i ich dostęp

W poniższej tabeli zdefiniowano role i dostęp do nich w usłudze Advisor:

| **Rola** | **Wyświetl rekomendacje** | **Edytuj reguły** | **Edytuj konfigurację subskrypcji** | **Edytuj konfigurację grupy zasobów**| **Odrzuć i odłóż zalecenia**|
|---|:---:|:---:|:---:|:---:|:---:|
|Właściciel subskrypcji|**X**|**X**|**X**|**X**|**X**|
|Współautor subskrypcji|**X**|**X**|**X**|**X**|**X**|
|Czytelnik subskrypcji|**X**|--|--|--|--|
|Właściciel grupy zasobów|**X**|--|--|**X**|**X**|
|Współautor grupy zasobów|**X**|--|--|**X**|**X**|
|Czytelnik grupy zasobów|**X**|--|--|--|--|
|Właściciel zasobu|**X**|--|--|--|**X**|
|Współautor zasobów|**X**|--|--|--|**X**|
|Czytnik zasobów|**X**|--|--|--|--|

> [!NOTE]
> Dostęp do zaleceń dotyczących widoku zależy od dostępu do zasobu, którego dotyczy problem.

## <a name="permissions-and-unavailable-actions"></a>Uprawnienia i niedostępne akcje

Brak prawidłowych uprawnień może blokować możliwość wykonywania działań w usłudze Advisor. Poniżej przedstawiono niektóre typowe problemy.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nie można skonfigurować subskrypcji lub grup zasobów

Podczas próby skonfigurowania subskrypcji lub grup zasobów w usłudze Advisor można zobaczyć, że opcja dołączania lub wykluczania jest wyłączona. Ten stan oznacza, że nie masz wystarczającego poziomu uprawnień dla tej grupy zasobów lub subskrypcji. Aby rozwiązać ten problem, Dowiedz się, jak [udzielić użytkownikowi dostępu](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nie można odłożyć lub odrzucić zalecenia

Jeśli wystąpi błąd podczas próby odroczenia lub odrzucenia zalecenia, możesz nie mieć wystarczających uprawnień. Upewnij się, że masz co najmniej dostęp współautora do zasobu, którego to dotyczy, lub którego nie brakuje. Aby rozwiązać ten problem, Dowiedz się, jak [udzielić użytkownikowi dostępu](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera omówienie sposobu używania RBAC przez klasyfikatora do kontrolowania uprawnień użytkowników i rozwiązywania typowych problemów. Aby dowiedzieć się więcej na temat usługi Advisor, zobacz:

- [Co to jest usługa Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Wprowadzenie do Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
