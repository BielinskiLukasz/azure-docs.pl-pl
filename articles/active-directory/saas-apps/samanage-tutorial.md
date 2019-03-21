---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Samanage | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b00e27a0620212fbc09f8b7d91d8f5b9d0971c8f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885236"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Samanage

Z tego samouczka dowiesz się, jak zintegrować usługę Samanage z usługą Azure Active Directory (Azure AD).
Zintegrowanie usługi Samanage z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi Samanage.
* Możesz zezwolić użytkownikom na automatyczne logowanie się do usługi Samanage (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Samanage, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi Samanage z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Samanage obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-samanage-from-the-gallery"></a>Dodawanie usługi Samanage z galerii

Aby skonfigurować integrację usługi Samanage z usługą Azure AD, musisz dodać usługę Samanage z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać usługę Samanage z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Samanage**, wybierz pozycję **Samanage** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Samanage na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z usługą Samanage, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi Samanage.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usłudze Samanage, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usłudze Samanage](#configure-samanage-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego usługi Samanage](#create-samanage-test-user)** — aby mieć w usłudze Samanage odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w usłudze Samanage, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji oprogramowania **Samanage** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL usługi Samanage — informacje o logowaniu jednokrotnym](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania, co zostało opisane w dalszej części tego samouczka. Aby uzyskać więcej informacji, skontaktuj się z [zespołem pomocy technicznej klienta usługi Samanage](https://www.samanage.com/support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie usługi Samanage** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-samanage-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze Samanage

1. W innym oknie przeglądarki internetowej zaloguj się do swojej firmowej witryny usługi Samanage jako administrator.

2. Kliknij pozycję **Dashboard** (Pulpit nawigacyjny) i wybierz pozycję **Setup** (Konfigurowanie) w lewym okienku nawigacji.
   
    ![Pulpit nawigacyjny](./media/samanage-tutorial/tutorial_samanage_001.png "Pulpit nawigacyjny")

3. Kliknij pozycję **Single Sign-On** (Logowanie jednokrotne).
   
    ![Logowanie jednokrotne](./media/samanage-tutorial/tutorial_samanage_002.png "Logowanie jednokrotne")

4. Przejdź do sekcji **Login using SAML** (Logowanie za pośrednictwem protokołu SAML) i wykonaj następujące kroki:
   
    ![Logowanie za pośrednictwem protokołu SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Logowanie za pośrednictwem protokołu SAML")
 
    a. Kliknij pozycję **Enable Single Sign-On with SAML** (Włącz logowanie jednokrotne z użyciem protokołu SAML).  
 
    b. W polu tekstowym **Identity Provider URL** (Adres URL dostawcy tożsamości) wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.    
 
    c. Upewnij się, że wartość pola **Login URL** (Adres URL logowania) odpowiada **adresowi URL logowania** z sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.
 
    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wprowadź wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.
 
    e. W polu tekstowym **SAML Issuer** (Wystawca SAML) wpisz identyfikator URI aplikacji ustawiony u dostawcy tożsamości.
 
    f. Otwórz w Notatniku certyfikat zakodowany w formacie Base-64 pobrany z witryny Azure Portal, skopiuj zawartość do Schowka, a następnie wklej ją w polu tekstowym **Paste your Identity Provider x.509 Certificate below** (Wklej poniżej swój certyfikat x.509 dostawcy tożsamości).
 
    g. Kliknij pole wyboru **Create users if they do not exist in Samanage** (Utwórz użytkowników, jeśli nie istnieją w usłudze Samanage).
 
    h. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi Samanage.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie wybierz pozycję **Samanage**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Samanage**.

    ![Link usługi Samanage na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-samanage-test-user"></a>Tworzenie użytkownika testowego usługi Samanage

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Samanage, muszą być oni aprowizowaniu w usłudze Samanage.  
W przypadku usługi Samanage aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do swojej firmowej witryny usługi Samanage jako administrator.

2. Kliknij pozycję **Dashboard** (Pulpit nawigacyjny) i wybierz pozycję **Setup** (Konfigurowanie) w lewym okienku nawigacji.
   
    ![Konfigurowanie](./media/samanage-tutorial/tutorial_samanage_001.png "Konfigurowanie")

3. Kliknij kartę **Users** (Użytkownicy).
   
    ![Użytkownicy](./media/samanage-tutorial/tutorial_samanage_006.png "Użytkownicy")

4. Kliknij pozycję **New User** (Nowy użytkownik).
   
    ![Nowy użytkownik](./media/samanage-tutorial/tutorial_samanage_007.png "Nowy użytkownik")

5. W polach **Name** (Nazwa) i **Email Address** (Adres e-mail) wpisz nazwę i adres e-mail konta usługi Azure Active Directory, które chcesz aprowizować, a następnie kliknij przycisk **Create User** (Utwórz użytkownika).
   
    ![Utwórz użytkownika](./media/samanage-tutorial/tutorial_samanage_008.png "Utwórz użytkownika")
   
   >[!NOTE]
   >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne. Do aprowizowania kont użytkowników usługi Azure Active Directory możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia kont użytkowników usługi Samanage oferowanych przez tę usługę.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Samanage na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi Samanage, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

