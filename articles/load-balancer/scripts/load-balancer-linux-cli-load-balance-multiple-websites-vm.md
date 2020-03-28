---
title: Równoważenie obciążenia wielu witryn sieci Web — azure cli — Azure Load Balancer
description: Ten przykładowy skrypt wiersza polecenia platformy Azure przedstawia sposób równoważenia obciążenia wielu witryn internetowych do tej samej maszyny wirtualnej
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.openlocfilehash: 80ec18ca722b9833307fc270c710abd93c6fb6f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76274619"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Przykładowy skrypt interfejsu wiersza polecenia platformy Azure: równoważenie obciążenia wielu witryn internetowych

Ten przykładowy skrypt wiersza polecenia platformy Azure tworzy sieć wirtualną z 2 maszynami wirtualnymi, które są elementami członkowskimi zestawu dostępności. Moduł równoważenia obciążenia kieruje ruch dla 2 osobnych adresów IP do 2 maszyn wirtualnych. Po uruchomieniu skryptu możesz wdrożyć oprogramowanie serwera internetowego na maszynach wirtualnych i hostować wiele witryn internetowych, z których każda będzie miała własny adres IP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | Tworzy publiczny adres IP przy użyciu statycznego adresu IP i skojarzonej nazwy DNS. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Tworzy moduł równoważenia obciążenia platformy Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | Tworzy sondę modułu równoważenia obciążenia. Sonda modułu równoważenia obciążenia służy do monitorowania każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia. Jeśli maszyna wirtualna stanie się niedostępna, ruch nie będzie do niej kierowany. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Tworzy regułę modułu równoważenia obciążenia. W tym przykładzie tworzona jest reguła dla portu 80. Ruch HTTP docierający do modułu równoważenia obciążenia jest kierowany do portu 80 jednej z maszyn wirtualnych w zestawie modułu równoważenia obciążenia. |
| [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) | Tworzy adres IP frontonu dla modułu równoważenia obciążenia. |
| [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) | Tworzy pulę adresów zaplecza. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | Tworzy wirtualną kartę sieciową i dołącza ją do sieci wirtualnej oraz podsieci. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Tworzy zestaw dostępności. Zestawy dostępności zapewniają działanie aplikacji bez przestojów dzięki rozmieszczeniu maszyn wirtualnych w ramach zasobów fizycznych tak, aby niepowodzenie nie wpływało na cały zestaw. |
| [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) | Tworzy konfigurację adresu IP. Funkcja Microsoft.Network/AllowMultipleIpConfigurationsPerNic musi być włączona dla subskrypcji. Jako podstawową konfigurację adresu IP można wyznaczyć tylko jedną konfigurację na kartę sieciową (przy użyciu flagi --make-primary). |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az-vm-availability-set-create) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia dla sieci można znaleźć w [dokumentacji i omówieniu sieci platformy Azure](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
