---
title: Infrastruktury jako kodu najlepsze rozwiązania platformy Azure Service Fabric | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące zarządzania usługi Service Fabric jako infrastruktura jako kod.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 23c851008988af06927d387daaa5a6df980dab96
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913898"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kod

W scenariuszu produkcyjnym Tworzenie klastrów usługi Azure Service Fabric za pomocą szablonów usługi Resource Manager. Szablony usługi Resource Manager zapewnia lepszą kontrolę nad właściwości zasobów i upewnij się, że model zasobu spójne.

Szablony usługi Resource Manager próbki są dostępne dla Windows i Linux w [przykładów dla platformy Azure w serwisie GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Te szablony może służyć jako punkt wyjścia do szablonu klastra. Pobierz `azuredeploy.json` i `azuredeploy.parameters.json` i edytować je zgodnie z wymaganiami niestandardowego.

Aby wdrożyć `azuredeploy.json` i `azuredeploy.parameters.json` szablony pobrane powyżej, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Tworzenie zasobu za pomocą programu Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
New-AzureRmResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Zasoby platformy Azure Service Fabric

Na klastrze usługi Service Fabric za pomocą usługi Azure Resource Manager można wdrożyć aplikacji i usług. Zobacz [zarządzania aplikacjami i usługami jako zasoby usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) Aby uzyskać szczegółowe informacje. Poniżej przedstawiono najlepsze praktyki usługi Service Fabric aplikacji określone zasoby do uwzględnienia w swoich zasobach szablonu usługi Resource Manager.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Aby wdrożyć aplikację za pomocą usługi Azure Resource Manager, najpierw musisz [tworzenie sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) pakietu aplikacji usługi Service Fabric. Poniższy skrypt języka python jest przykładem sposobu tworzenia sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra usługi Service Fabric dla systemu Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)