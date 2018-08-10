---
title: Tworzenie maszyny Wirtualnej przy użyciu statycznego publicznego adresu IP — PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: b59157b0f17380dbe4386fbd9ac75776e22f749e
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713979"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu programu PowerShell

Można utworzyć maszyny wirtualnej ze statycznym publicznym adresem IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy nie zmienia się. Dowiedz się więcej o [statyczne publiczne adresy IP](virtual-network-ip-addresses-overview-arm.md#allocation-method). Aby zmienić publiczny adres IP przypisane do istniejącej maszyny wirtualnej z dynamicznego na statyczne lub pracować w prywatnych adresów IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md). Publiczne adresy IP zostały [za symboliczną cenę](https://azure.microsoft.com/pricing/details/ip-addresses)i ma [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) do liczby publicznych adresów IP używanych na subskrypcję.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Z komputera lokalnego lub za pomocą usługi Azure Cloud Shell, można wykonać następujące czynności. Aby korzystać z komputera lokalnego, upewnij się, że [zainstalowany program Azure PowerShell](/powershell/azure/install-azurerm-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby użyć usługi Azure Cloud Shell, wybierz **wypróbuj** w prawym górnym rogu pola dowolnego polecenia, która jest zgodna. Cloud Shell zaloguje się do platformy Azure.

1. Jeśli używasz powłoki Cloud, przejdź do kroku 2. Otwórz polecenia sesji i logowania do platformy Azure przy `Connect-AzureRmAccount`.
2. Utwórz grupę zasobów na pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy grupę zasobów w regionie wschodnie stany USA Azure:

   ```azurepowershell-interactive
   New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Utwórz maszynę wirtualną za pomocą [New-AzureRmVM](/powershell/module/AzureRM.Compute/New-AzureRmVM) polecenia. `-AllocationMethod "Static"` Opcja przypisuje statyczny publiczny adres IP do maszyny wirtualnej. Poniższy przykład tworzy maszynę wirtualną systemu Windows Server przy użyciu statycznych i podstawowa jednostka SKU publicznego adresu IP o nazwie *myPublicIpAddress*. Po wyświetleniu monitu podaj nazwę użytkownika i hasło, które ma być używany jako poświadczenia logowania dla maszyny wirtualnej:

   ```azurepowershell-interactive
   New-AzureRmVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Jeśli publiczny adres IP musi być standardowej jednostki SKU, trzeba [tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address), [Utwórz interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface), [przypisać publiczny adres IP do interfejsu sieciowego](virtual-network-network-interface-addresses.md#add-ip-addresses), a następnie [Utwórz maszynę wirtualną z interfejsem sieciowym](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)w oddzielnych czynności. Dowiedz się więcej o [publicznego adresu IP jednostki SKU](virtual-network-ip-addresses-overview-arm.md#sku). Jeśli maszyna wirtualna zostanie dodany do puli zaplecza publicznej usługi Azure Load Balancer, jednostki SKU publicznego adresu IP maszyny wirtualnej musi być zgodna z jednostką SKU publicznego adresu IP modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [usługi Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Wyświetl przypisany publiczny adres IP i upewnij się, że został on utworzony jako adres statyczny, za pomocą [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

   ```azurepowershell-interactive
   Get-AzureRmPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure przypisany publiczny adres IP z adresów używanych w danym regionie utworzono maszynę wirtualną w. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
Nie należy modyfikować ustawienia adresu IP w ramach systemu operacyjnego maszyny wirtualnej. System operacyjny jest niezależnych od platformy Azure publicznych adresów IP. Chociaż można było dodać prywatnej ustawienia adresu IP do systemu operacyjnego, zaleca się nie ten sposób, chyba że to konieczne, a nie dopiero po odczytu [Dodaj prywatny adres IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [publiczne adresy IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure
- Dowiedz się więcej o wszystkich [ustawienia publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej o [prywatnych adresów IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) i przypisywanie [statyczny prywatny adres IP](virtual-network-network-interface-addresses.md#add-ip-addresses) na maszynie wirtualnej platformy Azure
- Dowiedz się więcej na temat tworzenia [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyn wirtualnych