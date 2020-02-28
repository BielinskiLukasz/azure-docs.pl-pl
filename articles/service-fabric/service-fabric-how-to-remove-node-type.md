---
title: Usuwanie typu węzła w usłudze Azure Service Fabric | Microsoft Docs
description: Dowiedz się, jak usunąć typ węzła z klastra Service Fabric działającego na platformie Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: d8ee2327f65332d32038806f2d2416cac190875b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661980"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Jak usunąć Service Fabric typ węzła
W tym artykule opisano sposób skalowania klastra Service Fabric platformy Azure przez usunięcie istniejącego typu węzła z klastra. Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno. Po utworzeniu klastra Service Fabric można skalować klaster w poziomie, usuwając typ węzła (zestaw skalowania maszyn wirtualnych) i wszystkie jego węzły.  Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.  W miarę skalowania klastra aplikacje są automatycznie skalowane.

> [!WARNING]
> Korzystanie z tego podejścia do usuwania typu węzła z klastra produkcyjnego nie jest zalecane. Jest to niebezpieczne polecenie, ponieważ usuwa zasób zestawu skalowania maszyn wirtualnych za typem węzła. 

## <a name="durability-characteristics"></a>Charakterystyki trwałości
W przypadku korzystania z polecenia Remove-AzServiceFabricNodeType zabezpieczenia są ustalane jako priorytetowe. Typ węzła musi być [poziomem trwałości](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)Silver lub Gold, ponieważ:
- Pakiet Bronze nie zapewnia żadnych gwarancji dotyczących zapisywania informacji o stanie.
- Pułapki srebrne i złota są dowolnymi zmianami w zestawie skalowania.
- Gold oferuje również kontrolę nad aktualizacjami platformy Azure w zestawie skalowania.

Service Fabric "organizuje" podstawowe zmiany i aktualizacje, aby dane nie zostały utracone. Jednak po usunięciu typu węzła z trwałością Bronze mogą zostać utracone informacje o stanie. Jeśli usuniesz typ węzła podstawowego, a aplikacja jest bezstanowa, akceptowalny jest brązowy. W przypadku uruchamiania obciążeń stanowych w środowisku produkcyjnym minimalna konfiguracja powinna być Silver. Podobnie w przypadku scenariuszy produkcyjnych typ węzła podstawowego powinien zawsze mieć wartość Silver lub Gold.

### <a name="more-about-bronze-durability"></a>Więcej informacji na temat trwałości Bronze

W przypadku usuwania typu węzła, który jest brązowy, wszystkie węzły w typie węzła zostaną natychmiast umieszczone w dół. Service Fabric nie jest Zalewka żadnych aktualizacji zestawu skalowania, a więc wszystkie maszyny wirtualne natychmiast przechodzą w dół. Jeśli w tych węzłach istniały stanowe elementy, dane są tracone. Teraz, nawet jeśli nie są bezstanowe, wszystkie węzły w Service Fabric biorą udział w pierścieniu, dzięki czemu cała klub może zostać utracona, co może spowodować utratę samego klastra.

## <a name="remove-a-non-primary-node-type"></a>Usuń typ węzła innego niż podstawowy

1. Przed rozpoczęciem procesu należy wziąć pod uwagę te wymagania wstępne.

    - Klaster jest w dobrej kondycji.
    - Po usunięciu węzła nie będzie dostępna wystarczająca pojemność, np. Liczba węzłów do umieszczenia wymaganej liczby replik.

2. Przenieś wszystkie usługi, które mają ograniczenia położenia, aby użyć typu węzła poza typem węzła.

    - Zmodyfikuj manifest aplikacji/usługi, aby nie odwoływać się już do typu węzła.
    - Wdróż zmianę.

    Następnie sprawdź, czy:
    - Wszystkie usługi zmodyfikowane powyżej nie są już uruchomione w węźle należącym do typu węzła.
    - Wszystkie usługi są w dobrej kondycji.

