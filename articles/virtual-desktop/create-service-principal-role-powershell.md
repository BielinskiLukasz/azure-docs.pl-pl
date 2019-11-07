---
title: Przypisanie roli głównej usługi pulpitu wirtualnego systemu Windows — Azure
description: Jak utworzyć jednostki usługi i przypisać role przy użyciu programu PowerShell na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
ms.openlocfilehash: 1141731697c9f649a4a8d4052cd550605049b52e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606939"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Samouczek: tworzenie jednostek usługi i przypisań ról przy użyciu programu PowerShell

Nazwy główne usług są tożsamościami, które można utworzyć w Azure Active Directory, aby przypisać role i uprawnienia do określonego celu. Na pulpicie wirtualnym systemu Windows można utworzyć jednostkę usługi, aby:

- Automatyzowanie określonych zadań zarządzania pulpitem wirtualnym systemu Windows.
- Użyj jako poświadczeń zamiast użytkowników wymaganych przez uwierzytelnianie wieloskładnikowe podczas uruchamiania dowolnego szablonu Azure Resource Manager dla pulpitu wirtualnego systemu Windows.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz nazwę główną usługi w Azure Active Directory.
> * Utwórz przypisanie roli na pulpicie wirtualnym systemu Windows.
> * Zaloguj się do pulpitu wirtualnego systemu Windows przy użyciu nazwy głównej usługi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było tworzyć jednostki usługi i przypisania ról, należy wykonać trzy czynności:

1. Zainstaluj moduł AzureAD. Aby zainstalować moduł, uruchom program PowerShell jako administrator i uruchom następujące polecenie cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview).

3. Wykonaj wszystkie instrukcje zawarte w tym artykule w tej samej sesji programu PowerShell. Proces może nie zadziałać, jeśli sesja programu PowerShell zostanie przerwana przez zamknięcie okna i ponowne jego otwarcie.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Tworzenie nazwy głównej usługi w usłudze Azure Active Directory

Po spełnieniu wymagań wstępnych w sesji programu PowerShell uruchom następujące polecenia cmdlet programu PowerShell, aby utworzyć nazwę główną usługi wielodostępnej na platformie Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Wyświetlanie poświadczeń w programie PowerShell

Przed utworzeniem przypisania roli dla jednostki usługi, Wyświetl swoje poświadczenia i Zapisz je w celu uwzględnienia w przyszłości. Hasło jest szczególnie ważne, ponieważ nie będzie można go pobrać po zamknięciu sesji programu PowerShell.

Poniżej przedstawiono trzy poświadczenia, które należy napisać, i polecenia cmdlet, które należy uruchomić, aby je pobrać:

- Hasło

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Identyfikator dzierżawy:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Identyfikator aplikacji:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Tworzenie przypisania roli w wersji zapoznawczej pulpitu wirtualnego systemu Windows

Następnie należy utworzyć przypisanie roli, aby nazwa główna usługi mogła zalogować się do pulpitu wirtualnego systemu Windows. Upewnij się, że logujesz się przy użyciu konta z uprawnieniami do tworzenia przypisań ról.

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

Uruchom następujące polecenia cmdlet programu PowerShell, aby połączyć się z pulpitem wirtualnym systemu Windows i wyświetlić dzierżawców.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Po znalezieniu nazwy dzierżawy dla dzierżawy, dla której chcesz utworzyć przypisanie roli, Użyj tej nazwy w następującym poleceniu cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Zaloguj się przy użyciu nazwy głównej usługi

Po utworzeniu przypisania roli dla jednostki usługi upewnij się, że jednostka usługi może się zalogować do pulpitu wirtualnego systemu Windows, uruchamiając następujące polecenie cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Po zalogowaniu się upewnij się, że wszystko działa, testując kilka poleceń cmdlet programu PowerShell dla pulpitu wirtualnego systemu Windows z jednostką usługi.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu jednostki usługi i przypisaniu jej do roli w dzierżawie pulpitu wirtualnego systemu Windows możesz użyć jej do utworzenia puli hostów. Aby dowiedzieć się więcej na temat pul hostów, przejdź do samouczka dotyczącego tworzenia puli hostów w programie Virtual Desktop systemu Windows.

 > [!div class="nextstepaction"]
 > [Samouczek puli hostów usług pulpitu wirtualnego systemu Windows](./create-host-pools-azure-marketplace.md)
