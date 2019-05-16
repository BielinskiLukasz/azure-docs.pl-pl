---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Flatter Files | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Flatter Files.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 48c5804df53c084715cac872aa431cf8694c6d35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65740285"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją Flatter Files

Z tego samouczka dowiesz się, jak zintegrować aplikację Flatter Files z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Flatter Files z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Flatter Files.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Flatter Files (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Flatter Files potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Flatter Files z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Flatter Files obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-flatter-files-from-the-gallery"></a>Dodawanie aplikacji Flatter Files z galerii

Aby skonfigurować integrację aplikacji Flatter Files w usłudze Azure AD, należy dodać aplikację Flatter Files z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Flatter Files z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Flatter Files**, wybierz pozycję **Flatter Files** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Flatter Files na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Flatter Files, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Flatter Files.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji Flatter Files, należy wykonać kroki opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Flatter Files](#configure-flatter-files-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Flatter Files](#create-flatter-files-test-user)** — aby mieć w aplikacji Flatter Files odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Flatter Files, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Flatter Files** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Flatter Files](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Flatter Files** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-flatter-files-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Flatter Files

1. Zaloguj się do aplikacji Flatter File jako administrator.

2. Kliknij pozycję **DASHBOARD** (Pulpit nawigacyjny). 
   
    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Kliknij pozycję **Settings** (Ustawienia), a następnie wykonaj następujące czynności na karcie **Company** (Firma): 
   
    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Wybierz pozycję **Use SAML 2.0 For Authentication** (Użyj protokołu SAML 2.0 na potrzeby uwierzytelniania).
    
    b. Kliknij pozycję **Skonfiguruj język SAML**.

4. W oknie dialogowym **SAML Configuration** (Konfiguracja SAML) wykonaj następujące kroki: 
   
    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. W polu tekstowym **Domain** (Domena) wpisz nazwę zarejestrowanej domeny.
   
   > [!NOTE]
   > Jeśli nie masz jeszcze zarejestrowanej domeny, skontaktuj się z zespołem pomocy technicznej aplikacji Flatter pod adresem [support@flatterfiles.com](mailto:support@flatterfiles.com). 
    
    b. W polu tekstowym **Identity Provider URL** (Adres URL dostawcy tożsamości) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
   
    c.  Otwórz certyfikat kodowany algorytmem base-64 w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat dostawcy tożsamości**.

    d. Kliknij przycisk **Aktualizuj**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Flatter Files.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Flatter Files**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Flatter Files**.

    ![Link do aplikacji Flatter Files na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-flatter-files-test-user"></a>Tworzenie użytkownika testowego aplikacji Flatter Files

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Flatter Files.

**Aby utworzyć użytkownika o nazwie Britta Simon w aplikacji Flatter Files, wykonaj następujące kroki:**

1. Zaloguj się w witrynie firmowej **Flatter Files** jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia), a następnie kartę **Users** (Użytkownicy).
   
    ![Tworzenie użytkownika aplikacji Flatter Files](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Kliknij przycisk **Dodaj użytkownika**. 

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
   
    ![Tworzenie użytkownika aplikacji Flatter Files](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. W polu tekstowym **First Name** (Imię) wpisz **Britta**.
   
    b. W polu tekstowym **Last Name** (Nazwisko) wpisz **Simon**. 
   
    c. W polu tekstowym **Email Address** (Adres e-mail) wpisz adres e-mail użytkownika Britta w witrynie Azure Portal.
   
    d. Kliknij przycisk **Submit** (Prześlij).   


### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Flatter Files w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Flatter Files, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