3. Usuń oznaczenie typu węzła jako innego niż podstawowy (Pomiń w przypadku typów węzłów innych niż podstawowe)

    - Znajdź szablon Azure Resource Manager używany do wdrożenia.
    - Znajdź sekcję powiązaną z typem węzła w sekcji Service Fabric.
    - Zmień właściwość isprimary na wartość false. \* * Nie usuwaj sekcji powiązanej z typem węzła w tym zadaniu.
    - Wdróż zmodyfikowany szablon Azure Resource Manager. \* * W zależności od konfiguracji klastra ten krok może chwilę potrwać.
    
    Następnie sprawdź, czy:
    - Sekcja Service Fabric w portalu wskazuje, że klaster jest gotowy.
    - Klaster jest w dobrej kondycji.
    - Żaden z węzłów należących do typu węzła nie jest oznaczony jako węzeł inicjatora.

4. Wyłącz dane dla typu węzła.

    Połącz się z klastrem przy użyciu programu PowerShell, a następnie uruchom następujący krok.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - W przypadku trwałości Bronze Zaczekaj, aż wszystkie węzły zostaną wyłączone.
    - W przypadku trwałości srebra i złota niektóre węzły zostaną wyłączone, a reszta będzie w stanie wyłączać. Sprawdź kartę Szczegóły węzłów w polu wyłączanie stanu, jeśli są one zablokowane w celu zapewnienia kworum dla partycji usługi infrastruktury, a następnie Kontynuuj, aby kontynuować.

5. Zatrzymaj dane dla typu węzła.

    Połącz się z klastrem przy użyciu programu PowerShell, a następnie uruchom następujący krok.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Zaczekaj, aż wszystkie węzły typu węzeł są oznaczone jako w dół.
    
6. Usuń dane dla typu węzła.

    Połącz się z klastrem przy użyciu programu PowerShell, a następnie uruchom następujący krok.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Zaczekaj, aż wszystkie węzły zostaną usunięte z klastra. Węzły nie powinny być wyświetlane w SFX.

7. Usuń typ węzła z sekcji Service Fabric.

    - Znajdź szablon Azure Resource Manager używany do wdrożenia.
    - Znajdź sekcję powiązaną z typem węzła w sekcji Service Fabric.
    - Usuń sekcję odpowiadającą typowi węzła.
    - W przypadku klastrów Silver i wyższych trwałości zaktualizuj zasób klastra w szablonie i skonfiguruj zasady dotyczące kondycji w celu ignorowania kondycji aplikacji sieci szkieletowej:/systemu, dodając `applicationDeltaHealthPolicies` jak podano poniżej. Poniższe zasady powinny ignorować istniejące błędy, ale nie pozwalają na nowe błędy kondycji. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    Wdróż zmodyfikowany szablon Azure Resource Manager. \* * Ten krok zajmie trochę czasu, zwykle maksymalnie dwie godziny. To uaktualnienie spowoduje zmianę ustawień na InfrastructureService, w związku z czym wymagane jest ponowne uruchomienie węzła. W tym przypadku `forceRestart` jest ignorowany. 
    Parametr `upgradeReplicaSetCheckTimeout` określa maksymalny czas, który Service Fabric czeka, aż partycja będzie w stanie bezpiecznym, jeśli nie jest jeszcze w stanie bezpiecznym. Gdy sprawdzanie bezpieczeństwa zostanie zakończone dla wszystkich partycji w węźle, Service Fabric kontynuuje uaktualnianie w tym węźle.
    Wartość parametru `upgradeTimeout` można skrócić do 6 godzin, ale w celu zapewnienia maksymalnego poziomu bezpieczeństwa 12 godzin powinno być używane.

    Następnie sprawdź, czy:
    - Zasób Service Fabric w portalu pokazuje gotowość.

8. Usuń wszystkie odwołania do zasobów odnoszących się do typu węzła.

    - Znajdź szablon Azure Resource Manager używany do wdrożenia.
    - Usuń zestaw skalowania maszyn wirtualnych i inne zasoby związane z typem węzła z szablonu.
    - Wdróż zmiany.

    Następnie:
    - Poczekaj na zakończenie wdrożenia.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [charakterystyce trwałości](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)klastra.
- Dowiedz się więcej na temat [typów węzłów i Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md).
- Dowiedz się więcej na temat [skalowania klastra Service Fabric](service-fabric-cluster-scaling.md).
