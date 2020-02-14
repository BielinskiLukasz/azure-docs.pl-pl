---
title: Konfigurowanie hybrydowych klastrów Kubernetes za pomocą Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Azure Monitor kontenerów do monitorowania klastrów Kubernetes hostowanych w Azure Stack lub innym środowisku.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198058"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurowanie hybrydowych klastrów Kubernetes za pomocą Azure Monitor dla kontenerów

Azure Monitor dla kontenerów zapewnia rozbudowane środowisko monitorowania dla usługi Azure Kubernetes Service (AKS) i [aparatu AKS na platformie Azure](https://github.com/Azure/aks-engine), która jest klastrem z własnym zarządzaniem Kubernetes hostowanym na platformie Azure. W tym artykule opisano, jak włączyć monitorowanie klastrów Kubernetes hostowanych poza platformą Azure i osiągnąć podobne środowisko monitorowania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Obszar roboczy usługi Log Analytics.

    Azure Monitor dla kontenerów obsługuje obszar roboczy Log Analytics w regionach wymienionych w produktach platformy Azure [według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Aby utworzyć własny obszar roboczy, można go utworzyć za pomocą [Azure Resource Manager](../platform/template-workspace-configuration.md), za pomocą [programu PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub [Azure Portal](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Włącz monitorowanie wielu klastrów o tej samej nazwie klastra w tym samym obszarze roboczym Log Analytics nie jest obsługiwane. Nazwy klastrów muszą być unikatowe.
    >

* Musisz być członkiem **roli współautor log Analytics** , aby umożliwić monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego Log Analytics, zobacz [Zarządzanie dostępem do obszaru roboczego i danymi dziennika](../platform/manage-access.md)

* [Helm klient](https://helm.sh/docs/using_helm/) Azure monitor do dołączania wykresu kontenerów dla określonego klastra Kubernetes.

* Następujące informacje o konfiguracji serwera proxy i zapory są wymagane przez kontener Log Analytics agenta dla systemu Linux w celu komunikowania się z Azure Monitor:

    |Zasób agenta|Porty |
    |------|---------|   
    |*.ods.opinsights.azure.com |Port 443 |  
    |*.oms.opinsights.azure.com |Port 443 |  
    |*.blob.core.windows.net |Port 443 |  
    |*. dc.services.visualstudio.com |Port 443 |

* Agent kontenera wymaga, aby Kubelet `cAdvisor secure port: 10250` lub `unsecure port :10255` być otwarty na wszystkich węzłach w klastrze w celu zbierania metryk wydajności. Zalecamy skonfigurowanie `secure port: 10250` w cAdvisor Kubelet, jeśli nie został on jeszcze skonfigurowany.

* Agent kontenerów wymaga określenia następujących zmiennych środowiskowych w kontenerze w celu komunikowania się z usługą interfejsu API Kubernetes w klastrze w celu zbierania danych spisu — `KUBERNETES_SERVICE_HOST` i `KUBERNETES_PORT_443_TCP_PORT`.

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana w przypadku monitorowania hybrydowych klastrów Kubernetes to ciprod10182019 lub nowszy.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Poniższe elementy są oficjalnie obsługiwane w przypadku kontenerów Azure Monitor.

- Środowiska: Kubernetes lokalnego, aparatu AKS na platformie Azure i Azure Stack. Aby uzyskać więcej informacji, zobacz [aparat AKS na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Wersje programu Kubernetes i zasady pomocy technicznej są takie same, jak wersje programu [AKS obsługiwane](../../aks/supported-kubernetes-versions.md).
- Środowisko uruchomieniowe kontenera: Docker i Moby
- Wydanie systemu operacyjnego Linux dla węzłów głównych i pracy: Ubuntu (18,04 LTS i 16,04 LTS)
- Obsługiwana kontrola dostępu: Kubernetes RBAC i bez kontroli RBAC

## <a name="enable-monitoring"></a>Włączanie monitorowania

Włączenie Azure Monitor kontenerów dla klastra hybrydowego Kubernetes składa się z następujących kroków w kolejności.

1. Skonfiguruj obszar roboczy Log Analytics przy użyciu rozwiązania datainsights.

2. Włącz Azure Monitor dla HELM kontenerów z obszarem roboczym Log Analytics.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Jak dodać rozwiązanie Azure Monitor Containers

Rozwiązanie można wdrożyć za pomocą podanego szablonu Azure Resource Manager za pomocą polecenia cmdlet Azure PowerShell `New-AzResourceGroupDeployment` lub z interfejsem wiersza polecenia platformy Azure.

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją wersję, uruchom `az --version`. Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Ta metoda obejmuje dwa szablony JSON. Jeden szablon Określa konfigurację, aby włączyć monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować w celu określ następujące ustawienia:

- **workspaceResourceId** — pełny identyfikator zasobu obszaru roboczego log Analytics.
- **workspaceRegion** — region, w którym jest tworzony obszar roboczy, który jest również określany jako **Lokalizacja** we właściwościach obszaru roboczego podczas wyświetlania z Azure Portal.

Aby najpierw zidentyfikować pełny identyfikator zasobu Log Analytics obszaru roboczego, który jest wymagany dla wartości parametru `workspaceResourceId` w pliku **containerSolutionParams. JSON** , wykonaj następujące kroki, a następnie uruchom polecenie cmdlet programu PowerShell lub interfejs wiersza polecenia platformy Azure, aby dodać rozwiązanie.

1. Wyświetl listę wszystkich subskrypcji, do których masz dostęp, za pomocą następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą wyglądać w następujący sposób:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Skopiuj wartość identyfikatora **subskrypcji**.

2. Przejdź do subskrypcji, w której znajduje się obszar roboczy Log Analytics, przy użyciu następującego polecenia:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Poniższy przykład wyświetla listę obszarów roboczych w Twoich subskrypcjach w domyślnym formacie JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych Znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego Log Analytics obszaru roboczego pod **identyfikatorem**pola.

4. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Zapisz ten plik jako containerSolution. JSON w folderze lokalnym.

6. Wklej następującą składnię JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Edytuj wartości dla **workspaceResourceId** przy użyciu wartości skopiowanej w kroku 3, a w przypadku **WorkspaceRegion** skopiuj wartość **region** po uruchomieniu polecenia wiersza poleceń platformy Azure [AZ Monitor Log-Analytics Workspace show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Zapisz ten plik jako containerSolutionParams. JSON w folderze lokalnym.

9. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

   * Aby wdrożyć program przy użyciu Azure PowerShell, użyj następujących poleceń w folderze zawierającym szablon:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Aby przeprowadzić wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="install-the-chart"></a>Instalowanie wykresu

Aby włączyć wykres HELM, wykonaj następujące czynności:

1. Dodaj repozytorium wykresów platformy Azure do listy lokalnej, uruchamiając następujące polecenie:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Zainstaluj wykres, uruchamiając następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Jeśli obszar roboczy Log Analytics jest w Chinach platformy Azure, uruchom następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Jeśli obszar roboczy Log Analytics jest w instytucji rządowych USA platformy Azure, uruchom następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Konfigurowanie zbierania danych agenta

W przypadku używania wykresu w wersji 1.0.0 ustawienia zbierania danych agentów są kontrolowane przez ConfigMap. W [tym miejscu](container-insights-agent-config.md)zapoznaj się z dokumentacją dotyczącą ustawień zbierania danych agentów.

Po pomyślnym wdrożeniu wykresu można przejrzeć dane dla hybrydowego klastra Kubernetes w Azure Monitor kontenerów z Azure Portal.  

>[!NOTE]
>Opóźnienie pozyskiwania trwa od pięciu do dziesięciu minut od agenta do zatwierdzenia w obszarze roboczym usługi Azure Log Analytics. Stan klastra pokazuje wartość **Brak danych lub nie** jest **znana** do momentu udostępnienia wszystkich wymaganych danych monitorowania w Azure monitor.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi błąd podczas próby włączenia monitorowania dla hybrydowego klastra Kubernetes, skopiuj skrypt programu PowerShell [TroubleshootError_nonAzureK8s. ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) i Zapisz go w folderze na komputerze. Ten skrypt zapewnia pomoc w wykrywaniu i usuwaniu napotkanych problemów. Te problemy zaprojektowano w celu wykrycia i wykonania korekty są następujące:

* Określony obszar roboczy Log Analytics jest prawidłowy
* W obszarze roboczym Log Analytics jest konfigurowany Azure Monitor rozwiązanie kontenerów. W przeciwnym razie Skonfiguruj obszar roboczy.
* OmsAgent REPLICASET są uruchomione
* OmsAgent elementu daemonset są uruchomione
* Usługa OmsAgent Health jest uruchomiona
* Identyfikator i klucz obszaru roboczego Log Analytics skonfigurowany na kontenerze kontenera jest zgodny z obszarem roboczym, w którym skonfigurowano szczegółowe informacje.
* Sprawdź, czy wszystkie węzły procesów roboczych systemu Linux mają etykietę `kubernetes.io/role=agent`, aby zaplanować RS. Jeśli nie istnieje, Dodaj ją.
* Sprawdź poprawność `cAdvisor secure port:10250` lub `unsecure port: 10255` jest otwarty na wszystkich węzłach w klastrze.

Aby wykonać z Azure PowerShell, użyj następujących poleceń w folderze, który zawiera skrypt:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania w celu zbierania informacji o kondycji i użyciu zasobów hybrydowego klastra Kubernetes oraz obciążeń na nich działających należy dowiedzieć się, [jak używać](container-insights-analyze.md) Azure monitor kontenerów.
