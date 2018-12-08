---
title: Automatyczne skalowanie na aplikacja działająca na platformie Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady automatycznego skalowania dla usług aplikacji usługi Service Fabric siatki.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94b1b4cfbc5e7a96be389f315a1c58dc311c60a0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104951"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Utwórz zasady automatycznego skalowania dla aplikacji usługi Service Fabric siatki
Jedną z głównych zalet wdrażania aplikacji w usłudze Service Fabric Mesh jest możliwość łatwego skalowania usług w pionie lub poziomie. Powinno to służyć do obsługi różnych ilości obciążenia w usługach lub do poprawiania dostępności. Można ręcznie skalować swoje usługi wewnątrz lub na zewnątrz lub skonfiguruj zasady skalowania automatycznego.

[Automatyczne skalowanie](service-fabric-mesh-scalability.md#autoscaling-service-instances) umożliwia dynamiczne skalowanie liczby wystąpień usługi (skalowanie w poziomie). Automatyczne skalowanie zapewnia dużą elastyczność i umożliwia inicjowanie obsługi administracyjnej lub usuwania wystąpień usługi na podstawie użycia procesora CPU lub pamięci.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opcje automatycznego skalowania zasad, wyzwalacz i mechanizm tworzenia
Zasady skalowania automatycznie jest zdefiniowana dla każdej usługi, który chcesz skalować. Zasada jest zdefiniowana w pliku YAML zasób usługi lub szablon wdrożenia JSON. Wszystkie zasady skalowania składa się z dwóch części: wyzwalacz i mechanizm skalowania.

Wyzwalacz definiuje, gdy zostanie wywołana zasadę automatycznego skalowania.  Określ rodzaj metryki monitorowania (procesor CPU lub pamięci) i wyzwalaczy (średnie obciążenie).  Progi obciążenia górny i dolny, określony jako wartość procentowa. Interwału skalowania definiuje, jak często chcesz sprawdzać (w sekundach) określonego wykorzystania (na przykład średnie obciążenie procesora CPU) we wszystkich wystąpieniach w obecnie wdrożonej usłudze.  Ten mechanizm jest wyzwalany, gdy monitorowane metryki spadnie poniżej niższego progu, lub zwiększa powyżej górnej wartości progowej.  

Mechanizm skalowania definiuje sposób wykonywania operacji skalowania po wyzwoleniu zasad.  Określ rodzaj mechanizmu (Dodawanie/usuwanie repliki), repliki minimalnej i maksymalnej liczby (jako liczby całkowite).  Liczba replik usługi nigdy nie będzie można skalować, poniżej minimalnej liczby lub powyżej maksymalna liczba.  Przyrost skalowania można również określić jako liczba całkowita, która jest liczba replik, które zostaną dodane lub usunięte w ramach operacji skalowania.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Zdefiniuj automatycznie zasady skalowania w szablonie JSON

Poniższy przykład pokazuje zasadę automatycznego skalowania w szablonie wdrożenia JSON.  Zasady skalowania automatycznego jest zadeklarowana we właściwości usługi można skalować.  W tym przykładzie zdefiniowano wyzwalacz średnie obciążenie procesora CPU.  Mechanizm zostanie wyzwolony, jeśli średnie użycie procesora CPU nie można załadować wszystkich wdrożonych wystąpień spadnie poniżej 0.2 (20%) lub zbliża się powyżej 0,8 (80%).  Obciążenie procesora CPU jest sprawdzane co 60 sekund.  Mechanizm skalowania jest zdefiniowany do dodawania lub usuwania wystąpień, jeśli zasady są wyzwalane.  Wystąpienia usługi zostaną dodane lub usunięte z przyrostem równym jedną.  Minimalna liczba wystąpień jednego i maksymalną liczbę wystąpień 40 jest również definiowany.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Zdefiniuj zasady skalowania automatycznego w pliku zasobów service.yaml
Poniższy przykład pokazuje zasady skalowania automatycznego w pliku zasobów (YAML) usługi.  Zasady skalowania automatycznego jest zadeklarowana jako właściwość, usługi skalowania.  W tym przykładzie zdefiniowano wyzwalacz średnie obciążenie procesora CPU.  Mechanizm zostanie wyzwolony, jeśli średnie użycie procesora CPU nie można załadować wszystkich wdrożonych wystąpień spadnie poniżej 0.2 (20%) lub zbliża się powyżej 0,8 (80%).  Obciążenie procesora CPU jest sprawdzane co 60 sekund.  Mechanizm skalowania jest zdefiniowany do dodawania lub usuwania wystąpień, jeśli zasady są wyzwalane.  Wystąpienia usługi zostaną dodane lub usunięte z przyrostem równym jedną.  Minimalna liczba wystąpień jednego i maksymalną liczbę wystąpień 40 jest również definiowany.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metricName: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [ręczne skalowanie usługi](service-fabric-mesh-tutorial-template-scale-services.md)
