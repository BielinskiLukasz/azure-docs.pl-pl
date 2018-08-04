---
title: Konfigurowanie logowania jednokrotnego usługi Azure AD dla aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do samoobsługi połączyć aplikacje do usługi Azure Active Directory przy użyciu języka SAML i logowania jednokrotnego opartego na hasło
services: active-directory
author: barbkess
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: barbkess
ms.reviewer: asmalser,luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f3511b4d9e75136fd0105785a1e033378d2ebd1
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494025"
---
# <a name="configure-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory

Ten artykuł dotyczy funkcja, która umożliwia administratorom skonfigurowanie logowania jednokrotnego do aplikacji, które nie znajduje się w galerii aplikacji usługi Azure Active Directory *bez konieczności pisania kodu*. Ta funkcja została wydana w wersji zapoznawczej Technical Preview 18 listopada 2015 i znajduje się w [usługi Azure Active Directory — wersja Premium](fundamentals/active-directory-whatis.md). Jeśli zamiast tego szukasz wskazówki dla deweloperów o tym, jak zintegrować aplikacje niestandardowe z usługą Azure AD za pomocą kodu, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](develop/authentication-scenarios.md).

Galeria aplikacji usługi Azure Active Directory zawiera listę aplikacji, które są znane formularz logowania jednokrotnego przy użyciu usługi Azure Active Directory, pomocy technicznej zgodnie z opisem w [w tym artykule](manage-apps/what-is-single-sign-on.md). Po (jako IT specjalista lub system integratora w swojej organizacji) znalezieniu aplikacji, którą chcesz się połączyć, możesz rozpocząć zgodnie z instrukcjami krok po kroku przedstawiony w witrynie Azure portal umożliwia logowanie jednokrotne.

Klienci z [usługi Azure Active Directory — wersja Premium](fundamentals/active-directory-whatis.md) licencji również uzyskać te dodatkowe możliwości:

