---
title: 'Samouczek: integracja Azure Active Directory z usługą TalentLMS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233360"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Samouczek: integracja Azure Active Directory z usługą TalentLMS

W tym samouczku dowiesz się, jak zintegrować usługę TalentLMS z usługą Azure Active Directory (Azure AD).
Integracja TalentLMS z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi TalentLMS.
* Możesz pozwolić użytkownikom na automatyczne logowanie do TalentLMS (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą TalentLMS, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w TalentLMS

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* TalentLMS obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-talentlms-from-the-gallery"></a>Dodawanie TalentLMS z galerii

Aby skonfigurować integrację programu TalentLMS z usługą Azure AD, musisz dodać TalentLMS z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać TalentLMS z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **TalentLMS**, wybierz pozycję **TalentLMS** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![TalentLMS na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą TalentLMS na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TalentLMS.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi TalentLMS, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-talentlms-single-sign-on)** jednokrotne w usłudze TalentLMS, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego TalentLMS](#create-talentlms-test-user)** , aby uzyskać odpowiednik Britta Simon w TalentLMS, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą TalentLMS, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **TalentLMS** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![TalentLMS domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.TalentLMSapp.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając rzeczywistego adresu URL logowania i identyfikatora. Skontaktuj się z [zespołem obsługi klienta TalentLMS](https://www.talentlms.com/contact) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie TalentLMS** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-talentlms-single-sign-on"></a>Konfigurowanie logowania jednokrotnego TalentLMS

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny TalentLMS jako administrator.

1. W sekcji **ustawienia & konta** kliknij kartę **Użytkownicy** .

    ![Ustawienia & konta](./media/talentlms-tutorial/IC777296.png "Ustawienia & konta")

1. Kliknij pozycję Logowanie jednokrotne **(SSO)** ,

1. W sekcji Single sign-on (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/talentlms-tutorial/IC777297.png "Logowanie jednokrotne")

    a. Z listy **Typ integracji z logowaniem jednokrotnym** wybierz pozycję **SAML 2,0**.

    b. W polu tekstowym **dostawca tożsamości (dostawcy tożsamości)** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal.

    c. Wklej wartość **odcisku palca** z Azure Portal do pola tekstowego **odcisku palca certyfikatu** .

    d.  W polu tekstowym **adres URL logowania zdalnego** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    e. W polu tekstowym **adres URL wylogowania zdalnego** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    f. Wypełnij następujące pola:

    * W polu tekstowym **TargetedID** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * W polu tekstowym **imię i nazwisko** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * W polu **tekstowym nazwisko** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * W polu tekstowym **adres e-mail** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi TalentLMS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **TalentLMS**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **TalentLMS**.

    ![Link TalentLMS na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-talentlms-test-user"></a>Utwórz użytkownika testowego TalentLMS

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze TalentLMS, muszą one być obsługiwane w usłudze TalentLMS. W przypadku TalentLMS, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do dzierżawy **TalentLMS** .

1. Kliknij pozycję **Użytkownicy**, a następnie kliknij pozycję **Dodaj użytkownika**.

1. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/talentlms-tutorial/IC777299.png "Dodaj użytkownika")  

    a. W polu tekstowym **First name (Imię)** wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.
 
    c. W polu tekstowym **adres e-mail** wprowadź wiadomości e-mail użytkownika, takie jak `brittasimon\@contoso.com`.

    d. Kliknij pozycję **Add User** (Dodaj użytkownika).

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników TalentLMS i interfejsów API udostępnionych przez usługę TalentLMS.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka TalentLMS w panelu dostępu należy automatycznie zalogować się do TalentLMS, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

