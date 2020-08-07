---
title: Aplikacje w chmurze lub akcje w zasadach dostępu warunkowego — Azure Active Directory
description: Co to są aplikacje w chmurze lub akcje w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: defb959a56446da337c4c7c16ee118ceecd3e674
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846313"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Dostęp warunkowy: aplikacje lub akcje w chmurze

Aplikacje w chmurze lub akcje są kluczowym sygnałem w zasadach dostępu warunkowego. Zasady dostępu warunkowego umożliwiają administratorom przypisywanie formantów do określonych aplikacji lub akcji.

- Administratorzy mogą wybrać z listy aplikacji, które zawierają wbudowane aplikacje firmy Microsoft i wszystkie [zintegrowane aplikacje usługi Azure AD](../manage-apps/what-is-application-management.md) , w tym galerię, nie galerię i aplikacje publikowane za pośrednictwem [serwera proxy aplikacji](../manage-apps/what-is-application-proxy.md).
- Administratorzy mogą określić, że zasady nie są oparte na aplikacji w chmurze, ale w akcji użytkownika. Jedyną obsługiwaną akcją jest rejestrowanie informacji o zabezpieczeniach (wersja zapoznawcza), co umożliwia dostęp warunkowy do wymuszania kontroli nad [połączonym doświadczeniem rejestracji informacji o zabezpieczeniach](../authentication/howto-registration-mfa-sspr-combined.md).

