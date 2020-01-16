---
title: Automatyzowanie dodawania użytkownika laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak zautomatyzować Dodawanie użytkownika laboratorium do laboratorium w Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: deec67a2c64a57bbb380b3fd87bf820499e6efed
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980046"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatyzuj Dodawanie użytkownika laboratorium do laboratorium w Azure DevTest Labs
Azure DevTest Labs umożliwia szybkie tworzenie samoobsługowych środowisk deweloperskich i testowych przy użyciu Azure Portal. Jeśli jednak masz kilka zespołów i kilka wystąpień DevTest Labs, Automatyzacja procesu tworzenia może zaoszczędzić czas. [Szablony Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) umożliwiają tworzenie laboratoriów, maszyn wirtualnych w laboratorium, obrazów niestandardowych, formuł i Dodawanie użytkowników w zautomatyzowany sposób. W tym artykule opisano sposób dodawania użytkowników do wystąpienia DevTest Labs.

Aby dodać użytkownika do laboratorium, należy dodać użytkownika do roli **użytkownika DevTest Labs** dla laboratorium. W tym artykule pokazano, jak zautomatyzować Dodawanie użytkownika do laboratorium przy użyciu jednego z następujących sposobów:

- Szablony usługi Azure Resource Manager
- Polecenia cmdlet programu PowerShell systemu Azure 
- Interfejs wiersza polecenia platformy Azure.

## <a name="use-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager
Następujący przykładowy szablon Menedżer zasobów określa użytkownika, który ma zostać dodany do roli **użytkownika laboratorium DevTest Labs** . 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Jeśli przypiszesz rolę w tym samym szablonie, który tworzy laboratorium, pamiętaj, aby dodać zależność między zasobem przypisania roli a laboratorium. Aby uzyskać więcej informacji, zobacz [Definiowanie zależności w artykule szablony Azure Resource Manager](../azure-resource-manager/templates/define-resource-dependency.md) .

### <a name="role-assignment-resource-information"></a>Informacje o zasobach przypisania roli
Zasób przypisania roli musi określać typ i nazwę.

Najpierw należy zwrócić uwagę, że typ zasobu nie jest `Microsoft.Authorization/roleAssignments` tak jak dla grupy zasobów.  Zamiast tego typ zasobu jest zgodny ze wzorcem `{provider-namespace}/{resource-type}/providers/roleAssignments`. W takim przypadku typ zasobu zostanie `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Sama nazwa przypisania roli musi być globalnie unikatowa.  Nazwa przypisania używa wzorca `{labName}/Microsoft.Authorization/{newGuid}`. `newGuid` jest wartością parametru szablonu. Gwarantuje to, że nazwa przypisania roli jest unikatowa. Ponieważ nie ma żadnych funkcji szablonu do tworzenia identyfikatorów GUID, musisz samodzielnie wygenerować identyfikator GUID przy użyciu dowolnego narzędzia generatora GUID.  

W szablonie nazwa przypisania roli jest definiowana przez zmienną `fullDevTestLabUserRoleName`. Dokładny wiersz z szablonu to:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Właściwości zasobów przypisania roli
Samo przypisanie roli definiuje trzy właściwości. Potrzebuje `roleDefinitionId`, `principalId`i `scope`.

### <a name="role-definition"></a>Definicja roli
Identyfikator definicji roli jest identyfikatorem ciągu dla istniejącej definicji roli. Identyfikator roli ma postać `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

Identyfikator subskrypcji uzyskuje się za pomocą funkcji `subscription().subscriptionId` Template.  

Musisz uzyskać definicję roli dla `DevTest Labs User` wbudowanej roli. Aby uzyskać identyfikator GUID dla roli [użytkownika DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) , można użyć [interfejsu API REST przypisania roli](/rest/api/authorization/roleassignments) lub polecenia cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) .

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Identyfikator roli jest zdefiniowany w sekcji zmienne i o nazwie `devTestLabUserRoleId`. W szablonie identyfikator roli jest ustawiony na: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Identyfikator podmiotu zabezpieczeń
Identyfikator podmiotu zabezpieczeń jest IDENTYFIKATORem obiektu Active Directory użytkownika, grupy lub jednostki usługi, który ma zostać dodany jako użytkownik laboratorium do laboratorium. Szablon używa `ObjectId` jako parametru.

