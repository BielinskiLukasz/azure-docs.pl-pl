---
title: Diagnozowanie problemu z routingiem sieciowym maszyny wirtualnej — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: W tym artykule dowiesz się, jak zdiagnozować problem z routingiem sieciowym maszyny wirtualnej przy użyciu funkcji następnego przeskoku Network Watcher platformy Azure.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: bf4c5e364b7f18b363f9915f54e43c7ea54c33c4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834683"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnozowanie problemu z routingiem sieciowym maszyny wirtualnej — interfejs wiersza polecenia platformy Azure

W tym artykule opisano wdrożenie maszyny wirtualnej, a następnie sprawdzenie komunikacji z adresem IP i adresem URL. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Po zweryfikowaniu wersji interfejsu wiersza polecenia uruchom polecenie `az login`, aby utworzyć połączenie z platformą Azure. Polecenie interfejsu wiersza polecenia w tym artykule są sformatowane do uruchamiania w powłoce bash.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny wirtualnej musisz utworzyć grupę zasobów, która będzie zawierała maszynę wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Wykonywanie pozostałych kroków możesz zacząć dopiero wtedy, gdy maszyna wirtualna zostanie utworzona, a interfejs wiersza polecenia zwróci dane wyjściowe.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikację sieciową z Network Watcher, należy najpierw włączyć obserwatora sieci w regionie, w którym ma zostać przetestowana maszyna wirtualna, a następnie użyć funkcji następnego przeskoku Network Watcher do testowania komunikacji.

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączony obserwator sieciowy w regionie Wschodnie stany USA, Pomiń, aby [użyć następnego skoku](#use-next-hop). Użyj polecenia [AZ Network obserwator Configure](/cli/azure/network/watcher#az-network-watcher-configure) , aby utworzyć obserwatora sieci w regionie Wschodnie stany USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Korzystanie z funkcji Następny przeskok

Na platformie Azure są automatycznie tworzone trasy do domyślnych miejsc docelowych. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy. Czasami użycie tras niestandardowych może spowodować niepowodzenie komunikacji. Aby przetestować Routing z maszyny wirtualnej, użyj [AZ Network obserwator show-Next-przeskok](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) , aby określić następny przeskok routingu, gdy ruch jest przeznaczony dla określonego adresu.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do jednego z adresów IP domeny www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Po kilku sekundach dane wyjściowe informuje o tym, że **nextHopType** jest **Internet**i że **routeTableId** jest **trasą systemową**. Dzięki temu wiadomo, że istnieje prawidłowa trasa do miejsca docelowego.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do adresu 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Zwrócone dane wyjściowe informuje o tym, że **żaden** z nich nie jest **NextHopType**i że **RouteTableId** jest również **trasą systemową**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego, ale nie ma następnego przeskoku umożliwiającego kierowanie ruchu do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

Aby dodatkowo analizować Routing, przejrzyj efektywne trasy dla interfejsu sieciowego za pomocą polecenia [AZ Network nic show-skuteczna-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) :

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Następujący tekst jest zawarty w zwracanych danych wyjściowych:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Gdy użyto `az network watcher show-next-hop` polecenia do testowania komunikacji wychodzącej do 13.107.21.200 w [następnym przeskoku](#use-next-hop), trasa z **addressPrefix** 0.0.0.0/0 * * została użyta do kierowania ruchu do adresu, ponieważ żadna inna trasa w danych wyjściowych nie zawiera adresu. Domyślnie wszystkie adresy, które nie zostały określone w prefiksie adresu innej trasy, są kierowane do Internetu.

W przypadku użycia polecenia `az network watcher show-next-hop` do testowania komunikacji wychodzącej na 172.31.0.100 jednak wynik informuje o braku typu następnego przeskoku. W zwracanych danych wyjściowych zobaczysz również następujący tekst:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Jak widać w danych wyjściowych polecenia `az network watcher nic show-effective-route-table`, chociaż istnieje trasa domyślna do prefiksu 172.16.0.0/12, który zawiera adres 172.31.0.100, **nextHopType** ma **wartość None**. Platforma Azure tworzy domyślną trasę dla zakresu adresów 172.16.0.0/12, ale nie określa typu następnego przeskoku, jeśli nie jest to wymagane. Jeśli na przykład dodaliśmy zakres adresów 172.16.0.0/12 do przestrzeni adresowej sieci wirtualnej, platforma Azure zmieni **nextHopType** na **sieć wirtualną** dla trasy. W wyniku sprawdzenia zostanie wyświetlona **Sieć wirtualna** jako **nextHopType**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono maszynę wirtualną i zdiagnozowano Routing sieciowy z maszyny wirtualnej. Uzyskano informacje o tworzeniu tras domyślnych na platformie Azure i przetestowano routing do dwóch różnych miejsc docelowych. Uzyskaj więcej informacji na temat [routingu na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dowiedz się, jak [tworzyć trasy niestandardowe](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

W przypadku wychodzących połączeń maszyn wirtualnych można również określić opóźnienia i dozwolony i zabroniony ruch sieciowy między maszyną wirtualną a punktem końcowym za pomocą funkcji [rozwiązywania problemów z połączeniem](network-watcher-connectivity-cli.md) Network Watcher. Można monitorować komunikację między maszyną wirtualną a punktem końcowym, takim jak adres IP lub adres URL, w czasie przy użyciu funkcji Network Watcher monitor połączeń. Aby dowiedzieć się, jak to zrobić, zobacz [monitorowanie połączenia sieciowego](connection-monitor.md).