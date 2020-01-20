---
title: Odwoływanie się do niestandardowego obrazu w szablonie zestawu skalowania platformy Azure
description: Dowiedz się, jak dodać obraz niestandardowy do istniejącego szablonu zestawu skalowania maszyn wirtualnych platformy Azure
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: fd1a567af1c35cf6b659995e998b11a61a526508
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275595"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Dodawanie niestandardowego obrazu do szablonu zestawu skalowania platformy Azure

W tym artykule pokazano, jak zmodyfikować [podstawowy szablon zestawu skalowania](virtual-machine-scale-sets-mvss-start.md) w celu wdrożenia go z obrazu niestandardowego.

## <a name="change-the-template-definition"></a>Zmiana definicji szablonu
W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) został utworzony podstawowy szablon zestawu skalowania. Teraz użyjemy tego wcześniejszego szablonu i zmodyfikujesz go, aby utworzyć szablon, który wdraża zestaw skalowania na podstawie obrazu niestandardowego.  

### <a name="creating-a-managed-disk-image"></a>Tworzenie obrazu dysku zarządzanego

Jeśli masz już niestandardowy obraz dysku zarządzanego (zasób typu `Microsoft.Compute/images`), możesz pominąć tę sekcję.

Najpierw Dodaj parametr `sourceImageVhdUri`, który jest identyfikatorem URI do uogólnionego obiektu BLOB w usłudze Azure Storage, który zawiera obraz niestandardowy do wdrożenia.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Następnie Dodaj zasób typu `Microsoft.Compute/images`, który jest obrazem dysku zarządzanego na podstawie uogólnionego obiektu BLOB znajdującego się w `sourceImageVhdUri`URI. Ten obraz musi znajdować się w tym samym regionie co zestaw skalowania, który go używa. We właściwościach obrazu określ typ systemu operacyjnego, lokalizację obiektu BLOB (z parametru `sourceImageVhdUri`) i typ konta magazynu:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

W zasobów zestawu skalowania Dodaj klauzulę `dependsOn` odwołującą się do obrazu niestandardowego, aby upewnić się, że obraz został utworzony przed próbą wdrożenia zestawu skalowania z tego obrazu:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Zmienianie właściwości zestawu skalowania w celu korzystania z obrazu dysku zarządzanego

Na `imageReference` zestawu skalowania `storageProfile`, zamiast określania wydawcy, oferty, jednostki SKU i wersji obrazu platformy, określ `id` zasobu `Microsoft.Compute/images`:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

W tym przykładzie Użyj funkcji `resourceId`, aby uzyskać identyfikator zasobu obrazu utworzonego w tym samym szablonie. Jeśli wcześniej utworzono obraz dysku zarządzanego, należy podać identyfikator tego obrazu. Ten identyfikator musi mieć postać: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
