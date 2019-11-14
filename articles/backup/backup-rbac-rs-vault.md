---
title: Zarządzanie kopiami zapasowymi przy użyciu Access Control opartej na rolach platformy Azure
description: Access Control oparte na rolach umożliwiają zarządzanie dostępem do operacji zarządzania kopiami zapasowymi w magazynie Recovery Services.
ms.reviewer: utraghuv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: dacurwin
ms.openlocfilehash: 8ba28829d3ee18b441227e537cb0a7ca97fb7638
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074053"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Zarządzanie Azure Backup punktów odzyskiwania przy użyciu Access Control opartych na rolach

Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Przy użyciu kontroli dostępu opartej na rolach można przeprowadzić segregowanie zadań w ramach zespołu i nadać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań.

> [!IMPORTANT]
> Role udostępniane przez Azure Backup są ograniczone do akcji, które mogą być wykonywane w Azure Portal lub za pośrednictwem interfejsu API REST lub poleceń cmdlet programu PowerShell dla magazynu Recovery Services lub interfejsu wiersza polecenia. Akcje wykonywane w interfejsie użytkownika klienta agenta usługi Azure Backup lub w interfejsie użytkownika programu System Center Data Protection Manager lub Azure Backup Server interfejsie użytkownika nie kontrolują tych ról.

Azure Backup udostępnia trzy wbudowane role do kontrolowania operacji zarządzania kopiami zapasowymi. Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

* [Współautor kopii zapasowych](../role-based-access-control/built-in-roles.md#backup-contributor) — ta rola ma wszystkie uprawnienia do tworzenia kopii zapasowych i zarządzania nimi z wyjątkiem usuwania magazynu Recovery Services i udzielania dostępu innym osobom. Wyobraź sobie tę rolę jako administrator zarządzania kopiami zapasowymi, który może wykonywać każdą operację zarządzania kopiami zapasowymi.
* [Operator kopii zapasowych](../role-based-access-control/built-in-roles.md#backup-operator) — ta rola ma uprawnienia do wszystkich elementów współautora, z wyjątkiem usuwania kopii zapasowych i zarządzania zasadami tworzenia kopii zapasowych. Ta rola jest równoznaczna z współautorem, z wyjątkiem sytuacji, w której nie może wykonać operacji niszczących, takich jak zatrzymanie wykonywania kopii zapasowej z usuwaniem danych lub usuwanie rejestracji zasobów lokalnych.
* [Czytnik kopii zapasowych](../role-based-access-control/built-in-roles.md#backup-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi. Wyobraź sobie, że ta rola jest osobą odpowiedzialną za monitorowanie.

Jeśli chcesz zdefiniować własne role, aby jeszcze bardziej kontrolować, zobacz jak [tworzyć role niestandardowe](../role-based-access-control/custom-roles.md) na platformie Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapowanie wbudowanych ról kopii zapasowej na akcje zarządzania kopiami zapasowymi

W poniższej tabeli przedstawiono akcje zarządzania kopiami zapasowymi i odpowiadające im minimalne role RBAC wymagane do wykonania tej operacji.

| Operacja zarządzania | Wymagana minimalna rola RBAC | Wymagany zakres |
| --- | --- | --- |
| Tworzenie magazynu usługi Recovery Services | Współautor kopii zapasowej | Grupa zasobów zawierająca magazyn |
| Włącz tworzenie kopii zapasowych maszyn wirtualnych platformy Azure | Operator kopii zapasowych | Grupa zasobów zawierająca magazyn |
| | Współautor maszyny wirtualnej | Zasób maszyny wirtualnej |
| Tworzenie kopii zapasowej maszyny wirtualnej na żądanie | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| Przywróć maszynę wirtualną | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor | Grupa zasobów, w której zostanie wdrożona maszyna wirtualna |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono |
| Przywróć dyski niezarządzane kopia zapasowa maszyny wirtualnej | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono |
| | Współautor konta magazynu | Zasób konta magazynu, w którym mają zostać przywrócone dyski |
| Przywróć dyski zarządzane z kopii zapasowej maszyny wirtualnej | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono |
| | Współautor konta magazynu | Konto magazynu tymczasowego wybrane w ramach przywracania do przechowywania danych z magazynu przed przekonwertowaniem ich na dyski zarządzane |
| | Współautor | Grupa zasobów, do której zostaną przywrócone dyski zarządzane |
| Przywróć pojedyncze pliki z kopii zapasowej maszyny wirtualnej | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono |
| Tworzenie zasad kopii zapasowych dla kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Zasób magazynu odzyskiwania |
| Modyfikowanie zasad tworzenia kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Zasób magazynu odzyskiwania |
| Usuwanie zasad tworzenia kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Zasób magazynu odzyskiwania |
| Zatrzymaj kopię zapasową (z zachowaniem Zachowaj dane lub Usuń dane) na kopii zapasowej maszyny wirtualnej | Współautor kopii zapasowej | Zasób magazynu odzyskiwania |
| Rejestrowanie lokalnego systemu Windows Server/Client/SCDPM lub Azure Backup Server | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| Usuń zarejestrowane lokalne serwery z systemem Windows/Client/SCDPM lub Azure Backup Server | Współautor kopii zapasowej | Zasób magazynu odzyskiwania |

> [!IMPORTANT]
> Jeśli określisz współautor maszyny wirtualnej w zakresie zasobów maszyny wirtualnej i klikniesz pozycję Utwórz kopię zapasową w ramach ustawień maszyny wirtualnej, zostanie otwarty ekran "Włącz kopię zapasową", nawet jeśli maszyna wirtualna została już utworzona jako wywołanie weryfikacji stanu kopii zapasowej działa tylko na poziomie subskrypcji. Aby tego uniknąć, należy przejść do magazynu i otworzyć widok elementu kopii zapasowej maszyny wirtualnej lub określić rolę współautor maszyny wirtualnej na poziomie subskrypcji.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimalne wymagania roli dla kopii zapasowej udziału plików platformy Azure

W poniższej tabeli przedstawiono akcje zarządzania kopiami zapasowymi i odpowiadające im role wymagane do wykonania operacji udziału plików platformy Azure.

| Operacja zarządzania | Rola jest wymagana | Zasoby |
| --- | --- | --- |
| Włącz tworzenie kopii zapasowych udziałów plików platformy Azure | Współautor kopii zapasowej |Magazyn usługi Recovery Services |
| |Konto magazynu | Zasób konta magazynu współautora |
| Tworzenie kopii zapasowej maszyny wirtualnej na żądanie | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| Przywróć udział plików | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor konta magazynu | Zasoby konta magazynu, w których znajdują się źródła przywracania i docelowe udziały plików |
| Przywróć pojedyncze pliki | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| |Współautor konta magazynu|Zasoby konta magazynu, w których znajdują się źródła przywracania i docelowe udziały plików |
| Zatrzymaj ochronę |Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Wyrejestrowywanie konta magazynu z magazynu |Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| |Współautor konta magazynu | Zasób konta magazynu|

## <a name="next-steps"></a>Następne kroki

* [Access Control oparte na rolach](../role-based-access-control/role-assignments-portal.md): wprowadzenie do kontroli RBAC w Azure Portal.
* Dowiedz się, jak zarządzać dostępem przy użyciu:
  * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie problemów Access Control opartych na rolach](../role-based-access-control/troubleshooting.md): Uzyskaj sugestie dotyczące rozwiązywania typowych problemów.
