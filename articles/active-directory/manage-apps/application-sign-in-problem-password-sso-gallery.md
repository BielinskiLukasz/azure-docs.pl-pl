---
title: Problemy z logowaniem do aplikacji galerii usługi Azure AD, skonfigurowanej do obsługi hasło logowania jednokrotnego | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z aplikacją w galerii usługi Azure AD skonfigurowane dla hasła logowania jednokrotnego
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6fe05948b5fd61f205b5511d519883b18b65f17
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172101"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemy z logowaniem do aplikacji galerii usługi Azure AD, skonfigurowanej do obsługi hasło logowania jednokrotnego

Panelu dostępu jest wyświetlanie i uruchamianie aplikacji opartej na chmurze procesów, które administrator usługi Azure AD udzielił, ich dostęp do portalu opartego na sieci web, który umożliwia użytkownikowi, który ma konto służbowe lub szkolne w usłudze Azure Active Directory (Azure AD). Użytkownik, który ma wersje usługi Azure AD umożliwia również grupa samoobsługi i możliwości zarządzania aplikacjami za pomocą panelu dostępu. Panel dostępu różni się w witrynie Azure portal, a nie wymagać od użytkowników posiadania subskrypcji platformy Azure.

Aby użyć opartego na hasłach logowanie jednokrotne (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowany w przeglądarce użytkownika. To rozszerzenie jest pobierany automatycznie, gdy użytkownik wybierze aplikacji, która jest skonfigurowana na podstawie hasła logowania jednokrotnego.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Spełniających wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki, która obsługuje język JavaScript i CSS została włączona. Aby użyć opartego na hasłach logowanie jednokrotne (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowany w przeglądarce użytkownika. To rozszerzenie jest pobierany automatycznie, gdy użytkownik wybierze aplikacji, która jest skonfigurowana na podstawie hasła logowania jednokrotnego.

W przypadku logowania jednokrotnego opartego na hasłach może być przeglądarki przez użytkownika końcowego:

-   Internet Explorer 8, 9, 10, 11 — Windows 7 lub nowszy

-   Chrome — Windows 7 lub nowszy i System MacOS x lub później

-   Firefox 26.0 lub nowsze wersje — na Windows XP z dodatkiem SP2 lub nowszy i w systemie Mac OS X 10,6 lub nowszy

>[!NOTE]
>Rozszerzenie logowania jednokrotnego opartego na hasłach stają się dostępne dla programu Microsoft Edge w systemie Windows 10 po rozszerzenia przeglądarki w miarę udostępniania obsługi dla programu Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące kroki:

1.  Otwórz [panelu dostępu](https://myapps.microsoft.com) w jednym z obsługiwanych przeglądarek i zaloguj się jako **użytkownika** w usłudze Azure AD.

2.  Kliknij przycisk **logowania jednokrotnego hasła aplikacji** w panelu dostępu.

3.  W oknie komunikatu z pytaniem zainstalować oprogramowanie, wybierz **Zainstaluj teraz**.

4.  W zależności od przeglądarki być przekierowywany link pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli przeglądarka zapyta, wybrać opcję **Włącz** lub **Zezwalaj** rozszerzenia.

6.  Po zainstalowaniu **ponowne uruchomienie** sesji przeglądarki.

7.  Zaloguj się do panelu dostępu i zobacz, jeśli możesz to zrobić **Uruchom** logowania jednokrotnego hasła aplikacji

Może również Pobierz rozszerzenie dla programu Chrome i Firefox z bezpośrednie linki poniżej:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu do przeglądarki Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, które umożliwiają zdalnie zainstalować rozszerzenie panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Wymagania wstępne obejmują:

-   Po skonfigurowaniu [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), i komputerów użytkowników mają być przyłączone do domeny.

-   Musi mieć uprawnienia "Edytuj ustawienia" do edycji obiektu zasad grupy (GPO). Domyślnie członkowie następujące grupy zabezpieczeń mają to uprawnienie: Administratorzy domeny, Administratorzy przedsiębiorstwa i Twórcy-właściciele zasad grupy. [Dowiedz się więcej](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Postępuj zgodnie z samouczkiem [sposób wdrażania rozszerzenia Panelu dostępu do programu Internet Explorer przy użyciu zasad grupy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) instrukcje krok po kroku skonfigurować zasady grupy i wdrażać je dla użytkowników.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Rozwiązywanie problemów z panelu dostępu w programie Internet Explorer

Postępuj zgodnie z [Rozwiązywanie problemów z rozszerzenia Panelu dostępu do programu Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) przewodnika uzyskać dostęp do narzędzia do diagnostyki i instrukcje krok po kroku dotyczące konfigurowania rozszerzenia dla programu Internet Explorer.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować hasło logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, które należy:

-   Dodawanie aplikacji z galerii usługi Azure AD

-   [Konfigurowanie aplikacji dla hasła logowania jednokrotnego](#configure-the-application-for-password-single-sign-on)

-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [witryny Azure portal](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministratora**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdujący się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  W **wprowadź nazwę** pole tekstowe z **Dodaj z galerii** sekcji, wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

8.  Przed dodaniem aplikacji, można zmienić jego nazwę, z **nazwa** pola tekstowego.

9.  Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

Po krótkim czasie można zobaczyć okienko konfiguracji aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji dla hasła logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6.  Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9.  Przypisywanie użytkowników do aplikacji.

10. Ponadto, możesz także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń, się po uruchomieniu.

### <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby przypisać jeden lub więcej użytkowników aplikacji bezpośrednio, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6.  Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9.  Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typ w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwanie według nazwy lub adresu e-mail** pole wyszukiwania, a następnie kliknij pole wyboru, aby dodać użytkownika do **wybrane** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano można uruchamiać te aplikacje w panelu dostępu.

## <a name="if-these-troubleshoot-steps-dont-resolve-the-issue"></a>Jeśli rozwiązać te kroki nie umożliwiają rozwiązania problemu 
Otwórz bilet pomocy technicznej, używając następujących informacji, jeśli jest dostępny:

-   Identyfikator błędu korelacji

-   Nazwy UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Występuje, strefę czasową i przedziału czasu/czasu podczas błędu

-   Ślady narzędzia fiddler

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
