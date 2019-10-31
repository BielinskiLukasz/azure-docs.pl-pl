---
title: Windows Virtual Desktop PowerShell — Azure
description: Jak rozwiązywać problemy z programem PowerShell podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 38d9a2dda945f3a9459aa8e3360012c6ef422608
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163303"
---
# <a name="windows-virtual-desktop-powershell"></a>Program PowerShell dla usługi Windows Virtual Desktop

W tym artykule opisano błędy i problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows. Aby uzyskać więcej informacji na Usługi pulpitu zdalnego PowerShell, zobacz [Windows Virtual Desktop PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Prześlij opinię

Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Polecenia programu PowerShell używane podczas instalacji pulpitu wirtualnego systemu Windows

Ta sekcja zawiera listę poleceń programu PowerShell, które są zwykle używane podczas konfigurowania pulpitu wirtualnego systemu Windows i zapewnia sposoby rozwiązywania problemów, które mogą wystąpić podczas ich używania.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Błąd: polecenie Add-RdsAppGroupUser--określony element UserPrincipalName jest już przypisany do grupy aplikacji RemoteApp w określonej puli hostów

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Przyczyna:** Użyta nazwa użytkownika została już przypisana do grupy aplikacji innego typu. Użytkownicy nie mogą być przypisani zarówno do pulpitu zdalnego, jak i zdalnej grupy aplikacji w ramach tej samej puli hostów sesji.

**Poprawka:** Jeśli użytkownik potrzebuje zarówno aplikacji zdalnych, jak i pulpitu zdalnego, należy utworzyć różne pule hostów lub udzielić użytkownikowi dostępu do pulpitu zdalnego, co pozwoli na korzystanie z dowolnej aplikacji na maszynie wirtualnej hosta sesji.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Błąd: polecenie Add-RdsAppGroupUser--określony element UserPrincipalName nie istnieje w Azure Active Directory skojarzony z dzierżawą Pulpit zdalny

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Przyczyna:** Nie można znaleźć użytkownika określonego przez-UserPrincipalName w Azure Active Directory powiązanym z dzierżawą pulpitu wirtualnego systemu Windows.

**Poprawka:** Potwierdź elementy z poniższej listy.

- Użytkownik jest zsynchronizowany do Azure Active Directory.
- Użytkownik nie jest powiązany z handlem firmy z klientem (B2C) ani z firmą B2B (Business-to-Business).
- Dzierżawa pulpitu wirtualnego systemu Windows jest powiązana z poprawnym Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Błąd: Get-RdsDiagnosticActivities — użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Przyczyna:** -dzierżawca parametru

**Poprawka:** Wydaj błąd Get-RdsDiagnosticActivities z-Dzierżawcname \<dzierżawca >.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Get-RdsDiagnosticActivities — użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Przyczyna:** Użycie przełącznika-Deployment.

**Naprawa:** przełącznik wdrożenia może być używany tylko przez administratorów wdrożenia. Ci Administratorzy są zwykle członkami zespołu pulpitów wirtualnych Usługi pulpitu zdalnego/Windows. Zastąp ciąg-Deployment Switch z parametrem-Dzierżawcname \<dzierżawca >.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: New-RdsRoleAssignment--użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania

**Przyczyna 1:** Używane konto nie ma uprawnień właściciela Usługi pulpitu zdalnego w dzierżawie.

**Poprawka 1:** Użytkownik z uprawnieniami Usługi pulpitu zdalnego właściciela musi wykonać przypisanie roli.

**Przyczyna 2:** Używane konto ma Usługi pulpitu zdalnego uprawnienia właściciela, ale nie jest częścią Azure Active Directory dzierżawy lub nie ma uprawnień do wykonywania zapytań w Azure Active Directory, w którym znajduje się użytkownik.

**Poprawka 2:** Użytkownik z uprawnieniami Active Directory musi wykonać przypisanie roli.

>[!Note]
>New-RdsRoleAssignment nie może udzielić uprawnień użytkownikowi, który nie istnieje w Azure Active Directory (AD).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [pulpit zdalny połączenia klientów](troubleshoot-client-connection.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
