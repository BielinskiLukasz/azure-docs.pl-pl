---
title: Link szablonów do wdrażania na platformie Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób użycia połączonymi szablonami w szablonie usługi Azure Resource Manager w celu utworzenia rozwiązania modułowe szablonu. Pokazuje, jak przekazać wartości parametrów, określ plik parametrów i dynamicznie utworzoną adresów URL.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2018
ms.author: tomfitz
ms.openlocfilehash: fbfe7255f2b848187c74fd832f349186eef5eaef
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287578"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Za pomocą połączone i zagnieżdżone szablony, podczas wdrażania zasobów platformy Azure

Aby wdrożyć swoje rozwiązanie, służy pojedynczy szablon lub szablon głównego przy użyciu wielu powiązanych szablonów. Pokrewne szablonu może być w oddzielnym pliku, który jest połączony z głównego szablonu lub szablon, który zostało zagnieżdżone w elemencie głównym szablonu.

Dla małych i średnich rozwiązania pojedynczy szablon jest łatwiejsze do zrozumienia i utrzymania. Można wyświetlić wszystkich zasobów i wartości w jednym pliku. W przypadku zaawansowanych scenariuszy połączone Szablony umożliwiają podzielenie rozwiązania do elementów docelowych i ponowne użycie szablonów.

Korzystając z połączonymi szablonami, można utworzyć głównego szablonu, który odbiera wartości parametrów podczas wdrażania. Szablon głównego zawiera połączonymi szablonami i przekazuje wartości do tych szablonów, zgodnie z potrzebami.

Aby zapoznać się z samouczkiem, zobacz [samouczek: Tworzenie szablonów usługi połączonej usługi Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).

## <a name="link-or-nest-a-template"></a>Połącz lub zagnieżdżanie szablonu

Aby utworzyć link do innego szablonu, należy dodać **wdrożeń** zasobów do głównego szablonu.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

Właściwości, które zapewniają zasobu wdrożenia różnią się zależnie od one łączenie z szablonem zewnętrznych czy zagnieżdżania szablonem wbudowanego w głównym szablonu.

Dla obu połączone i zagnieżdżone szablony, można użyć tylko [przyrostowe](deployment-modes.md) tryb wdrożenia.

### <a name="nested-template"></a>Zagnieżdżony szablon

Aby zagnieździć szablonu w ramach głównego szablonu, należy użyć **szablonu** właściwości i określ składni szablonu.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Zagnieżdżone szablony nie można używać parametry lub zmienne, które są zdefiniowane w obrębie zagnieżdżonych szablonów. Można użyć parametrów i zmiennych z głównego szablonu. W powyższym przykładzie `[variables('storageName')]` pobiera wartość z głównego szablonu, a nie zagnieżdżonych szablonów. To ograniczenie nie ma zastosowania do szablonów zewnętrznych.
>
> Nie można użyć `reference` funkcji w danych wyjściowych części zagnieżdżonych szablonów. Aby zwrócić wartości dla zasobów wdrożonych w zagnieżdżonych szablonów, należy przekonwertować zagnieżdżony szablon do dołączonego szablonu.

Zagnieżdżony szablon wymaga [tymi samymi właściwościami](resource-group-authoring-templates.md) jako standardowego szablonu.

### <a name="external-template-and-external-parameters"></a>Szablon zewnętrznej i zewnętrznego parametry

Łącza do zewnętrznych szablonu i pliku parametrów, użyj **templateLink** i **parametersLink**. Podczas ustanawiania połączenia szablonu usługi Resource Manager należy uzyskiwać do niego dostęp. Nie można określić w lokalnym pliku lub plików, która jest dostępna tylko w sieci lokalnej. Należy podać tylko wartości identyfikatora URI, który zawiera jedną **http** lub **https**. Jedną z opcji jest umieszczenie szablonu połączone w ramach konta magazynu, i użyj identyfikatora URI dla tego elementu.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parametersLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Nie trzeba podać `contentVersion` właściwości dla szablonu lub parametrów. Jeśli nie podasz wartość wersja zawartości jest wdrażany bieżącą wersję szablonu. Jeżeli podasz wartość wersji zawartości, musi być zgodna z wersją w połączony szablon; w przeciwnym razie wdrożenie zakończy się niepowodzeniem z powodu błędu.

### <a name="external-template-and-inline-parameters"></a>Zewnętrzne parametrów szablonu i wbudowane

Ewentualnie możesz podać wbudowane parametru. Nie można użyć wbudowanego parametrów i link do pliku parametrów. Wdrożenie zakończy się niepowodzeniem z powodu błędu podczas zarówno `parametersLink` i `parameters` zostały określone.

Aby przekazać wartość z głównego szablonu do dołączonego szablonu, należy użyć **parametry**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Używanie zmiennych połączyć szablonów

W poprzednich przykładach pokazano zakodowanych wartości adresu URL dla łączy szablonu. Takie podejście może działać w przypadku prostego szablonu, ale nie działa dobrze, podczas pracy z szerokiej gamy modułowej szablonów. Zamiast tego można utworzyć zmienną statyczną, przechowujący podstawowy adres URL dla głównego szablonu, a następnie dynamicznie utworzyć adresy URL dla szablonów połączonych z tym podstawowy adres URL. Zaletą tego podejścia jest można łatwo przenosić lub rozwidlenie szablonu, ponieważ trzeba zmienić statycznych zmiennych z głównego szablonu. Szablon głównego przekazuje poprawne identyfikatory URI w całym rozkładany szablonu.

