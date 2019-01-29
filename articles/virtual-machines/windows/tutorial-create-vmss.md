---
title: Samouczek — tworzenie zestawów skalowania maszyn wirtualnych dla systemu Windows na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak za pomocą programu Azure PowerShell utworzyć i wdrożyć aplikację o wysokiej dostępności na maszynach wirtualnych z systemem Windows przy użyciu zestawu skalowania maszyn wirtualnych
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 11/07/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 4d7d1ad57feab0c73b0f2e77b815bad746040b00
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426594"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Samouczek: tworzenie zestawu skalowania maszyn wirtualnych i wdrażanie aplikacji o wysokiej dostępności w systemie Windows za pomocą programu Azure PowerShell
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania można skalować ręcznie. Można też zdefiniować reguły skalowania automatycznego na podstawie użycia zasobów, takich jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. W tym samouczku wdrożysz zestaw skalowania maszyn wirtualnych na platformie Azure i dowiesz się, jak wykonać następujące zadania:

> [!div class="checklist"]
> * Definiowanie witryny usług IIS do skalowania za pomocą rozszerzenia niestandardowego skryptu
> * Tworzenie modułu równoważenia obciążenia dla zestawu skalowania
> * Tworzenie zestawu skalowania maszyn wirtualnych
> * Zwiększanie lub zmniejszanie liczby wystąpień w zestawie skalowania
> * Tworzenie reguł skalowania automatycznego

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 6.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.


## <a name="scale-set-overview"></a>Omówienie zestawów skalowania
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania są dystrybuowane w ramach domen aktualizacji i usterek logiki w co najmniej jednej *grupie umieszczania*. Grupy umieszczania to grupy podobnie skonfigurowanych maszyn wirtualnych — podobne do [zestawów dostępności](tutorial-availability-sets.md).

Maszyny wirtualne są tworzone zgodnie z potrzebami w zestawie skalowania. W celu kontrolowania tego, jak i kiedy maszyny wirtualne są dodawane lub usuwane z zestawu skalowania, definiuje się reguły skalowania automatycznego. Reguły te mogą być wyzwalane na podstawie metryk takich jak obciążenie procesora CPU, użycie pamięci lub ruch sieciowy.

Zestawy skalowania obsługują maksymalnie 1000 maszyn wirtualnych, gdy jest używany obraz platformy Azure. W przypadku obciążeń ze znaczącymi wymaganiami dotyczącymi dostosowywania instalacji lub maszyn wirtualnych warto rozważyć [utworzenie niestandardowego obrazu maszyny wirtualnej](tutorial-custom-images.md). Przy użyciu obrazu niestandardowego można utworzyć maksymalnie 300 maszyn wirtualnych w zestawie skalowania.


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet*, używającego obrazu platformy systemu *Windows Server 2016 Datacenter*. Zasoby sieciowe platformy Azure dla sieci wirtualnej, publiczny adres IP i moduł równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu możesz podać własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="deploy-sample-application"></a>Wdrażanie przykładowej aplikacji
Aby przetestować zestaw skalowania, należy zainstalować podstawową aplikację internetową. Niestandardowe rozszerzenie skryptu platformy Azure służy do pobierania i uruchamiania skryptu, który instaluje usługi IIS na wystąpieniach maszyn wirtualnych. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](extensions-customscript.md).

Użyj niestandardowego rozszerzenia skryptu, aby zainstalować podstawowy serwer internetowy usług IIS. Zastosuj niestandardowe rozszerzenie skryptu, które instaluje usługi IIS, w następujący sposób:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Zezwalanie na ruch do aplikacji

Aby zezwolić na dostęp do podstawowej aplikacji internetowej, utwórz sieciową grupę zabezpieczeń za pomocą poleceń [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) i [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Aby uzyskać więcej informacji, zobacz [Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zobaczyć, jak działa zestaw skalowania, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). W poniższym przykładzie pokazano wyświetlanie adresu IP dla modułu *myPublicIP* utworzonego w ramach zestawu skalowania:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Wprowadź publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona aplikacja internetowa wraz z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch:

![Uruchamianie witryny usług IIS](./media/tutorial-create-vmss/running-iis-site.png)

Aby zobaczyć zestaw skalowania w działaniu, możesz wymusić odświeżenie przeglądarki internetowej i przyjrzeć się temu, jak moduł równoważenia obciążenia rozdziela ruch między wszystkie maszyny wirtualne, na których działa aplikacja.


## <a name="management-tasks"></a>Zadania zarządzania
W całym cyklu życia zestawu skalowania konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Program Azure PowerShell umożliwia szybkie wykonywanie tych zadań. Poniżej przedstawiono kilka typowych zadań.

### <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj polecenia [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) w następujący sposób:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

Następujące przykładowe dane wyjściowe zawierają dwa wystąpienia maszyn wirtualnych w zestawie skalowania:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Aby wyświetlić dodatkowe informacje na temat określonego wystąpienia maszyny wirtualnej, dodaj parametr `-InstanceId` do polecenia [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). W poniższym przykładzie są widoczne informacje o wystąpieniu maszyny wirtualnej *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Zwiększanie lub zmniejszanie liczby wystąpień maszyn wirtualnych
Aby wyświetlić bieżącą liczbę wystąpień w zestawie skalowania, użyj polecenia [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) i wykonaj zapytanie o wartość *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Następnie możesz ręcznie zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w zestawie skalowania za pomocą polecenia [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). W poniższym przykładzie liczba maszyn wirtualnych w zestawie skalowania jest ustawiana na *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Zaktualizowanie określonej liczby wystąpień w zestawie skalowania potrwa kilka minut.


### <a name="configure-autoscale-rules"></a>Konfigurowanie skalowania automatycznego
Zamiast ręcznie skalować liczbę wystąpień w zestawie skalowania zdefiniuj reguły automatycznego skalowania. Te reguły monitorują wystąpienia w zestawie skalowania i odpowiednio reagują na podstawie zdefiniowanych metryk i progów. W poniższym przykładzie skala liczby wystąpień jest zwiększana o jedno, gdy średnie obciążenie procesora przekracza 60% w okresie 5 minut. Jeśli w okresie 5 minut średnie obciążenie procesora CPU spadnie poniżej 30%, liczba wystąpień zostanie przeskalowana w pionie o jedno wystąpienie:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

Aby uzyskać więcej informacji dotyczących projektowania na temat korzystania ze skalowania automatycznego, zobacz [najlepsze rozwiązania w zakresie skalowania automatycznego](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Następne kroki
W tym samouczku tworzony jest zestaw skalowania maszyn wirtualnych. W tym samouczku omówiono:

> [!div class="checklist"]
> * Definiowanie witryny usług IIS do skalowania za pomocą rozszerzenia niestandardowego skryptu
> * Tworzenie modułu równoważenia obciążenia dla zestawu skalowania
> * Tworzenie zestawu skalowania maszyn wirtualnych
> * Zwiększanie lub zmniejszanie liczby wystąpień w zestawie skalowania
> * Tworzenie reguł skalowania automatycznego

Przejdź do kolejnego samouczka, aby uzyskać więcej informacji o pojęciach dotyczących równoważenia obciążenia dla maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Równoważenie obciążenia maszyn wirtualnych](tutorial-load-balancer.md)
