---
title: Tworzenie specyfikacji szablonu z połączonymi szablonami
description: Dowiedz się, jak utworzyć specyfikację szablonu z połączonymi szablonami.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: b952baa465092fef19ad2feb11a43328a6177d1c
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387867"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Samouczek: Tworzenie specyfikacji szablonu z połączonymi szablonami (wersja zapoznawcza)

Dowiedz się, jak utworzyć [specyfikację szablonu](template-specs.md) z [połączonym szablonem](linked-templates.md#linked-template). Specyfikacje szablonu są używane do udostępniania szablonów ARM innym użytkownikom w organizacji. W tym artykule opisano sposób tworzenia specyfikacji szablonu do pakowania szablonu głównego i jego połączonych szablonów przy użyciu nowej `relativePath` właściwości [zasobu wdrożenia](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specyfikacje szablonu są obecnie w wersji zapoznawczej. Aby go użyć, musisz [utworzyć konto w wersji zapoznawczej](https://aka.ms/templateSpecOnboarding).

## <a name="create-linked-templates"></a>Tworzenie połączonych szablonów

Utwórz szablon główny i połączony szablon.

Aby połączyć szablon, Dodaj [zasób wdrożenia](/azure/templates/microsoft.resources/deployments) do szablonu głównego. We `templateLink` Właściwości określ ścieżkę względną dla połączonego szablonu zgodnie ze ścieżką szablonu nadrzędnego.

Połączony szablon jest nazywany **linkedTemplate.json**i jest przechowywany w podfolderze o nazwie **artefakty** w ścieżce, w której jest przechowywany szablon główny.  Możesz użyć jednej z następujących wartości dla relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath`Właściwość jest zawsze względna w stosunku do pliku szablonu `relativePath` , gdzie jest zadeklarowany, więc jeśli istnieje inny linkedTemplate2.js, który jest wywoływany z linkedTemplate.json i linkedTemplate2.jsjest przechowywany w tym samym podfolderze artefaktów, RelativePath określona w linkedTemplate.jsjest tylko `linkedTemplate2.json` .

1. Utwórz szablon główny przy użyciu poniższego kodu JSON. Zapisz szablon główny jako **azuredeploy.jsna** komputerze lokalnym. W tym samouczku założono, że Zapisano w ścieżce **c:\Templates\linkedTS\azuredeploy.jsna** , ale można użyć dowolnej ścieżki.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > ApiVersion `Microsoft.Resources/deployments` musi mieć wartość 2020-06-01 lub nowszą.

1. Utwórz katalog o nazwie **artefakty** w folderze, w którym jest zapisany szablon główny.
1. Utwórz połączony szablon przy użyciu następującego kodu JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Zapisz szablon jako **linkedTemplate.js** w folderze **artefaktów** .

## <a name="create-template-spec"></a>Utwórz specyfikację szablonu

Specyfikacje szablonów są przechowywane w grupach zasobów.  Utwórz grupę zasobów, a następnie utwórz specyfikację szablonu przy użyciu następującego skryptu. Nazwa specyfikacji szablonu to **webspec**.

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

Gdy skończysz, możesz wyświetlić specyfikację szablonu z poziomu Azure Portal lub przy użyciu następującego polecenia cmdlet:

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>Wdróż specyfikację szablonu

Teraz można wdrożyć specyfikację szablonu. wdrożenie specyfikacji szablonu jest tak samo samo jak wdrożenie szablonu zawartego w szablonie, z wyjątkiem tego, że został przekazany identyfikator zasobu specyfikacji szablonu. Używasz tych samych poleceń wdrażania i w razie potrzeby Przekaż wartości parametrów dla specyfikacji szablonu.

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wdrażaniu specyfikacji szablonu jako połączonego szablonu, zobacz [Samouczek: wdrażanie specyfikacji szablonu jako połączonego szablonu](template-specs-deploy-linked-template.md).
