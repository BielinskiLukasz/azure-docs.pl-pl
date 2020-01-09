---
title: Uaktualnianie konfiguracji klastra usługi Azure Service Fabric
description: Dowiedz się, jak uaktualnić konfigurację, w której działa klaster Service Fabric na platformie Azure przy użyciu szablonu Menedżer zasobów.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463285"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Uaktualnianie konfiguracji klastra na platformie Azure 

W tym artykule opisano sposób dostosowywania różnych ustawień sieci szkieletowej dla klastra Service Fabric. W przypadku klastrów hostowanych na platformie Azure można dostosować ustawienia za pomocą [Azure Portal](https://portal.azure.com) lub szablonu Azure Resource Manager.

> [!NOTE]
> Nie wszystkie ustawienia są dostępne w portalu i [najlepszym rozwiązaniem jest dostosowanie go przy użyciu szablonu Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). Portal jest przeznaczony wyłącznie dla Service Fabric scenariusza Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Dostosowywanie ustawień klastra przy użyciu szablonów Menedżer zasobów
Klastry platformy Azure można skonfigurować za pomocą szablonu Menedżer zasobów JSON. Aby dowiedzieć się więcej na temat różnych ustawień, zobacz [Ustawienia konfiguracji klastrów](service-fabric-cluster-fabric-settings.md). Przykładowo w poniższych krokach pokazano, jak dodać nowe ustawienie *MaxDiskQuotaInMB* do sekcji *diagnostyka* przy użyciu Azure Resource Explorer.

1. Przejdź do usługi https://resources.azure.com
2. Przejdź do swojej subskrypcji, rozwijając **subskrypcje** ->  **\<subskrypcję >**  -> **resourceGroups** -> \<**grupy zasobów** > -> **dostawców -> dostawcy** -> **Microsoft. servicefabric** -> 
3. W prawym górnym rogu wybierz pozycję **Odczyt/zapis.**
4. Wybierz pozycję **Edytuj** i zaktualizuj element `fabricSettings` JSON i Dodaj nowy element:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Ustawienia klastra można również dostosować w jeden z następujących Azure Resource Manager sposobów:

- Użyj [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) , aby wyeksportować i zaktualizować szablon Menedżera zasobów.
- Użyj [programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) , aby wyeksportować i zaktualizować szablon Menedżer zasobów.
- Użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) , aby wyeksportować i zaktualizować szablon Menedżer zasobów.
- Aby zmodyfikować ustawienie bezpośrednio, Użyj poleceń Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) i [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) .
- Aby zmodyfikować ustawienie bezpośrednio, użyj interfejsu wiersza polecenia platformy Azure [AZ SF Cluster Setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) .

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [ustawieniach klastra Service Fabric](service-fabric-cluster-fabric-settings.md).
* Dowiedz się [, jak skalować klaster w programie i na zewnątrz](service-fabric-cluster-scale-up-down.md).
