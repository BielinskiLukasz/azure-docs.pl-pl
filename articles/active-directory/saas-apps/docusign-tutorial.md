---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą DocuSign | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3022303f319ba7955f80ae0b52783fa9b5d411f2
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290074"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą DocuSign

W tym samouczku dowiesz się, jak zintegrować usługę DocuSign z usługą Microsoft Azure Active Directory (Azure AD). Po zintegrowaniu usługi DocuSign z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do usługi DocuSign.
* Włącz automatyczne logowanie do DocuSign dla użytkowników za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS (Software as a Service) z usługą Azure AD, zobacz [Logowanie jednokrotne do aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi DocuSign, która jest włączona (Logowanie jednokrotne).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym w celu sprawdzenia, czy:

* DocuSign obsługuje logowanie jednokrotne przez dostawcę usług (SP).

* DocuSign obsługuje Inicjowanie obsługi użytkowników *just in Time* .

* DocuSign obsługuje [Automatyczne Inicjowanie obsługi użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).
* Po skonfigurowaniu DocuSign można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>Dodawanie DocuSign z galerii

Aby skonfigurować integrację programu DocuSign z usługą Azure AD, musisz dodać DocuSign z galerii do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub za pomocą konto Microsoft osobistych.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Docusign** w polu wyszukiwania.
1. Wybierz pozycję **Docusign** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla DocuSign

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą DocuSign przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w DocuSign.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą DocuSign, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj Logowanie jednokrotne](#configure-docusign-sso) w usłudze Docusign, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego Docusign](#create-docusign-test-user) w celu wygenerowania odpowiednika B. Simon w Docusign, która jest połączona z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Docusign** Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę pióra dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące kroki:

    a. W polu **adres URL logowania** wprowadź adres URL przy użyciu następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL przy użyciu następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Te wartości ujęte w nawiasy są symbolami zastępczymi. Zastąp je wartościami w rzeczywistym adresie URL logowania i identyfikatorem. Te szczegóły zostały wyjaśnione w sekcji "Zobacz punkty końcowe protokołu SAML 2,0" w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **certyfikat (base64)** . Wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfiguracja Docusign** skopiuj odpowiedni adres URL (lub adresy URL) zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. W górnej części ekranu wybierz pozycję **nowy użytkownik**.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wpisz **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź `<username>@<companydomain>.<extension>`. Na przykład: `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie zanotuj wartość wyświetlaną w polu **hasło** .
   1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji zostanie udzielony dostęp do usługi DocuSign. Simon, aby ten użytkownik mógł korzystać z logowania jednokrotnego na platformie Azure.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Docusign**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy **Użytkownicy** , a następnie naciśnij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie naciśnij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz przycisk **Przypisz** .

## <a name="configure-docusign-sso"></a>Konfigurowanie logowania jednokrotnego DocuSign

1. Aby zautomatyzować konfigurację w programie DocuSign, należy zainstalować rozszerzenie przeglądarki Moje aplikacje bezpieczne logowanie, wybierając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję **Setup Docusign**. Nastąpi przekierowanie do aplikacji DocuSign. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi DocuSign. Rozszerzenie przeglądarki automatycznie konfiguruje aplikację i automatyzuje kroki od 3 do 5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować DocuSign, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy DocuSign jako administrator.

4. W prawym górnym rogu strony wybierz logo profilu, a następnie wybierz pozycję **Przejdź do administratora**.
  
    ![Przejdź do administratora w obszarze Profil][51]

5. Na stronie rozwiązania domeny wybierz pozycję **domeny**.

    ![Rozwiązania domeny/domeny][50]

6. W sekcji **domeny** wybierz pozycję **domena roszczeń**.

    ![Opcja domeny dotyczącej roszczeń][52]

7. W oknie dialogowym **Przejmij domenę** w polu **nazwa domeny** wpisz domenę firmy, a następnie wybierz pozycję **Przejmij**. Upewnij się, że zweryfikowano domenę i że jej stan jest aktywny.

    ![Przejmij okno dialogowe nazwy domeny/domeny][53]

8. Na stronie rozwiązania domeny wybierz pozycję **dostawcy tożsamości**.
  
    ![Opcja dostawcy tożsamości][54]

9. W sekcji **dostawcy tożsamości** wybierz pozycję **Dodaj dostawcę tożsamości**.

    ![Dodaj opcję dostawcy tożsamości][55]

10. Na stronie **Ustawienia dostawcy tożsamości** wykonaj następujące czynności:

    ![Pola ustawień dostawcy tożsamości][56]

    a. W polu **Nazwa** wpisz unikatową nazwę konfiguracji. Nie używaj spacji.

    b. W **polu wystawca dostawcy tożsamości**wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    d. W polu **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. W polu **adres URL wylogowania dostawcy tożsamości** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    e. Wybierz pozycję **Podpisz żądanie AuthN**.

    f. W przypadku **wysyłania żądania AuthN przez**, wybierz pozycję **post**.

    g. W przypadku **wysyłania żądania wylogowania przez**program wybierz pozycję **Pobierz**.

    h. W sekcji **Mapowanie atrybutów niestandardowych** wybierz pozycję **Dodaj nowe mapowanie**.

       ![Niestandardowy interfejs użytkownika mapowania atrybutów][62]

    i. Wybierz pole, które chcesz zmapować do usługi Azure AD. W tym przykładzie **EmailAddress** jest mapowany na wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`. To jest domyślna nazwa usługi w usłudze Azure AD dla żądania poczty e-mail. Wybierz pozycję **Zapisz**.

       ![Pola mapowania atrybutów niestandardowych][57]

       > [!NOTE]
       > Użyj odpowiedniego **identyfikatora użytkownika** , aby zamapować użytkownika z usługi Azure AD do Docusign mapowania użytkownika. Wybierz odpowiednie pole i wprowadź odpowiednią wartość na podstawie ustawień organizacji.

    j. W sekcji **Certyfikaty dostawcy tożsamości** wybierz pozycję **Dodaj certyfikat**, Przekaż certyfikat pobrany z portalu usługi Azure AD, a następnie wybierz pozycję **Zapisz**.

       ![Certyfikaty dostawcy tożsamości/Dodawanie certyfikatu][58]

    k. W sekcji **dostawcy tożsamości** wybierz pozycję **Akcje**, a następnie wybierz pozycję **punkty końcowe**.

       ![Dostawcy tożsamości/punkty końcowe][59]

    l. W sekcji **Wyświetl punkty końcowe protokołu SAML 2,0** w portalu administracyjnym Docusign wykonaj następujące kroki:
       1. Skopiuj **adres URL wystawcy dostawcy usługi**, a następnie wklej go w polu **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

       1. Skopiuj **adres URL logowania dostawcy usługi**, a następnie wklej go do pola **Zaloguj się w adresie URL** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

       1. Wybierz polecenie **Zamknij**.

       ![Wyświetl punkty końcowe SAML 2,0][60]

### <a name="create-docusign-test-user"></a>Utwórz użytkownika testowego DocuSign

W tej sekcji użytkownik o nazwie B. Simon został utworzony w DocuSign. DocuSign obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze DocuSign, zostanie utworzony nowy po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Docusign](https://support.docusign.com/).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka DocuSign w panelu dostępu należy automatycznie zalogować się do wystąpienia DocuSign, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj DocuSign z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić DocuSign z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
