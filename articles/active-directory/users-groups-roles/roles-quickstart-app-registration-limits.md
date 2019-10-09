---
title: Przyznaj indywidualną zgodę na przekroczenie ograniczeń rejestracji aplikacji — Azure Active Directory | Microsoft Docs
description: Przypisz rolę niestandardową, aby udzielić nieograniczonej rejestracji aplikacji w usłudze Azure AD Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91de3de743d168bea207f27fb162486ea625a63
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026286"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Szybki Start: Przyznawanie uprawnień do tworzenia nieograniczonej rejestracji aplikacji

W tym przewodniku szybki start utworzysz rolę niestandardową z uprawnieniem do tworzenia nieograniczonej liczby rejestracji aplikacji, a następnie przypiszesz tę rolę użytkownikowi. Przypisany użytkownik może następnie użyć portalu usługi Azure AD, programu Azure AD PowerShell, usługi Azure AD interfejs API programu Graph lub interfejsu API Microsoft Graph do tworzenia rejestracji aplikacji. W przeciwieństwie do wbudowanej roli Deweloper aplikacji ta rola niestandardowa umożliwia tworzenie nieograniczonej liczby rejestracji aplikacji. Rola dewelopera aplikacji zapewnia możliwość, ale całkowita liczba utworzonych obiektów jest ograniczona do 250, aby zapobiec wykorzystaniu [limitu przydziału obiektów całego katalogu](directory-service-limits-restrictions.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisite"></a>Warunek wstępny

Najmniej uprzywilejowana rola wymagana do tworzenia i przypisywania ról niestandardowych usługi Azure AD jest administratorem roli uprzywilejowanej.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Tworzenie nowej roli niestandardowej przy użyciu portalu usługi Azure AD

1. Zaloguj się do [Centrum administracyjnego usługi Azure ad](https://aad.portal.azure.com)@no__t 1With administrator ról uprzywilejowanych lub administratora globalnego w organizacji usługi Azure AD.
1. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **role i Administratorzy**, a następnie wybierz pozycję **Nowa rola niestandardowa**.

    ![Tworzenie lub edytowanie ról na stronie role i Administratorzy](./media/roles-create-custom/new-custom-role.png)

1. Na karcie **podstawowe** podaj wartość "Kreator rejestracji aplikacji" dla nazwy roli i "można utworzyć nieograniczoną liczbę rejestracji aplikacji" w opisie roli, a następnie wybierz przycisk **dalej**.

    ![Podaj nazwę i opis roli niestandardowej na karcie podstawowe](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Na karcie **uprawnienia** wprowadź ciąg "Microsoft. Directory/Applications/Create" w polu wyszukiwania, a następnie zaznacz pola wyboru obok żądanych uprawnień, a następnie wybierz przycisk **dalej**.

    ![Wybierz uprawnienia roli niestandardowej na karcie uprawnienia](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Na karcie **Recenzja + tworzenie** Przejrzyj uprawnienia i wybierz pozycję **Utwórz**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Przypisywanie roli do użytkownika przy użyciu portalu usługi Azure AD

1. Zaloguj się do [Centrum administracyjnego usługi Azure ad](https://aad.portal.azure.com)@no__t 1with administrator ról uprzywilejowanych lub administratora globalnego w organizacji usługi Azure AD.
1. Wybierz pozycję **Azure Active Directory** a następnie wybierz pozycję **role i Administratorzy**.
1. Wybierz rolę twórcę rejestracji aplikacji i wybierz pozycję **Dodaj przypisanie**.
1. Wybierz żądanego użytkownika, a następnie kliknij pozycję **Wybierz** , aby dodać użytkownika do roli.

Gotowe! W tym przewodniku szybki start pomyślnie utworzono rolę niestandardową z uprawnieniem do tworzenia nieograniczonej liczby rejestracji aplikacji, a następnie przypiszesz tę rolę do użytkownika.

> [!TIP]
> Aby przypisać rolę do aplikacji przy użyciu portalu usługi Azure AD, wprowadź nazwę aplikacji w polu wyszukiwania na stronie przypisanie. Aplikacje nie są domyślnie wyświetlane na liście, ale są zwracane w wynikach wyszukiwania.

### <a name="app-registration-permissions"></a>Uprawnienia do rejestracji aplikacji

Dostępne są dwa uprawnienia do przyznawania możliwości tworzenia rejestracji aplikacji, z których każdy ma inne zachowanie.

- Microsoft. Directory/Applications/createAsOwner: przypisanie wyników tego uprawnienia do twórcy dodawanego jako pierwszy właściciel utworzonej rejestracji aplikacji, a rejestracja utworzonej aplikacji będzie liczona względem przydziału 250 obiektów utworzonych przez twórcę.
- Microsoft. Directory/applicationPolicies/Create: przypisanie wyników tego uprawnienia do twórcy nie jest dodawany jako pierwszy właściciel utworzonej rejestracji aplikacji, a rejestracja utworzonej aplikacji nie będzie liczona względem utworzonych obiektów 250 przez twórcę działa. Należy dokładnie użyć tego uprawnienia, ponieważ nie ma możliwości uniemożliwienia sobie tworzenia rejestracji aplikacji, dopóki nie zostanie osiągnięty limit przydziału na poziomie katalogu. Jeśli przypisane są oba uprawnienia, ma to pierwszeństwo.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Tworzenie roli niestandardowej przy użyciu programu Azure AD PowerShell

Utwórz nową rolę przy użyciu następującego skryptu programu PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Przypisywanie roli niestandardowej przy użyciu programu Azure AD PowerShell

#### <a name="prepare-powershell"></a>Przygotowywanie programu PowerShell

Najpierw zainstaluj moduł Azure AD PowerShell z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Następnie zaimportuj moduł programu Azure AD PowerShell w wersji zapoznawczej przy użyciu następującego polecenia:

```powershell
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, Dopasuj wersję zwróconą przez następujące polecenie do wymienionego poniżej:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Przypisywanie roli niestandardowej

Przypisz rolę przy użyciu poniższego skryptu programu PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Tworzenie roli niestandardowej przy użyciu interfejsu API Microsoft Graph

Żądanie HTTP, aby utworzyć rolę niestandardową.

POUBOJOWEGO

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Treść

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Przypisywanie roli niestandardowej przy użyciu interfejsu API Microsoft Graph

Przypisanie roli łączy Identyfikator podmiotu zabezpieczeń (który może być podmiotem użytkownika lub usługi), IDENTYFIKATORem roli (roli) i zakresem zasobów usługi Azure AD.

Żądanie HTTP do przypisania roli niestandardowej.

POUBOJOWEGO

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Treść

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Następne kroki

- Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](directory-assign-admin-roles.md).
- Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
