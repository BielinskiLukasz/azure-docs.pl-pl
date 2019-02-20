---
title: Samouczek — wysoka dostępność dla maszyn wirtualnych z systemem Windows na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak za pomocą programu Azure PowerShell wdrażać maszyny wirtualne o wysokiej dostępności w zestawach dostępności
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3ee9740f9ef7e364c47bb205315683d1e4ea9294
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977132"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Samouczek: tworzenie i wdrażanie maszyn wirtualnych o wysokiej dostępności za pomocą programu Azure PowerShell

Z tego samouczka dowiesz się, jak zwiększyć dostępność i niezawodność maszyn wirtualnych przy użyciu funkcji zestawów dostępności. Zestawy dostępności zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure między wieloma izolowanymi węzłami sprzętowymi w klastrze. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych
> * Sprawdzanie usługi Azure Advisor


## <a name="availability-set-overview"></a>Zestaw dostępności — omówienie

Zestaw dostępności to możliwość grupowania logicznego na potrzeby izolowania zasobów maszyny wirtualnej od siebie w przypadku ich wdrażania. Platforma Azure zapewnia, że maszyny wirtualne umieszczone w zestawie dostępności korzystają z wielu serwerów fizycznych, regałów obliczeniowych, jednostek magazynowych i przełączników sieciowych. Ewentualna awaria sprzętu lub oprogramowania ma wpływ tylko na podzestaw maszyn wirtualnych, a całe rozwiązanie nadal działa. Zestawy dostępności są niezbędne do tworzenia niezawodnych rozwiązań w chmurze.

Rozważmy typowe rozwiązanie z użyciem maszyn wirtualnych, obejmujące cztery serwery internetowe frontonu oraz 2 maszyny wirtualne zaplecza. Przed wdrożeniem maszyn wirtualnych na platformie Azure należałoby w takim przypadku zdefiniować dwa zestawy dostępności: jeden dla warstwy internetowej, a drugi dla warstwy zaplecza. Podczas tworzenia nowej maszyny wirtualnej należy określić zestaw dostępności jako parametr. Platforma Azure zapewnia, że maszyny wirtualne są izolowane na wielu fizycznych zasobach sprzętowych. W przypadku problemu ze sprzętem fizycznym, na którym uruchomiono jeden z serwerów, masz pewność, że pozostałe wystąpienia serwerów będą nadal działać, ponieważ korzystają z innego sprzętu.

Zestawy dostępności umożliwiają wdrażanie niezawodnych rozwiązań z użyciem maszyn wirtualnych na platformie Azure.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Sprzęt w jednej lokalizacji jest podzielony na wiele domen aktualizacji i domen błędów. **Domena aktualizacji** to grupa maszyn wirtualnych i używanego przez nie sprzętu fizycznego, które mogą być jednocześnie ponownie uruchamiane. Maszyny wirtualne w jednej **domenie błędów** korzystają ze wspólnego magazynu oraz ze wspólnego źródła zasilania i przełącznika sieciowego.  

Aby utworzyć zestaw dostępności, użyj polecenia [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). W tym przykładzie liczba domen aktualizacji i domen błędów wynosi *2*, a zestaw dostępności ma nazwę *myAvailabilitySet*.

Utwórz grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Utwórz zarządzany zestaw dostępności przy użyciu polecenia [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) z parametrem `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Tworzenie maszyn wirtualnych w zestawie dostępności
Aby zapewnić właściwe rozproszenie maszyn wirtualnych na sprzęcie, należy utworzyć je w ramach zestawu dostępności. Nie można dodać istniejącej, wcześniej utworzonej maszyny wirtualnej do zestawu dostępności. 


Podczas tworzenia maszyny wirtualnej przy użyciu polecenia [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parametr `-AvailabilitySetName` umożliwia określenie nazwy zestawu dostępności.

Najpierw ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz dwie maszyny wirtualne w zestawie dostępności, używając polecenia [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Utworzenie i skonfigurowanie obu maszyn wirtualnych może potrwać kilka minut. Po zakończeniu powstaną dwie maszyny wirtualne rozproszone między wiele elementów sprzętowych. 

Możesz wyświetlić zestaw dostępności w portalu, przechodząc do pozycji **Grupy zasobów** > **myResourceGroupAvailability** > **myAvailabilitySet**, aby zobaczyć, w jaki sposób maszyny wirtualne są rozmieszczone w dwóch domenach aktualizacji i błędów.

![Zestaw dostępności w portalu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Sprawdzanie dostępnych rozmiarów maszyn wirtualnych 

Możesz później dodać więcej maszyn wirtualnych do zestawu dostępności, ale potrzebujesz do tego informacji o rozmiarach maszyn wirtualnych udostępnianych przez sprzęt. Użyj polecenia [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize), aby wyświetlić listę wszystkich rozmiarów dostępnych w klastrze sprzętowym zestawu dostępności.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Sprawdzanie usługi Azure Advisor 

Możesz także uzyskać dodatkowe informacje na temat sposobów poprawy dostępności maszyn wirtualnych, korzystając z usługi Azure Advisor. Usługa Azure Advisor analizuje konfigurację i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com), wybierz pozycję **Wszystkie usługi**, a następnie wpisz **Advisor**. Pulpit nawigacyjny usługi Advisor będzie zawierać spersonalizowane zalecenia dotyczące wybranej subskrypcji. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych
> * Sprawdzanie usługi Azure Advisor

Przejdź do następnego samouczka, aby poznać zestawy skalowania maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie zestawu skalowania maszyn wirtualnych](tutorial-create-vmss.md)


