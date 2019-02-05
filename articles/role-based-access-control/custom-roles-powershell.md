---
title: Tworzenie ról niestandardowych przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach (RBAC) przy użyciu programu Azure PowerShell. W tym jak listy, tworzenia, aktualizacji i usuwania ról niestandardowych.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 26e5b33504ff543e8442108e4368ce3b04f25df4
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696765"
---
# <a name="create-custom-roles-using-azure-powershell"></a>Tworzenie ról niestandardowych przy użyciu programu Azure PowerShell

Jeśli [role wbudowane](built-in-roles.md) nie spełniają specyficznych potrzeb Twojej organizacji, możesz utworzyć własne role niestandardowe. W tym artykule opisano, jak tworzyć i zarządzać nimi przy użyciu programu Azure PowerShell ról niestandardowych.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć niestandardowe role, potrzebne są:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- Program [Azure PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę ról, które są dostępne do przypisania w zakresie, należy użyć [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) polecenia. Poniższy przykład wyświetla listę wszystkich ról, które są dostępne do przypisania w wybranej subskrypcji.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

Poniższy przykład wyświetla listę tylko role niestandardowe, które są dostępne do przypisania w wybranej subskrypcji.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Jeśli wybrana subskrypcja nie znajduje się w `AssignableScopes` roli, nie będą wyświetlane roli niestandardowej.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, należy użyć [New AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) polecenia. Istnieją dwie metody tworzenia struktury tej roli, przy użyciu `PSRoleDefinition` obiektu lub szablon JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Pobierz operacje dla dostawcy zasobów

Podczas tworzenia ról niestandardowych należy znać wszystkich możliwych operacji od dostawcy zasobów.
Można wyświetlić listę [operacji dostawcy zasobów](resource-provider-operations.md) lub użyć [Get AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) polecenie, aby uzyskać te informacje.
Na przykład jeśli chcesz sprawdzić wszystkich dostępnych operacji dla maszyn wirtualnych, należy użyć tego polecenia:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Utworzyć rolę niestandardową z obiektem PSRoleDefinition

Gdy używasz programu PowerShell można utworzyć rolę niestandardową, można użyć dowolnego [wbudowane role](built-in-roles.md) jako punkt początkowy, lub możesz zacząć od podstaw. Pierwszy przykład w tej sekcji zaczyna się od wbudowana rola i dostosowuje go za pomocą większe uprawnienia. Edycja atrybutów do dodania `Actions`, `NotActions`, lub `AssignableScopes` , a następnie zapisz zmiany jako nową rolę.

Poniższy przykład rozpoczyna się od [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) rolę wbudowaną, aby utworzyć rolę niestandardową o nazwie *Operator maszyny wirtualnej*. Nowa rola przyznaje dostęp do wszystkich operacji odczytu z *Microsoft.Compute*, *Microsoft.Storage*, i *Microsoft.Network* dostawców i przyznaje dostęp do zasobów można uruchomić , ponownie uruchomić i monitorować maszyny wirtualne. Rola niestandardowa może służyć do dwóch subskrypcji.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

W poniższym przykładzie pokazano innym sposobem na utworzenie *Operator maszyny wirtualnej* roli niestandardowej. Uruchamia przez utworzenie nowego `PSRoleDefinition` obiektu. Operacje akcji są określone w `perms` zmiennej i ustawienie `Actions` właściwości. `NotActions` Właściwość jest ustawiona, czytając `NotActions` z [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) wbudowana rola. Ponieważ [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) nie ma żadnych `NotActions`, ten wiersz nie jest wymagana, ale pokazuje, jak można pobrać informacji z inną rolą.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Tworzenie roli niestandardowej za pomocą szablonu JSON

Szablon JSON może służyć jako definicję źródła dla roli niestandardowej. W poniższym przykładzie utworzono rolę niestandardową, która umożliwia dostęp do odczytu do magazynu oraz zasobów obliczeniowych, dostęp do pomocy technicznej, i dodaje tę rolę z dwiema subskrypcjami. Utwórz nowy plik `C:\CustomRoles\customrole1.json` z poniższym przykładzie. Identyfikator powinien być ustawiony na `null` przy tworzeniu rola początkowa jako nowy identyfikator jest generowany automatycznie. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Aby dodać rolę do subskrypcji, uruchom następujące polecenie programu PowerShell:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Podobnie jak tworzenie roli niestandardowej, możesz zmodyfikować przy użyciu istniejącej roli niestandardowej `PSRoleDefinition` obiektu lub szablon JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aktualizacja roli niestandardowej za pomocą obiektu PSRoleDefinition

Aby zmodyfikować rolę niestandardową, najpierw użyj [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) polecenie, aby pobrać definicji roli. Po drugie wprowadź żądane zmiany do definicji roli. Na koniec użyj [AzRoleDefinition zestaw](/powershell/module/az.resources/set-azroledefinition) polecenie, aby zapisać definicji roli zmodyfikowane.

W poniższym przykładzie dodano `Microsoft.Insights/diagnosticSettings/*` operacji *Operator maszyny wirtualnej* roli niestandardowej.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

Poniższy przykład dodaje subskrypcji platformy Azure na zakresy można przypisać *Operator maszyny wirtualnej* roli niestandardowej.

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Aktualizacja roli niestandardowej przy użyciu szablonu JSON

Przy użyciu poprzednich szablonu JSON, można łatwo modyfikować istniejące rolę niestandardową, aby dodawać lub usuwać akcje. Aktualizowanie szablonu JSON, a następnie dodaj akcję odczytu dla sieci, jak pokazano w poniższym przykładzie. Łącznie z definicjami wymienionymi w szablonie nie są stosowane do istniejącej definicji, co oznacza, że rola jest wyświetlana dokładnie tak, jak określić w szablonie. Należy również zaktualizować pole identyfikatora z Identyfikatorem roli. Jeśli nie masz pewności, ta wartość jest, możesz użyć [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) polecenia cmdlet, aby uzyskać te informacje.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Aby zaktualizować istniejącą rolę, uruchom następujące polecenie programu PowerShell:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć niestandardową rolę, użyj [AzRoleDefinition Usuń](/powershell/module/az.resources/remove-azroledefinition) polecenia.

Poniższy przykład usuwa *Operator maszyny wirtualnej* roli niestandardowej.

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Utworzyć rolę niestandardową przy użyciu programu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Role niestandardowe na platformie Azure](custom-roles.md)
- [Operacje dostawcy zasobów w usłudze Azure Resource Manager](resource-provider-operations.md)
