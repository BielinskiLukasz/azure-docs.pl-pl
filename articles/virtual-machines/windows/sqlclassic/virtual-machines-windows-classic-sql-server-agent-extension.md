---
title: Automatyzowanie zadań zarządzania na maszynach wirtualnych SQL (klasyczny) | Microsoft Docs
description: W tym temacie opisano sposób zarządzania rozszerzeniem agenta SQL Server, które automatyzuje określone SQL Server zadania administracyjne. Obejmują one automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i integrację Azure Key Vault. W tym temacie jest stosowany klasyczny tryb wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e58b3f651a7a8f65d3450cf4b069d4e3f252d928
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100280"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatyzowanie zadań zarządzania na platformie Azure Virtual Machines przy użyciu rozszerzenia agenta SQL Server (klasycznego)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../classic/sql-server-agent-extension.md)
> 
>
 
Rozszerzenie IaaS Agent (SQLIaaSAgent) jest uruchamiane na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. SQL Server Ten temat zawiera omówienie usług obsługiwanych przez rozszerzenie oraz instrukcje dotyczące instalacji, stanu i usuwania.

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Menedżer zasobów i klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję Menedżer zasobów tego artykułu, zapoznaj się z tematem [SQL Server rozszerzenia agenta dla maszyn wirtualnych SQL Server Menedżer zasobów](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta SQL Server IaaS obsługuje następujące zadania administracyjne:

| Funkcja administrowania | Opis |
| --- | --- |
| **Automatyczne kopie zapasowe SQL** |Automatyzuje Planowanie kopii zapasowych dla wszystkich baz danych dla domyślnego wystąpienia SQL Server w maszynie wirtualnej. Aby uzyskać więcej informacji, zobacz [zautomatyzowane tworzenie kopii zapasowych SQL Server na platformie Azure Virtual Machines (klasyczne)](../classic/sql-automated-backup.md). |
| **Automatyczne stosowanie poprawek SQL** |Konfiguruje okno obsługi, w którym mogą być wykonywane ważne aktualizacje systemu Windows dla maszyny wirtualnej, dzięki czemu można uniknąć aktualizacji w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [zautomatyzowane stosowanie poprawek dla SQL Server na platformie Azure Virtual Machines (klasyczne)](../classic/sql-automated-patching.md). |
| **Integracja z usługą Azure Key Vault** |Umożliwia automatyczne instalowanie i Konfigurowanie Azure Key Vault na maszynie wirtualnej SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie integracji Azure Key Vault na potrzeby SQL Server na maszynach wirtualnych platformy Azure (klasycznych)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Wymagania wstępne
Wymagania dotyczące używania SQL Server rozszerzenia agenta IaaS na maszynie wirtualnej:

### <a name="operating-system"></a>System operacyjny:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server wersje:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Pobierz i skonfiguruj najnowsze polecenia Azure PowerShell](/powershell/azure/overview).

Uruchom program Windows PowerShell, a następnie połącz go z subskrypcją platformy Azure za pomocą polecenia **Add-AzureAccount** .

    Add-AzureAccount

Jeśli masz wiele subskrypcji, użyj **opcji Select-AzureSubscription** , aby wybrać subskrypcję zawierającą docelową KLASYCZNą maszynę wirtualną.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

W tym momencie można uzyskać listę klasycznych maszyn wirtualnych i skojarzonych z nimi nazw usług za pomocą polecenia **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Instalacja
W przypadku klasycznych maszyn wirtualnych należy użyć programu PowerShell, aby zainstalować rozszerzenie agenta SQL Server IaaS i skonfigurować skojarzone z nim usługi. Użyj polecenia cmdlet **Set-AzureVMSqlServerExtension** programu PowerShell, aby zainstalować rozszerzenie. Na przykład następujące polecenie powoduje zainstalowanie rozszerzenia na maszynie wirtualnej z systemem Windows Server (klasyczne) i nadaje mu nazwę "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

W przypadku aktualizacji do najnowszej wersji rozszerzenia programu SQL IaaS Agent należy ponownie uruchomić maszynę wirtualną po zaktualizowaniu rozszerzenia.

> [!NOTE]
> Klasyczne maszyny wirtualne nie mają opcji instalacji i konfiguracji rozszerzenia agenta SQL IaaS za pomocą portalu.

> [!NOTE]
> Rozszerzenie agenta SQL Server IaaS jest obsługiwane tylko w obrazach z [galerii maszyn wirtualnych SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (płatność zgodnie z rzeczywistym użyciem lub do własnego użycia licencji). Nie jest obsługiwana w przypadku ręcznej instalacji SQL Server na maszynie wirtualnej z systemem Windows Server lub w przypadku wdrożenia dostosowanego SQL Server wirtualnego dysku twardego maszyny wirtualnej. W takich przypadkach może być możliwe ręczne zainstalowanie rozszerzenia i zarządzanie nim przy użyciu programu PowerShell, ale zdecydowanie zaleca się zainstalowanie obrazu SQL Server VM Gallery, a następnie dostosowanie go.

## <a name="status"></a>State
Jednym ze sposobów sprawdzenia, czy rozszerzenie jest zainstalowane, jest wyświetlenie stanu agenta w witrynie Azure Portal. Wybierz maszynę wirtualną w bloku maszyna wirtualna, a następnie kliknij pozycję **rozszerzenia**. Powinna zostać wyświetlona lista rozszerzeń **SQLIaaSAgent** .

![SQL Server rozszerzenia agenta IaaS w witrynie Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Można również użyć polecenia cmdlet **Get-AzureVMSqlServerExtension** programu Azure PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Usunięcie
W witrynie Azure Portal można odinstalować rozszerzenie, klikając wielokropek w bloku **rozszerzenia** we właściwościach maszyny wirtualnej. Następnie kliknij przycisk **Odinstaluj**.

![Odinstalowywanie rozszerzenia Agent SQL Server IaaS w witrynie Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Można również użyć polecenia cmdlet **Remove-AzureVMSqlServerExtension** programu PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Następne kroki
Rozpocznij korzystanie z jednej z usług obsługiwanych przez rozszerzenie. Aby uzyskać więcej informacji, zobacz tematy wymienione w sekcji [obsługiwane usługi](#supported-services) w tym artykule.

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na platformie Azure Virtual Machines, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

