---
title: Zdefiniuj wiele wystąpień wartości wyjściowej
description: Użyj operacji kopiowania w szablonie Azure Resource Manager, aby wykonać iterację wiele razy podczas zwracania wartości z wdrożenia.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617836"
---
# <a name="output-iteration-in-arm-templates"></a>Iteracja danych wyjściowych w szablonach ARM

W tym artykule pokazano, jak utworzyć więcej niż jedną wartość danych wyjściowych w szablonie Azure Resource Manager (ARM). Po dodaniu elementu **copy** do sekcji dane wyjściowe szablonu można dynamicznie zwrócić liczbę elementów podczas wdrażania.

Można również użyć kopiowania z [zasobami](copy-resources.md), [właściwości w zasobie](copy-properties.md)i [zmiennych](copy-variables.md).

## <a name="outputs-iteration"></a>Iteracja danych wyjściowych

Element Copy ma następujący format ogólny:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

Właściwość **Count** określa liczbę iteracji dla wartości wyjściowej.

Właściwość **Input** określa właściwości, które mają być powtarzane. Tworzysz tablicę elementów skonstruowanych na podstawie wartości we właściwości **wejściowej** . Może to być pojedyncza Właściwość (na przykład ciąg) lub obiekt z kilkoma właściwościami.

Poniższy przykład tworzy zmienną liczbę kont magazynu i zwraca punkt końcowy dla każdego konta magazynu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Poprzedni szablon zwraca tablicę z następującymi wartościami:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Następny przykład zwraca trzy właściwości z nowych kont magazynu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

Poprzedni przykład zwraca tablicę o następujących wartościach:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi ARM](template-tutorial-create-multiple-instances.md).
* Aby poznać inne zastosowania elementu Copy, zobacz:
  * [Iteracja zasobów w szablonach ARM](copy-resources.md)
  * [Iteracja właściwości w szablonach ARM](copy-properties.md)
  * [Iteracja zmiennej w szablonach ARM](copy-variables.md)
* Jeśli chcesz dowiedzieć się więcej na temat sekcji szablonu, zobacz [Tworzenie szablonów ARM](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu ARM](deploy-powershell.md).

