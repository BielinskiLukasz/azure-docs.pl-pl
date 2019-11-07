---
title: Skalowalność aplikacji usługi Azure Service Fabric siatka | Microsoft Docs
description: Jedną z zalet wdrażania aplikacji do Service Fabric siatki jest możliwość łatwego skalowania usług — ręcznie lub za pomocą zasad skalowania automatycznego.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 59fdf68ed1ead4665ec8944d67f2d5112d370716
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73662995"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skalowanie aplikacji Service Fabric siatki

Jedną z głównych zalet wdrażania aplikacji do Service Fabric siatki jest możliwość łatwego skalowania usług do lub wyprowadzenia. Ta usługa powinna być używana do obsługi różnych ilości obciążeń w usługach lub do zwiększenia dostępności. Możesz ręcznie skalować usługi w lub na zewnątrz lub skonfigurować zasady skalowania automatycznego.

## <a name="manual-scaling-instances"></a>Ręczne skalowanie wystąpień

W szablonie wdrażania dla zasobu aplikacji każda usługa ma właściwość *replicaCount*, która może służyć do ustawiania liczby wdrożeń usługi. Aplikacja może składać się z wielu usług, z których każda ma unikatową liczbę właściwości *replicaCount* oraz które są razem wdrażane i zarządzane. Aby skalować liczbę replik usług, zmodyfikuj wartość *replicaCount* dla każdej usługi, którą chcesz skalować, w szablonie wdrożenia lub pliku parametrów. Następnie uaktualnij aplikację.

Przykłady ręcznego skalowania wystąpień usług można znaleźć [w temacie ręczne skalowanie usług w programie lub na zewnątrz](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Automatyczne skalowanie wystąpień usługi
Skalowanie automatyczne jest dodatkową możliwością Service Fabric do dynamicznego skalowania liczby wystąpień usługi (skalowanie w poziomie). Skalowanie automatyczne zapewnia doskonałą elastyczność i umożliwia inicjowanie obsługi lub usuwanie wystąpień usługi na podstawie użycia procesora lub pamięci.  Funkcja automatycznego skalowania umożliwia uruchamianie odpowiedniej liczby wystąpień usługi dla obciążenia i optymalizację pod kątem kosztów.

Zasady automatycznego skalowania są definiowane dla każdej usługi w pliku zasobów usługi. Każda zasada skalowania składa się z dwóch części:

- Wyzwalacz skalowania, który opisuje, kiedy zostanie wykonane skalowanie usługi. Istnieją trzy czynniki, które określają, kiedy usługa będzie skalowana. *Dolny próg obciążenia* jest wartością, która określa, kiedy usługa będzie skalowana w programie. Jeśli średnie obciążenie wszystkich wystąpień partycji jest mniejsze niż ta wartość, usługa zostanie przeskalowana w poziomie. *Górny próg obciążenia* jest wartością, która określa, kiedy usługa będzie skalowana w poziomie. Jeśli średnie obciążenie wszystkich wystąpień partycji jest wyższe niż ta wartość, usługa zostanie przeskalowana w poziomie. *Interwał skalowania* określa, jak często (w sekundach) zostanie sprawdzony wyzwalacz. Po sprawdzeniu wyzwalacza, jeśli jest wymagany, mechanizm zostanie zastosowany. Jeśli skalowanie nie jest wymagane, nie zostanie podjęta żadna akcja. W obu przypadkach wyzwalacz nie zostanie ponownie sprawdzony przed upływem interwału skalowania.

- Mechanizm skalowania, który opisuje, jak skalowanie zostanie wykonane po jego wyzwoleniu. *Przyrost skali* określa liczbę wystąpień, które zostaną dodane lub usunięte, gdy mechanizm zostanie wyzwolony. *Maksymalna liczba wystąpień* definiuje górny limit skalowania. Jeśli liczba wystąpień osiągnie ten limit, usługa nie zostanie skalowana w poziomie niezależnie od obciążenia. *Minimalna liczba wystąpień* definiuje dolny limit skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, usługa nie będzie skalowana w przypadku, gdy jest to możliwe.

Aby dowiedzieć się, jak ustawić zasady automatycznego skalowania dla usługi, Przeczytaj [usługi skalowania automatycznego](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Następne kroki

Informacje o modelu aplikacji można znaleźć w temacie [Service Fabric Resources](service-fabric-mesh-service-fabric-resources.md)