Poniższy przykład pokazuje, jak utworzyć dwa adresy URL dla połączonych szablonów przy użyciu podstawowego adresu URL (**sharedTemplateUrl** i **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Można również użyć [deployment()](resource-group-template-functions-deployment.md#deployment) uzyskać podstawowy adres URL dla bieżącego szablonu i używać, aby uzyskać adres URL dla innych szablonów w tej samej lokalizacji. To podejście jest przydatne w przypadku zmiany lokalizacji szablonu, lub gdy chcesz uniknąć ciężko kodowania adresów URL w pliku szablonu. Właściwości templateLink jest zwracany tylko wtedy, w trakcie łączenia ze zdalnym szablonu przy użyciu adresu URL. Jeśli używasz lokalnego szablonu, ta właściwość nie jest dostępna.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Pobieranie wartości z połączonego szablonu

Aby uzyskać wartość wyjściową dołączonego szablonu, pobrać wartość właściwości przy użyciu składni, takich jak: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Poniższe przykłady pokazują, jak odwoływać się do dołączonego szablonu i pobierania wartości danych wyjściowych. Połączony szablon zwróci komunikat proste.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

Główny szablon wdraża połączony szablon i pobiera zwracanej wartości. Należy zauważyć, że odwołuje się do zasobu wdrożenia według nazwy, przy czym nazwa właściwości zwrócony przez szablon połączone.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Podobnie jak inne typy zasobów można ustawić zależności między połączony szablon i innych zasobów. W związku z tym gdy inne zasoby wymagają wartość wyjściową z dołączonego szablonu, upewnij się, że wdrożono połączony szablon przed ich. Lub, gdy połączony szablon opiera się na inne zasoby, sprawdź, czy innych zasobów wdrożonych przed dołączonego szablonu.

Poniższy przykład przedstawia szablon, który wdraża publiczny adres IP i zwraca identyfikator zasobu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Aby użyć publiczny adres IP z poprzedniego szablonu podczas wdrażania modułu równoważenia obciążenia, łącza do szablonu i dodać zależność od zasobu wdrażania. Publiczny adres IP modułu równoważenia obciążenia jest równa wartości danych wyjściowych z dołączonego szablonu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Połączone i zagnieżdżone szablony w historii wdrożenia

Usługi Resource Manager przetwarza każdy szablon jako osobne wdrożenia w historii wdrażania. W związku z tym głównego szablonu za pomocą trzech połączonych lub zagnieżdżonych szablonów pojawia się w historii wdrożenia jako:

![Historia wdrożenia](./media/resource-group-linked-templates/deployment-history.png)

Te oddzielne wpisy w historii służy do pobierania wartości danych wyjściowych po wdrożeniu. Następujący szablon tworzy publiczny adres IP, a także generuje adres IP:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

Poniższe linki szablonu, aby Powyższy szablon. Tworzy trzy publicznych adresów IP.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
            }
        }
    ]
}
```

Po wdrożeniu możesz pobrać wartości danych wyjściowych za pomocą następującego skryptu programu PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Lub skryptu wiersza polecenia platformy Azure w powłoce Bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Zabezpieczanie zewnętrznego szablonu

Mimo że dołączonego szablonu musi być dostępny zewnętrznie, nie musi być ogólnie dostępne publicznie. Szablon można dodać do konta magazynu prywatnego, który jest dostępny dla właściciela konta magazynu. Następnie należy utworzyć token sygnatury (SAS) dostępu współdzielonego, aby umożliwić dostęp podczas wdrażania. Token sygnatury dostępu Współdzielonego możesz dodać do identyfikatora URI do dołączonego szablonu. Nawet jeśli token jest przekazywany jako bezpieczny ciąg, identyfikator URI szablonu połączony, w tym token sygnatury dostępu Współdzielonego jest rejestrowany w operacji wdrażania. Aby ograniczyć zagrożenia, należy ustawić wygaśnięcia tokenu.

Plik parametrów również może być ograniczona do dostępu za pomocą tokenu sygnatury dostępu Współdzielonego.

Poniższy przykład pokazuje, jak przekazać token sygnatury dostępu Współdzielonego podczas łączenia do szablonu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

W programie PowerShell możesz uzyskać token dla kontenera i wdrażanie szablonów za pomocą następujących poleceń. Należy zauważyć, że **containerSasToken** parametru jest zdefiniowane w szablonie. Nie jest parametrem w **New-AzureRmResourceGroupDeployment** polecenia.

```azurepowershell-interactive
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

For Azure CLI w powłoce Bash możesz uzyskać token dla kontenera i wdrażania szablonów przy użyciu następującego kodu:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach pokazano typowych zastosowań połączonymi szablonami.

|Szablon głównego  |Połączony szablon |Opis  |
|---------|---------| ---------|
|[Witaj, świecie](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Zwraca ciąg z dołączonego szablonu. |
|[Load Balancer z publicznym adresem IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Zwraca publiczny adres IP z dołączonego szablonu i ustawia tę wartość w module równoważenia obciążenia. |
|[Wiele adresów IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Tworzy kilka publicznych adresów IP dołączonego szablonu.  |

## <a name="next-steps"></a>Kolejne kroki

* Aby wykonać kroki samouczka, zobacz [samouczek: Tworzenie szablonów usługi połączonej usługi Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).
* Aby dowiedzieć się więcej na temat definiowania kolejność wdrażania zasobów, zobacz [Definiowanie zależności w szablonach usługi Azure Resource Manager](resource-group-define-dependencies.md).
* Aby dowiedzieć się, jak zdefiniować jeden zasób, lecz wiele wystąpień, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).
* Aby uzyskać instrukcje dotyczące konfigurowania szablonu w ramach konta magazynu i generowania tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](resource-group-template-deploy.md) lub [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i Interfejs wiersza polecenia Azure](resource-group-template-deploy-cli.md).
