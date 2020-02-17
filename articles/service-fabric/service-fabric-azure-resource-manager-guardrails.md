---
title: Service Fabric wdrożenia Azure Resource Manager guardrails
description: Ten artykuł zawiera omówienie typowych błędów wykonywanych podczas wdrażania klastra Service Fabric za pomocą Azure Resource Manager i sposobu ich unikania.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368583"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
Podczas wdrażania klastra Service Fabric są umieszczane guardrails, co spowoduje niepowodzenie wdrożenia Azure Resource Manager w przypadku nieprawidłowej konfiguracji klastra. Poniższe sekcje zawierają omówienie typowych problemów z konfiguracją klastra oraz czynności wymagane w celu rozwiązania tych problemów. 

## <a name="durability-mismatch"></a>Niezgodność trwałości
### <a name="overview"></a>Omówienie
Wartość trwałości dla typu węzła Service Fabric jest definiowana w dwóch różnych częściach szablonu Azure Resource Manager. Sekcja rozszerzenia zestawu skalowania maszyn wirtualnych w ramach zasobu zestawu skalowania maszyn wirtualnych oraz sekcja typ węzła zasobu klastra Service Fabric. Wymagana jest zgodność wartości trwałości w tych sekcjach, w przeciwnym razie wdrażanie zasobów zakończy się niepowodzeniem.

Poniższa sekcja zawiera przykład zgodności trwałości między ustawieniem trwałości rozszerzenia zestawu skalowania maszyn wirtualnych a ustawieniem trwałości typu węzła Service Fabric:  

**Ustawienie trwałości zestawu skalowania maszyn wirtualnych**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Ustawienie trwałości typu węzła Service Fabric** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Komunikaty o błędach
* Niezgodność trwałości zestawu skalowania maszyn wirtualnych nie jest zgodna z bieżącym poziomem trwałości typu węzła Service Fabric.
* Trwałość zestawu skalowania maszyn wirtualnych nie jest zgodna z elementem docelowym typu węzła Service Fabric
* Trwałość zestawu skalowania maszyn wirtualnych jest zgodna z bieżącym poziomem trwałości Service Fabric lub poziomem trwałości typu węzła Service Fabric docelowego 

### <a name="mitigation"></a>Środki zaradcze
Aby rozwiązać niezgodność trwałości, która jest wskazywana przez którykolwiek z powyższych komunikatów o błędzie:
1. Zaktualizuj poziom trwałości w sekcji rozszerzenia zestawu skalowania maszyn wirtualnych lub Service Fabric typ węzła szablonu Azure Resource Manager, aby upewnić się, że wartości są zgodne.
2. Wdróż ponownie szablon Azure Resource Manager ze zaktualizowanymi wartościami.


## <a name="seed-node-deletion"></a>Usuwanie węzła inicjatora 
### <a name="overview"></a>Omówienie
Klaster Service Fabric ma właściwość [warstwy niezawodności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) , która jest używana do określania liczby replik usług systemowych, które są uruchomione na podstawowym typie węzła klastra. Liczba wymaganych replik określi minimalną liczbę węzłów, które muszą być utrzymywane w podstawowym typie klastra. Jeśli liczba węzłów w typie węzła podstawowego spadnie poniżej wymaganej wartości minimalnej dla warstwy niezawodności, klaster stanie się niestabilny.  

### <a name="error-messages"></a>Komunikaty o błędach 
Wykryto operację usuwania węzła inicjatora i zostanie on odrzucony. 
* Ta operacja spowoduje, że {0} potencjalnych węzłów inicjatora pozostanie w klastrze, podczas gdy {1} są niezbędne jako minimum.
* Usunięcie {0} węzłów inicjatora z {1} spowodują utratę kworum węzłów inicjatora. Maksymalna liczba węzłów inicjatora, które można usunąć w danym momencie, jest {2}.
 
### <a name="mitigation"></a>Środki zaradcze 
Upewnij się, że typ węzła podstawowego ma wystarczającą Virtual Machines, aby zapewnić niezawodność określoną w klastrze. Nie będzie można usunąć maszyny wirtualnej, jeśli zestaw skalowania maszyn wirtualnych zostanie ustawiony poniżej minimalnej liczby węzłów dla danej warstwy niezawodności.
* Jeśli warstwa niezawodności jest określona prawidłowo, upewnij się, że masz wystarczającą liczbę węzłów w podstawowym typie węzła, zgodnie z wymaganiami dla warstwy niezawodności. 
* Jeśli warstwa niezawodności jest niepoprawna, przed zainicjowaniem operacji zestawu skalowania maszyn wirtualnych należy zaczekać na zainicjowanie Service Fabric zmiany poziomu niezawodności, a następnie zaczekaj na jej zakończenie.
* Jeśli warstwa niezawodności jest Bronem, wykonaj następujące [kroki](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) , aby bezpiecznie skalować klaster.

## <a name="next-steps"></a>Następne kroki
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Rozwiązywanie problemów Service Fabric: [przewodniki dotyczące rozwiązywania problemów](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
