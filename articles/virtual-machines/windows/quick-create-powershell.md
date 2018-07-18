---
title: Szybki start — tworzenie maszyny wirtualnej z systemem Windows za pomocą programu Azure PowerShell | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Windows za pomocą programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b199d031d4aaef4c1a707199f5692c86543e6360
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38767208"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Windows za pomocą programu Azure PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów. Z tego przewodnika Szybki start dowiesz się, jak za pomocą modułu programu Azure PowerShell wdrożyć na platformie Azure maszynę wirtualną z systemem Windows Server 2016. Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu RDP i zainstalujesz serwer internetowy usług IIS.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Podaj nazwy poszczególnych zasobów, a polecenie cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) utworzy je, jeśli jeszcze nie istnieją.

Po wyświetleniu monitu podaj nazwę użytkownika i hasło, które będą używane jako poświadczenia logowania dla maszyny wirtualnej:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Po zakończeniu wdrażania połącz się przez protokół RDP z maszyną wirtualną. Aby następnie zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy usług IIS.

Aby wyświetlić publiczny adres IP maszyny wirtualnej, użyj polecenia cmdlet [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z komputera lokalnego. Zastąp adres IP publicznym adresem IP swojej maszyny wirtualnej. Gdy zostanie wyświetlony monit, wprowadź poświadczenia użyte podczas tworzenia maszyny wirtualnej:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy usług IIS. Na maszynie wirtualnej otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po zakończeniu zamknij połączenie RDP z maszyną wirtualną.

## <a name="view-the-web-server-in-action"></a>Oglądanie działającego serwera internetowego

Po zainstalowaniu usług IIS i otwarciu portu 80 na maszynie wirtualnej dla ruchu z Internetu wyświetl domyślną stronę powitalną serwera usług IIS przy użyciu wybranej przeglądarki internetowej. Użyj publicznego adresu IP maszyny wirtualnej uzyskanego w poprzednim kroku. W poniższym przykładzie przedstawiono domyślną witrynę internetową usług IIS:

![Domyślna witryna usług IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono prostą maszynę wirtualną, otwarto port sieciowy na ruch internetowy oraz zainstalowano podstawowy serwer internetowy. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](./tutorial-manage-vm.md)
