---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia Check Point CloudGuard Dome9 Arc | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Dome9 łuk Check Point CloudGuard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46ac34db21576c7e2de2271a468e3e782ff6aa9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885348"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia Check Point CloudGuard Dome9 Arc

W tym samouczku dowiesz się, jak zintegrować łuk Check Point CloudGuard Dome9 z Azure Active Directory (Azure AD). Podczas integrowania łuku Check Point CloudGuard Dome9 z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do łuku Check Point CloudGuard Dome9.
* Zezwól użytkownikom na automatyczne logowanie do usługi Check Point CloudGuard Dome9 ARC z kontami w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Usługa Check Point CloudGuard Dome9 — subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Łuk Check Point CloudGuard Dome9, który obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Dodawanie łuku Check Point CloudGuard Dome9 z galerii

Aby skonfigurować integrację usługi Check Point CloudGuard Dome9 w usłudze Azure AD, należy dodać łuk Check Point CloudGuard Dome9 z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Check Point CloudGuard Dome9 Arc** w polu wyszukiwania.
1. Wybierz pozycję **Check Point CloudGuard Dome9 łuk** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla CloudGuard Check Point Dome9 Arc

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą łuku Check Point CloudGuard Dome9 przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w CloudGuard Check Point Dome9 Arc.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą łuku Check Point CloudGuard Dome9, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj pozycję Check Point CloudGuard Dome9 Arc SSO](#configure-check-point-cloudguard-dome9-arc-sso)** -, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz pozycję Check Point CloudGuard Dome9 Arc test użytkownika](#create-check-point-cloudguard-dome9-arc-test-user)** — Aby uzyskać odpowiedni odpowiednik B. Simon w elemencie Check Point CloudGuard Dome9 ARC, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Check Point CloudGuard Dome9 Arc** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://secure.dome9.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami adresu URL odpowiedzi i adresu URL logowania. Otrzymasz wartość `<company name>` z sekcji **Configure Check Point CloudGuard Dome9 Arc SSO** , która została omówiona w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja typu Check Point CloudGuard Dome9 oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja Check Point CloudGuard Dome9 Arc oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Kliknij [tutaj](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial) , aby dowiedzieć się, jak utworzyć role w usłudze Azure AD.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Na stronie **Konfigurowanie łuku Check Point CloudGuard Dome9** , skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do łuku Check Point CloudGuard Dome9.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Check Point CloudGuard Dome9 Arc**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Konfigurowanie opcji Check Point CloudGuard Dome9 Arc SSO

1. Aby zautomatyzować konfigurację w programie Check Point CloudGuard Dome9 ARC, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij przycisk **Setup Check Point CloudGuard Dome9 Arc** kieruje użytkownika do aplikacji Check Point CloudGuard Dome9 Arc. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Check Point CloudGuard Dome9 Arc. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz skonfigurować ręcznie pozycję Check Point CloudGuard Dome9 ARC, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy w programie Check Point CloudGuard Dome9e jako administrator i wykonaj następujące czynności:

2. Kliknij pozycję **Profile Settings** (Ustawienia profilu) w prawym górnym rogu, a następnie kliknij przycisk **Account Settings** (Ustawienia konta). 

    ![Konfiguracja Arc Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure1.png)

3. Przejdź na kartę **SSO** (Logowanie jednokrotne), a następnie kliknij przycisk **ENABLE** (WŁĄCZ).

    ![Konfiguracja Arc Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure2.png)

4. W sekcji SSO Configuration (Konfiguracja logowania jednokrotnego) wykonaj następujące czynności:

    ![Konfiguracja Arc Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure3.png)

    a. Wprowadź nazwę firmy w polu tekstowym **Account ID** (Identyfikator konta). Ta wartość jest używana w adresie URL **odpowiedzi** i **logowania** wymienionym w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. W polu tekstowym **wystawca** wklej wartość identyfikatora usługi **Azure AD**, która została skopiowana, a Azure Portal.

    d. W polu tekstowym **Idp endpoint url** (Adres URL punktu końcowego dostawcy tożsamości) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. Otwórz w Notatniku pobrany certyfikat zakodowany w formacie Base64, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **SAML X.509 certificate** (Certyfikat X.509 SAML).

    e. Kliknij pozycję **Zapisz**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Tworzenie użytkownika testowego Arc Check Point CloudGuard Dome9

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Check Point CloudGuard Dome9 ARC, muszą one być obsługiwane w aplikacji. Opcja Check Point CloudGuard Dome9 Arc obsługuje obsługę just-in-Time, ale w celu zapewnienia prawidłowego działania użytkownik musi wybrać określoną **rolę** i przypisać ją do użytkownika.

   >[!Note]
   >Aby uzyskać szczegółowe informacje dotyczące tworzenia **ról** i innych szczegółów, skontaktuj się z działem [Check Point CloudGuard Dome9 Arc Client Support Team](mailto:Dome9@checkpoint.com).

**Aby ręcznie aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy The CloudGuard Dome9 Arc jako administrator.

2. Kliknij pozycję **Users & Roles** (Użytkownicy i role), a następnie kliknij pozycję **Users** (Użytkownicy).

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user1.png)

3. Kliknij przycisk **ADD USER** (DODAJ UŻYTKOWNIKA).

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user2.png)

4. W sekcji **Create User** (Tworzenie użytkownika) wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user3.png)

    a. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład B.Simon@contoso.com.

    b. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. B.

    d. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak Simon.

    d. W polu **SSO User** (Użytkownik logowania jednokrotnego) wybierz ustawienie **On** (Włączone).

    e. Kliknij przycisk **UTWÓRZ**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Arc Check Point CloudGuard Dome9 w panelu dostępu należy automatycznie zalogować się do łuku usługi Check Point CloudGuard Dome9, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj pozycję Check Point CloudGuard Dome9 ARC z usługą Azure AD](https://aad.portal.azure.com/)