---
title: Pojęcia — tożsamość i dostęp
description: Dowiedz się więcej na temat pojęć dotyczących tożsamości i uzyskiwania dostępu do rozwiązania Azure VMware
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: e9c0d62968d94e2b018186f67072b6ae7078db02
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536102"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Pojęcia dotyczące tożsamości rozwiązań VMware platformy Azure

Chmury prywatne rozwiązań VMware platformy Azure są obsługiwane za pomocą programu vCenter Server i Menedżera NSX-T. Program vCenter służy do zarządzania obciążeniami maszyny wirtualnej. Aby zwiększyć chmurę prywatną, należy użyć Menedżera NSX-T.

Zarządzanie dostępem i tożsamościami używa uprawnień grupy CloudAdmin dla programu vCenter i ograniczonych uprawnień administratora dla Menedżera NSX-T. Dzięki temu platforma chmury prywatnej jest automatycznie uaktualniana z najnowszymi funkcjami i poprawkami.  Aby uzyskać więcej informacji, zobacz artykuł dotyczący [pojęć dotyczących uaktualnień w chmurze prywatnej][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>dostęp i tożsamość vCenter

Grupa CloudAdmin zapewnia uprawnienia w programie vCenter. Zarządzasz grupą lokalnie w programie vCenter. Kolejną opcją jest integracja logowania jednokrotnego w usłudze vCenter LDAP przy użyciu Azure Active Directory. Integrację należy włączyć po wdrożeniu chmury prywatnej. 

W tabeli przedstawiono uprawnienia **CloudAdmin** i **CloudGlobalAdmin** .

|  Zestaw uprawnień           | CloudAdmin | CloudGlobalAdmin | Komentarz |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Alarmy                  | Użytkownik CloudAdmin ma wszystkie przywileje alarmowe dotyczące alarmów w Compute-ResourcePool i maszynach wirtualnych.     |          --        |  -- |
|  Wdróż ponownie             |  --  |        --        |  Firma Microsoft zarządza zarządzaniem hostami.  |
|  Certyfikaty            |  --  |        --       |  Firma Microsoft zarządza certyfikatami.  |
|  Biblioteka zawartości         | Użytkownik CloudAdmin ma uprawnienia do tworzenia plików i używania ich w bibliotece zawartości.    |         Włączone z logowaniem jednokrotnym.         |  Firma Microsoft będzie dystrybuować pliki w bibliotece zawartości na hosty ESXi.  |
|  Centrum danych              |  --  |        --          |  Firma Microsoft wykonuje wszystkie operacje centrów danych.  |
|  Magazyn danych               | Datastore. AllocateSpace, datastore. Browse, Datastore.Config, datastore. DeleteFile, datastore. usługi filemanagement, datastore. UpdateVirtualMachineMetadata     |    --    |   -- |
|  Menedżer agentów ESX       |  --  |         --       |  Firma Microsoft wykonuje wszystkie operacje.  |
|  Folder                  |  Użytkownik CloudAdmin ma wszystkie uprawnienia do folderów.     |  --  |  --  |
|  Globalnie                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. metody LogEvent, Global. ManageCustomFields, Global. ServiceManager, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host. Hbr. HbrManagement      |        --          |  Firma Microsoft wykonuje wszystkie inne operacje hosta.  |
|  InventoryService        |  InventoryService. tagowanie      |        --          |  --  |
|  Sieć                 |  Network. Assign    |                  |  Firma Microsoft wykonuje wszystkie inne operacje sieciowe.  |
|  Uprawnienia             |  --  |        --       |  Firma Microsoft wykonuje wszystkie operacje związane z uprawnieniami.  |
|  Magazyn oparty na profilach  |  --  |        --       |  Firma Microsoft wykonuje wszystkie operacje profilowania.  |
|  Zasób                |  Użytkownik CloudAdmin ma wszystkie uprawnienia do zasobów.        |      --       | --   |
|  Zaplanowane zadanie          |  Użytkownik CloudAdmin ma wszystkie uprawnienia ScheduleTask —.   |   --   | -- |
|  Sesje                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  Firma Microsoft wykonuje wszystkie pozostałe operacje.  |
|  Widoki magazynu           |  StorageViews. View   |        --          |  Firma Microsoft wykonuje wszystkie operacje widoku magazynu (Konfiguracja usługi).  |
|  Zadania                   |  --  |  --   |  Firma Microsoft zarządza rozszerzeniami, które zarządzają zadaniami.  |
|  vApp                    |  Użytkownik CloudAdmin ma wszystkie uprawnienia vApp.  |  --  |  --  |
|  Maszyna wirtualna         |  Użytkownik CloudAdmin ma wszystkie uprawnienia VirtualMachine.  |  --  |  --  |
|  vService                |  Użytkownik CloudAdmin ma wszystkie uprawnienia vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Dostęp i tożsamość menedżera NSX-T

Użyj konta "Administrator", aby uzyskać dostęp do Menedżera NSX-T. Ma pełne uprawnienia i umożliwia tworzenie routerów T1, przełączników logicznych i wszystkich usług oraz zarządzanie nimi. Uprawnienia zapewniają dostęp do routera T0 NSX-T. Zmiana w routerze T0 może spowodować spadek wydajności sieci lub brak dostępu do chmury prywatnej. Otwórz żądanie obsługi w Azure Portal, aby zażądać wszelkich zmian w routerze T0 NSX-T.
  
## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest zapoznanie się z [pojęciami dotyczącymi uaktualniania chmury prywatnej][concepts-upgrades].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md