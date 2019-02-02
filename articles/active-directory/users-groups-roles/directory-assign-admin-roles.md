---
title: Opisy ról administratora i uprawnienia — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Roli administratora można dodać użytkowników, przypisywać role administracyjne, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 64b8ca0412461ae1001eecce335f94d9f2f0825f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659069"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Uprawnienia roli administratora w usłudze Azure Active Directory

Za pomocą usługi Azure Active Directory (Azure AD), można wyznaczyć oddzielny administratorom różne funkcje. Administratorzy mogą umieszczoną w portalu usługi Azure AD do wykonania zadania, takie jak dodanie lub zmiana użytkowników, przypisywanie ról administracyjnych, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników i zarządzanie nazwami domen.

Administrator globalny ma dostęp do wszystkich funkcji administracyjnych. Domyślnie osoba, która zarejestruje się w subskrypcji platformy Azure przypisano rolę administratora globalnego dla katalogu. Tylko administratorzy globalni i Administratorzy ról uprzywilejowanych mogą delegować ról administratora.

## <a name="assign-or-remove-administrator-roles"></a>Przypisywanie lub usuwanie ról administratora

Aby dowiedzieć się, jak przypisywać role administracyjne dla użytkownika w usłudze Azure Active Directory, zobacz [widoku i przypisywanie ról administratorów w usłudze Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Dostępne role

Dostępne są następujące role administratora:

* **[Administrator aplikacji](#application-administrator)**: Użytkownicy w tej roli można tworzyć i zarządzać wszystkimi aspektami aplikacje dla przedsiębiorstw i rejestracje aplikacji, ustawienia serwera proxy aplikacji. Ta rola daje również możliwość wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem programu Microsoft Graph i Azure AD Graph. Członkowie tej roli nie są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

  <b>Ważne</b>: Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli można dodać poświadczeń do aplikacji i spersonifikować tożsamości aplikacji przy użyciu tych poświadczeń. Jeśli tożsamości aplikacji udzielono dostępu do usługi Azure Active Directory, takie jak możliwość utworzenia lub zaktualizowania użytkownika lub inne obiekty, przypisani do tej roli użytkownika może wykonać te czynności podczas personifikacji aplikacji. Ta możliwość spersonifikować tożsamości aplikacji może być podniesienie uprawnień za pośrednictwem co użytkownik może wykonać za pomocą swoje przypisania roli w usłudze Azure AD. Jest ważne dowiedzieć się, że przypisanie użytkownika do roli administratora aplikacji daje im możliwość personifikacji tożsamość aplikacji.

* **[Deweloper aplikacji](#application-developer)**: Użytkownicy w tej roli mogą tworzyć rejestracje aplikacji po "Użytkownicy mogą rejestrować aplikacje" został ustawiony na nie. Ta rola pozwala również elementy członkowskie do wyrażenia zgody we własnym imieniu po "Użytkownicy mogą zezwalać aplikacjom uzyskiwanie dostępu do danych firmy w ich imieniu" został ustawiony na nie. Członkowie tej roli są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

* **[Administrator rozliczeń](#billing-administrator)**: Dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

* **[Administrator aplikacji w chmurze](#cloud-application-administrator)**: Użytkownicy w tej roli mają takie same uprawnienia, jak rolę Administrator aplikacji z wyjątkiem możliwości zarządzania serwera proxy aplikacji. Ta rola umożliwia tworzenie i zarządzanie nimi wszystkie aspekty aplikacji dla przedsiębiorstw i rejestracje aplikacji. Ta rola daje również możliwość wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem programu Microsoft Graph i Azure AD Graph. Członkowie tej roli nie są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

  <b>Ważne</b>: Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli można dodać poświadczeń do aplikacji i spersonifikować tożsamości aplikacji przy użyciu tych poświadczeń. Jeśli tożsamości aplikacji udzielono dostępu do usługi Azure Active Directory, takie jak możliwość utworzenia lub zaktualizowania użytkownika lub inne obiekty, przypisani do tej roli użytkownika może wykonać te czynności podczas personifikacji aplikacji. Ta możliwość spersonifikować tożsamości aplikacji może być podniesienie uprawnień za pośrednictwem co użytkownik może wykonać za pomocą swoje przypisania roli w usłudze Azure AD. Jest ważne dowiedzieć się, że przypisanie użytkownika do roli Administrator aplikacji w chmurze daje im możliwość personifikacji tożsamość aplikacji.

* **[Administrator urządzenia w chmurze](#cloud-device-administrator)**: Użytkownicy w tej roli można włączyć, wyłączyć i usuwanie urządzeń w usłudze Azure AD i klucze do odczytu funkcją BitLocker systemu Windows 10 (jeśli istnieje) w witrynie Azure portal. Rola nie powoduje przyznania uprawnień do zarządzania innych właściwości, na urządzeniu.

* **[Administrator do spraw zgodności](#compliance-administrator)**: Użytkownicy z tą rolą mają uprawnienia do zarządzania związanych ze zgodnością funkcji w Centrum zgodności usługi Microsoft 365, Centrum administracyjnego usługi Microsoft 365, Azure i zabezpieczeń usługi Office 365 i Centrum zgodności. Użytkownicy mogą również zarządzać wszystkie funkcje w ramach Centrum administracyjne Exchange, zespoły i Skype dla firm Centrum administracyjnego i tworzyć bilety pomocy technicznej dla platformy Azure i usługi Microsoft 365. Więcej informacji znajduje się w temacie [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  W | Można zrobić
  ----- | ----------
  [Microsoft 365 Centrum zgodności](https://protection.microsoft.com) | Ochrona danych i zarządzanie nimi w organizacji w usługach Microsoft 365<br>Zarządzanie alertami zgodności
  [Menedżer zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledzenie, przypisz i Zweryfikuj działań zgodność z przepisami w organizacji
  [Centrum zabezpieczeń usługi Office 365 i zgodności](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie nadzór nad danymi<br>Sprawdzać informacje prawne i danych<br>Zarządzanie żądanie podmiotu danych
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetlać wszystkie dane inspekcji usługi Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Można tworzyć i modyfikować zasady dotyczące plików i Zezwalaj na akcje nadzoru plików<br> Można wyświetlić wszystkie wbudowane raporty w obszarze Zarządzanie danymi

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.microsoft.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[Administrator dostępu warunkowego](#conditional-access-administrator)**: Użytkownicy z tą rolą mają możliwość zarządzania ustawieniami dostępu warunkowego usługi Azure Active Directory.
  > [!NOTE]
  > Aby wdrożyć zasady dostępu warunkowego programu Exchange ActiveSync na platformie Azure, użytkownik musi być administratorem globalnym.
  
* **[Administratorzy urządzenia](#device-administrators)**: Ta rola jest dostępne do przypisania tylko jako dodatkowego administratora lokalnego w [ustawienia urządzenia](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Użytkownicy z tą rolą stają się administratorami maszyny lokalnej na wszystkich urządzeniach z systemem Windows 10 dołączonych do usługi Azure Active Directory. Nie mają możliwość zarządzania obiektami urządzeń w usłudze Azure Active Directory. 

* **[Odczytywanie katalogów](#directory-readers)**: Jest to rola starszej wersji, która ma być przypisana do aplikacji, które nie obsługują [zgody Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nie powinien zostać przypisany do żadnych użytkowników.

* **[Konta synchronizacji katalogu](#directory-synchronization-accounts)**: Nie należy używać. Ta rola jest przypisywany do usługi Azure AD Connect i jest nie przeznaczone lub obsługiwane w przypadku innych celów.

* **[Zapisywanie katalogów](#directory-writers)**: Jest to rola starszej wersji, która ma być przypisana do aplikacji, które nie obsługują [zgody Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nie powinien zostać przypisany do żadnych użytkowników.

* **[Dynamics 365 administratora / administratora CRM](#crm-service-administrator)**: Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Dynamics 365 Online, jeśli usługa została zainstalowana, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [używać roli administratora usługi do zarządzania dzierżawą](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Dynamics 365". Jest on "Administrator Dynamics 365" [witryny Azure portal](https://portal.azure.com).

* **[Administrator programu Exchange](#exchange-service-administrator)**: Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft CRM Online, jeśli została zainstalowana. a także możliwość tworzenia i zarządzania wszystkich grup usługi Office 365, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Exchange". Jest on "Administrator programu Exchange" [witryny Azure portal](https://portal.azure.com).

* **[Administrator globalny / Administrator firmy](#company-administrator)**: Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure Active Directory, a także usług korzystających z tożsamości usługi Azure Active Directory, takich jak Centrum zabezpieczeń firmy Microsoft 365, Microsoft 365 Centrum zgodności, Exchange Online, SharePoint Online i Skype dla firm Online. Osoba, która zarejestruje się dla dzierżawy usługi Azure Active Directory staje się administratorem globalnym. Tylko administratorzy globalni mogą przypisywać pozostałe role administratorów. Może istnieć więcej niż jednego administratora globalnego w Twojej firmie. Administratorzy globalni mogą resetować hasła dla wszystkich użytkowników oraz wszystkich innych administratorów.

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator firmy". Jest on "Administrator globalny" [witryny Azure portal](https://portal.azure.com).
  >
  >

* **[Osoba zapraszająca gości](#guest-inviter)**: Użytkownicy w tej roli mogą zarządzać zaproszeniami użytkowników gości B2B usługi Azure Active Directory po **członkowie mogą zapraszać** użytkownika został ustawiony na nie. Więcej informacji na temat współpracy B2B w [współpracy usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nie ma żadnych innych uprawnień.

* **[Administrator usługi Information Protection](#information-protection-administrator)**: Użytkownicy z tą rolą mają wszystkie uprawnienia w usłudze Azure Information Protection. Ta rola umożliwia konfigurowanie etykiet w zasadach usługi Azure Information Protection, Zarządzanie szablonami ochrony i aktywacja ochrony. Ta rola nie przyznaje wszystkie uprawnienia w Centrum usługi Identity Protection, Privileged Identity Management, kondycji usługi Monitor Office 365, lub Office 365 Centrum zabezpieczeń i zgodności.

* **[Administrator usługi Intune](#intune-service-administrator)**: Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft CRM Online, gdy ta usługa została zainstalowana. Ponadto ta rola oferuje możliwość zarządzania użytkownikami i urządzeniami w celu kojarzenia zasad, a także tworzenie grup i zarządzanie nimi. Więcej informacji o [kontrola administracji opartej na rolach (RBAC) w usłudze Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Intune". Jest on "Administrator usługi Intune" [witryny Azure portal](https://portal.azure.com).

* **[Administrator licencji](#license-administrator)**: Użytkownicy w tej roli mogą dodawać, usuwanie i aktualizowanie licencji przydziały dla użytkowników, grup (z wykorzystaniem Licencjonowanie na podstawie grupy), a zarządzanie lokalizacji użytkowania — na użytkownikach. Rola nie powoduje przyznania możliwość zakupu lub Zarządzaj subskrypcjami Tworzenie lub zarządzać grupami, lub Utwórz lub Zarządzanie użytkownikami poza lokalizacji użytkowania. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Czytnik Centrum wiadomości](#message-center-reader)**: Użytkownicy w tej roli można monitorować powiadomienia i aktualizacje porad dotyczących kondycji w [Centrum wiadomości usługi Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) dla swojej organizacji w skonfigurowanych usług, takich jak Exchange, usługa Intune i Microsoft Teams. Czytelnicy Centrum wiadomości otrzymasz tygodniowy skróty e-mail wpisów i aktualizacji i mogą udostępniać wpisy Centrum wiadomości w usłudze Office 365. W usłudze Azure AD użytkownicy przypisani do tej roli tylko mają dostęp tylko do odczytu w usługach Azure AD, takich jak użytkownicy i grupy. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Partner obsługi 1](#partner-tier1-support)**: Nie należy używać. Ta rola jest przestarzała i zostanie usunięty z usługi Azure AD w przyszłości. Ta rola jest przeznaczony do użytku przez małą liczbę partnerów firmy Microsoft w odsprzedaży i nie jest przeznaczona do użytku ogólnego.

* **[Partner pomocy technicznej w warstwie 2](#partner-tier2-support)**: Nie należy używać. Ta rola jest przestarzała i zostanie usunięty z usługi Azure AD w przyszłości. Ta rola jest przeznaczony do użytku przez małą liczbę partnerów firmy Microsoft w odsprzedaży i nie jest przeznaczona do użytku ogólnego.

* **[Administrator haseł / Administrator pomocy technicznej](#helpdesk-administrator)**: Użytkownicy z tą rolą można zmienić hasła, unieważnienie tokeny odświeżania, zarządzanie żądaniami obsługi i monitorowania kondycji usługi. Unieważnienie token odświeżania wymusza na użytkowniku, aby zalogować się ponownie. Administratorzy pomocy technicznej można resetować hasła i unieważnić tokenów odświeżania innych użytkowników, którzy są użytkowników niebędących administratorami lub członków z następujących ról:
  * Odczytywanie katalogów
  * Osoba zapraszająca gościa
  * Administrator pomocy technicznej
  * Czytelnik Centrum wiadomości
  * Czytnik raportów
  
  <b>Ważne</b>: Użytkownicy z tą rolą mogą zmienić hasła dla osób, które mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji i spoza usługi Azure Active Directory. Zmiana hasła użytkownika może oznaczać, że możliwość przyjęcia tożsamości i uprawnienia tego użytkownika. Na przykład:
  * Rejestrowanie aplikacji i aplikacji przedsiębiorstwa właścicieli, którzy mogą zarządzać poświadczeniami aplikacje, które są właścicielami. Te aplikacje mogą mieć uprzywilejowany uprawnień w usłudze Azure AD i gdzie indziej nie zostały przyznane dla administratorów pomocy technicznej. Za pomocą tej ścieżki, które Administrator pomocy technicznej może przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość uprzywilejowanych aplikacji przez zaktualizowanie poświadczeń dla aplikacji.
  * Właścicieli subskrypcji platformy Azure, którzy mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji na platformie Azure.
  * Grupy zabezpieczeń i grupy usługi Office 365 właścicieli, którzy mogą zarządzać członkostwem. Te grupy mogą udzielać dostępu do informacje poufne lub prywatne lub krytyczne konfiguracji w usłudze Azure AD i w innych miejscach.
  * Administratorzy w innych usługach poza usługi Azure AD, takich jak Exchange Online, zabezpieczenia pakietu Office i Centrum zgodności i zarządzania zasobami ludzkimi systemów.
  * Kierownicy doradcą prawnym i pracownicy działu kadr, którzy mogą uzyskiwać dostęp do poufne lub prywatne informacje, takie jak użytkownicy niebędący administratorami.

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator pomocy technicznej". Jest on "Administrator haseł" [witryny Azure portal](https://portal.azure.com/).
  >
  
* **[Power BI Administrator](#power-bi-service-administrator)**: Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Power BI, jeśli ta usługa została zainstalowana, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [opis roli administratora usługi Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Power BI". Jest on "Administrator usługi Power BI" [witryny Azure portal](https://portal.azure.com).

* **[Administrator ról uprzywilejowanych](#privileged-role-administrator)**: Użytkownicy z tą rolą mogą zarządzać przypisaniami ról, w usłudze Azure Active Directory, a także w ramach usługi Azure AD Privileged Identity Management. Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami Privileged Identity Management.

  <b>Ważne</b>: Ta rola daje możliwość zarządzania członkostwem we wszystkich rolach usługi Azure AD, łącznie z rolą administratora globalnego. Ta rola nie ma innych możliwości uprzywilejowanego w usłudze Azure AD, takie jak tworzenie lub aktualizowanie użytkowników. Użytkownicy przypisani do tej roli można przyznać samych lub innych dodatkowych uprawnień, przypisując dodatkowych ról.

* **[Czytnik raportów](#reports-reader)**: Użytkownicy z tą rolą mogą przeglądać dane raportowania użycia i pulpit nawigacyjny raportów w Centrum administracyjnym usługi Office 365 i kontekstu przyjęcia pakietu w usłudze Power BI. Ponadto rola zapewnia dostęp do logowania w raportach i działań w usłudze Azure AD i interfejsu API raportowania danych zwróconych przez program Microsoft Graph. Użytkownik przypisany do roli czytelnika raportów dostęp tylko odpowiednie użycia i metryk przyjęcia. Nie mają żadnych uprawnień administratora do skonfigurowania ustawień lub dostępu do Centrum administracyjnego konkretnych produktów, takich jak program Exchange. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Administrator zabezpieczeń](#security-administrator)**: Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjach zabezpieczeń dostępnych w Centrum zabezpieczeń firmy Microsoft 365, Azure Active Directory Identity Protection, Azure Information Protection i zabezpieczeń usługi Office 365 i Centrum zgodności. Więcej informacji na temat usługi Office 365 uprawnień znajduje się w temacie [uprawnienia w Centrum zgodności i zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  W | Można zrobić
  --- | ---
  [Centrum zabezpieczeń firmy Microsoft 365](https://protection.microsoft.com) | Monitorowanie zasad zabezpieczeń w usługach Microsoft 365<br>Zarządzanie zagrożeniami i alerty<br>Wyświetlanie raportów
  Centrum usługi Identity Protection | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji Centrum usługi Identity Protection z wyjątkiem resetowania haseł
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>**Nie można** zarządzania członkostwa w roli usługi Azure AD lub ustawienia
  [Centrum zabezpieczeń usługi Office 365 i zgodności](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zasadami zabezpieczeń<br>Wyświetl, zbadaj i reagowania na zagrożenia bezpieczeństwa<br>Wyświetlanie raportów
  Azure Advanced Threat Protection | Monitorowanie i odpowiadanie na podejrzane zabezpieczeń działania
  Windows Defender ATP i EDR | Przypisywanie ról<br>Zarządzaj grupami maszyn<br>Konfigurowanie punktu końcowego wykrywania zagrożeń i automatyczne rozwiązywanie problemów<br>Wyświetl, zbadaj i odpowiadanie na alerty
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Widoki użytkownika, urządzenia, rejestracji, konfiguracji i informacje o aplikacji<br>Nie można wprowadzać zmiany w usłudze Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Dodaj administratorów, dodawania zasad i ustawień, przekazywania dzienników i wykonywania akcji ładu
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Można wyświetlić zasady zabezpieczeń, widoku stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlanie alertów i zaleceń, Odrzuć alerty i zalecenia
  [Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetl kondycję usługi Office 365

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.microsoft.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[Czytelnik zabezpieczeń](#security-reader)**: Użytkownicy z tą rolą mają globalny dostęp tylko do odczytu w funkcji związanych z zabezpieczeniami, w tym wszystkie informacje w Microsoft 365 usługi security center, Azure Active Directory, Identity Protection, Privileged Identity Management, a także możliwość odczytu w usłudze Azure Active Katalog raportów logowania i dzienniki inspekcji w Centrum zgodności i zabezpieczeń usługi Office 365. Więcej informacji na temat usługi Office 365 uprawnień znajduje się w temacie [uprawnienia w Centrum zgodności i zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  W | Można zrobić
  --- | ---
  [Centrum zabezpieczeń firmy Microsoft 365](https://protection.microsoft.com) | Wyświetl zasady zabezpieczeń w usługach Microsoft 365<br>Zagrożenia bezpieczeństwa View i alerty<br>Wyświetlanie raportów
  Centrum usługi Identity Protection | Odczyt wszystkich raporty dotyczące zabezpieczeń i informacje o ustawieniach dla funkcji zabezpieczeń<br><ul><li>Anti-spam<li>Szyfrowanie<li>Ochrona przed utratą danych<li>Chroniące przed złośliwym kodem<li>Zaawansowana ochrona przed zagrożeniami<li>Przed wyłudzaniem<li>Reguły Mailflow
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Ma dostęp tylko do odczytu, aby wszystkie informacje są prezentowane z użyciem usługi Azure AD PIM: Zasady i raportów dotyczących przypisania roli usługi Azure AD, zabezpieczenia przeglądów i w przyszłości dostęp do odczytu do zasad, dane i raporty dla scenariuszy, oprócz przypisania roli usługi Azure AD.<br>**Nie można** Zarejestruj się w usłudze Azure AD PIM lub wprowadzać żadnych zmian. W portalu usługi PIM lub za pośrednictwem programu PowerShell ktoś jest w tej roli można aktywować dodatkowe role (na przykład administrator globalny lub Administrator ról uprzywilejowanych), jeśli użytkownik jest uprawniona do nich.
  [Centrum zabezpieczeń usługi Office 365 i zgodności](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wyświetlanie zasad zabezpieczeń<br>Wyświetlanie i badanie zagrożeń<br>Wyświetlanie raportów
  Windows Defender ATP i EDR | Wyświetlanie i badanie alertów
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Widoki użytkownika, urządzenia, rejestracji, konfiguracji i informacje o aplikacji. Nie można wprowadzać zmiany w usłudze Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Można wyświetlić, zaleceń i alertów, widok zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, ale nie może wprowadzać zmian
  [Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetl kondycję usługi Office 365

* **[Administrator pomocy technicznej usługi](#service-support-administrator)**: Użytkownicy z tą rolą mogą otwierać żądania pomocy technicznej firmy Microsoft dla usług platformy Azure i Office 365 oraz wyświetlać pulpit nawigacyjny usługi i centrum komunikatów w witrynie Azure Portal i portalu administracyjnym usługi Office 365. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator pomocy technicznej usługi." Jest on "Administrator usługi" [witryny Azure portal](https://portal.azure.com), portalu administracyjnego usługi Office 365 i portalu usługi Intune.

* **[Administrator programu SharePoint](#sharepoint-service-administrator)**: Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft SharePoint Online, jeśli usługa została zainstalowana, a także możliwość tworzenia i zarządzania wszystkich grup usługi Office 365, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi SharePoint." Jest on "Administrator programu SharePoint" [witryny Azure portal](https://portal.azure.com).

* **[Skype dla firm / Administrator usługi Lync](#lync-service-administrator)**: Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Skype dla firm, gdy usługa jest obecna, a także zarządzać specyficzne dla programu Skype atrybutów użytkownika w usłudze Azure Active Directory. Ponadto ta rola daje możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi i uzyskać dostęp do zespołów i Skype dla firm Centrum administracyjnego. Konto musi mieć również licencję dla zespołów lub nie można uruchomić polecenia cmdlet programu PowerShell zespołów. Więcej informacji o [dotyczące programu Skype dla firm przypisaną rolę administratora](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) i zespołów, informacje o licencjonowaniu na [Skype dla firm i Microsoft Teams dodatek licencjonowania](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Lync". ""Skype dla firm Administrator znajduje się w [witryny Azure portal](https://portal.azure.com/).

* **[Zespoły administratora](#teams-service-administrator)**: Użytkownicy w tej roli mogą zarządzać wszystkimi aspektami obciążenie Microsoft Teams przy użyciu programu Microsoft Teams & Skype Centrum administracyjnego usługi biznesowe i odpowiednie moduły programu PowerShell. W tym, wśród innych obszarów, wszystkie narzędzia do zarządzania związane z telefoniczne, wiadomości, spotkań i same zespoły dbają. Ta rola również przyznaje możliwość tworzenia i zarządzania wszystkich grup usługi Office 365, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi.
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "zespoły Administrator usługi". "Administrator zespoły" znajduje się w [witryny Azure portal](https://portal.azure.com).

* **[Zespoły administratora komunikacji](#teams-communications-administrator)**: Użytkownicy w tej roli może zarządzać aspektami Microsoft Teams obciążenie związane z głosu & telefonii. W tym narzędzia do zarządzania dla przypisywania numerów telefonów, głos i spotkania zasady i pełny dostęp do narzędzi analizy wywołania.

* **[Zespoły ze specjalistą pomocy technicznej komunikacji](#teams-communications-support-engineer)**: Użytkownicy w tej roli można rozwiązywać problemy z komunikacją w ramach Microsoft Teams i Skype dla firm przy użyciu wywołania użytkownika narzędzi w Microsoft Teams i Skype dla firm Centrum administracyjnego do rozwiązywania problemów. Użytkownicy w tej roli mogą wyświetlać pełną wywołanie rejestrowanie informacji dla wszystkich uczestników związane. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Zespoły, specjalista ds. pomocy technicznej komunikacji](#teams-communications-support-specialist)**: Użytkownicy w tej roli można rozwiązywać problemy z komunikacją w ramach Microsoft Teams i Skype dla firm przy użyciu wywołania użytkownika narzędzi w Microsoft Teams i Skype dla firm Centrum administracyjnego do rozwiązywania problemów. Użytkownicy w tej roli mogą wyświetlać szczegóły użytkownika tylko w wywołaniu dla określonego użytkownika, że ma wyszukiwać. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Administrator kont użytkowników](#user-account-administrator)**: Użytkownicy z tą rolą mogą tworzyć użytkowników i zarządzać wszystkimi aspektami użytkowników z pewnymi ograniczeniami (patrz poniżej). Ponadto użytkownicy posiadający tę rolę można tworzyć i zarządzać wszystkich grup. Ta rola obejmuje również możliwość tworzenia i zarządzania widoki użytkowników, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi.

  | | |
  | --- | --- |
  |Uprawnieniami ogólnymi|<p>Tworzenie użytkowników i grup</p><p>Tworzenie i zarządzanie nimi widoki użytkowników</p><p>Zarządzanie biletami pomocy technicznej pakietu Office|
  |<p>Na wszystkich użytkowników w tym wszystkich administratorów</p>|<p>Zarządzanie licencjami</p><p>Zarządzanie wszystkich właściwości użytkownika, z wyjątkiem główna nazwa użytkownika</p>
  |Tylko na użytkowników, którzy są administratorami bez lub w jednym z następujących ograniczonych ról administratora:<ul><li>Odczytywanie katalogów<li>Osoba zapraszająca gościa<li>Administrator pomocy technicznej<li>Czytelnik Centrum wiadomości<li>Czytnik raportów<li>Administrator kont użytkowników|<p>Usuń i przywracania</p><p>Wyłącz i Włącz</p><p>Unieważnienie tokenów odświeżania</p><p>Zarządzanie wszystkich właściwości użytkownika, w tym nazwa główna użytkownika</p><p>Resetowanie hasła</p><p>Aktualizuj klucze urządzenia (FIDO)</p>
  
  <b>Ważne</b>: Użytkownicy z tą rolą mogą zmienić hasła dla osób, które mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji i spoza usługi Azure Active Directory. Zmiana hasła użytkownika może oznaczać, że możliwość przyjęcia tożsamości i uprawnienia tego użytkownika. Na przykład:
  * Rejestrowanie aplikacji i aplikacji przedsiębiorstwa właścicieli, którzy mogą zarządzać poświadczeniami aplikacje, które są właścicielami. Te aplikacje mogą mają uprzywilejowany uprawnień w usłudze Azure AD i gdzie indziej nie przyznawać Administratorzy użytkowników. Za pomocą tej ścieżki, które Administrator użytkowników może przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość uprzywilejowanych aplikacji przez zaktualizowanie poświadczeń dla aplikacji.
  * Właścicieli subskrypcji platformy Azure, którzy mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji na platformie Azure.
  * Grupy zabezpieczeń i grupy usługi Office 365 właścicieli, którzy mogą zarządzać członkostwem. Te grupy mogą udzielać dostępu do informacje poufne lub prywatne lub krytyczne konfiguracji w usłudze Azure AD i w innych miejscach.
  * Administratorzy w innych usługach poza usługi Azure AD, takich jak Exchange Online, zabezpieczenia pakietu Office i Centrum zgodności i zarządzania zasobami ludzkimi systemów.
  * Kierownicy doradcą prawnym i pracownicy działu kadr, którzy mogą uzyskiwać dostęp do poufne lub prywatne informacje, takie jak użytkownicy niebędący administratorami.

## <a name="role-permissions"></a>Uprawnienia roli
W poniższych tabelach opisano określone uprawnienia w usłudze Azure Active Directory do każdej roli. Niektóre role mogą mieć dodatkowe uprawnienia w usługach firmy Microsoft poza usługą Azure Active Directory.

### <a name="application-administrator"></a>Administrator aplikacji
Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aktualizowanie właściwości applications.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Aktualizowanie właściwości applications.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aktualizowanie właściwości podstawowych w aplikacjach w usłudze Active Directory Azure. |
| microsoft.aad.directory/applications/create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Aktualizowanie właściwości applications.credentials w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/DELETE | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Aktualizowanie właściwości applications.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Aktualizowanie właściwości applications.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Aktualizowanie właściwości applications.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Tworzenie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Odczytywanie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizowanie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuwanie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowane) w dziennikach inspekcji w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Odczytywanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aktualizowanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Odczytywanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aktualizowanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Odczytywanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualizowanie właściwości servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualizowanie właściwości servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualizowanie właściwości servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizowanie właściwości podstawowych elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Tworzenie elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualizowanie właściwości servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Usuwanie elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualizowanie właściwości servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualizowanie właściwości servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualizowanie właściwości servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowanych) w elemencie signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="application-developer"></a>Deweloper aplikacji
Rejestracje aplikacji można tworzyć niezależnie od "Użytkownicy mogą rejestrować aplikacje" ustawienie.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Tworzenie aplikacji w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Tworzenie elementu appRoleAssignments w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Tworzenie elementu oAuth2PermissionGrants w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Tworzenie elementu servicePrincipals w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |

### <a name="billing-administrator"></a>Administrator rozliczeń
Może wykonywać typowe zadania związane z rozliczeniami, takie jak aktualizowanie informacji o płatności.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Organization/Basic/Update | Aktualizowanie właściwości podstawowych w organizacji w usłudze Active Directory Azure. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Aktualizowanie właściwości organization.trustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.commerce.billing/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczenia usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="desktop-analytics-administrator"></a>Administrator usługi Desktop Analytics
Można uzyskać dostęp i zarządzać nimi, Zarządzanie pulpitem narzędzi i usług, w tym w usłudze Intune.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Zarządzanie wszystkimi aspektami analizy komputera. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="cloud-application-administrator"></a>Administrator aplikacji w chmurze
Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa (z wyjątkiem serwera proxy aplikacji) oraz zarządzać nimi.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aktualizowanie właściwości applications.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Aktualizowanie właściwości applications.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aktualizowanie właściwości podstawowych w aplikacjach w usłudze Active Directory Azure. |
| microsoft.aad.directory/applications/create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Aktualizowanie właściwości applications.credentials w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/DELETE | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Aktualizowanie właściwości applications.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Aktualizowanie właściwości applications.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Aktualizowanie właściwości applications.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Tworzenie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizowanie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuwanie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowane) w dziennikach inspekcji w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Odczytywanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aktualizowanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Odczytywanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aktualizowanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Odczytywanie właściwości policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualizowanie właściwości servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualizowanie właściwości servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualizowanie właściwości servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizowanie właściwości podstawowych elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Tworzenie elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualizowanie właściwości servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Usuwanie elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualizowanie właściwości servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualizowanie właściwości servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualizowanie właściwości servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowanych) w elemencie signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="cloud-device-administrator"></a>Administrator urządzenia w chmurze
Pełny dostęp do zarządzania urządzeniami w usłudze Azure AD.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowane) w dziennikach inspekcji w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytywanie właściwości devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/DELETE | Usuwanie urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Wyłączanie urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Włączanie urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowanych) w elemencie signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="company-administrator"></a>Administrator firmy
Może zarządzać wszystkimi aspektami usług Azure AD i Microsoft korzystających z tożsamości usługi Azure AD.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów, a także odczytywanie i aktualizowanie standardowych właściwości w witrynie microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Tworzenie i usuwanie elementu administrativeUnits oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Tworzenie i usuwanie aplikacji oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Tworzenie i usuwanie elementu appRoleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowane) w dziennikach inspekcji w usłudze Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Tworzenie i usuwanie kontaktów oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Tworzenie i usuwanie umów oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Tworzenie i usuwanie urządzeń oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Tworzenie i usuwanie elementu directoryRoles oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Tworzenie i usuwanie elementu directoryRoleTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Tworzenie i usuwanie grup oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Tworzenie i usuwanie elementu groupSettings oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Tworzenie i usuwanie elementu groupSettingTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Tworzenie i usuwanie elementu loginTenantBranding oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie elementu OAuth2PermissionGrants oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Tworzenie i usuwanie organizacji oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Tworzenie i usuwanie zasad oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie elementu roleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie elementu roleDefinitions oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie elementu ScopedRoleMemberships oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Może wykonać akcję usługi Activateservice w usłudze Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Może wykonać akcję usługi Disabledirectoryfeature w usłudze Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Może wykonać akcję usługi Enabledirectoryfeature w usłudze Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Może wykonać akcję usługi Getavailableextentionproperties w usłudze Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Tworzenie i usuwanie elementu servicePrincipals oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowanych) w elemencie signInReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Tworzenie i usuwanie elementu subscribedSkus oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Tworzenie i usuwanie użytkowników oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Wykonywanie wszystkich akcji w programie Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w elemencie microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczytywanie wszystkich zasobów w elemencie microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Odczytywanie wszystkich zasobów w elemencie microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.commerce.billing/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczenia usługi Office 365. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Zarządzanie wszystkimi aspektami Menedżera zgodności usługi Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Zarządzanie wszystkimi aspektami analizy komputera. |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Zarządzanie wszystkimi aspektami Skrytki klienta usługi Office 365 |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w elemencie microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Odczytywanie elementu securityMessages w elemencie microsoft.office365.messageCenter. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Power BI. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Zarządzanie wszystkimi aspektami Centrum ochrony usługi Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w elemencie microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w elemencie microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Odczytywanie raportów dotyczących użycia usługi Office 365. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Dynamics 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Odczytywanie wszystkich zasobów w elemencie microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Administrator zgodności
Może odczytywać konfigurację i raporty zgodności oraz zarządzać nimi w usługach Azure AD i Office 365.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Zarządzanie wszystkimi aspektami Menedżera zgodności usługi Office 365 |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w elemencie microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="conditional-access-administrator"></a>Administrator dostępu warunkowego
Może zarządzać możliwościami dostępu warunkowego.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Odczytywanie właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Aktualizowanie właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Odczytywanie właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Aktualizowanie właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Odczytywanie właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Aktualizowanie właściwości policies.conditionalAccess w usłudze Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrator usługi CRM
Może zarządzać wszystkimi aspektami produktu Dynamics 365.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="customer-lockbox-access-approver"></a>Osoba zatwierdzająca dostęp do skrytki klienta
Może zatwierdzać żądania pomocy technicznej firmy Microsoft dotyczące uzyskania dostępu do danych organizacyjnych klienta. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Zarządzanie wszystkimi aspektami Skrytki klienta usługi Office 365 |

### <a name="device-administrators"></a>Administratorzy urządzenia
Członkowie tej roli są dodawane do grupy Administratorzy lokalni na urządzeniach przyłączonych do usługi AD systemu Azure.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Odczytywanie właściwości podstawowych w elemencie groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Odczytywanie właściwości podstawowych w elemencie groupSettingTemplates w usłudze Azure Active Directory. |

### <a name="directory-readers"></a>Odczytywanie katalogów
Może odczytywać informacje o katalogu podstawowego. Przyznawania dostępu do aplikacji, nie przeznaczone dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Odczytywanie właściwości podstawowych w elemencie administrativeUnit w usłudze Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Odczytywanie właściwości administrativeUnits.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Odczytywanie właściwości podstawowych w aplikacjach w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Odczytywanie właściwości applications.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Odczytywanie właściwości applications.policies w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Basic/Read | Odczytywanie właściwości podstawowych w kontaktach w usłudze Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Odczytywanie właściwości contacts.memberOf w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/contracts/Basic/Read | Odczytywanie właściwości podstawowych w umowach w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Odczytywanie właściwości podstawowych w urządzeniach w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Odczytywanie właściwości devices.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Odczytywanie właściwości devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Odczytywanie właściwości devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Odczytywanie właściwości podstawowych w obiektach directoryRoles w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Odczytywanie właściwości directoryRoles.eligibleMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Odczytywanie właściwości directoryRoles.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Odczytywanie właściwości podstawowych w domenach w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Odczytywanie właściwości groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Odczytywanie właściwości podstawowych w grupach w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Odczytywanie właściwości groups.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Odczytywanie właściwości groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Odczytywanie właściwości groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Odczytywanie właściwości groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Odczytywanie właściwości podstawowych w elemencie groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Odczytywanie właściwości podstawowych w elemencie groupSettingTemplates w usłudze Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Odczytywanie właściwości standardowych w elemencie oAuth2PermissionGrants w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Organization/Basic/Read | Odczytywanie właściwości podstawowych w organizacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Odczytywanie właściwości organization.trustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Odczytywanie podstawowych właściwości elementu roleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Odczytywanie podstawowych właściwości elementu roleDefinitions w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Odczytywanie właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Odczytywanie właściwości servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Odczytywanie właściwości podstawowych elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Odczytywanie właściwości servicePrincipals.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Odczytywanie właściwości servicePrincipals.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Odczytywanie właściwości servicePrincipals.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Odczytywanie właściwości servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Odczytywanie właściwości servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Odczytywanie właściwości podstawowych w elemencie subscribedSkus w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Odczytywanie właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Odczytywanie właściwości podstawowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Odczytywanie właściwości users.directReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Odczytywanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Odczytywanie właściwości users.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Odczytywanie właściwości users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Odczytywanie właściwości users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Odczytywanie właściwości users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Odczytywanie właściwości users.registeredDevices w usłudze Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konta synchronizacji katalogu
Używane tylko przez usługę Azure AD Connect.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Aktualizowanie właściwości organization.dirSync w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/Basic/Read | Odczytywanie właściwości podstawowych w zasadach w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aktualizowanie właściwości podstawowych w zasadach w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Odczytywanie właściwości policies.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Aktualizowanie właściwości policies.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Odczytywanie właściwości policies.policiesAppliedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Aktualizowanie właściwości policies.tenantDefault w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Odczytywanie właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizowanie właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Odczytywanie właściwości servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualizowanie właściwości servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualizowanie właściwości servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualizowanie właściwości servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Odczytywanie właściwości podstawowych elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizowanie właściwości podstawowych elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Tworzenie elementu servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualizowanie właściwości servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Odczytywanie właściwości servicePrincipals.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Odczytywanie właściwości servicePrincipals.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Odczytywanie właściwości servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualizowanie właściwości servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Odczytywanie właściwości servicePrincipals.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualizowanie właściwości servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Odczytywanie właściwości servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualizowanie właściwości servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Wykonywanie wszystkich akcji w programie Azure AD Connect. |

### <a name="directory-writers"></a>Składniki zapisywania w katalogach
Może odczytywać i zapisywać informacje katalogu podstawowego. Przyznawania dostępu do aplikacji, nie przeznaczone dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualizowanie właściwości groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizowanie właściwości podstawowych w grupach w usłudze Active Directory Azure. |
| microsoft.aad.directory/groups/members/update | Aktualizowanie właściwości groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aktualizowanie właściwości groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aktualizowanie właściwości groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Aktualizowanie właściwości podstawowych w elemencie groupSettings w usłudze Active Directory Azure. |
| microsoft.aad.directory/groupSettings/create | Tworzenie elementu groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Usuwanie elementu groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualizowanie właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizowanie właściwości podstawowych użytkowników w usłudze Active Directory Azure. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aktualizowanie właściwości users.userPrincipalName w usłudze Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrator usługi Exchange
Może zarządzać wszystkimi aspektami produktu Exchange.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualizowanie właściwości groups.unified w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aktualizowanie podstawowych właściwości grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/create | Tworzenie grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/delete | Usuń grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aktualizowanie członkostwa w grupach usługi Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aktualizowanie własności grup usługi Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="guest-inviter"></a>Osoba zapraszająca gościa
Może zapraszać użytkowników-gości niezależnie od ustawienia „członkowie mogą zapraszać gości”.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Odczytywanie właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Odczytywanie właściwości podstawowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Odczytywanie właściwości users.directReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Zapraszanie użytkowników-gości w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Odczytywanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Odczytywanie właściwości users.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Odczytywanie właściwości users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Odczytywanie właściwości users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Odczytywanie właściwości users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Odczytywanie właściwości users.registeredDevices w usłudze Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrator pomocy technicznej
Może resetować hasła dla użytkowników niebędących administratorami i administratorów pomocy.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytywanie właściwości devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="information-protection-administrator"></a>Administrator usługi Information Protection
Może zarządzać wszystkimi aspektami produktu Azure Information Protection.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="intune-service-administrator"></a>Administrator usługi Intune
Może zarządzać wszystkimi aspektami produktu Intune.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizowanie właściwości podstawowych w kontaktach w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Tworzenie kontaktów w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Basic/Update | Aktualizowanie właściwości podstawowych w urządzeniach w usłudze Active Directory Azure. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytywanie właściwości devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/create | Tworzenie urządzeń w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/DELETE | Usuwanie urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Aktualizowanie właściwości devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Aktualizowanie właściwości devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualizowanie właściwości groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizowanie właściwości podstawowych w grupach w usłudze Active Directory Azure. |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytywanie właściwości groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aktualizowanie właściwości groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aktualizowanie właściwości groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aktualizowanie właściwości groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualizowanie właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizowanie właściwości podstawowych użytkowników w usłudze Active Directory Azure. |
| microsoft.aad.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |

### <a name="license-administrator"></a>Administrator licencji
Można zarządzać licencje produktów dla użytkowników i grup.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Aktualizowanie właściwości users.usageLocation w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="lync-service-administrator"></a>Administrator usługi Lync
Może zarządzać wszystkimi aspektami produktu Skype dla firm.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="message-center-reader"></a>Czytelnik Centrum wiadomości
Może czytać wiadomości i aktualizacje dla swojej organizacji tylko w Centrum wiadomości usługi Office 365. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w elemencie microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Pomoc techniczna dla partnerów (warstwa 1)
Nie używaj — nie są przeznaczone do użytku ogólnego.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizowanie właściwości podstawowych w kontaktach w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Tworzenie kontaktów w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/members/update | Aktualizowanie właściwości groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aktualizowanie właściwości groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualizowanie właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizowanie właściwości podstawowych użytkowników w usłudze Active Directory Azure. |
| microsoft.aad.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aktualizowanie właściwości users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="partner-tier2-support"></a>Pomoc techniczna dla partnerów (warstwa 2)
Nie używaj — nie są przeznaczone do użytku ogólnego.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizowanie właściwości podstawowych w kontaktach w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Tworzenie kontaktów w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie właściwości standardowych w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aktualizowanie właściwości groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Organization/Basic/Update | Aktualizowanie właściwości podstawowych w organizacji w usłudze Active Directory Azure. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Aktualizowanie właściwości organization.trustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualizowanie właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizowanie właściwości podstawowych użytkowników w usłudze Active Directory Azure. |
| microsoft.aad.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aktualizowanie właściwości users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="power-bi-service-administrator"></a>Administrator usługi Power BI
Może zarządzać wszystkimi aspektami produktu Power BI.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="privileged-role-administrator"></a>Administrator uprawnionej roli
Mogą zarządzać przypisaniami ról w usłudze Azure AD i wszystkimi aspektami Privileged Identity Management.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Aktualizowanie elementu directoryRoles w usłudze Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w elemencie microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Czytnik raportów
Może odczytywać raporty logowania i inspekcji.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowane) w dziennikach inspekcji w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowanych) w elemencie signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Odczytywanie raportów dotyczących użycia usługi Office 365. |

### <a name="security-administrator"></a>Administrator zabezpieczeń
Może odczytywać informacje o zabezpieczeniach i raporty oraz zarządzać konfiguracją w usłudze Azure AD i Office 365.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Aktualizowanie właściwości applications.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowane) w dziennikach inspekcji w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytywanie właściwości devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aktualizowanie właściwości podstawowych w zasadach w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Aktualizowanie właściwości policies.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Aktualizowanie właściwości policies.tenantDefault w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualizowanie właściwości servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowanych) w elemencie signInReports w usłudze Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Odczytywanie wszystkich zasobów w elemencie microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Aktualizowanie wszystkich zasobów w elemencie microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczytywanie wszystkich zasobów w elemencie microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Odczytywanie wszystkich aspektów Centrum ochrony usługi Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Aktualizowanie wszystkich zasobów w elemencie microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="security-reader"></a>Odczytywanie zabezpieczeń
Może odczytywać informacje zabezpieczające i raporty o zabezpieczeniach w usługach Azure AD i Office 365.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowane) w dziennikach inspekcji w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytywanie właściwości devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczytywanie wszystkich właściwości (w tym właściwości uprzywilejowanych) w elemencie signInReports w usłudze Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Odczytywanie wszystkich zasobów w elemencie microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczytywanie wszystkich zasobów w elemencie microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Odczytywanie wszystkich aspektów Centrum ochrony usługi Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="service-support-administrator"></a>Administrator pomocy technicznej dotyczącej usług
Może odczytywać informacje o kondycji usług i zarządzać biletami pomocy technicznej.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrator usługi SharePoint
Może zarządzać wszystkimi aspektami usługi SharePoint.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualizowanie właściwości groups.unified w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aktualizowanie podstawowych właściwości grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/create | Tworzenie grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/delete | Usuń grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aktualizowanie członkostwa w grupach usługi Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aktualizowanie własności grup usługi Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w elemencie microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="teams-communications-administrator"></a>Administrator komunikacji w usłudze Teams
Może zarządzać funkcjami rozmów telefonicznych i spotkań w ramach usługi Microsoft Teams.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Odczytywanie raportów dotyczących użycia usługi Office 365. |

### <a name="teams-communications-support-engineer"></a>Inżynier pomocy technicznej komunikacji w usłudze Teams
Może rozwiązywać problemy z komunikacją w usłudze Teams przy użyciu zaawansowanych narzędzi.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="teams-communications-support-specialist"></a>Specjalista pomocy technicznej komunikacji w usłudze Teams
Może rozwiązywać problemy z komunikacją w usłudze Teams przy użyciu podstawowych narzędzi.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="teams-service-administrator"></a>Administrator usługi Teams
Może zarządzać usługą Microsoft Teams. 

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytywanie właściwości groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualizowanie właściwości groups.unified w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aktualizowanie podstawowych właściwości grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/create | Tworzenie grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/delete | Usuń grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aktualizowanie członkostwa w grupach usługi Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aktualizowanie własności grup usługi Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Odczytywanie raportów dotyczących użycia usługi Office 365. |

### <a name="user-account-administrator"></a>Administrator kont użytkowników
Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetowaniem haseł dla administratorów z ograniczonymi uprawnieniami.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Tworzenie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuwanie elementu appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizowanie elementu appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizowanie właściwości podstawowych w kontaktach w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Tworzenie kontaktów w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualizowanie właściwości groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizowanie właściwości podstawowych w grupach w usłudze Active Directory Azure. |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytywanie właściwości groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aktualizowanie właściwości groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aktualizowanie właściwości groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aktualizowanie właściwości groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aktualizowanie właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizowanie właściwości podstawowych użytkowników w usłudze Active Directory Azure. |
| microsoft.aad.directory/users/create | Tworzenie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aktualizowanie właściwości users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytywanie podstawowych właściwości we wszystkich zasobach w elemencie microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

## <a name="deprecated-roles"></a>Przestarzałe ról

Nie można używać następujących ról. One są przestarzałe i zostaną usunięte z usługi Azure AD w przyszłości.

* Administrator licencji adhoc
* Dołączanie urządzeń
* Menedżerowie urządzenia
* Użytkownicy urządzeń
* Tworzenie użytkowników zweryfikowanych za pośrednictwem poczty e-mail
* Administrator skrzynki pocztowej
* Dołączanie urządzeń w miejscu pracy

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat sposobu przypisywania użytkownika jako administratora subskrypcji platformy Azure, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
