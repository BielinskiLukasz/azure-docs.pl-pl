---
title: 'Samouczek: integracja Azure Active Directory z Cisco parasol | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Cisco Umbrella.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee91ca53e32bfdc387dc20054493d02d506a75da
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158629"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Samouczek: integracja Azure Active Directory z Cisco parasol

Z tego samouczka dowiesz się, jak zintegrować aplikację Cisco Umbrella z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Cisco Umbrella z usługą Azure AD oferuje następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Cisco Umbrella.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Cisco Umbrella (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Cisco Umbrella potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Cisco Umbrella z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Cisco Umbrella obsługuje logowanie jednokrotne inicjowane za pomocą **SP oraz IDP**.

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Dodawanie aplikacji Cisco Umbrella z galerii

Aby skonfigurować integrację aplikacji Cisco Umbrella z usługą Azure AD, musisz dodać aplikację Cisco Umbrella z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Cisco Umbrella z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **Cisco Umbrella**, wybierz pozycję **Cisco Umbrella** w panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Cisco Umbrella na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją [Application name] w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji [Application name].

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją [Application name], należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Cisco Umbrella](#configure-cisco-umbrella-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Cisco Umbrella](#create-cisco-umbrella-test-user)** — aby mieć w aplikacji Cisco Umbrella odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją [Application name], wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Cisco Umbrella** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Domena i adresy URL aplikacji Cisco Umbrella — informacje o logowaniu jednokrotnym](common/both-preintegrated-signon.png)

    a. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **SP**, wykonaj następujące kroki:

    b. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://login.umbrella.com/sso`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Skonfiguruj aplikację Cisco Umbrella** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-cisco-umbrella-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Cisco Umbrella

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji Cisco Umbrella jako administrator.

2. W menu po lewej stronie kliknij pozycję **Administrator** i przejdź do obszaru **Uwierzytelnianie**, a następnie kliknij pozycję **SAML**.

    ![Administrator](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Wybierz pozycję **Inne** i kliknij przycisk **DALEJ**.

    ![Inne](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Na stronie **metadanych aplikacji Cisco Umbrella** kliknij przycisk **DALEJ**.

    ![Metadane](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Na karcie **Przekazywanie metadanych**, jeśli wstępnie skonfigurowano język SAML, wybierz opcję **Kliknij tutaj, aby je zmienić** i wykonaj poniższe kroki.

    ![Dalej](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. W **opcji A: przekazywanie pliku**XML, Przekaż plik **XML metadanych federacji** pobrany z Azure Portal i po przekazaniu metadanych poniższe wartości są automatycznie wypełniane, a następnie kliknij przycisk **dalej**.

    ![Wybieranie pliku](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. W sekcji **Walidowanie konfiguracji języka SAML** kliknij pozycję **TESTUJ KONFIGURACJĘ JĘZYKA SAML**.

    ![Test](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Kliknij przycisk **SAVE** (Zapisz).

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

W tej sekcji włączysz możliwość logowania jednokrotnego na platformie Azure dla użytkownika, udzielając dostępu do aplikacji Cisco Umbrella.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Cisco Umbrella**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz ciąg **Cisco Umbrella**.

    ![Link do aplikacji Cisco Umbrella na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-cisco-umbrella-test-user"></a>Tworzenie użytkownika testowego aplikacji Cisco Umbrella

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Cisco Umbrella, trzeba ich aprowizować w aplikacji Cisco Umbrella.  
W przypadku aplikacji Cisco Umbrella aprowizowanie to zadanie wykonywane ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji Cisco Umbrella jako administrator.

2. W menu po lewej stronie kliknij pozycję **Administrator** i przejdź do pozycji **Konta**.

    ![Konta](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Na stronie **Konta** kliknij przycisk **Dodaj** w prawej górnej części strony i wykonaj poniższe kroki.

    ![Użytkownik](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. W polu **Imię** wprowadź imię, takie jak **Britta**.

    b. W polu **Nazwisko** wprowadź nazwisko, takie jak **simon**.

    d. Z listy **Wybierz rolę administratora delegowanego** wybierz swoją rolę.
  
    d. W polu **adres e-mail** wprowadź EmailAddress użytkownika, na przykład **brittasimon\@contoso.com**.

    e. W polu **Hasło** wprowadź hasło.

    f. W polu **Potwierdź hasło** ponownie wprowadź hasło.

    g. Kliknij przycisk **UTWÓRZ**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Cisco Umbrella w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Cisco Umbrella, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