* Samoobsługowa integracja dowolnej aplikacji, która obsługuje dostawców tożsamości SAML 2.0 (zainicjowanego przez dostawcę usług lub inicjowane przez dostawcę tożsamości)
* Samoobsługowa integracja dowolnej aplikacji sieci web, który jest oparty na języku HTML strony logowania za pomocą [SSO oparte na hasłach](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* Samoobsługowe połączenia aplikacji, które używają protokołu Standard SCIM do inicjowania obsługi użytkowników ([opisane w tym miejscu](manage-apps/use-scim-to-provision-users-and-groups.md))
* Możliwość dodawania łączy do dowolnej aplikacji w [uruchamianie aplikacji usługi Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panelu dostępu usługi Azure AD](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users)

Może to obejmować nie tylko aplikacje SaaS, które używają, ale ma nie jeszcze została uruchomiona w galerii aplikacji usługi Azure AD, ale aplikacje sieci web innych firm, które Twoja organizacja została wdrożona do serwerów, które możesz kontrolować, zarówno w chmurze lub lokalnie.

Te funkcje, nazywane również *szablony integracji aplikacji*, punkty połączeń opartych na standardach dla aplikacji, które obsługują SAML, Standard SCIM lub uwierzytelnianie oparte na formularzach i obejmują elastyczne opcje i ustawienia zgodność z szerokim liczby aplikacji. 

## <a name="adding-an-unlisted-application"></a>Dodawanie aplikacji nieznajdujących się na liście
Aby połączyć aplikację przy użyciu szablonu usługi integracji aplikacji, zaloguj się do witryny Azure portal przy użyciu konta administratora usługi Azure Active Directory. Przejdź do **usługi Active Directory > aplikacje dla przedsiębiorstw > Nowa aplikacja > Aplikacja spoza galerii** zaznacz **Dodaj**, a następnie **Dodawanie aplikacji z galerii** .

  ![](./media/active-directory-saas-custom-apps/customapp1.png)

W galerii aplikacji nieznajdujących się na liście aplikacji można dodawać przez zaznaczenie **aplikacji spoza galerii** Kafelek, który jest wyświetlany w wynikach wyszukiwania, jeśli nie można znaleźć żądanej aplikacji. Po wpisaniu nazwy aplikacji, można skonfigurować opcje logowania jednokrotnego i zachowania. 

**Szybkie porady**: najlepszym rozwiązaniem, należy użyć funkcji wyszukiwania, sprawdź, czy aplikacja już istnieje w galerii aplikacji. Jeśli aplikacja zostanie znaleziony, i jego opis wymienia logowanie jednokrotne, aplikacja jest już obsługiwane dla federacyjnego logowania jednokrotnego.

  ![](./media/active-directory-saas-custom-apps/customapp2.png)

Dodawanie aplikacji w ten sposób zapewnia podobne do tego dostępne dla wstępnie zintegrowanych aplikacji. Aby rozpocząć, wybierz opcję **skonfigurować logowanie jednokrotne** lub kliknąć **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji. Następny ekran przedstawia opcje konfigurowania logowania jednokrotnego. Opcje są opisane w kolejnych sekcjach tego artykułu.
  
![](./media/active-directory-saas-custom-apps/customapp3.png)

## <a name="saml-based-single-sign-on"></a>Logowanie jednokrotne oparte na języku SAML
Wybierz tę opcję, aby skonfigurować uwierzytelnianie oparte na protokole SAML dla aplikacji. Wymaga to, że aplikacja obsługuje SAML 2.0. Należy zbierać informacje dotyczące sposobu używania funkcji SAML aplikacji przed kontynuowaniem. Wykonaj poniższe sekcje, aby skonfigurować logowanie jednokrotne między aplikacją i usługi Azure AD.

### <a name="enter-basic-saml-configuration"></a>Wprowadź podstawową konfigurację protokołu SAML

Aby skonfigurować usługę Azure AD, wprowadź podstawową konfigurację protokołu SAML. Można ręcznie wprowadzić wartości lub Przekaż plik metadanych do wyodrębnienia wartości pól.

  ![Firma litware, domena i adresy URL](./media/active-directory-saas-custom-apps/customapp4.png)

- **Na adres URL logowania (zainicjowanego przez dostawcę usług tylko)** — w przypadku, gdy użytkownik przechodzi do logowania się w tej aplikacji. Jeśli aplikacja jest skonfigurowany do wykonania usługi zainicjowane z dostawcą logowania jednokrotnego, gdy użytkownik przechodzi do tego adresu URL, dostawca usług będzie wykonywać niezbędne przekierowanie do usługi Azure AD do uwierzytelniania i logowania użytkownika w. Jeśli to pole zostanie wypełnione, usługi Azure AD będzie używać tego adresu URL można uruchomić aplikacji z usługi Office 365 i stronie panelu dostępu usługi Azure AD. Jeśli to pole zostanie pominięte, a następnie usługi Azure AD zamiast tego będzie wykonywać dostawcy tożsamości — inicjowane logowania jednokrotnego, gdy aplikacja jest uruchamiana z panelu dostępu usługi Azure AD, Office 365 lub Azure AD pojedynczy adres URL logowania (z możliwością kopiowania z karty Pulpit nawigacyjny).
- **Identyfikator** -musi jednoznacznie wskazywać na aplikację, dla których logowanie jednokrotne jest konfigurowane. Możesz znaleźć tę wartość jako element wystawcy w AuthRequest (żądaniu SAML) wysłana przez aplikację. Ta wartość pojawia się również jako **identyfikator jednostki** w żadnych metadanych SAML udostępniany przez aplikację. W dokumentacji aplikacji SAML szczegółowe informacje na temat co to jest wartość Identyfikatora jednostki lub grupy odbiorców. 

    Oto przykład sposobu wyświetlania identyfikatora lub wystawcy żądania języka SAML wysłanego przez aplikację do usługi Azure AD:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **Adres URL odpowiedzi** — adres URL odpowiedzi to, gdzie aplikacja oczekuje otrzymać SAML token. To również jest określane jako adresu URL asercji konsumenta Service (ACS). Sprawdź dokumentacji języka SAML aplikacji, aby uzyskać szczegółowe informacje na temat odpowiedzi tokenu SAML adres URL lub adres URL usługi ACS. 

    Konfigurowanie wielu adresów URL odpowiedzi służy poniższy skrypt programu PowerShell.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Aby uzyskać więcej informacji, zobacz [żądania uwierzytelniania SAML 2.0 i odpowiedzi, które obsługuje usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Przeglądanie lub dostosowywanie oświadczeń wystawionych w tokenie języka SAML

Po użytkownik uwierzytelnia się do aplikacji, usługi Azure AD wystawi SAML token do aplikacji, która zawiera informacje (lub oświadczenia) o użytkowniku, który unikatowo identyfikuje je. Domyślnie w tym nazwę użytkownika, adres e-mail, imię i nazwisko użytkownika. 

Umożliwia wyświetlenie i edytowanie oświadczenia wysyłane w tokenie SAML do aplikacji w ramach **atrybuty** kartę.

  ![](./media/active-directory-saas-custom-apps/customapp7.png)

Istnieją dwie przyczyny, dlaczego może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:

- Aplikacja została zapisana wymaga innego zestawu oświadczeń identyfikatorów URI lub wartości oświadczeń.
- Twoja aplikacja została wdrożona w sposób, który wymaga NameIdentifier roszczenia inne niż nazwa użytkownika (zwane również główną nazwę użytkownika) przechowywanych w usłudze Azure Active Directory. 

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](./develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Data wygaśnięcia certyfikatu przeglądu, stan i powiadomienia e-mail

Po utworzeniu galerii lub aplikacja spoza galerii usługi Azure AD utworzy certyfikat specyficzne dla aplikacji z datą wygaśnięcia 3 lat od daty utworzenia. Potrzebujesz tego certyfikatu do skonfigurowania zaufania między usługą Azure AD i aplikacji. Aby uzyskać szczegółowe informacje na temat formatu certyfikatu w dokumentacji aplikacji SAML. 

Z usługi Azure AD możesz pobrać certyfikat w formacie Base64 lub w formacie nieprzetworzonym. Ponadto można uzyskać certyfikatu przez pobranie pliku XML metadanych aplikacji lub za pomocą adresu URL metadanych Federacji aplikacji.

  ![Certyfikat](./media/active-directory-saas-custom-apps/certificate.png)

Sprawdź, czy certyfikat ma:

- Data wygaśnięcia żądaną. Można skonfigurować datę wygaśnięcia maksymalnie 3 lata.
- Stan aktywny. Jeśli stan jest nieaktywny, Zmień stan na aktywny. Aby zmienić stan, zapoznaj się z **Active** , a następnie Zapisz konfigurację. 
- Wiadomość e-mail z powiadomieniem poprawne. Gdy aktywnego certyfikatu zbliża się data wygaśnięcia, usługi Azure AD będzie wysyłał powiadomienie na adres e-mail skonfigurowany w tym polu.  

Aby uzyskać więcej informacji, zobacz [zarządzać certyfikatami federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Konfigurowanie aplikacji docelowej

Aby skonfigurować aplikację do logowania jednokrotnego, zlokalizuj dokumentacji aplikacji. Aby znaleźć dokumentację, przewiń do końca strony konfiguracji opartej na SAML logowania jednokrotnego, a następnie kliknij pozycję **Konfiguruj <application name>** . 

Wymagane wartości różny w zależności od aplikacji. Aby uzyskać szczegółowe informacje Zobacz dokumentacji języka SAML aplikacji. Adres URL usługi wylogowania jednokrotnego logowania i zarówno prowadzić do tego samego punktu końcowego, który jest punktem końcowym obsługiwanie żądań SAML dla swojego wystąpienia usługi Azure AD. Identyfikator jednostki SAML jest wartość, która jest wyświetlana jako wystawca w tokenu SAML, które jest wystawiony dla aplikacji.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Przypisywanie użytkowników i grup do aplikacji SAML

Gdy aplikacja została skonfigurowana do użycia usługi Azure AD jako dostawcy tożsamości opartej na SAML, następnie jest już prawie gotowe do testowania. Jako formant zabezpieczeń usługi Azure AD nie będzie wystawiać tokenu, pozwalając użytkownikom na logowanie się do aplikacji, chyba że usługi Azure AD udzielił dostępu dla użytkownika. Użytkownicy mogą otrzymać dostęp bezpośrednio lub za pomocą członkostwa w grupie. 

Aby przypisać użytkownika lub grupy do aplikacji, kliknij przycisk **Przypisz użytkowników** przycisku. Wybierz użytkownika lub grupy, które chcesz przypisać, a następnie wybierz pozycję **przypisać** przycisku.

  ![](./media/active-directory-saas-custom-apps/customapp6.png)

Przypisanie użytkownika umożliwi usłudze Azure AD wystawić token dla użytkownika. Powoduje również kafelka dla tej aplikacji, które będą wyświetlane na panelu dostępu użytkownika. Kafelek aplikacji pojawi się także w module uruchamiania aplikacji usługi Office 365, jeśli użytkownik korzysta z usługi Office 365. 

> [!NOTE] 
> Możesz przekazać logo kafelka na potrzeby aplikacji przy użyciu **Przekaż Logo** znajdujący się na **Konfiguruj** kartę dla aplikacji. 


### <a name="test-the-saml-application"></a>Testowanie aplikacji SAML

Przed testowaniem aplikacji SAML, konieczne jest posiadanie Konfigurowanie aplikacji z usługą Azure AD oraz przypisać użytkowników lub grup do aplikacji. Aby przetestować aplikację SAML, zobacz [sposób debugowania opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory](develop/active-directory-saml-debugging.md).

## <a name="password-single-sign-on"></a>Hasło logowania jednokrotnego

Wybierz tę opcję, aby skonfigurować [opartego na hasłach logowania jednokrotnego](manage-apps/what-is-single-sign-on.md) dla aplikacji sieci web, który ma HTML strony logowania. Logowanie Jednokrotne oparte na hasłach, określane również jako hasło vaulting, pozwala na zarządzanie dostępem użytkowników i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w której kilka użytkownicy potrzebują do udostępnienia jednego konta, takie jak do kont aplikacji mediów społecznościowych organizacji. 

Po wybraniu **dalej**, użytkownik jest monitowany o podanie adresu URL aplikacji sieci web stronę logowania. Należy pamiętać, że musi to być strony, która zawiera pola danych wejściowych użytkownika i hasło. Po wprowadzeniu usługi Azure AD rozpoczyna się proces analizy strony logowania wprowadź nazwę użytkownika i hasło z danych wejściowych. Jeśli proces zakończy się niepowodzeniem, następnie go przeprowadzi Cię przez alternatywny proces instalowania rozszerzenia przeglądarki (wymaga programu Internet Explorer, Chrome lub Firefox), która umożliwi Ci do przechwycenia ręcznie pola.

Po przechwyceniu strony logowania, można przypisać użytkowników i grup i poświadczeń zasady można ustawić tak samo jak zwykła [hasło logowania jednokrotnego aplikacji](manage-apps/what-is-single-sign-on.md).

> [!NOTE] 
> Możesz przekazać logo kafelka na potrzeby aplikacji przy użyciu **Przekaż Logo** znajdujący się na **Konfiguruj** kartę dla aplikacji. 
>

## <a name="existing-single-sign-on"></a>Istniejące logowanie jednokrotne
Wybierz tę opcję, aby dodać łącze do aplikacji w celu portalu panelu dostępu usługi Azure AD lub Office 365 Twojej organizacji. Służy to do dodawania łączy do aplikacji niestandardowe sieci web, które obecnie używają usługi Azure Active Directory Federation Services (lub innej usługi federacyjnej) zamiast usługi Azure AD do uwierzytelniania. Lub można dodać linków bezpośrednich do określonej strony programu SharePoint lub innych stron sieci web, które mają być wyświetlane w panelach dostępu użytkowników. 

Po wybraniu **dalej**, użytkownik jest monitowany o podanie adresu URL aplikacji, aby połączyć. Po zakończeniu użytkowników i grup można przypisać do aplikacji, co powoduje, że są wyświetlane w aplikacji [uruchamianie aplikacji usługi Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panelu dostępu usługi Azure AD](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) dla tych użytkowników.

> [!NOTE] 
> Możesz przekazać logo kafelka na potrzeby aplikacji przy użyciu **Przekaż Logo** znajdujący się na **Konfiguruj** kartę dla aplikacji. 
>

## <a name="related-articles"></a>Powiązane artykuły

- [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
- [Dostosowywanie oświadczeń wystawionych w tokenie SAML wstępnie zintegrowanych aplikacji](active-directory-saml-claims-customization.md)
- [Rozwiązywanie problemów z opartej na SAML logowania jednokrotnego](active-directory-saml-debugging.md)

