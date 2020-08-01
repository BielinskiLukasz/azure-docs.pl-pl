---
title: Usuwanie przypisań ról z grupy w Azure Active Directory | Microsoft Docs
description: Wyświetl podgląd niestandardowych ról usługi Azure AD na potrzeby delegowania zarządzania tożsamościami. Zarządzanie rolami platformy Azure w Azure Portal, PowerShell lub interfejs API programu Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de09fddf6b02cfd57504994b4b8d3f7dc4254870
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476091"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Usuwanie przypisań ról z grupy w Azure Active Directory

W tym artykule opisano, jak administrator IT może usunąć role usługi Azure AD przypisane do grup. W Azure Portal można teraz usunąć przypisania roli bezpośrednie i pośrednie do użytkownika. Jeśli użytkownik ma przypisaną rolę według członkostwa w grupie, Usuń użytkownika z grupy, aby usunąć przypisanie roli.

## <a name="using-azure-admin-center"></a>Korzystanie z centrum administracyjnego platformy Azure

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu uprawnień administrator ról uprzywilejowanych lub Administrator globalny w organizacji usługi Azure AD.

1. Wybierz pozycję **role i Administratorzy**  >  ***Nazwa roli***.

1. Wybierz grupę, z której chcesz usunąć przypisanie roli, a następnie wybierz pozycję **Usuń przypisanie**.

   ![Usuń przypisanie roli z wybranej grupy.](./media/roles-groups-remove-assignment/remove-assignment.png)

1. Po wyświetleniu monitu o potwierdzenie działania wybierz pozycję **tak**.

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Tworzenie grupy, która może być przypisana do roli

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Pobierz definicję roli, do której chcesz przypisać grupę

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Usuń przypisanie roli

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Korzystanie z interfejsu API Microsoft Graph

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Tworzenie grupy, do której można przypisać rolę usługi Azure AD

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Pobierz definicję roli

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Utwórz przypisanie roli

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"resourceScope":"/"
}
```

### <a name="delete-role-assignment"></a>Usuń przypisanie roli

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie przypisaniami ról przy użyciu grup chmur](roles-groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup chmury](roles-groups-faq-troubleshooting.md)
