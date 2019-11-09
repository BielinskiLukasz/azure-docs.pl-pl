---
title: Wdrażanie Azure Policy do delegowanych subskrypcji na dużą skalę
description: Dowiedz się, jak zarządzanie zasobami delegowanymi przez platformę Azure umożliwia wdrożenie definicji zasad i przypisania zasad dla wielu dzierżawców.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 11/8/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 537802358af4dbca4f9e24699fc8455c1c6ef01b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893767"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Wdrażanie Azure Policy do delegowanych subskrypcji na dużą skalę

Jako dostawca usług możesz dołączyć wielu dzierżawców klientów do zarządzania zasobami delegowanymi przez platformę Azure. Usługa Azure Lighthouse umożliwia dostawcom usług wykonywanie operacji na dużą skalę w wielu dzierżawcach, co sprawia, że zadania zarządzania są bardziej wydajne.

W tym temacie przedstawiono sposób użycia [Azure Policy](https://docs.microsoft.com/azure/governance/policy/) do wdrożenia definicji zasad i przypisania zasad dla wielu dzierżawców przy użyciu poleceń programu PowerShell. W tym przykładzie definicja zasad gwarantuje, że konta magazynu są zabezpieczone przez umożliwienie tylko ruchu HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Korzystanie z grafu zasobów platformy Azure do wykonywania zapytań w dzierżawach klientów

Korzystając z [grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/) , można wykonywać zapytania dotyczące wszystkich subskrypcji w dzierżawach klientów, którymi zarządzasz. W tym przykładzie określimy wszystkie konta magazynu w tych subskrypcjach, które nie wymagają obecnie ruchu HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Wdrażanie zasad dla wielu dzierżawców klientów

W poniższym przykładzie pokazano, jak użyć [szablonu Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json) do wdrożenia definicji zasad i przypisania zasad w ramach delegowanych subskrypcji w wielu dzierżawach klientów. Ta definicja zasad wymaga, aby wszystkie konta magazynu używały ruchu HTTPS, uniemożliwiając tworzenie nowych kont magazynu, które nie spełniają wymagań i oznacza istniejące konta magazynu bez ustawienia jako niezgodne.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Weryfikowanie wdrożenia zasad

Po wdrożeniu szablonu Azure Resource Manager można potwierdzić, że definicja zasad została pomyślnie zastosowana, próbując utworzyć konto magazynu z **EnableHttpsTrafficOnly** ustawionym na **wartość false** w jednej z delegowanych subskrypcji. W związku z przypisaniem zasad nie powinno być możliwe utworzenie tego konta magazynu.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu usuń definicję zasad i przypisanie utworzone przez wdrożenie.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Azure Policy](https://docs.microsoft.com/azure/governance/policy/).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