![Definiowanie zasad dostępu warunkowego i określanie aplikacji w chmurze](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Aplikacje w chmurze firmy Microsoft

Wiele z istniejących aplikacji w chmurze firmy Microsoft znajduje się na liście aplikacji, z których można wybierać. 

Administratorzy mogą przypisywać zasady dostępu warunkowego do następujących aplikacji w chmurze firmy Microsoft. Niektóre aplikacje, takie jak pakiet Office 365 (wersja zapoznawcza) i zarządzanie Microsoft Azure obejmują wiele powiązanych aplikacji lub usług podrzędnych. Poniższa lista nie jest wyczerpująca i może ulec zmianie.

- [Office 365 (wersja zapoznawcza)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Usługi Azure SQL Database i Data Warehouse](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Analiza Application Insights firmy Microsoft
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Zarządzanie Microsoft Azure](#microsoft-azure-management)
- Microsoft Azure zarządzanie subskrypcjami
- Microsoft Cloud App Security
- Portal Access Control Microsoft Commerce Tools
- Usługa uwierzytelniania Microsoft Commerce Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Rejestracja Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Wyszukiwanie w usłudze Bing firmy Microsoft
- Microsoft StaffHub
- Usługa Microsoft Stream
- Microsoft Teams
- Pakiet Office 365 Exchange Online
- Pakiet Office 365 SharePoint Online
- Usługa Yammer pakietu Office 365
- Office Delve
- Pakiet Office Sway
- Outlook Groups
- Usługa Power BI
- Project Online
- Skype dla firm Online
- Wirtualna sieć prywatna (VPN)
- Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender

### <a name="office-365-preview"></a>Office 365 (wersja zapoznawcza)

Pakiet Office 365 zapewnia oparte na chmurze usługi do pracy i współpracy, takie jak Exchange, SharePoint i Microsoft Teams. Usługi w chmurze pakietu Office 365 są głęboko zintegrowane w celu zapewnienia bezproblemowego i wspólnego środowiska. Ta integracja może spowodować pomyłkę podczas tworzenia zasad, ponieważ niektóre aplikacje, takie jak Microsoft Teams, mają zależności od innych, takich jak SharePoint lub Exchange.

Aplikacja pakietu Office 365 (wersja zapoznawcza) umożliwia wszystkim wszystkim kierowanie tych usług jednocześnie. Zalecamy używanie nowej aplikacji pakietu Office 365 (wersja zapoznawcza), a nie kierowanie poszczególnych aplikacji w chmurze w celu uniknięcia problemów z [zależnościami usług](service-dependencies.md). Kierowanie do tej grupy aplikacji pozwala uniknąć problemów, które mogą wystąpić z powodu niespójnych zasad i zależności.

Administratorzy mogą zdecydować się na wykluczenie określonych aplikacji z zasad, jeśli chcą, dołączając aplikację Office 365 (wersja zapoznawcza) i wykluczając wybrane aplikacje w ramach zasad.

Najważniejsze aplikacje, które są zawarte w aplikacji klienckiej pakietu Office 365 (wersja zapoznawcza):

   - Microsoft Flow
   - Microsoft Forms
   - Usługa Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Pakiet Office 365 Exchange Online
   - Pakiet Office 365 SharePoint Online
   - Search Service pakietu Office 365
   - Usługa Yammer pakietu Office 365
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype dla firm Online
   - Sway

### <a name="microsoft-azure-management"></a>Zarządzanie Microsoft Azure

Aplikacja do zarządzania Microsoft Azure obejmuje wiele podstawowych usług. 

   - Azure Portal
   - Dostawca Azure Resource Manager
   - Interfejsy API klasycznego modelu wdrażania
   - Azure PowerShell
   - Portal administratora subskrypcji programu Visual Studio
   - Azure DevOps
   - Portal Azure Data Factory

> [!NOTE]
> Aplikacja do zarządzania Microsoft Azure ma zastosowanie do Azure PowerShell, która wywołuje interfejs API Azure Resource Manager. Nie dotyczy to programu PowerShell usługi Azure AD, który wywołuje Microsoft Graph.

## <a name="other-applications"></a>Inne aplikacje

Oprócz aplikacji firmy Microsoft Administratorzy mogą dodać do zasad dostępu warunkowego dowolną zarejestrowaną aplikację usługi Azure AD. Mogą to być następujące aplikacje: 

- Aplikacje opublikowane za pomocą [usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/what-is-application-proxy.md)
- [Aplikacje dodane z galerii](../manage-apps/add-application-portal.md)
- [Niestandardowe aplikacje poza galerią](../manage-apps/add-non-gallery-app.md)
- [Starsze aplikacje opublikowane za poorednictwem kontrolerów dostarczania aplikacji i sieci](../manage-apps/secure-hybrid-access.md)
- Aplikacje korzystające z [logowania jednokrotnego opartego na hasłach](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

> [!NOTE]
> Ponieważ zasady dostępu warunkowego określają wymagania dotyczące uzyskiwania dostępu do usługi, nie można jej zastosować do aplikacji klienta (publicznej/natywnej). Inne wyrazy zasady nie są ustawiane bezpośrednio w aplikacji klienta (publicznej/natywnej), ale są stosowane, gdy klient wywołuje usługę. Na przykład zestaw zasad w usłudze SharePoint ma zastosowanie do klientów wywołujących program SharePoint. Zestaw zasad dla programu Exchange ma zastosowanie przy próbie uzyskania dostępu do poczty e-mail przy użyciu klienta programu Outlook. Dlatego aplikacje klienckie (publiczne/natywne) nie są dostępne do wyboru w selektorze aplikacji w chmurze i opcja dostępu warunkowego jest niedostępna w ustawieniach aplikacji dla aplikacji klienta (publicznej/natywnej) zarejestrowanej w dzierżawie. 


## <a name="user-actions"></a>Akcje użytkownika

Akcje użytkownika to zadania, które mogą być wykonywane przez użytkownika. Jedyną obsługiwaną akcją jest **Rejestrowanie informacji o zabezpieczeniach**, co umożliwia stosowanie zasad dostępu warunkowego, gdy użytkownicy, którzy są włączeni do łączenia się ze sobą, zarejestrują swoje informacje zabezpieczające. Więcej informacji można znaleźć w artykule [dotyczącej rejestracji informacji o zabezpieczeniach](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: warunki](concept-conditional-access-conditions.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
- [Zależności aplikacji klienckich](service-dependencies.md)
