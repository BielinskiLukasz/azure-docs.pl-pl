---
title: Omówienie magazynów usługi Recovery Services
description: Omówienie i porównanie między magazynami Recovery Services i magazynami Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: dacurwin
ms.openlocfilehash: 3c451d1bd77fa1cec85a9d42b5d4d3d819b2d19a
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210347"
---
# <a name="recovery-services-vaults-overview"></a>Omówienie magazynów Recovery Services

W tym artykule opisano funkcje magazynu Recovery Services. Magazyn Recovery Services to jednostka magazynowa na platformie Azure, która zawiera dane. Dane są zwykle kopiami danych lub informacjami o konfiguracji maszyn wirtualnych, obciążeń, serwerów lub stacji roboczych. Magazynów Recovery Services można używać do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takich jak maszyny wirtualne IaaS (Linux lub Windows) i bazy danych Azure SQL. Magazyny Recovery Services obsługują program System Center DPM, system Windows Server, Azure Backup Server i nie tylko. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem.

W ramach subskrypcji platformy Azure Możesz utworzyć do 500 magazynów Recovery Services na subskrypcję na region.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Porównywanie magazynów Recovery Services i magazynów kopii zapasowych

Jeśli nadal masz magazyny kopii zapasowych, są one uaktualniane do Recovery Services magazynów. Do listopada 2017 wszystkie magazyny kopii zapasowych zostały uaktualnione do magazynów Recovery Services.

Magazyny Recovery Services opierają się na Azure Resource Manager modelu platformy Azure, a magazyny kopii zapasowych opierają się na modelu Service Manager platformy Azure. Po uaktualnieniu magazynu kopii zapasowych do magazynu Recovery Services dane kopii zapasowej pozostają nienaruszone podczas procesu uaktualniania. Magazyny Recovery Services udostępniają funkcje niedostępne dla magazynów kopii zapasowych, na przykład:

