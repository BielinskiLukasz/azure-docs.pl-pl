---
title: Historia wdrożenia
description: Opisuje sposób wyświetlania Azure Resource Manager operacji wdrażania przy użyciu portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure i usługi API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 753071a3edca62690b772f7b8d34fec43641466f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477865"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Wyświetlanie historii wdrożenia za pomocą Azure Resource Manager

Azure Resource Manager umożliwia wyświetlanie historii wdrożenia i sprawdzanie określonych operacji w poprzednich wdrożeniach. Możesz zobaczyć wdrożone zasoby i uzyskać informacje o błędach.

Aby uzyskać pomoc dotyczącą rozwiązywania określonych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure za pomocą Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Pobierz wdrożenia i identyfikator korelacji

Szczegółowe informacje o wdrożeniu można wyświetlić za pomocą programu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. Każde wdrożenie ma identyfikator korelacji, który jest używany do śledzenia powiązanych zdarzeń. Może być przydatne podczas pracy z pomocą techniczną w celu rozwiązywania problemów z wdrożeniem.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Wybierz grupę zasobów, którą chcesz przeanalizować.

1. Wybierz link w obszarze **wdrożenia**.

   ![Wybierz historię wdrożenia](./media/deployment-history/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrażania.

   ![Wybieranie wdrożenia](./media/deployment-history/select-details.png)

1. Zostanie wyświetlone podsumowanie wdrożenia, w tym identyfikator korelacji. 

    ![Podsumowanie wdrożenia](./media/deployment-history/show-correlation-id.png)

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyświetlić listę wszystkich wdrożeń dla grupy zasobów, użyj polecenia [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Aby uzyskać określone wdrożenie z grupy zasobów, Dodaj parametr **deploymentname** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Aby uzyskać identyfikator korelacji, użyj:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę wdrożenia dla grupy zasobów, użyj polecenie [AZ Group Deployment list](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-list).

```azurecli-interactive
az group deployment list --resource-group ExampleGroup
```

Aby uzyskać określone wdrożenie, użyj polecenie [AZ Group Deployment show](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-show).

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment
```
  
Aby uzyskać identyfikator korelacji, użyj:

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Aby wyświetlić listę wdrożeń dla grupy zasobów, użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [wdrożenia — lista według grupy zasobów](/rest/api/resources/deployments/listbyresourcegroup). 

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

W celu uzyskania określonego wdrożenia. Użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Deployments-Get](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Odpowiedź zawiera identyfikator korelacji.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Pobierz operacje wdrażania i komunikat o błędzie

Każde wdrożenie może obejmować wiele operacji. Aby wyświetlić więcej szczegółów na temat wdrożenia, Wyświetl operacje wdrażania. W przypadku niepowodzenia wdrożenia operacje wdrażania obejmują komunikat o błędzie.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Na stronie Podsumowanie wdrożenia wybierz pozycję **szczegóły operacji**.

    ![Wybierz operacje wdrażania](./media/deployment-history/get-operation-details.png)

1. Zobaczysz szczegóły dotyczące tego kroku wdrożenia. W przypadku wystąpienia błędu szczegóły obejmują komunikat o błędzie.

    ![Pokaż szczegóły operacji](./media/deployment-history/see-operation-details.png)

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyświetlić operacje wdrażania dla wdrożenia w grupie zasobów, użyj polecenia [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) .

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Aby wyświetlić operacje zakończone niepowodzeniem, Filtruj operacje z **niepowodzeniem** stanu.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Aby uzyskać komunikat o stanie zakończonych niepowodzeniem operacji, użyj następującego polecenia:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić operacje wdrażania dla wdrożenia w grupie zasobów, użyj polecenia [AZ Group Deployment Operation](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-group-deployment-operation-list) .

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeployment
```

Aby wyświetlić operacje zakończone niepowodzeniem, Filtruj operacje z **niepowodzeniem** stanu.

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Aby uzyskać komunikat o stanie zakończonych niepowodzeniem operacji, użyj następującego polecenia:

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Aby pobrać operacje wdrażania, użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Operations-list](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Odpowiedź zawiera komunikat o błędzie.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pomoc dotyczącą rozwiązywania określonych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure za pomocą Azure Resource Manager](common-deployment-errors.md).
* Aby dowiedzieć się więcej o korzystaniu z dzienników aktywności do monitorowania innych typów akcji, zobacz [Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure](../management/view-activity-logs.md).
* Aby sprawdzić poprawność wdrożenia przed jego wykonaniem, zobacz [wdrażanie grupy zasobów przy użyciu szablonu Azure Resource Manager](deploy-powershell.md).

