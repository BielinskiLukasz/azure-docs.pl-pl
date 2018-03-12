---
title: "Zarządzanie kontrolą dostępu na podstawie ról (RBAC) przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft"
description: "Informacje o sposobie zarządzania opartego na rolach kontrola dostępu (RBAC) przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą listę ról i akcje ról i przypisywania ról do zakresów subskrypcji i aplikacji."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 6c9df11e528601d94cb72a8e3ef0868dc7781e12
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Zarządzanie oparte na rolach kontrola dostępu przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Program PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](role-based-access-control-manage-access-azure-cli.md)
> * [Interfejs API REST](role-based-access-control-manage-access-rest.md)


Z opartej na rolach kontroli dostępu (RBAC), należy zdefiniować dostęp dla użytkowników, grup i nazwy główne usług poprzez przypisywanie ról w określonym zakresie. W tym artykule opisano, jak zarządzać dostępem przy użyciu interfejsu wiersza polecenia platformy Azure (CLI).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarządzać RBAC przy użyciu wiersza polecenia platformy Azure, musi mieć następujące wymagania wstępne:

* [Interfejs wiersza polecenia platformy Azure 2.0](/cli/azure). Możesz używać go w przeglądarce dzięki usłudze [Azure Cloud Shell](../cloud-shell/overview.md), albo [zainstalować](/cli/azure/install-azure-cli) w systemach macOS, Linux i Windows, a następnie uruchamiać z wiersza polecenia.

## <a name="list-roles"></a>Lista ról

### <a name="list-role-definitions"></a>Definicje ról listy

Aby wyświetlić listę wszystkich dostępnych definicji ról, należy użyć [listy definicji roli az](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list
```

Poniższy przykład zawiera nazwę i opis wszystkich dostępnych definicji ról:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Poniższy przykład zawiera listę wszystkich definicji wbudowanej roli:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description, "type":.properties.type}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "type": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>Akcje listy definicji roli

Aby wyświetlić listę akcji definicji roli, należy użyć [listy definicji roli az](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list --name <role_name>
```

Na poniższych listach przykład *współautora* definicji roli:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "properties": {
      "additionalProperties": {
        "createdBy": null,
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedBy": null,
        "updatedOn": "2016-12-14T02:04:45.1393855Z"
      },
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything except access to resources.",
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": [
            "Microsoft.Authorization/*/Delete",
            "Microsoft.Authorization/*/Write",
            "Microsoft.Authorization/elevateAccess/Action"
          ]
        }
      ],
      "roleName": "Contributor",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Na poniższych listach przykład *akcje* i *notActions* z *współautora* roli:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.properties.permissions[0].actions, "notActions":.properties.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

Poniższy przykład zawiera listę działań *Współautor·maszyny·wirtualnej* roli:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .properties.permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Dostęp do listy

### <a name="list-role-assignments-for-a-user"></a>Lista przypisań ról dla użytkownika