Identyfikator ObjectId można uzyskać przy użyciu poleceń cmdlet [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup lub [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) programu PowerShell. Te polecenia cmdlet zwracają pojedyncze lub listy obiektów Active Directory, które mają Właściwość ID, która jest wymaganym IDENTYFIKATORem obiektu. Poniższy przykład pokazuje, jak uzyskać identyfikator obiektu pojedynczego użytkownika w firmie.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Można również użyć Azure Active Directory poleceń cmdlet programu PowerShell, które obejmują polecenia [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)i [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Zakres
Zakres określa zasób lub grupę zasobów, dla której ma być stosowane przypisanie roli. W przypadku zasobów zakres ma postać: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Szablon używa funkcji `subscription().subscriptionId`, aby wypełnić część `subscription-id` i funkcję szablonu `resourceGroup().name`, aby wypełnić część `resource-group-name`. Korzystanie z tych funkcji oznacza, że laboratorium, do którego przypiszesz rolę, musi istnieć w bieżącej subskrypcji i tej samej grupie zasobów, w której nastąpi wdrożenie szablonu. Ostatnia część, `resource-name`, jest nazwą laboratorium. Ta wartość jest odbierana za pośrednictwem parametru szablonu w tym przykładzie. 

Zakres roli w szablonie: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Wdrażanie szablonu
Najpierw utwórz plik parametrów (na przykład: azuredeploy. Parameters. JSON), który przekazuje wartości parametrów w szablonie Menedżer zasobów. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Następnie użyj polecenia cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) programu PowerShell, aby wdrożyć szablon Menedżer zasobów. Następujące przykładowe polecenie przypisuje osobę, grupę lub jednostkę usługi do roli użytkownika DevTest Labs dla laboratorium.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Należy pamiętać, że nazwa wdrożenia grupy i identyfikator GUID przypisania roli muszą być unikatowe. W przypadku próby wdrożenia przypisania zasobu z nieunikatowym identyfikatorem GUID zostanie wyświetlony komunikat o błędzie `RoleAssignmentUpdateNotPermitted`.

Jeśli planujesz użyć szablonu kilka razy, aby dodać kilka Active Directory obiektów do roli użytkownika DevTest Labs dla laboratorium, rozważ użycie obiektów dynamicznych w poleceniu programu PowerShell. W poniższym przykładzie za pomocą polecenia cmdlet [New-GUID](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) można dynamicznie określić nazwę wdrożenia grupy zasobów i identyfikator GUID przypisania roli.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Zgodnie z opisem we wprowadzeniu, tworzysz nowe przypisanie roli platformy Azure, aby dodać użytkownika do roli **użytkownika DevTest Labs** dla laboratorium. W programie PowerShell można to zrobić za pomocą polecenia cmdlet [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) . To polecenie cmdlet ma wiele opcjonalnych parametrów umożliwiających elastyczność. `ObjectId`, `SigninName`lub `ServicePrincipalName` można określić jako obiekt otrzymujący uprawnienia.  

Oto przykładowe Azure PowerShell polecenie, które dodaje użytkownika do roli użytkownika DevTest Labs w określonym laboratorium.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Aby określić zasób, do którego przyznano uprawnienia, można określić za pomocą kombinacji `ResourceName`, `ResourceType`, `ResourceGroup` lub przez `scope` parametru. Niezależnie od używanej kombinacji parametrów Podaj do polecenia cmdlet wystarczające informacje umożliwiające jednoznaczną identyfikację obiektu Active Directory (użytkownika, grupy lub nazwy głównej usługi), zakresu (grupy zasobów lub zasobu) i definicji roli.

## <a name="use-azure-command-line-interface-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure (CLI)
W interfejsie wiersza polecenia platformy Azure dodanie użytkownika Labs do laboratorium odbywa się przy użyciu `az role assignment create` polecenie. Aby uzyskać więcej informacji na temat poleceń cmdlet interfejsu wiersza polecenia platformy Azure, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md).

Obiekt, do którego uzyskuje się dostęp, może być określony przez `objectId`, `signInName``spn` parametrów. Laboratorium, do którego uzyskuje się dostęp do obiektu, może być identyfikowane za pomocą adresu URL `scope` lub kombinacji `resource-name`, `resource-type`i `resource-group` parametrów.

Poniższy przykład interfejsu wiersza polecenia platformy Azure pokazuje, jak dodać osobę do roli użytkownika DevTest Labs dla określonego laboratorium.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Tworzenie maszyn wirtualnych i zarządzanie nimi za pomocą DevTest Labs przy użyciu interfejsu wiersza polecenia platformy Azure](devtest-lab-vmcli.md)
- [Tworzenie maszyny wirtualnej za pomocą DevTest Labs przy użyciu Azure PowerShell](devtest-lab-vm-powershell.md)
- [Uruchamianie i zatrzymywanie Azure DevTest Labs maszyn wirtualnych przy użyciu narzędzi wiersza polecenia](use-command-line-start-stop-virtual-machines.md)

