---
title: Automatyzowanie zadań zarządzania przy użyciu rozszerzenia agenta IaaS
description: W tym artykule opisano sposób zarządzania rozszerzeniem SQL Server agenta IaaS, które automatyzuje określone SQL Server zadania administracyjne. Obejmują one automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i integrację Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 9aae386e21df6711fc4984a7abfd34f418399f76
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034197"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatyzowanie zadań zarządzania na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozszerzenie agenta IaaS w programie SQL Server (SqlIaasExtension) działa na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. Ten artykuł zawiera omówienie usług obsługiwanych przez rozszerzenie. Ten artykuł zawiera również instrukcje dotyczące instalacji, stanu i usuwania rozszerzenia.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Aby wyświetlić klasyczną wersję tego artykułu, zobacz [SQL Server rozszerzenia agenta IaaS dla SQL Server maszyn wirtualnych (klasyczny)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta SQL Server IaaS obsługuje następujące zadania administracyjne:

| Funkcja administrowania | Opis |
| --- | --- |
| **SQL Server automatyczne kopie zapasowe** |Automatyzuje Planowanie kopii zapasowych dla wszystkich baz danych dla wystąpienia domyślnego lub [poprawnie zainstalowane](virtual-machines-windows-sql-server-iaas-faq.md#administration) nazwane wystąpienie SQL Server na maszynie wirtualnej. Aby uzyskać więcej informacji, zobacz [zautomatyzowane tworzenie kopii zapasowych SQL Server w usłudze Azure Virtual Machines (Menedżer zasobów)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server zautomatyzowane stosowanie poprawek** |Konfiguruje okno obsługi, w którym mogą być wykonywane ważne aktualizacje systemu Windows dla maszyny wirtualnej, dzięki czemu można uniknąć aktualizacji w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [zautomatyzowane stosowanie poprawek dla SQL Server w usłudze Azure Virtual Machines (Menedżer zasobów)](virtual-machines-windows-sql-automated-patching.md). |
| **Integracja Azure Key Vault** |Umożliwia automatyczne instalowanie i Konfigurowanie Azure Key Vault na maszynie wirtualnej SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie integracji Azure Key Vault dla SQL Server na platformie Azure Virtual Machines (Menedżer zasobów)](virtual-machines-windows-ps-sql-keyvault.md). |

Po zainstalowaniu i uruchomieniu rozszerzenia SQL Server IaaS Agent udostępnia funkcje administracyjne:

* W panelu SQL Server maszyny wirtualnej w Azure Portal i za pomocą Azure PowerShell dla obrazów SQL Server w witrynie Azure Marketplace.
* Za pomocą Azure PowerShell ręcznej instalacji rozszerzenia. 

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania dotyczące używania SQL Server rozszerzenia agenta IaaS na maszynie wirtualnej:

**System operacyjny**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Wersja SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Pobieranie i Konfigurowanie najnowszych poleceń Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalacja
SQL Server rozszerzenie IaaS jest instalowane po zarejestrowaniu maszyny wirtualnej SQL Server z [dostawcą zasobów maszyny wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md). W razie potrzeby można zainstalować agenta SQL Server IaaS ręcznie przy użyciu poniższego polecenia programu PowerShell: 

  ```powershell-interactive
    Set-AzVMExtension -ResourceGroupName "<ResourceGroupName>" `
    -Location "<VMLocation>" -VMName "<VMName>" `
    -Name "SqlIaasExtension" -Publisher "Microsoft.SqlServer.Management" `
    -ExtensionType "SqlIaaSAgent" -TypeHandlerVersion "2.0";  
  ```

> [!NOTE]
> Zainstalowanie rozszerzenia powoduje ponowne uruchomienie usługi SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalowanie na maszynie wirtualnej z jednym wystąpieniem o nazwie SQL Server
SQL Server rozszerzenie IaaS będzie współdziałać z nazwanym wystąpieniem na SQL Server, jeśli wystąpienie domyślne zostanie odinstalowane i zostanie zainstalowane ponownie rozszerzenie IaaS.

Aby użyć nazwanego wystąpienia SQL Server, wykonaj następujące kroki:
   1. Wdróż maszynę wirtualną SQL Server z poziomu portalu Azure Marketplace. 
   1. Odinstaluj rozszerzenie IaaS z [Azure Portal](https://portal.azure.com).
   1. Odinstaluj SQL Server całkowicie w SQL Server maszynie wirtualnej.
   1. Zainstaluj SQL Server z nazwanym wystąpieniem w ramach maszyny wirtualnej SQL Server. 
   1. Zainstaluj rozszerzenie IaaS z Azure Portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Pobierz stan rozszerzenia IaaS SQL Server
Jednym ze sposobów sprawdzenia, czy rozszerzenie jest zainstalowane, jest wyświetlenie stanu agenta w Azure Portal. Wybierz pozycję **wszystkie ustawienia** w oknie maszyna wirtualna, a następnie wybierz pozycję **rozszerzenia**. Powinna zostać wyświetlona lista rozszerzeń **SqlIaasExtension** .

![Stan rozszerzenia agenta SQL Server IaaS w Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Można również użyć polecenia cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Poprzednie polecenie potwierdza, że Agent jest zainstalowany i zawiera ogólne informacje o stanie. Informacje o stanie automatycznej kopii zapasowej i poprawek można uzyskać, korzystając z następujących poleceń:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Praw
W Azure Portal można odinstalować rozszerzenie, wybierając wielokropek w oknie **rozszerzenia** we właściwościach maszyny wirtualnej. Następnie wybierz pozycję **Usuń**.

![Odinstalowywanie rozszerzenia SQL Server Agent IaaS w Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Można również użyć polecenia cmdlet **Remove-AzVMSqlServerExtension** programu PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Następne kroki
Zacznij korzystać z jednej z usług obsługiwanych przez rozszerzenie. Aby uzyskać więcej informacji, zobacz artykuły, do których odwołuje się sekcja [obsługiwane usługi](#supported-services) w tym artykule.

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na platformie Azure Virtual Machines, zobacz temat [co to jest SQL Server na platformie azure Virtual Machines?](virtual-machines-windows-sql-server-iaas-overview.md).
