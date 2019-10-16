---
title: Obsługa używania Azure Site Recovery z Azure Backup
description: Zawiera omówienie sposobu, w jaki Azure Site Recovery i Azure Backup mogą być używane razem.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376213"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Obsługa używania Site Recovery z Azure Backup

W tym artykule podsumowano obsługę używania [usługi Site Recovery](site-recovery-overview.md) razem z [usługą Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Akcja** | **Obsługa Site Recovery** | **Szczegóły**
--- | --- | ---
**Wdróż wspólnie usługi** | Obsługiwane | Usługi są współobsługiwane i można je skonfigurować razem.
**Kopia zapasowa/przywracanie pliku** | Obsługiwane | Gdy włączono tworzenie kopii zapasowych i replikacja dla maszyny wirtualnej, a kopie zapasowe są wykonywane, nie ma żadnego problemu podczas przywracania plików na maszynach wirtualnych po stronie źródłowej ani w grupie maszyn wirtualnych. Replikacja jest kontynuowana jak zwykle bez zmian kondycji replikacji.
**Przywracanie dysku** | Brak bieżącej pomocy technicznej | W przypadku przywrócenia dysku z kopią zapasową należy ponownie wyłączyć i ponownie włączyć replikację maszyny wirtualnej.
**Przywracanie maszyny wirtualnej** | Brak bieżącej pomocy technicznej | W przypadku przywrócenia maszyny wirtualnej lub grupy maszyn wirtualnych należy wyłączyć i ponownie włączyć replikację maszyny wirtualnej.  


