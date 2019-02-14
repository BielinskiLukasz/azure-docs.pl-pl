---
title: 'Samouczek: Integracja usługi Azure Active Directory z portalem Mimecast Personal Portal | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a portalem Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cce0543ac47b1abc4a37ca06a0ff73f6ad90999e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189008"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Samouczek: Integracja usługi Azure Active Directory z portalem Mimecast Personal Portal

Z tego samouczka dowiesz się, jak zintegrować portal Mimecast Personal Portal z usługą Azure Active Directory (Azure AD).
Zintegrowanie portalu Mimecast Personal Portal z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania dostępu do portalu Mimecast Personal Portal za pomocą usługi Azure AD.
* Możliwość skonfigurowania automatycznego logowania użytkowników do portalu Mimecast Personal Portal (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z portalem Mimecast Personal Portal, są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja portalu Mimecast Personal Portal z obsługą jednokrotnego logowania

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Portal Mimecast Personal Portal obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Dodawanie portalu Mimecast Personal Portal z galerii

Aby skonfigurować integrację portalu Mimecast Personal Portal z usługą Azure AD, należy dodać portal Mimecast Personal Portal z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać portal Mimecast Personal Portal z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Mimecast Personal Portal**, wybierz pozycję **Mimecast Personal Portal** na panelu, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Portal Mimecast Personal Portal na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z portalem Mimecast Personal Portal, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem portalu Mimecast Personal Portal.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z portalem Mimecast Personal Portal, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w portalu Mimecast Personal Portal](#configure-mimecast-personal-portal-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego portalu Mimecast Personal Portal](#create-mimecast-personal-portal-test-user)** — aby mieć w portalu Mimecast Personal Portal odpowiednik użytkownika Britta Simon, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą portalu Mimecast Personal Portal, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Mimecast Personal Portal** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego portalu Mimecast Personal Portal](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL: 

    | Region  |  Wartość | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/login/saml`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | Region  |  Wartość | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australia       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: 

    | Region  |  Wartość | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/login/saml`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego identyfikatora. Skontaktuj się z [zespołem pomocy technicznej klienta portalu Mimecast Personal Portal](https://www.mimecast.com/customer-success/technical-support/), aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie portalu Mimecast Personal Portal** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Konfigurowanie logowania jednokrotnego portalu Mimecast Personal Portal

1. W innym oknie przeglądarki internetowej zaloguj się do portalu Mimecast Personal Portal jako administrator.

2. Przejdź do strony **Usługi \> Aplikacje**.
   
    ![Aplikacje](./media/mimecast-personal-portal-tutorial/ic794998.png "Aplikacje")

3. Kliknij pozycję **Profile uwierzytelniania**.
   
    ![Profile uwierzytelniania](./media/mimecast-personal-portal-tutorial/ic794999.png "Profile uwierzytelniania")

4. Kliknij pozycję **Nowy profil uwierzytelniania**.
   
    ![Nowy profil uwierzytelniania](./media/mimecast-personal-portal-tutorial/ic795000.png "Nowy profil uwierzytelniania")

5. W sekcji **Profil uwierzytelniania** wykonaj następujące kroki:
   
    ![Profil uwierzytelniania](./media/mimecast-personal-portal-tutorial/ic795001.png "Profil uwierzytelniania")
   
    a. W polu tekstowym **Opis** wpisz nazwę konfiguracji.
   
    b. Wybierz pozycję **Wymuś uwierzytelnianie SAML dla portalu Mimecast Personal Portal**.
   
    d. W polu **Dostawca** wybierz opcję **Azure Active Directory**.
   
    d. W polu tekstowym **Adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.
   
    e. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
   
    f. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    g. Otwórz w Notatniku swój zakodowany w formacie **base-64** certyfikat pobrany z witryny Azure Portal, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat dostawcy tożsamości (metadane)**.

    h. Wybierz pozycję **Zezwalaj na logowanie jednokrotne**.
   
    i. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do portalu Mimecast Personal Portal.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Mimecast Personal Portal**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz **Mimecast Personal Portal** i wybierz odpowiednią pozycję.

    ![Link portalu Mimecast Personal Portal na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-mimecast-personal-portal-test-user"></a>Tworzenie użytkownika testowego portalu Mimecast Personal Portal

Aby umożliwić użytkownikom usługi Azure AD logowanie do portalu Mimecast Personal Portal, należy ich aprowizować w portalu Mimecast Personal Portal. W przypadku portalu Mimecast Personal Portal aprowizowanie to zadanie wykonywane ręczne.

Należy zarejestrować domenę, aby można było utworzyć użytkowników.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do portalu **Mimecast Personal Portal** jako administrator.

2. Przejdź do pozycji **Directories (Katalogi) \> Internal (Wewnętrzny)**.
   
    ![Katalogi](./media/mimecast-personal-portal-tutorial/ic795003.png "Katalogi")

3. Kliknij pozycję **Register New Domain** (Rejestruj nową domenę).
   
    ![Rejestruj nową domenę](./media/mimecast-personal-portal-tutorial/ic795004.png "Rejestruj nową domenę")

4. Po utworzeniu nowej domeny kliknij pozycję **New Address** (Nowy adres).
   
    ![Nowy adres](./media/mimecast-personal-portal-tutorial/ic795005.png "Nowy adres")

5. W oknie dialogowym Nowy adres wykonaj następujące kroki dla prawidłowego konta usługi Azure AD, które chcesz aprowizować:
   
    ![Zapisz](./media/mimecast-personal-portal-tutorial/ic795006.png "Zapisz")
   
    a. W polu tekstowym **Adres e-mail** wpisz **adres e-mail** użytkownika, na przykład **BrittaSimon@contoso.com**.
    
    b. W polu tekstowym **Nazwa globalna** wpisz **nazwę użytkownika**, na przykład **BrittaSimon**.

    d. W polach tekstowych **Hasło** i **Potwierdź hasło** wpisz **hasło** użytkownika.
   
    b. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Do aprowizowania kont użytkowników usługi Azure AD można użyć dowolnych innych narzędzi do tworzenia kont użytkowników portalu Mimecast Personal Portal udostępnianych przez ten portal.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Mimecast Personal Portal na panelu dostępu powinno nastąpić automatyczne zalogowanie do portalu Mimecast Personal Portal, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

