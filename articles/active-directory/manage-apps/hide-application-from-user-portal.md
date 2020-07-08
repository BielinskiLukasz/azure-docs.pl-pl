---
title: Ukrywanie aplikacji przedsiębiorstwa ze środowiska użytkownika w usłudze Azure AD
description: Sposób ukrycia aplikacji Enterprise ze środowiska użytkownika w Azure Active Directory panele dostępu lub uruchamiania pakietu Office 365.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ba14fba24c9b8e0b460e56b93d0e5212bfb27
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367703"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Ukryj aplikacje dla przedsiębiorstw od użytkowników końcowych w Azure Active Directory

Instrukcje dotyczące sposobu ukrywania aplikacji z poziomu panelu Moje aplikacje użytkowników końcowych lub uruchamiania pakietu Office 365. Gdy aplikacja jest ukryta, użytkownicy nadal mają uprawnienia do aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Uprawnienia administratora aplikacji są wymagane do ukrycia aplikacji z poziomu panelu Moje aplikacje i uruchamiania pakietu Office 365.

Aby ukryć wszystkie aplikacje pakietu Office 365, wymagane są uprawnienia administratora globalnego.


## <a name="hide-an-application-from-the-end-user"></a>Ukrywanie aplikacji od użytkownika końcowego
Wykonaj następujące kroki, aby ukryć aplikację z poziomu panelu aplikacje i uruchamiania aplikacji pakietu Office 365.

1.  Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny katalogu.
2.  Wybierz pozycję **Azure Active Directory**.
3.  Wybierz pozycję **aplikacje dla przedsiębiorstw**. Zostanie otwarty blok **aplikacje dla przedsiębiorstw — wszystkie aplikacje** .
4.  W obszarze **Typ aplikacji**wybierz pozycję **aplikacje dla przedsiębiorstw**, jeśli nie została jeszcze wybrana.
5.  Wyszukaj aplikację, którą chcesz ukryć, a następnie kliknij aplikację.  Zostanie otwarta przegląd aplikacji.
6.  Kliknij pozycję **Właściwości**. 
7.  W przypadku pytania **widoczny dla użytkowników** kliknij przycisk **nie**.
8.  Kliknij pozycję **Zapisz**.

> [!NOTE]
> Te instrukcje dotyczą tylko aplikacji dla przedsiębiorstw.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Ukrywanie aplikacji przy użyciu programu Azure AD PowerShell

Aby ukryć aplikację z poziomu panelu Moje aplikacje, można ręcznie dodać tag HideApp do nazwy głównej usługi dla aplikacji. Uruchom następujące polecenia [programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) w programie AzureAD, aby ustawić **widoczność aplikacji dla użytkowników?** wartość **nie**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Ukrywanie aplikacji pakietu Office 365 z poziomu panelu Moje aplikacje

Wykonaj następujące kroki, aby ukryć wszystkie aplikacje pakietu Office 365 z poziomu panelu Moje aplikacje. Aplikacje są nadal widoczne w portalu pakietu Office 365.

1.  Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny katalogu.
2.  Wybierz pozycję **Azure Active Directory**.
3.  Wybierz pozycję **Użytkownicy**.
4.  Wybierz pozycję **Ustawienia użytkownika**.
5.  W obszarze **aplikacje dla przedsiębiorstw**kliknij pozycję **Zarządzaj sposobem uruchamiania i wyświetlania aplikacji przez użytkowników końcowych.**
6.  Aby **użytkownicy mogli zobaczyć tylko aplikacje pakietu office 365 w portalu pakietu office 365**, kliknij przycisk **tak**.
7.  Kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](assign-user-or-group-access-portal.md)
* [Usuwanie przypisania użytkownika lub grupy z aplikacji dla przedsiębiorstw](remove-user-or-group-access-portal.md)
* [Zmiana nazwy lub logo aplikacji dla przedsiębiorstw](change-name-or-logo-portal.md)

