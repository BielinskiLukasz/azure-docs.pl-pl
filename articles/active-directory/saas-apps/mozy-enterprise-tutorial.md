---
title: 'Samouczek: integracja Azure Active Directory z usługą Mozy Enterprise | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 0f1aca75045f674fa45f5ad00c14a37c1abe7aea
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160415"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Samouczek: integracja Azure Active Directory z usługą Mozy Enterprise

W tym samouczku dowiesz się, jak zintegrować usługę Mozy Enterprise z usługą Azure Active Directory (Azure AD).
Integracja Mozy Enterprise z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do Mozy Enterprise.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Mozy Enterprise (Logowanie jednokrotne) przy użyciu swoich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Mozy Enterprise, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w przedsiębiorstwie Mozy

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Mozy Enterprise obsługuje zainicjowanie rejestracji jednokrotnej w programie **SP**

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Dodawanie Mozy przedsiębiorstwa z galerii

Aby skonfigurować integrację usługi Mozy Enterprise w usłudze Azure AD, musisz dodać Mozy Enterprise z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Mozy Enterprise z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Mozy Enterprise**, wybierz pozycję **Mozy Enterprise** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Mozy Enterprise na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Mozy Enterprise na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Mozy Enterprise.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Mozy Enterprise, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Konfigurowanie logowania jednokrotnego w **[przedsiębiorstwie Mozy](#configure-mozy-enterprise-single-sign-on)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Mozy Enterprise](#create-mozy-enterprise-test-user)** , aby dysponować odpowiednikiem Britta Simon w firmie Mozy Enterprise, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą usługi Mozy Enterprise, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **Mozy Enterprise** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Mozy domeny przedsiębiorstwa i adresy URL Logowanie jednokrotne](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej Mozy Enterprise Client](https://support.mozy.com/) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie programu Mozy Enterprise** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w przedsiębiorstwie Mozy

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmowej Mozy jako administrator.

2. W sekcji **Konfiguracja** kliknij pozycję **zasady uwierzytelniania**.
   
    ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777314.png "Zasady uwierzytelniania")

3. W sekcji **zasady uwierzytelniania** wykonaj następujące czynności:
   
    ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777315.png "Zasady uwierzytelniania")
   
    a. Wybierz pozycję **Usługa katalogowa** jako **dostawca**.
   
    b. Wybierz pozycję **Użyj protokołu LDAP push**.
   
    d. Kliknij kartę **SAML Authentication** (Uwierzytelnianie SAML).
   
    d. Wklej **adres URL logowania**, który został skopiowany z Azure Portal do pola tekstowego **adres URL uwierzytelniania** .
   
    e. Wklej **Identyfikator usługi Azure AD**, który został skopiowany z Azure Portal do pola tekstowego **punktu końcowego SAML** .
   
    f. Otwórz pobrany certyfikat szyfrowanego Base-64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej cały certyfikat do pola tekstowego **certyfikatu SAML** .
   
    g. Wybierz pozycję **Włącz logowanie jednokrotne dla administratorów, aby zalogować się przy użyciu swoich poświadczeń sieciowych**.
   
    h. Kliknij przycisk **Save Changes** (Zapisz zmiany).

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do przedsiębiorstwa Mozy Enterprise.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Mozy Enterprise**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Mozy Enterprise**.

    ![Link Mozy Enterprise na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-mozy-enterprise-test-user"></a>Utwórz użytkownika testowego Mozy Enterprise

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Mozy Enterprise, muszą one być obsługiwane w usłudze Mozy Enterprise. W przypadku Mozy Enterprise Inicjowanie obsługi jest zadaniem ręcznym.

>[!NOTE]
>Możesz użyć innych narzędzi do tworzenia kont użytkowników przedsiębiorstwa Mozy lub interfejsów API dostarczonych przez Mozy Enterprise do udostępniania kont użytkowników usługi AAD.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do dzierżawy **przedsiębiorstwa Mozy** .

2. Kliknij pozycję **Użytkownicy**, a następnie kliknij pozycję **Dodaj nowego użytkownika**.
   
    ![Użytkownicy](./media/mozy-enterprise-tutorial/ic777317.png "Użytkownicy")
   
    >[!NOTE]
    >Opcja **Dodaj nowy użytkownik** jest wyświetlana tylko wtedy, gdy wybrano **Mozy** jako dostawcę w obszarze **zasady uwierzytelniania**. W przypadku skonfigurowania uwierzytelniania przy użyciu protokołu SAML użytkownicy są dodawani automatycznie przy pierwszym logowaniu za pomocą logowania jednokrotnego.
    
3. W oknie dialogowym Nowy użytkownik wykonaj następujące czynności:
   
    ![Dodawanie użytkowników](./media/mozy-enterprise-tutorial/ic777318.png "Dodawanie użytkowników")
   
    a. Z listy **Wybierz grupę** wybierz grupę.
   
    b. Z listy **Typ użytkownika** wybierz typ.
   
    d. W polu tekstowym **username** wpisz nazwę użytkownika usługi Azure AD.
   
    d. W polu tekstowym **adres e-mail** wpisz adres e-mail użytkownika usługi Azure AD.
   
    e. Wybierz opcję **Wyślij wiadomość e-mail dotyczącą instrukcji użytkownika**.
   
    f. Kliknij pozycję **Dodaj użytkowników**.

     >[!NOTE]
     > Po utworzeniu użytkownika wiadomość e-mail zostanie wysłana do użytkownika usługi Azure AD, który zawiera link umożliwiający potwierdzenie konta przed jego rozpoczęciem.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Mozy Enterprise w panelu dostępu należy automatycznie zalogować się do Mozy przedsiębiorstwa, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

