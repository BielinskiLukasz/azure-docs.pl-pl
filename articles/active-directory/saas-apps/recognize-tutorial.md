---
title: 'Samouczek: Integracja Azure Active Directory z rozpoznawaniem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a rozpoznawaniem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943341"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Samouczek: Integracja Azure Active Directory z rozpoznawaniem

W tym samouczku dowiesz się, jak zintegrować program z usługą Azure Active Directory (Azure AD).
Integracja z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do rozpoznawania.
* Możesz włączyć automatyczne logowanie użytkowników, aby rozpoznawać (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu rozpoznawania, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Rozpoznaj subskrypcję obsługującą Logowanie jednokrotne

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozpoznawanie obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**

## <a name="adding-recognize-from-the-gallery"></a>Dodawanie rozpoznawania z galerii

Aby skonfigurować integrację rozpoznawania z usługą Azure AD, należy dodać rozpoznawanie z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać rozpoznawanie z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg,wybierz opcję Rozpoznaj z poziomu panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Rozpoznawanie na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą rozpoznawania na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach rozpoznawania.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu rozpoznawania, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie rozpoznawania logowania](#configure-recognize-single-sign-on)** jednokrotnego — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie rozpoznawania użytkownika testowego](#create-recognize-test-user)** — Aby uzyskać odpowiednik Britta Simon w rozpoznawaniu, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu rozpoznawania, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie Rozpoznaj integrację aplikacji wybierz pozycję **Logowanie**jednokrotne.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    >[!NOTE]
    >**Plik metadanych dostawcy usług** zostanie pobrany z sekcji **Konfigurowanie rozpoznawania logowania** jednokrotnego w samouczku.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartość **identyfikatora** zostanie wypełniona automatycznie w sekcji Podstawowa konfiguracja SAML.

    ![Rozpoznawanie informacji o rejestracji jednokrotnej w domenach i adresach URL](common/sp-identifier.png)

     W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Jeśli wartość **identyfikatora** nie zostanie wypełniona automatycznie, otrzymasz wartość identyfikatora, otwierając adres URL metadanych dostawcy usług z sekcji Ustawienia logowania jednokrotnego, która została omówiona w dalszej części sekcji **Konfigurowanie rozpoznawania logowania** jednokrotnego w temacie. Ręczny. Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta](mailto:support@recognizeapp.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfiguracja Rozpoznaj** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-recognize-single-sign-on"></a>Konfigurowanie rozpoznawania logowania jednokrotnego

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej dzierżawy jako administrator.

2. W prawym górnym rogu kliknij **menu**. Przejdź do pozycji **administrator firmy**.
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_000.png)

3. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Wykonaj następujące kroki w sekcji **Ustawienia logowania** jednokrotnego.
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Jako **włączenie logowania**jednokrotnego wybierz pozycję **włączone**.

    b. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , który został skopiowany z Azure Portal.
    
    c. W polu tekstowym **adres URL elementu docelowego logowania** jednokrotnego wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.
    
    d. W polu tekstowym **adres URL celu SLO** wklej wartość **adresu URL wylogowywania** skopiowanego z Azure Portal. 
    
    e. Otwórz pobrany plik **certyfikatu (base64)** w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **certyfikatu** .
    
    f. Kliknij przycisk **Zapisz ustawienia** . 

5. W sekcji **Ustawienia rejestracji** jednokrotnej Skopiuj adres URL w polu **adres URL metadanych dostawcy usług**.
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Otwórz **link adresu URL metadanych** w pustej przeglądarce, aby pobrać dokument metadanych. Następnie skopiuj wartość elementem EntityDescriptor (entityID) z pliku i wklej ją w polu tekstowym **Identyfikator** w **podstawowej konfiguracji SAML** na Azure Portal.
    
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do rozpoznawania.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję Rozpoznaj.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Rozpoznaj**.

    ![Link rozpoznawania na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-recognize-test-user"></a>Utwórz użytkownika testowego rozpoznawania

Aby umożliwić użytkownikom usługi Azure AD logowanie się w rozpoznawaniu, muszą one być obsługiwane do rozpoznawania. W przypadku rozpoznawania, Inicjowanie obsługi jest zadaniem ręcznym.

Ta aplikacja nie obsługuje aprowizacji Standard scim, ale ma alternatywną synchronizację użytkowników, która inicjuje obsługę użytkowników. 

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do strony firmy rozpoznawania jako administrator.

2. W prawym górnym rogu kliknij **menu**. Przejdź do pozycji **administrator firmy**.

3. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).

4. Wykonaj następujące kroki w sekcji **Synchronizacja użytkownika** .
   
    ![Nowy użytkownik](./media/recognize-tutorial/tutorial_recognize_005.png "Nowy użytkownik")
   
    a. Gdy **Synchronizacja jest włączona**, wybierz pozycję **włączone**.
   
    b. Jako **Wybierz dostawcę synchronizacji**wybierz pozycję **Microsoft/Office 365**.
   
    c. Kliknij pozycję **Uruchom synchronizację użytkowników**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Rozpoznaj w panelu dostępu należy automatycznie zalogować się do rozpoznania, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

