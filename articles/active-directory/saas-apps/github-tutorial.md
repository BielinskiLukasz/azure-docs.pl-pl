---
title: 'Samouczek: integracja Azure Active Directory z usługą GitHub | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f4084532ac370880df69d825927240c297dd371
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129910"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) w usłudze GitHub

W tym samouczku dowiesz się, jak zintegrować usługę GitHub z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi GitHub z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do serwisu GitHub.
* Zezwól użytkownikom na automatyczne logowanie do usługi GitHub przy użyciu swoich kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją GitHub, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Organizacja usługi GitHub utworzona w [chmurze usługi GitHub dla przedsiębiorstw](https://help.github.com/articles/github-s-products/#github-enterprise), która wymaga [planu rozliczeniowego usługi GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja GitHub obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi (SP)**

* Aplikacja GitHub obsługuje [**zautomatyzowaną** aprowizację użytkowników](github-provisioning-tutorial.md)
* Po skonfigurowaniu usługi GitHub można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>Dodawanie aplikacji GitHub z galerii

Aby skonfigurować integrację aplikacji GitHub z usługą Azure AD, należy dodać z galerii usługę GitHub do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **GitHub** w polu wyszukiwania.
1. Wybierz pozycję **GitHub** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi GitHub

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD z usługą GitHub przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w serwisie GitHub.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi GitHub, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-github-sso)** w usłudze GitHub, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego usługi GitHub](#create-github-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze GitHub, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji usługi **GitHub** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

   a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://github.com/orgs/<entity-id>/sso`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Należy pamiętać, że nie są to rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W tym miejscu zalecamy użycie unikatowej wartości ciągu w identyfikatorze. Przejdź do sekcji dotyczącej administrowania aplikacją GitHub, aby pobrać te wartości.

5. Aplikacja GitHub oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja GitHub oczekuje, że atrybut **nameidentifier** będzie mapowany na atrybut **user.mail**, dlatego należy zmodyfikować mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutów.

    ![image](common/edit-attribute.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie aplikacji GitHub** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do serwisu GitHub.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **GitHub**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-github-sso"></a>Konfigurowanie rejestracji jednokrotnej usługi GitHub

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji GitHub jako administrator.

2. Przejdź do karty **Settings** (Ustawienia) i kliknij pozycję **Security** (Zabezpieczenia)

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Zaznacz pole **Enable SAML authentication** (Włącz uwierzytelnianie SAML), aby aktywować pola konfiguracji logowania jednokrotnego. Następnie użyj wartości adresu URL logowania jednokrotnego do zaktualizowania wartości adresu URL logowania jednokrotnego w konfiguracji usługi Azure AD.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Skonfiguruj następujące pola:

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. W polu tekstowym **Sign on URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Issuer** (Wystawca) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    c. Otwórz certyfikat pobrany z witryny Azure Portal w programie Notatnik i wklej jego zawartość w polu tekstowym **Public Certificate** (Certyfikat publiczny).

    d. Kliknij ikonę **edycji**, aby zmodyfikować wartości pól **Signature Method** (Metoda podpisu) i **Digest Method** (Metoda szyfrowania) z **RSA-SHA1** i **SHA1** na **RSA-SHA256** i **SHA256**, jak pokazano poniżej.
    
    e. Zaktualizuj **adres url Assertion Consumer Service (adres URL odpowiedzi)** z domyślnego adresu URL, aby adres URL w usłudze GITHUB odpowiadał adresowi URL w rejestracji aplikacji platformy Azure.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Kliknij przycisk **Test SAML configuration** (Testuj konfigurację SAML), aby się upewnić, że podczas logowania jednokrotnego nie występują żadne niepowodzenia weryfikacji ani błędy.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Kliknij pozycję **Zapisz**

> [!NOTE]
> Logowanie jednokrotne w aplikacji GitHub przeprowadza uwierzytelnianie w konkretnej organizacji w usłudze GitHub i nie zastępuje uwierzytelniania samej usługi GitHub. W związku z tym jeśli sesja użytkownika w witrynie github.com wygaśnie, w czasie logowania jednokrotnego może pojawić się prośba o uwierzytelnienie za pomocą identyfikatora/hasła usługi GitHub.

### <a name="create-github-test-user"></a>Tworzenie użytkownika testowego w aplikacji GitHub

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w aplikacji GitHub. Aplikacja GitHub obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznej aprowizacji użytkowników można znaleźć [tutaj](github-provisioning-tutorial.md).

**Jeśli musisz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny aplikacji GitHub jako administrator.

2. Kliknij kartę **People** (Osoby).

    ![Osób](./media/github-tutorial/tutorial_github_config_github_08.png "People")

3. Kliknij przycisk **Invite member** (Zaproś członka).

    ![Zapraszanie użytkowników](./media/github-tutorial/tutorial_github_config_github_09.png "Zapraszanie użytkowników")

4. W oknie dialogowym **Invite member** (Zapraszanie członka) wykonaj następujące kroki:

    a. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika Britta Simon.

    ![Zapraszanie osób](./media/github-tutorial/tutorial_github_config_github_10.png "Zapraszanie osób")

    b. Kliknij przycisk **Send Invitation** (Wyślij zaproszenie).

    ![Zapraszanie osób](./media/github-tutorial/tutorial_github_config_github_11.png "Zapraszanie osób")

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka GitHub na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji GitHub, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę GitHub z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
