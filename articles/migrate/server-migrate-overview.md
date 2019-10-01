---
title: Wybierz opcję migracji VMware z migracją na serwer Azure Migrate | Microsoft Docs
description: Zawiera omówienie opcji migrowania maszyn wirtualnych VMware na platformę Azure przy użyciu migracji Azure Migrate serwera
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9ddb63fcf4d960d2d8ed2671eba83fc38cddcbe4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710049"
---
# <a name="select-a-vmware-migration-option"></a>Wybierz opcję migracji VMware

Maszyny wirtualne programu VMware można migrować do platformy Azure za pomocą narzędzia migracji Azure Migrate Server. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Migracja przy użyciu replikacji bez wykorzystania agentów. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich jakichkolwiek elementów.
- Migracja z agentem na potrzeby replikacji. Zainstaluj agenta na maszynie wirtualnej na potrzeby replikacji.




## <a name="compare-migration-methods"></a>Porównanie metod migracji

Użyj tych wybranych porównań, aby określić metodę, która ma zostać użyta. Można także zapoznać się z pełnymi wymaganiami dotyczącymi obsługi dla migracji [opartej](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) [na agentach](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) .

**Konfigurowania** | **Bez agenta** | **Na podstawie agenta**
--- | --- | ---
**Uprawnienia platformy Azure** | Musisz mieć uprawnienia do tworzenia projektu Azure Migrate i rejestrowania aplikacji usługi Azure AD utworzonych podczas wdrażania urządzenia Azure Migrate. | Musisz mieć uprawnienia współautora w ramach subskrypcji platformy Azure. 
**Równoczesna replikacja** | Maksymalnie 100 maszyn wirtualnych może być replikowanych z vCenter Server.<br/> Jeśli masz więcej niż 50 maszyn wirtualnych do migracji, Utwórz wiele partii maszyn wirtualnych.<br/> Replikacja więcej jednocześnie będzie miała wpływ na wydajność. | POTRĄCON
**Wdrażanie urządzenia** | [Urządzenie Azure Migrate](migrate-appliance.md) jest wdrażane lokalnie. | [Urządzenie replikacji Azure Migrate](migrate-replication-appliance.md) jest wdrażane lokalnie.
**Zgodność Site Recovery** | Zgodność. | Nie można przeprowadzić replikacji z migracją serwera Azure Migrate, jeśli skonfigurowano replikację dla maszyny przy użyciu Site Recovery.
**Dysk docelowy** | Dyski zarządzane | Dyski zarządzane
**Limity dysku** | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 4 TB<br/><br/> Maksymalna liczba dysków: 60 | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 8 TB<br/><br/> Maksymalna liczba dysków: 63
**Przekazywanie dysków** | Nieobsługiwane | Obsługiwane
**Rozruch z interfejsem UEFI** | Nieobsługiwane | Migrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową w systemie BIOS.<br/><br/> Dysk systemu operacyjnego powinien mieć maksymalnie cztery partycje, a woluminy powinny być sformatowane w systemie plików NTFS.


## <a name="deployment-steps-comparison"></a>Porównanie kroków wdrożenia

Po przejrzeniu ograniczeń można zrozumieć kroki związane z wdrażaniem poszczególnych rozwiązań, które mogą pomóc zdecydować, którą opcję wybrać.

**Zadaniem** | **Uzyskać** |**Bez agenta** | **Na podstawie agenta**
--- | --- | --- | ---
**Przygotuj serwery i maszyny wirtualne VMware do migracji** | Skonfiguruj kilka ustawień na serwerach i maszynach wirtualnych VMware. | Wymagane | Wymagane
**Dodawanie narzędzia migracji serwera** | Dodaj narzędzie do migracji Azure Migrate Server w projekcie Azure Migrate. | Wymagane | Wymagane
**Wdróż urządzenie Azure Migrate** | Skonfiguruj lekkie urządzenie na maszynie wirtualnej VMware na potrzeby odnajdywania i oceny maszyn wirtualnych. | Wymagane | Niewymagane.
**Instalowanie usługi mobilności na maszynach wirtualnych** | Zainstaluj usługę mobilności na każdej maszynie wirtualnej, która ma zostać zreplikowana | Niewymagane | Wymagane
**Wdrażanie urządzenia replikacji migracji serwera Azure Migrate** | Konfigurowanie urządzenia na maszynie wirtualnej VMware w celu odnajdywania maszyn wirtualnych i mostkowania między usługą mobilności działającą na maszynach wirtualnych i migracji Azure Migrate serwera | Niewymagane | Wymagane
**Replikowanie maszyn wirtualnych**. Włącz replikację maszyny wirtualnej. | Skonfiguruj ustawienia replikacji i wybierz maszyny wirtualne do replikacji | Wymagane | Wymagane
**Uruchom migrację testową** | Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. | Wymagane | Wymagane
**Uruchamianie pełnej migracji** | Migrowanie maszyn wirtualnych. | Wymagane | Wymagane




## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych VMware](tutorial-migrate-vmware.md) z migracją bez agenta.