Aby wyświetlić listę przypisań ról określonego użytkownika, należy użyć [listy przypisania roli az](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --assignee <assignee>
```

Domyślnie będą wyświetlane tylko przypisania ograniczone do subskrypcji. Aby wyświetlić przypisania ograniczone w zależności od zasobów w grupie, należy użyć `--all`.

Poniższy przykład zawiera listę przypisań ról, które są przypisane bezpośrednio do  *patlong@contoso.com*  użytkownika:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.properties.principalName, "roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Lista przypisań ról dla grupy zasobów.

Aby wyświetlić listę przypisań ról, które istnieją dla grupy zasobów, należy użyć [listy przypisania roli az](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Poniższy przykład zawiera listę przypisań ról *pharma sprzedaży projectforecast* grupy zasobów:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="assign-access"></a>Przypisywanie dostępu

### <a name="assign-a-role-to-a-user"></a>Przypisywanie użytkownikowi roli

Aby przypisać rolę do użytkownika w zakresie grupy zasobów, należy użyć [utworzenia przypisania roli az](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

W poniższym przykładzie przypisano *współautora maszyny wirtualnej* rolę  *patlong@contoso.com*  użytkownika na *pharma sprzedaży projectforecast* zakres grupy zasobów:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="assign-a-role-to-a-group"></a>Przypisywanie roli do grupy

Aby przypisać rolę do grupy, należy użyć [utworzenia przypisania roli az](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

W poniższym przykładzie przypisano *czytnika* rolę *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, można użyć [listy grup ad az](/cli/azure/ad/group#az_ad_group_list) lub [Pokaż grupy ad az](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

W poniższym przykładzie przypisano *współautora maszyny wirtualnej* rolę *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie zasobów dla sieci wirtualnej o nazwie *projektu sieci, pharma sprzedaży w-*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="assign-a-role-to-an-application"></a>Przypisywanie roli do aplikacji

Aby przypisać rolę do aplikacji, należy użyć [utworzenia przypisania roli az](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

W poniższym przykładzie przypisano *Współautor·maszyny·wirtualnej* roli do aplikacji z 44444444-4444-4444-4444-444444444444 Identyfikatora obiektu w *pharma sprzedaży projectforecast* grupy zasobów zakres. Aby uzyskać identyfikator aplikacji, można użyć [listy aplikacji usługi ad az](/cli/azure/ad/app#az_ad_app_list) lub [Pokaż aplikacji ad az](/cli/azure/ad/app#az_ad_app_show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Usuń dostęp

### <a name="remove-a-role-assignment"></a>Usuń przypisanie roli

Aby usunąć przypisanie roli, należy użyć [usunąć przypisanie roli az](/cli/azure/role/assignment#az_role_assignment_delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

Poniższy przykład umożliwia usunięcie *Współautor·maszyny·wirtualnej* przypisania roli z  *patlong@contoso.com*  użytkownika na *pharma sprzedaży projectforecast* zasobów Grupa:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

Poniższy przykład umożliwia usunięcie *czytnika* rolę z *zespołu Mack pods* z Identyfikatorem 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać identyfikator grupy, można użyć [listy grup ad az](/cli/azure/ad/group#az_ad_group_list) lub [Pokaż grupy ad az](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Role niestandardowe

### <a name="list-custom-roles"></a>Lista ról niestandardowych

Aby wyświetlić listę ról, które są dostępne do przypisania w zakresie, należy użyć [listy definicji roli az](/cli/azure/role/definition#az_role_definition_list).

Oba poniższe przykłady listy role niestandardowe w bieżącej subskrypcji:

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.properties.roleName, "type":.properties.type}'
```

```azurecli
az role definition list --output json | jq '.[] | if .properties.type == "CustomRole" then {"roleName":.properties.roleName, "type":.properties.type} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Tworzenie niestandardowej roli zabezpieczeń

Aby utworzyć niestandardową rolę, użyj [utworzenia definicji roli az](/cli/azure/role/definition#az_role_definition_create). Definicja roli może być opisu JSON lub ścieżka do pliku zawierającego opis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

W poniższym przykładzie tworzona rola niestandardowa o nazwie *Operator maszyny wirtualnej*. Tę rolę niestandardową przypisuje dostęp do wszystkich operacji odczytu z *Microsoft.Compute*, *Microsoft.Storage*, i *Microsoft.Network* dostęp do dostawcy i przypisuje zasobów Aby rozpocząć, uruchom ponownie i monitorowania maszyn wirtualnych. Tę rolę niestandardową można w dwóch subskrypcji. W tym przykładzie używany jest plik JSON jako danych wejściowych.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>Aktualizacja niestandardowej roli zabezpieczeń

Aby zaktualizować niestandardowej roli zabezpieczeń, należy najpierw użyć [listy definicji roli az](/cli/azure/role/definition#az_role_definition_list) można pobrać definicji roli. Po drugie wprowadź żądane zmiany do definicji roli. Na koniec użyj [aktualizacji definicji roli az](/cli/azure/role/definition#az_role_definition_update) można zapisać definicji roli zaktualizowane.

```azurecli
az role definition update --role-definition <role_definition>
```

W poniższym przykładzie dodano *Microsoft.Insights/diagnosticSettings/* operacji *akcje* z *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>Usunięcia niestandardowej roli zabezpieczeń

Aby usunąć niestandardową rolę, użyj [usunąć definicji roli az](/cli/azure/role/definition#az_role_definition_delete). Aby określić roli do usunięcia, użyj nazwy roli lub identyfikator roli. Aby określić identyfikator roli, należy użyć [listy definicji roli az](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition delete --name <role_name or role_id>
```

W następującym przykładzie *Operator maszyny wirtualnej* niestandardowej roli zabezpieczeń:

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

