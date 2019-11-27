---
title: Wdrażanie zasad, które można skorygować
description: Dowiedz się, jak dołączyć klienta do zarządzania zasobami delegowanymi przez platformę Azure, umożliwiając dostęp do zasobów i zarządzanie nimi za pomocą własnej dzierżawy.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: 4522c9ebad741f5ec0cb7e56e68467312ef8f037
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463882"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Wdróż zasady, które można skorygować w ramach delegowanej subskrypcji

Usługa [Azure Lighthouse](../overview.md) umożliwia dostawcom usług tworzenie i edytowanie definicji zasad w ramach delegowanej subskrypcji. Aby jednak wdrażać zasady korzystające z [zadania korygowania](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) (czyli zasad z efektem [deployIfNotExists](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) lub [Modyfikacja](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify) ), należy utworzyć [tożsamość zarządzaną](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) w dzierżawie klienta. Ta tożsamość zarządzana może być używana przez Azure Policy do wdrożenia szablonu w ramach zasad. Aby włączyć ten scenariusz, należy wykonać kroki w przypadku dołączenia klienta do zarządzania zasobami delegowanymi przez platformę Azure i po wdrożeniu samej zasady.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Tworzenie użytkownika, który może przypisywać role do tożsamości zarządzanej w dzierżawie klienta

Po dołączeniu klienta do zarządzania zasobami delegowanymi przez platformę Azure można użyć [szablonu Azure Resource Manager](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer#create-an-azure-resource-manager-template) wraz z plikiem parametrów, który definiuje użytkowników, grupy użytkowników i jednostki usługi w dzierżawie zarządzającej, które będą mogły uzyskiwać dostęp do delegowanych zasobów w dzierżawie klienta. W pliku parametrów każdy z tych użytkowników (**principalId**) ma przypisaną [wbudowaną rolę](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (**zduplikowanych**), która definiuje poziom dostępu.

Aby umożliwić usłudze **principalId** utworzenie tożsamości zarządzanej w dzierżawie klienta, należy ustawić jej **zduplikowanych** na **administratora dostępu użytkowników**. Chociaż ta rola nie jest ogólnie obsługiwana, może być używana w tym konkretnym scenariuszu, co umożliwia użytkownikom z uprawnieniami do przypisywania do tożsamości zarządzanych co najmniej jednej konkretnej wbudowanej roli. Role te są zdefiniowane we właściwości **delegatedRoleDefinitionIds** . W tym miejscu możesz dołączyć dowolną wbudowaną rolę z wyjątkiem administratora dostępu użytkownika lub właściciela.

Po dołączeniu klienta **principalId** utworzony w ramach tej autoryzacji będzie mógł przypisywać te wbudowane role do zarządzanych tożsamości w dzierżawie klienta. Nie będą jednak mieć żadnych innych uprawnień zwykle skojarzonych z rolą administratora dostępu użytkownika.

W poniższym przykładzie przedstawiono **principalId** , którzy będą mieć rolę administratora dostępu użytkownika. Ten użytkownik będzie mógł przypisać dwie wbudowane role do zarządzanych tożsamości w dzierżawie klienta: współautor i Log Analytics.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Wdrażanie zasad, które można skorygować

Po utworzeniu użytkownika z wymaganymi uprawnieniami, zgodnie z powyższym opisem, ten użytkownik będzie mógł wdrażać zasady w dzierżawie klienta korzystającej z zadań korygowania.

Załóżmy na przykład, że chcesz włączyć diagnostykę zasobów Azure Key Vault w dzierżawie klienta, jak pokazano w tym [przykładzie](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring). Użytkownik w dzierżawie zarządzającej z odpowiednimi uprawnieniami (zgodnie z powyższym opisem) wdroży [szablon Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) , aby włączyć ten scenariusz.

Należy pamiętać, że tworzenie przypisania zasad, które ma być używane z delegowaną subskrypcją, musi być obecnie wykonywane za pomocą interfejsów API, a nie w Azure Portal. W takim przypadku **apiVersion** musi mieć ustawioną wartość **2019-04-01-Preview**, która obejmuje nową właściwość **delegatedManagedIdentityResourceId** . Ta właściwość umożliwia dołączenie tożsamości zarządzanej, która znajduje się w dzierżawie klienta (w ramach subskrypcji lub grupy zasobów, która została dołączona do zarządzania zasobami delegowanymi przez platformę Azure).

Poniższy przykład przedstawia przypisanie roli z **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> [Podobny przykład](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) jest dostępny w celu zademonstrowania sposobu wdrażania zasad, które dodają lub usuwają tag (przy użyciu efektu Modyfikuj) do delegowanej subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Azure Policy](https://docs.microsoft.com/azure/governance/policy/).
- Poznaj [zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
