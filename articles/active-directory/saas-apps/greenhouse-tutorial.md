---
title: 'Samouczek: integracja Azure Active Directory z szklarnią | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Greenhouse.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 78ec1766-4f79-4f16-9a66-d5584c4b6151
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: cd5a52ea47d7166c6f197b233d1a10ff063ff7c5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159179"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Samouczek: integracja Azure Active Directory z szklarnią

Z tego samouczka dowiesz się, jak zintegrować aplikację Greenhouse z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Greenhouse z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Greenhouse.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Greenhouse (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Greenhouse potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Greenhouse z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Greenhouse obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-greenhouse-from-the-gallery"></a>Dodawanie aplikacji Greenhouse z galerii

Aby skonfigurować integrację aplikacji Greenhouse z usługą Azure AD, należy dodać aplikację Greenhouse z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Greenhouse z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Greenhouse**, wybierz pozycję **Greenhouse** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Greenhouse na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Greenhouse, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Greenhouse.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD przy użyciu aplikacji Greenhouse, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Greenhouse](#configure-greenhouse-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Greenhouse](#create-greenhouse-test-user)** — aby w aplikacji Greenhouse utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z usługą Greenhouse, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Greenhouse** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji Greenhouse — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.greenhouse.io`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.greenhouse.io`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Greenhouse](https://www.greenhouse.io/contact) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji Greenhouse** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-greenhouse-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Greenhouse

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Greenhouse**, musisz wysłać pobrany **kod XML metadanych federacji** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej aplikacji Greenhouse](https://www.greenhouse.io/contact). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Greenhouse.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Greenhouse**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Greenhouse**.

    ![Link Greenhouse na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-greenhouse-test-user"></a>Tworzenie użytkownika testowego aplikacji Greenhouse

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Greenhouse, należy ich aprowizować w aplikacji Greenhouse. W przypadku aplikacji Greenhouse aprowizacja jest zadaniem ręcznym.

>[!NOTE]
>Aby aprowizować konta użytkowników usługi AAD, możesz użyć jakichkolwiek innych narzędzi tworzenia konta użytkownika aplikacji Greenhouse lub interfejsów API udostępnianych przez aplikację Greenhouse. 

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do swojej firmowej witryny aplikacji **Greenhouse** jako administrator.

2. W menu u góry kliknij pozycję **Configure** (Konfiguruj), a następnie kliknij pozycję **Users** (Użytkownicy).
   
    ![Użytkownicy](./media/greenhouse-tutorial/ic790791.png "Użytkownicy")

3. Kliknij pozycję **New Users** (Nowi użytkownicy).
   
    ![Nowy użytkownik](./media/greenhouse-tutorial/ic790792.png "Nowy użytkownik")

4. W sekcji **Add New User (Dodaj nowego użytkownika)** wykonaj następujące kroki:
   
    ![Dodaj nowego użytkownika](./media/greenhouse-tutorial/ic790793.png "Dodaj nowego użytkownika")

    a. W polu tekstowym **Enter user emails** (Wprowadź adresy e-mail użytkowników) wpisz adres e-mail prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

    b. Kliknij przycisk **Save** (Zapisz).    
   
      >[!NOTE]
      >Właściciele kont usługi Azure Active Directory otrzymają wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Greenhouse w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Greenhouse, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

