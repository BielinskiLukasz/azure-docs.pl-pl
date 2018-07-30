---
title: Omówienie usługi Azure Service Fabric Mesh | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure Service Fabric Mesh. Za pomocą usługi Service Fabric Mesh możesz wdrożyć i skalować aplikację bez martwienia się o wymagania infrastruktury aplikacji.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 06/27/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 65a9b1afcc0e1e6d4fcbb60a38ab0764e6fe2f18
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226448"
---
# <a name="what-is-service-fabric-mesh"></a>Co to jest Service Fabric Mesh?

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Aplikacje hostowane w usłudze Service Fabric Mesh są uruchamiane i skalowane bez konieczności martwienia się o ich infrastrukturę.  Usługa Service Fabric Mesh składa się z klastrów tysięcy maszyn.  Wszystkie operacje klastrów są ukryte przed deweloperem. Wystarczy przekazać swój kod i określić zasoby, których potrzebujesz, wymagania w zakresie dostępności oraz limity zasobów.  Usługa Service Fabric Mesh automatycznie alokuje infrastrukturę żądaną przez wdrożenie aplikacji i obsługuje także błędy infrastruktury, zapewniając wysoką dostępność aplikacji. Musisz zadbać jedynie o kondycję i szybkość reakcji aplikacji, nie martwiąc się o infrastrukturę.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Ten artykuł zawiera omówienie najważniejszych korzyści zapewnianych przez usługę Service Fabric Mesh.

## <a name="great-developer-experience"></a>Wspaniałe środowisko programowania

Usługa Service Fabric Mesh obsługuje dowolny język lub strukturę programowania, które można uruchomić w kontenerze. Obsługa narzędzi programów Visual Studio 2017 i Visual Studio Code udostępnia zaawansowane środowisko edycji i debugowania dla aplikacji .NET i .NET Core. 

Usługa Service Fabric Mesh umożliwia:

- Migrowanie aplikacji metodą „lift-and-shift” do kontenerów w celu modernizacji i uruchamiania istniejących aplikacji na dużą skalę. 
- Tworzenie i wdrażanie nowych aplikacji mikrousług na dużą skalę na platformie Azure.  Integrację z innymi usługami platformy Azure lub istniejącymi aplikacjami działającymi w kontenerach. Każda mikrousługa jest częścią bezpiecznej aplikacji izolowanej od sieci, z zasadami zarządzania zasobami zdefiniowanymi dla rdzeni procesora CPU, pamięci, miejsca na dysku i innymi.
- Integrację z istniejącymi aplikacjami i rozszerzenie ich bez wprowadzania zmian w tych aplikacjach. Połączenie istniejącej aplikacji z nową aplikacją za pomocą własnej sieci wirtualnej.  
- Modernizowanie istniejących aplikacji usług Cloud Services przez migrowanie ich do usługi Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Prosty operacyjny cykl życia

Łatwe zarządzanie uruchomionymi aplikacjami, w tym uaktualnienia aplikacji oraz zarządzanie ich wersjami, monitorowanie aplikacji i debugowanie w środowisku produkcyjnym. Te aplikacje mogą składać się z jednej mikrousługi lub wielu mikrousług izolowanych we własnej sieci. Aplikacje działają efektywnie dzięki szybkiemu wdrażaniu, umieszczaniu i przełączaniu w tryb failover.

Usługa Service Fabric Mesh umożliwia:

- Wdrażanie aplikacji i zarządzanie nimi bez konieczności jawnego aprowizowania infrastruktury i zarządzania nią.  Usługa Service Fabric Mesh aprowizuje, uaktualnia, stosuje poprawki i utrzymuje podstawową infrastrukturę za Ciebie.
- Skonfigurowanie ciągłej integracji przy użyciu zintegrowanych narzędzi w celu łatwego pakowania i wdrażania aplikacji.
- Korzystanie ze wszystkich funkcji zasobów usługi Azure Resource Manager (na przykład dziennika inspekcji i [kontroli dostępu opartej na rolach (RBAC)](/azure/role-based-access-control/overview)), ponieważ wszystkie zasoby (na przykład aplikacje, usługi, wpisy tajne itd.) wdrażane w usłudze Service Fabric Mesh na platformie Azure są zasobami usługi Azure Resource Manager. 
- Wdrażanie zasobów i zarządzanie nimi za pomocą [witryny Azure Portal](https://portal.azure.com), szablonów usługi Resource Manager lub bibliotek interfejsu wiersza polecenia platformy Azure/programu PowerShell.
- Konfigurowanie operacyjnego monitorowania i zgłaszania alertów za pomocą usługi [Application Insights](/azure/application-insights/) (lub wybranego narzędzia) w celu przechwytywania śladów operacyjnych i diagnostycznych z platformy. 
- Uzyskiwanie dostępu do informacji diagnostycznych dotyczących aplikacji emitowanych przez model aplikacji przy użyciu [usługi Application Insights](/azure/application-insights/) lub wybranego narzędzia.
- Optymalizowanie użycia zasobów przez określenie reguł skalowania automatycznego dla usług w definicji aplikacji.  (już wkrótce)
- Tworzenie izolacji sieci i granic zabezpieczeń dla aplikacji — zaawansowana funkcja w połączeniu z kontenerami funkcji Hyper-V. Izolowanie ruchu sieciowego do i z usług za pomocą wielu adresów IP na usługę i izolowanych sieci wirtualnych na aplikację.  (już wkrótce) 


## <a name="mission-critical-platform-capabilities"></a>Możliwości platformy o kluczowym znaczeniu

Usługa Service Fabric Mesh tworzy kolekcję klastrów, które obejmują [strefy dostępności platformy Azure](/azure/availability-zones/az-overview) i/lub geopolityczne granice regionalne. Aplikacje są opisane z zestawem intencji, takich jak skala, wymagania sprzętowe, wymagania dotyczące trwałości oraz zasady zabezpieczeń.  Po wdrożeniu aplikacji usługa Service Fabric Mesh znajduje optymalne miejsce do jej uruchomienia.

Usługa Service Fabric Mesh umożliwia:

- Korzystanie z wysokiej dostępności, skalowania na zewnątrz/do wewnątrz, możliwości odnajdywania, orkiestracji, routingu komunikatów, niezawodnej obsługi komunikatów, uaktualnień bez przestojów, zarządzania zabezpieczeniami/wpisami tajnymi, odzyskiwania po awarii, zarządzania stanem, zarządzania konfiguracją i transakcji rozproszonych.
- Wybieranie spośród wielu modeli aplikacji podczas tworzenia aplikacji.
- Używanie możliwości platformy udostępnianych przez punkty końcowe REST poprzez korzystanie z powiązań specyficznych dla języka wygenerowanych za pomocą struktury Swagger.
- Wdrażanie aplikacji w [strefach dostępności](/azure/availability-zones/az-overview) i w wielu regionach na potrzeby niezawodności replikacji geograficznej.
- Korzystanie ze wszystkich funkcji zabezpieczeń i zgodności udostępnianych przez platformę Azure.

## <a name="next-steps"></a>Następne kroki

Wystarczy parę kroków, aby wdrożyć przykładowy projekt za pomocą programu Visual Studio. Aby uzyskać więcej informacji, zobacz [Tworzenie witryny internetowej platformy ASP.NET Core](service-fabric-mesh-quickstart-dotnet-core.md). 


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