- **Udoskonalone funkcje ułatwiające zabezpieczanie danych kopii zapasowych**: W przypadku Recovery Services magazynów Azure Backup zapewnia funkcje zabezpieczeń, aby chronić kopie zapasowe w chmurze. Funkcje zabezpieczeń zapewniają możliwość zabezpieczenia kopii zapasowych i bezpiecznego odzyskiwania danych, nawet jeśli naruszone są serwery produkcyjne i zapasowe. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie hybrydowego środowiska informatycznego**: W przypadku magazynów Recovery Services można monitorować nie tylko [maszyny wirtualne IaaS platformy Azure](backup-azure-manage-vms.md) , ale również [Zasoby lokalne](backup-azure-manage-windows-server.md#manage-backup-items) z portalu centralnego. [Dowiedz się więcej](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Access Control oparte na rolach (RBAC)** : RBAC zapewnia precyzyjną kontrolę zarządzania dostępem na platformie Azure. [Platforma Azure udostępnia różne wbudowane role](../role-based-access-control/built-in-roles.md), a Azure Backup ma trzy [wbudowane role do zarządzania punktami odzyskiwania](backup-rbac-rs-vault.md). Magazyny Recovery Services są zgodne z RBAC, które ograniczają dostęp do kopii zapasowych i przywracania do zdefiniowanego zestawu ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Chroń wszystkie konfiguracje Virtual Machines platformy Azure**: Recovery Services magazyny chronią maszyny wirtualne oparte na Menedżer zasobówach, w tym dyski w warstwie Premium, Managed Disks i zaszyfrowane maszyny wirtualne. Uaktualnienie magazynu kopii zapasowych do magazynu Recovery Services daje możliwość uaktualnienia maszyn wirtualnych opartych na Service Manager do maszyn wirtualnych opartych na Menedżer zasobów. Podczas uaktualniania magazynu można zachować Service Manager punkty odzyskiwania maszyny wirtualnej i skonfigurować ochronę dla uaktualnionych (Menedżer zasobów włączonych) maszyn wirtualnych. [Dowiedz się więcej](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Natychmiastowe przywracanie dla maszyn wirtualnych IaaS**: Korzystając z Recovery Services magazynów, można przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, co umożliwia skrócenie czasów przywracania. Natychmiastowe przywracanie dla maszyn wirtualnych IaaS jest dostępne zarówno dla maszyn wirtualnych z systemem Windows, jak i Linux. [Dowiedz się więcej](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Zarządzanie magazynami Recovery Services w portalu
Tworzenie Recovery Services magazynów i zarządzanie nimi w Azure Portal jest łatwe, ponieważ usługa Backup integruje się z innymi usługami platformy Azure. Ta integracja oznacza, że *w kontekście usługi docelowej*można utworzyć magazyn Recovery Services lub zarządzać nim. Aby na przykład wyświetlić punkty odzyskiwania dla maszyny wirtualnej, wybierz maszynę wirtualną, a następnie kliknij pozycję **kopia zapasowa** w menu operacje.

![Maszyna wirtualna szczegółów magazynu usługi Recovery Services](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Jeśli maszyna wirtualna nie ma skonfigurowanej kopii zapasowej, zostanie wyświetlony monit o skonfigurowanie kopii zapasowej. Jeśli została skonfigurowana kopia zapasowa, zobaczysz informacje o kopii zapasowej maszyny wirtualnej, w tym listę punktów przywracania.  

![Maszyna wirtualna szczegółów magazynu usługi Recovery Services](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

W poprzednim przykładzie **ContosoVM** to nazwa maszyny wirtualnej. **ContosoVM-demovault** to nazwa magazynu Recovery Servicesowego. Nie musisz pamiętać nazwy magazynu Recovery Services, w którym są przechowywane punkty odzyskiwania, możesz uzyskać dostęp do tych informacji z maszyny wirtualnej.  

Jeśli jeden Recovery Services magazyn chroni wiele serwerów, może być bardziej logiczny, aby przyjrzeć się magazynowi Recovery Services. Możesz wyszukać wszystkie Recovery Services magazyny w subskrypcji i wybrać jedną z listy.

Poniższe sekcje zawierają linki do artykułów, które wyjaśniają, jak używać magazynu Recovery Services w każdym typie działania.

> [!NOTE]
> Nie można utworzyć magazynu Recovery Services o tej samej nazwie, jeśli został on usunięty w ciągu 24 godzin. Użyj innej nazwy zasobu lub wybierz inną grupę zasobów lub spróbuj ponownie po 24 godzinach.

### <a name="back-up-data"></a>Tworzenie kopii zapasowej danych
- [Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-vms-first-look-arm.md)
- [Tworzenie kopii zapasowej systemu Windows Server lub stacji roboczej systemu Windows](backup-try-azure-backup-in-10-mins.md)
- [Tworzenie kopii zapasowych obciążeń programu DPM na platformie Azure](backup-azure-dpm-introduction.md)
- [Przygotowanie do tworzenia kopii zapasowych obciążeń przy użyciu Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Zarządzanie punktami odzyskiwania
- [Zarządzanie kopiami zapasowymi maszyny wirtualnej platformy Azure](backup-azure-manage-vms.md)
- [Zarządzanie plikami i folderami](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Przywracanie danych z magazynu
- [Odzyskiwanie pojedynczych plików z maszyny wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md)
- [Przywracanie maszyny wirtualnej platformy Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Zabezpieczanie magazynu
- [Zabezpieczanie danych kopii zapasowej w chmurze w magazynach Recovery Services](backup-azure-security-feature.md)



## <a name="next-steps"></a>Następne kroki
Skorzystaj z następujących artykułów, aby:</br>
[Tworzenie kopii zapasowej maszyny wirtualnej IaaS](backup-azure-arm-vms-prepare.md)</br>
[Tworzenie kopii zapasowej Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Tworzenie kopii zapasowej systemu Windows Server](backup-configure-vault.md)
