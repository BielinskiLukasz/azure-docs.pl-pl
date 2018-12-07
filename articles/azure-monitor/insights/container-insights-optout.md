---
title: Jak zatrzymać monitorowanie Twojej usługi Azure Kubernetes Service klastra | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można przerwać monitorowania klastra usługi AKS platformy Azure z usługą Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/04/2018
ms.author: magoedte
ms.openlocfilehash: 1df4e345645fb216f1656b2bbe87396cf0719d1c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994980"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers-preview"></a>Jak zatrzymać monitorowanie usługi Azure Kubernetes Service (AKS) z usługą Azure Monitor dla kontenerów (wersja zapoznawcza)

Jeśli po włączeniu monitorowania klastra usługi AKS, okażą się już nie chcesz, aby ją monitorować, możesz *zrezygnować*.  W tym artykule pokazano, jak to zrobić przy użyciu wiersza polecenia platformy Azure lub przy użyciu dostarczonych szablonów usługi Azure Resource Manager.  


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj [az aks disable dodatków](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) polecenie, aby wyłączyć usługi Azure Monitor dla kontenerów. Polecenie usuwa agenta z węzłów klastra, nie powoduje usunięcia rozwiązania lub dane już zebrane i przechowywane w zasobie usługi Log Analytics.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Aby ponownie włączyć monitorowania dla klastra, zobacz [Włącz monitorowanie przy użyciu wiersza polecenia platformy Azure](container-insights-onboard.md#enable-monitoring-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Czy podana, dwa szablonu usługi Azure Resource Manager umożliwiają usunięcie spójnego i wielokrotnego zasoby rozwiązania w grupie zasobów. Jednym jest określenie konfiguracji szablonu JSON *zrezygnować z* , a drugi zawiera wartości parametrów, które można skonfigurować w celu określenia AKS grupę zasobów klastra identyfikator i zasobów wdrożoną w klastrze. 

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.
>

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Tworzenie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Zapisz ten plik jako **OptOutTemplate.json** do folderu lokalnego.
3. Wklej następującą składnię JSON do pliku:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Edytuj wartości **aksResourceId** i **aksResourceLocation** przy użyciu wartości klastra AKS, który można znaleźć na **właściwości** strona dla wybranego klastra .

    ![Strona właściwości kontenera](media/container-insights-optout/container-properties-page.png)

    Gdy jesteś w **właściwości** strony, również skopiować **identyfikator zasobu obszaru roboczego**. Ta wartość jest wymagana, jeśli zdecydujesz, że chcesz później usunąć obszar roboczy usługi Log Analytics. Trwa usuwanie obszaru roboczego usługi Log Analytics nie jest wykonywane jako część tego procesu. 

5. Zapisz ten plik jako **OptOutParam.json** do folderu lokalnego.
6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

### <a name="remove-the-solution-using-azure-cli"></a>Usuń rozwiązanie za pomocą wiersza polecenia platformy Azure
W systemie Linux, aby usunąć rozwiązanie i wyczyścić konfiguracji klastra usługi AKS, wykonaj następujące polecenie z wiersza polecenia platformy Azure.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Zmiana konfiguracji może potrwać kilka minut. Po ukończeniu, jest zwracany komunikat podobny do poniższego, który zawiera wynik:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Usuń rozwiązanie przy użyciu programu PowerShell

Wykonaj następujące polecenia programu PowerShell w folderu zawierającego szablon, aby usunąć rozwiązanie i wyczyścić konfiguracji z klastra usługi AKS.    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Zmiana konfiguracji może potrwać kilka minut. Po ukończeniu, jest zwracany komunikat podobny do poniższego, który zawiera wynik:

```powershell
ProvisioningState       : Succeeded
```

Jeśli obszar roboczy został utworzony tylko do obsługi monitorowania klastra i nie jest już potrzebny, należy ręcznie je usunąć. Jeśli nie znasz sposób usuwania obszaru roboczego, zobacz [usunąć obszar roboczy usługi Azure Log Analytics w witrynie Azure portal](../../azure-monitor/platform/delete-workspace.md). Nie zapomnij o **identyfikator zasobu obszaru roboczego** możemy skopiowany wcześniej w kroku 4, możesz zacząć potrzebna. 

