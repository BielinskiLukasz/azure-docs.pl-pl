---
title: Automatyczne stosowanie poprawek dla maszyn wirtualnych programu SQL Server (Resource Manager) | Dokumentacja firmy Microsoft
description: Wyjaśnia funkcji Automatyczne stosowanie poprawek dla maszyn wirtualnych SQL Server działających na platformie Azure przy użyciu usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a7c6bea1c45cfe704bcff9ad2d4e47a30a9780cb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439625"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatyczne stosowanie poprawek dla programu SQL Server w usłudze Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatyczne stosowanie poprawek ustanawia oknem konserwacji na maszynie wirtualnej platformy Azure działa program SQL Server. Automatyczne aktualizacje można zainstalować tylko w tym oknie konserwacji. Dla programu SQL Server to ograniczenie gwarantuje, że aktualizacje systemu i wszystkie skojarzone ponownych uruchomień wystąpić na najlepszy czas dla bazy danych. 

> [!IMPORTANT]
> Tylko Windows aktualizacje oznaczone **ważne** są zainstalowane. Inne aktualizacje programu SQL Server, takich jak aktualizacje zbiorcze, należy zainstalować ręcznie. 

Automatyczne stosowanie poprawek, który jest zależny od [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć, automatyczne stosowanie poprawek, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja programu SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Zainstaluj najnowsze polecenia programu Azure PowerShell](/powershell/azure/overview) Jeśli użytkownik chce skonfigurować automatyczne stosowanie poprawek za pomocą programu PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatyczne stosowanie poprawek opiera się na rozszerzenie agenta IaaS programu SQL Server. Bieżący obrazy galerii maszyn wirtualnych SQL dodać to rozszerzenie, domyślnie. Aby uzyskać więcej informacji, zobacz [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego stosowania poprawek. Kroki rzeczywistą konfigurację się różnić w zależności od tego, czy korzystasz z witryny Azure portal lub poleceń programu Windows Azure PowerShell.

| Ustawienie | Możliwe wartości | Opis |
| --- | --- | --- |
| **Automatyczne stosowanie poprawek** |Włącz/Wyłącz (wyłączony) |Włącza lub wyłącza automatyczne stosowanie poprawek dla maszyny wirtualnej platformy Azure. |
| **Harmonogram konserwacji** |Codziennie, poniedziałek, Wtorek, Środa, czwartek, Friday, Saturday lub Sunday |Harmonogram pobierania i instalowania aktualizacji Windows, SQL Server i Microsoft dla maszyny wirtualnej. |
| **Godzina rozpoczęcia konserwacji** |0-24 |Godzina rozpoczęcia lokalne, które można zaktualizować maszyny wirtualnej. |
| **Czas trwania okna obsługi** |30-180 |Liczba minut na ukończenie pobierania i instalacji aktualizacji. |
| **Kategoria poprawki** |Ważne | Kategoria aktualizacji Windows, aby pobrać i zainstalować.|

## <a name="configuration-in-the-portal"></a>Konfiguracja w portalu
Za pomocą witryny Azure portal skonfigurować automatyczne stosowanie poprawek, podczas udostępniania lub dla istniejących maszyn wirtualnych.

### <a name="new-vms"></a>Nowe maszyny wirtualne
Użyj witryny Azure portal, aby skonfigurować automatyczne stosowanie poprawek, podczas tworzenia nowej maszyny wirtualnej programu SQL Server w modelu wdrażania usługi Resource Manager.

W **ustawień programu SQL Server** bloku wybierz **automatyczne stosowanie poprawek**. Ilustruje poniższy zrzut ekranu na portalu Azure **SQL automatyczne stosowanie poprawek** bloku.

![Automatyczne stosowanie poprawek SQL w w witrynie Azure portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Kontekst, w temacie pełną na [obsługi maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Istniejące maszyny wirtualne
W przypadku istniejących maszyn wirtualnych programu SQL Server należy wybrać maszyny wirtualnej programu SQL Server. Następnie wybierz pozycję **konfiguracji programu SQL Server** części **ustawienia** bloku.

![SQL automatyczne stosowanie poprawek dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

W **konfiguracji programu SQL Server** bloku kliknij **Edytuj** przycisk Automatyczne stosowanie poprawek sekcji.

![Konfigurowanie programu SQL automatyczne stosowanie poprawek dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Po zakończeniu kliknij przycisk **OK** przycisk w dolnej części **konfiguracji programu SQL Server** bloku, aby zapisać zmiany.

Jeśli włączasz automatyczne stosowanie poprawek po raz pierwszy, Azure konfiguruje agenta IaaS programu SQL Server w tle. W tym czasie witryny Azure portal może nie pokazać, że skonfigurowano automatyczne stosowanie poprawek. Poczekaj kilka minut, zanim agent, który ma zostać zainstalowane, skonfigurowane. Po tym witryny Azure portal odzwierciedla nowych ustawień.

> [!NOTE]
> Można również skonfigurować automatyczne stosowanie poprawek za pomocą szablonu. Aby uzyskać więcej informacji, zobacz [szablon szybkiego startu platformy Azure automatyczne stosowanie poprawek](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
Po aprowizacji maszyny Wirtualnej SQL, skonfiguruj automatyczne stosowanie poprawek za pomocą programu PowerShell.

W poniższym przykładzie programu PowerShell służy do konfigurowania automatyczne stosowanie poprawek istniejącej maszyny wirtualnej programu SQL Server. **AzureRM.Compute\New AzVMSqlServerAutoPatchingConfig** polecenie konfiguruje nowe okno konserwacji dla aktualizacji automatycznych.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Jeśli rozszerzenie nie jest już zainstalowany, instalowania rozszerzenia powoduje ponowne uruchomienie usługi programu SQL Server.

Na podstawie tego przykładu, w poniższej tabeli opisano praktycznego wpływu na docelowej maszynie Wirtualnej platformy Azure:

| Parametr | Efekt |
| --- | --- |
| **dayOfWeek** |Każdy czwartek zainstalowanych poprawek. |
| **MaintenanceWindowStartingHour** |Rozpocznij aktualizacje o 11:00. |
| **MaintenanceWindowsDuration** |Poprawki muszą być zainstalowane w ciągu 120 minut. Na podstawie czasu uruchomienia, muszą wykonać przez 1:00 pm. |
| **PatchCategory** |Tylko to możliwe ustawienie tego parametru to **ważne**. Spowoduje to zainstalowanie aktualizacji Windows oznaczone jako ważne; nie instaluje wszystkie aktualizacje programu SQL Server, które nie są uwzględnione w tej kategorii. |

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta IaaS programu SQL Server.

Aby wyłączyć automatyczne stosowanie poprawek, uruchom ten sam skrypt bez **-Włącz** parametr **AzureRM.Compute\New AzVMSqlServerAutoPatchingConfig**. Brak **-Włącz** parametru sygnalizuje polecenie, aby wyłączyć tę funkcję.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o innych zadaniach automatyzacji dostępności, zobacz [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

