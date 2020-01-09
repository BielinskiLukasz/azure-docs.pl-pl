---
title: GMSA Instalatora usługi Azure Service Fabric Container Services
description: Dowiedz się teraz, jak skonfigurować konta usług zarządzane przez grupę (gMSA) dla kontenera działającego na platformie Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639211"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Skonfiguruj gMSA dla kontenerów systemu Windows uruchomionych w systemie Service Fabric

Aby skonfigurować gMSA (konta usług zarządzane przez grupę), plik specyfikacji poświadczeń (`credspec`) jest umieszczany na wszystkich węzłach w klastrze. Plik można skopiować na wszystkie węzły przy użyciu rozszerzenia maszyny wirtualnej.  Plik `credspec` musi zawierać informacje o koncie gMSA. Aby uzyskać więcej informacji na temat pliku `credspec`, zobacz [Tworzenie specyfikacji poświadczeń](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specyfikacja poświadczeń i znacznik `Hostname` są określone w manifeście aplikacji. Tag `Hostname` musi być zgodny z nazwą konta gMSA, w którym działa kontener.  Znacznik `Hostname` umożliwia kontener do samodzielnego uwierzytelnienia w innych usługach w domenie przy użyciu uwierzytelniania Kerberos.  Przykład służący do określania `Hostname` i `credspec` w manifeście aplikacji przedstawiono w następującym fragmencie kodu:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Następnym krokiem jest zapoznanie się z następującymi artykułami:

* [Wdrażanie kontenera systemu Windows w celu Service Fabric w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w celu Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)
