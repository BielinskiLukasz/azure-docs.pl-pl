---
title: Dostosowywanie maszyny wirtualnej z systemem Windows na platformie Azure | Microsoft Docs
description: "Dowiedz się, jak przy użyciu rozszerzenia niestandardowego skryptu zautomatyzować instalację aplikacji na maszynach wirtualnych z systemem Windows na platformie Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 63858da0a4a47d67ec659e922ab10f9f7bc97938
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Jak dostosować maszynę wirtualną z systemem Windows na platformie Azure
Aby w szybki i spójny sposób skonfigurować maszyny wirtualne, stosuje się na ogół jakąś formę automatyzacji. Typowym rozwiązaniem w przypadku dostosowywania maszyn wirtualnych z systemem Windows jest użycie [rozszerzenia niestandardowego skryptu dla systemu Windows](extensions-customscript.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie usług IIS za pomocą rozszerzenia niestandardowego skryptu
> * Tworzenie maszyny wirtualnej korzystającej z rozszerzenia niestandardowego skryptu
> * Wyświetlanie działającej witryny usług IIS po zastosowaniu rozszerzenia

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.3 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 


## <a name="custom-script-extension-overview"></a>Rozszerzenie niestandardowego skryptu — omówienie
Rozszerzenie niestandardowego skryptu pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub GitHub bądź można je dostarczyć do witryny Azure Portal w czasie wykonywania rozszerzenia.

Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager, a także uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, witryny Azure Portal lub interfejsu API REST maszyny wirtualnej platformy Azure.

Rozszerzenia niestandardowego skryptu można używać na maszynach wirtualnych z systemem Windows lub Linux.


## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej
Najpierw ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* w lokalizacji *EastUS*. Grupa zasobów *myResourceGroupAutomate* i pomocnicze zasoby sieciowe zostaną utworzone, jeśli jeszcze nie istnieją. To polecenie cmdlet otwiera również port *80* w celu obsługi ruchu internetowego.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Utworzenie maszyny wirtualnej i zasobów może potrwać kilka minut.


## <a name="automate-iis-install"></a>Automatyzowanie instalacji usług IIS
Zainstaluj rozszerzenie niestandardowego skryptu przy użyciu polecenia [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). To rozszerzenie uruchamia polecenie `powershell Add-WindowsFeature Web-Server`, aby zainstalować serwer internetowy usług IIS, a następnie aktualizuje stronę *Default.htm* w celu wyświetlenia nazwy hosta maszyny wirtualnej:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testowanie witryny internetowej
Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIPAddress*:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona witryna internetowa z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Działająca witryna internetowa usług IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem zautomatyzowano instalację usług IIS na maszynie wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Instalowanie usług IIS za pomocą rozszerzenia niestandardowego skryptu
> * Tworzenie maszyny wirtualnej korzystającej z rozszerzenia niestandardowego skryptu
> * Wyświetlanie działającej witryny usług IIS po zastosowaniu rozszerzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak tworzyć niestandardowe obrazy maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych obrazów maszyn wirtualnych](./tutorial-custom-images.md)
