---
title: Zmień ustawienia domyślne okresu istnienia tokenu dla niestandardowych aplikacji usługi Azure AD
description: Jak zaktualizować zasady okresu istnienia tokenu dla aplikacji opracowywanej w usłudze Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 9483fe972cf1a4dce4fb285ced3cb390d0bda725
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516787"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić wartości domyślne okresu istnienia tokenu dla aplikacji opracowanej niestandardowo

W tym artykule pokazano, jak ustawić zasady okresu istnienia tokenu przy użyciu programu Azure AD PowerShell. Azure AD — wersja Premium umożliwia deweloperom aplikacji i administratorom dzierżawy skonfigurowanie okresu istnienia tokenów wystawionych dla klientów niepoufnych. Zasady okresu istnienia tokenu są ustawiane dla całej dzierżawy lub do zasobów, do których uzyskuje się dostęp.

> [!IMPORTANT]
> Po 30 stycznia 2021 dzierżawcy nie będą już w stanie konfigurować okresów istnienia tokenów odświeżania i sesji, a Azure Active Directory nie będą przestrzegać istniejących konfiguracji odświeżania i tokenu sesji w zasadach po tej dacie. Nadal można skonfigurować okresy istnienia tokenu dostępu po zakończeniu działania. Aby uzyskać więcej informacji, Przeczytaj [konfigurowalne okresy istnienia tokenu w usłudze Azure AD](./active-directory-configurable-token-lifetimes.md).
> Zaimplementowano [funkcje zarządzania sesjami uwierzytelniania](../conditional-access/howto-conditional-access-session-lifetime.md)   w dostępie warunkowym usługi Azure AD. Ta nowa funkcja służy do konfigurowania okresów istnienia tokenu odświeżania przez ustawienie częstotliwości logowania.  

Aby ustawić zasady istnienia tokenu, należy pobrać [moduł programu PowerShell usługi Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).
Uruchom polecenie **Connect-AzureAD-Confirm** .

Poniżej przedstawiono przykładowe zasady, które wymagają, aby użytkownicy uwierzytelniali się częściej w aplikacji sieci Web. Te zasady określają okres istnienia tokenów dostępu/identyfikatora oraz maksymalny wiek tokenu sesji wieloskładnikowej do jednostki usługi aplikacji sieci Web. Utwórz zasady i przypisz je do nazwy głównej usługi. Należy również uzyskać identyfikator ObjectId nazwy głównej usługi.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Następne kroki

* Zobacz [konfigurowalne okresy istnienia tokenu w usłudze Azure AD](./active-directory-configurable-token-lifetimes.md) , aby dowiedzieć się, jak skonfigurować okresy istnienia tokenów wystawionych przez usługę Azure AD, w tym informacje o sposobie ustawiania okresów istnienia tokenu dla wszystkich aplikacji w organizacji, dla aplikacji z wieloma dzierżawami lub dla określonej jednostki usługi w organizacji. 
* [Odwołanie do tokenu usługi Azure AD](./id-tokens.md)