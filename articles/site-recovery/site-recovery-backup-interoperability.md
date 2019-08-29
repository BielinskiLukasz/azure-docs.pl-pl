---
title: Obsługa używania Azure Site Recovery z Azure Backup
description: Zawiera omówienie sposobu, w jaki Azure Site Recovery i Azure Backup mogą być używane razem.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146871"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Obsługa używania Site Recovery z Azure Backup

W tym artykule podsumowano obsługę używania [usługi Site Recovery](site-recovery-overview.md) razem z [usługą Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Akcja** | **Obsługa Site Recovery** | **Szczegóły**
--- | --- | ---
**Wdróż wspólnie usługi** | Obsługiwane | Usługi są współobsługiwane i można je skonfigurować razem.
**Kopia zapasowa/przywracanie pliku** | Obsługiwane | Gdy włączono tworzenie kopii zapasowych i replikacja dla maszyny wirtualnej, a kopie zapasowe są wykonywane, nie ma żadnego problemu podczas przywracania plików na maszynach wirtualnych po stronie źródłowej ani w grupie maszyn wirtualnych. Replikacja jest kontynuowana jak zwykle bez zmian kondycji replikacji.
**Kopia zapasowa/przywracanie dysku** | Brak bieżącej pomocy technicznej | W przypadku przywrócenia dysku z kopią zapasową należy ponownie wyłączyć i włączyć replikację maszyny wirtualnej.
**Kopia zapasowa/przywracanie maszyny wirtualnej** | Brak bieżącej pomocy technicznej | W przypadku tworzenia kopii zapasowej lub przywracania maszyny wirtualnej lub grupy maszyn wirtualnych należy wyłączyć i ponownie włączyć replikację maszyny wirtualnej.  


