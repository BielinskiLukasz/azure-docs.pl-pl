---
title: Utwórz obszar roboczy usługi Log Analytics przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć obszar roboczy usługi Log Analytics umożliwia zarządzanie rozwiązaniami i gromadzenia danych z środowiska w chmurze i lokalnych przy użyciu wiersza polecenia platformy Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: magoedte
ms.openlocfilehash: bc3b96ee55ccd28cce89b1f37494b836851977f1
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259251"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Utwórz obszar roboczy usługi Log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0

Interfejs wiersza polecenia platformy Azure 2.0 umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi z poziomu wiersza polecenia lub za pomocą skryptów. Ten przewodnik Szybki Start dowiesz się, jak wdrożyć obszar roboczy usługi Log Analytics na platformie Azure, która jest unikatowa środowiska z własnym repozytorium danych, źródłami danych i rozwiązań za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0.  Kroki opisane w tym artykule są wymagane, jeśli jest planowane na zbieranie danych z następujących źródeł:

* Zasoby platformy Azure w ramach subskrypcji  
* Lokalnych komputerów monitorowanych przez program System Center Operations Manager  
* Kolekcje w programie System Center Configuration Manager  
* Dane diagnostyczne lub dziennika z usługi Azure Storage  
 
W przypadku innych źródeł, takie jak maszyny wirtualne platformy Azure i Windows lub maszyny wirtualne systemu Linux w środowisku zobacz następujące tematy:

* [Zbieranie danych z maszyn wirtualnych platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md)
* [Zbieranie danych z komputera z systemem Linux hybrydowe](../../azure-monitor/learn/quick-collect-linux-computer.md)
* [Zbieranie danych z komputera Windows hybrydowe](quick-collect-windows-computer.md)

Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed przystąpieniem do wykonywania.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Utwórz obszar roboczy z [Utwórz wdrożenie grupy az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). Poniższy przykład tworzy obszar roboczy o nazwie *TestWorkspace* w grupie zasobów *laboratorium* w *eastus* lokalizacji za pomocą szablonu usługi Resource Manager z lokalnym maszyny. Szablon JSON jest skonfigurowany do tylko wyświetlenie monitu o nazwę obszaru roboczego i określenie wartości domyślnej dla innych parametrów, które prawdopodobnie będzie służyć jako standardowej konfiguracji w danym środowisku. Możesz także przechowywać szablon na koncie magazynu platformy Azure w celu zapewnienia dostępu współdzielonego w Twojej organizacji. Aby uzyskać dodatkowe informacje na temat pracy z szablonami, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Następujące parametry ustawiona wartość domyślna:

* Lokalizacja — wartość domyślna to wschodnie stany USA
* Jednostka SKU — wartość domyślna to nowej warstwy cenowej na GB, wydana w kwietniu 2018 r., model cen

>[!WARNING]
>W przypadku tworzenia lub konfigurowania obszaru roboczego usługi Log Analytics w ramach subskrypcji, który występował w nowych z kwietnia 2018 r modelu cen, jest prawidłowa tylko usługi Log Analytics warstwy cenowej **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Tworzenie i wdrażanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Edytuj szablon do własnych wymagań.  Przegląd [szablonu Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) odwołania, aby dowiedzieć się, jakie właściwości i wartości są obsługiwane. 
3. Zapisz ten plik jako **deploylaworkspacetemplate.json** do folderu lokalnego.   
4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Użyj następujących poleceń z poziomu folderu zawierającego szablon:

    ```azurecli
    azure group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego, który zawiera wynik:

![Przykład wyniku, gdy wdrożenie jest ukończone](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz dostępnego obszaru roboczego, możesz skonfigurować zbieranie danych telemetrycznych monitorowania, uruchamiają przeszukiwanie dzienników w celu analizowania danych i dodać rozwiązanie do zarządzania w celu zapewnienia dodatkowych danych i szczegółowych informacji analitycznych.  

* Aby włączyć zbieranie danych z zasobów platformy Azure Diagnostyka Azure lub usługi Azure storage, zobacz [zbieranie dzienników platformy Azure usługi i metryk do użycia w usłudze Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md).  
* Dodaj [programu System Center Operations Manager jako źródła danych](../../azure-monitor/platform/om-agents.md) do zbierania danych z agentów raportujących do grupy zarządzania programu Operations Manager i zapisać ją w obszarze roboczym usługi Log Analytics.  
* Połącz [programu Configuration Manager](../../azure-monitor/platform/collect-sccm.md) do zaimportowania komputerów, które są członkami kolekcji w hierarchii.  
* Przegląd [rozwiązań do zarządzania](../../azure-monitor/insights/solutions.md) dostępne i jak dodać lub usunąć rozwiązanie z obszaru roboczego.
