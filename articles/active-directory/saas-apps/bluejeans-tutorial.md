---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z usługą BlueJeans dla usługi Azure AD | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i BlueJeans dla usługi Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595044"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z usługą BlueJeans dla usługi Azure AD

W tym samouczku dowiesz się, jak zintegrować usługę BlueJeans for Azure AD z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi BlueJeans for Azure AD z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do BlueJeans dla usługi Azure AD.
* Zezwól użytkownikom na automatyczne logowanie do usługi BlueJeans w usłudze Azure AD przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* BlueJeans dla subskrypcji z obsługą logowania jednokrotnego (SSO) usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* BlueJeans dla usługi Azure AD **obsługuje** zainicjowanie rejestracji jednokrotnej w programie

* Usługa BlueJeans for Azure AD obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Dodawanie BlueJeans dla usługi Azure AD z galerii

Aby skonfigurować integrację BlueJeans dla usługi Azure AD z usługą Azure AD, musisz dodać BlueJeans do usługi Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **BlueJeans dla usługi Azure AD** w polu wyszukiwania.
1. Wybierz pozycję **BlueJeans dla usługi Azure AD** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla BlueJeans dla usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą BlueJeans dla usługi Azure AD przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze BlueJeans dla usługi Azure AD.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z usługą BlueJeans dla usługi Azure AD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj BlueJeans dla logowania jednokrotnego usługi Azure AD](#configure-bluejeans-for-azure-ad-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz BlueJeans dla użytkownika testowego usługi Azure AD](#create-bluejeans-for-azure-ad-test-user)** , aby uzyskać odpowiednik B. Simon w BlueJeans dla usługi Azure AD, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **BlueJeans dla integracji aplikacji usługi Azure AD** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać wartość, skontaktuj się z [BlueJeans dla zespołu pomocy technicznej usługi Azure AD Client](https://support.bluejeans.com/contact) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie usługi BlueJeans for Azure AD** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do BlueJeans dla usługi Azure AD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **BlueJeans dla usługi Azure AD**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Konfigurowanie BlueJeans na potrzeby logowania jednokrotnego w usłudze Azure AD

1. W innym oknie przeglądarki sieci Web Zaloguj się do usługi **BlueJeans dla witryny firmy Azure AD** jako administrator.

2. Wybierz pozycję **ADMIN (ADMINISTRATOR) \> GROUP SETTINGS (USTAWIENIA GRUPY) \> SECURITY (ZABEZPIECZENIA)** .

    ![Administratora](./media/bluejeans-tutorial/ic785868.png "Administratora")

3. W sekcji **SECURITY** (ZABEZPIECZENIA) wykonaj następujące czynności:

    ![Logowanie jednokrotne SAML](./media/bluejeans-tutorial/ic785869.png "Logowanie jednokrotne SAML")

    a. Wybierz pozycję **SAML Single Sign On** (Logowanie jednokrotne SAML).

    b. Wybierz pozycję **Enable automatic provisioning** (Włącz automatyczną aprowizację).

4. Przejdź dalej, wykonując następujące czynności:

    ![Ścieżka certyfikatu](./media/bluejeans-tutorial/ic785870.png "Ścieżka certyfikatu")

    a. Kliknij pozycję **Choose File** (Wybierz plik), aby przekazać certyfikat zakodowany w formacie base-64, pobrany wcześniej z witryny Azure Portal.

    b. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Password Change URL** (Adres URL zmiany hasła) wklej wartość **adresu URL zmiany hasła** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

5. Przejdź dalej, wykonując następujące czynności:

    ![Zapisz zmiany](./media/bluejeans-tutorial/ic785874.png "Zapisz zmiany")

    a. W polu tekstowym **Identyfikator użytkownika** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    d. Kliknij przycisk **SAVE CHANGES** (ZAPISZ ZMIANY).

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Tworzenie BlueJeans dla użytkownika testowego usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w BlueJeans dla usługi Azure AD. Usługa BlueJeans dla usługi Azure AD obsługuje automatyczne Inicjowanie obsługi użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](bluejeans-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do usługi **BlueJeans dla witryny firmy Azure AD** jako administrator.

2. Wybierz pozycję **ADMIN (ADMINISTRATOR) \> MANAGE USERS (ZARZĄDZAJ UŻYTKOWNIKAMI) \> ADD USER (DODAJ UŻYTKOWNIKA)** .

    ![Administratora](./media/bluejeans-tutorial/ic785877.png "Administratora")

    > [!IMPORTANT]
    > Karta **ADD USER** (DODAJ UŻYTKOWNIKA) jest dostępna tylko wtedy, gdy na **karcie SECUTIRY** (ZABEZPIECZENIA) nie zaznaczono pola wyboru **Enable automatic provisioning** (Włącz automatyczną aprowizację).

3. W sekcji **ADD USER** (DODAJ UŻYTKOWNIKA) wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/bluejeans-tutorial/ic785886.png "Dodaj użytkownika")

    a. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **B**.

    b. W polu tekstowym **Last name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    d. W polu tekstowym **Wybierz BlueJeans dla nazwy użytkownika usługi Azure AD** wprowadź nazwę użytkownika, na przykład **Brittasimon**

    d. W polu tekstowym **Create a Password** (Utwórz hasło) wprowadź hasło.

    e. W polu tekstowym **Company** (Firma) wprowadź nazwę Twojej firmy.

    f. W polu tekstowym **adres e-mail** wprowadź wiadomości e-mail użytkownika, takie jak `b.simon\@contoso.com`.

    g. W polu tekstowym **Utwórz BlueJeans dla spotkania usługi Azure AD** wpisz swój identyfikator spotkania.

    h. W polu tekstowym **Pick a Moderator Passcode** (Wybierz kod dostępu moderatora) wprowadź kod dostępu.

    i. Kliknij pozycję **CONTINUE** (KONTYNUUJ).

    ![Dodaj użytkownika](./media/bluejeans-tutorial/ic785887.png "Dodaj użytkownika")

    J. Kliknij przycisk **ADD USER** (DODAJ UŻYTKOWNIKA).

> [!NOTE]
> Możesz użyć innych BlueJeans dla narzędzi do tworzenia kont użytkowników usługi Azure AD lub interfejsów API udostępnianych przez usługę Azure AD do obsługi kont użytkowników usługi Azure AD.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka BlueJeans dla usługi Azure AD w panelu dostępu należy automatycznie zalogować się do BlueJeans usługi Azure AD, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę BlueJeans dla usługi Azure AD z usługą Azure AD](https://aad.portal.azure.com/)

