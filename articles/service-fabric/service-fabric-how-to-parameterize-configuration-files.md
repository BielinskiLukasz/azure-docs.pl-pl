---
title: Pliki konfiguracji Sparametryzuj na platformie Azure Service Fabric
description: Dowiedz się, jak Sparametryzuj pliki konfiguracyjne w Service Fabric, przydatną techniką w zarządzaniu wieloma środowiskami.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75644634"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak Sparametryzuj pliki konfiguracji w Service Fabric

W tym artykule opisano sposób Sparametryzuj pliku konfiguracji w Service Fabric.  Jeśli nie znasz już podstawowych pojęć związanych z zarządzaniem aplikacjami dla wielu środowisk, przeczytaj artykuł [Zarządzanie aplikacjami w wielu środowiskach](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedura dla plików konfiguracji parametryzacja

W tym przykładzie zastąpisz wartość konfiguracji przy użyciu parametrów we wdrożeniu aplikacji.

1. Otwórz plik * \<MyService>\PackageRoot\Config\Settings.xml* w projekcie usługi.
1. Ustaw nazwę i wartość parametru konfiguracji, na przykład rozmiar pamięci podręcznej równej 25, dodając następujący kod XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Zapisz i zamknij plik.
1. Otwórz plik * \<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* .
1. W pliku ApplicationManifest.xml Zadeklaruj parametr i wartość domyślną w `Parameters` elemencie.  Zaleca się, aby nazwa parametru zawierała nazwę usługi (na przykład "Moja usługa").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. W `ServiceManifestImport` sekcji pliku ApplicationManifest.xml Dodaj `ConfigOverrides` `ConfigOverride` element i, odwołując się do pakietu konfiguracyjnego, sekcji i parametru.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> W przypadku dodania elementu ConfigOverride, Service Fabric zawsze wybiera parametry aplikacji lub wartość domyślną określoną w manifeście aplikacji.
>
>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat innych funkcji zarządzania aplikacjami, które są dostępne w programie Visual Studio, zobacz [Zarządzanie aplikacjami Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).
