---
title: Najlepsze rozwiązania dotyczące sieci Service Fabric platformy Azure
description: Najlepsze rozwiązania i zagadnienia dotyczące projektowania dotyczące zarządzania łącznością sieciową za pomocą usługi Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551798"
---
# <a name="networking"></a>Networking

Podczas tworzenia klastrów Service Fabric platformy Azure i zarządzania nimi można zapewnić łączność sieciową dla węzłów i aplikacji. Zasoby sieci obejmują zakresy adresów IP, sieci wirtualne, moduły równoważenia obciążenia i sieciowe grupy zabezpieczeń. W tym artykule przedstawiono najlepsze rozwiązania dotyczące tych zasobów.

Przejrzyj [wzorce sieci Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) platformy Azure, aby dowiedzieć się, jak tworzyć klastry korzystające z następujących funkcji: istniejąca sieć wirtualna lub podsieć, statyczny publiczny adres IP, moduł równoważenia obciążenia wyłącznie wewnętrznie lub wewnętrzny i zewnętrzny moduł równoważenia obciążenia.

## <a name="infrastructure-networking"></a>Sieć infrastruktury
Zmaksymalizuj wydajność maszyny wirtualnej za pomocą przyspieszonej sieci, deklarując Właściwość enableAcceleratedNetworking w szablonie Menedżer zasobów, Poniższy fragment kodu jest NetworkInterfaceConfigurations zestawu skalowania maszyn wirtualnych. Włącza przyspieszone sieci:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Klaster Service Fabric może być inicjowany w systemie [Linux przy użyciu przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)i [systemu Windows z przyspieszoną siecią](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Przyspieszona sieć jest obsługiwana w przypadku jednostek SKU serii maszyn wirtualnych platformy Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 i MS/MMS. Przyspieszona sieć została pomyślnie przetestowana przy użyciu jednostki SKU Standard_DS8_v3 na 1/23/2019 dla Service Fabric klastra systemu Windows i przy użyciu Standard_DS12_v2 na 01/29/2019 dla klastra Service Fabric Linux.

Aby włączyć przyspieszone sieci w istniejącym klastrze Service Fabric, należy najpierw [skalować klaster Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), aby wykonać następujące czynności:
1. Inicjowanie obsługi administracyjnej NodeType z włączoną obsługą przyspieszonej sieci
2. Migrowanie usług i ich stanu do aprowizacji NodeType z włączoną obsługą przyspieszonej sieci

Skalowanie w górę infrastruktury jest wymagane do włączenia przyspieszonej sieci w istniejącym klastrze, ponieważ włączenie przyspieszonej sieci spowoduje przestoje, ponieważ wymaga ono zatrzymania i cofnięcia przydziału wszystkich maszyn wirtualnych w zestawie dostępności [przed włączeniem przyspieszonej sieci na dowolnej istniejącej karcie sieciowej](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Sieć klastrów

* Klastry Service Fabric można wdrożyć w istniejącej sieci wirtualnej, wykonując czynności opisane w temacie [Service Fabric wzorców sieci](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są zalecane w przypadku typów węzłów, które ograniczają ruch przychodzący i wychodzący do klastra. Upewnij się, że wymagane porty są otwarte w sieciowej grupy zabezpieczeń. Na przykład: ![Service Fabric reguł sieciowej grupy zabezpieczeń][NSGSetup]

* Typ węzła podstawowego, który zawiera Service Fabric usług systemu nie musi być narażony za pośrednictwem zewnętrznego modułu równoważenia obciążenia i może być narażony przez [wewnętrzny moduł równoważenia obciążenia](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Użyj [statycznego publicznego adresu IP](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) dla klastra.

## <a name="application-networking"></a>Sieć aplikacji

* Aby można było uruchomić obciążenia kontenerów systemu Windows, należy użyć [trybu otwartej sieci](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) do ułatwienia komunikacji między usługami.

* Użyj zwrotnego serwera proxy, takiego jak [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) lub [Service Fabric zwrotnego serwera proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) w celu udostępnienia typowych portów aplikacji, takich jak 80 lub 443.

* W przypadku kontenerów systemu Windows hostowanych na maszynach gapped powietrznych, które nie mogą pobierać warstw podstawowych z magazynu w chmurze platformy Azure, Zastąp zachowanie warstwy obcej przy użyciu flagi [--Allow-undystrybucyjne-artefakty](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) w demona platformy Docker.

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
