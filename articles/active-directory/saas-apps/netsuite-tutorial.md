---
title: 'Samouczek: integracja usługi Azure Active Directory z aplikacją NetSuite | Microsoft Docs'
description: Dowiedz się, jak można skonfigurować funkcję logowania jednokrotnego między usługą Azure Active Directory i aplikacją NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58767438e7a1faf7e1e783fc95a33a35ece3774a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202625"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją NetSuite

Z tego samouczka dowiesz się, jak można zintegrować aplikację NetSuite z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji NetSuite z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji NetSuite.
* Możesz zezwolić użytkownikom na automatyczne logowanie się do aplikacji NetSuite (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją NetSuite, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji NetSuite z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja NetSuite obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**
* Aplikacja NetSuite obsługuje aprowizację użytkowników typu **Just In Time**
* Aplikacja NetSuite obsługuje [zautomatyzowaną aprowizację użytkowników](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Dodawanie aplikacji NetSuite z galerii

Aby skonfigurować integrację aplikacji NetSuite w usłudze Azure AD, należy dodać aplikację NetSuite z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację NetSuite z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **NetSuite**, w panelu wyników wybierz pozycję **NetSuite**, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja NetSuite na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz funkcję logowania jednokrotnego usługi Azure AD z aplikacją NetSuite, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w aplikacji NetSuite.

Aby skonfigurować i przetestować funkcję logowania jednokrotnego usługi Azure AD z aplikacją NetSuite, należy zrealizować następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji NetSuite](#configure-netsuite-single-sign-on)** — aby skonfigurować ustawienia funkcji logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji NetSuite](#create-netsuite-test-user)** — aby w aplikacji NetSuite znajdował się odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować funkcję logowania jednokrotnego usługi Azure AD z aplikacją NetSuite, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **NetSuite** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Dane logowania jednokrotnego dotyczące domeny i adresów URL aplikacji NetSuite](common/idp-reply.png)

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja NetSuite oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:
    
    | Name (Nazwa) | Atrybut źródłowy | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

    >[!NOTE]
    >Wartość atrybutu konta nie jest prawdziwa. Zaktualizujesz tę wartość, co zostanie wyjaśnione w dalszej części tego samouczka.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie usługi NetSuite** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-netsuite-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji NetSuite

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmowej w aplikacji NetSuite jako administrator.

2. Na pasku narzędzi w górnej części strony kliknij opcję  **Setup** (Konfiguracja), a następnie przejdź do pozycji  **Company**  (Firma) i kliknij przycisk  **Enable Features** (Włączanie funkcji).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na pasku narzędzi w środkowej części strony kliknij opcję  **SuiteCloud**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-suitecloud.png)

4. W sekcji  **Manage Authentication**  zaznacz opcję  **SAML SINGLE SIGN-ON**  (LOGOWANIE JEDNOKROTNE SAML), aby włączyć opcję LOGOWANIE JEDNOKROTNE SAML w aplikacji NetSuite.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na pasku narzędzi w górnej części strony kliknij opcję  **Setup** (Konfiguracja).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

6. Na liście **SETUP TASKS** (ZADANIA KONFIGURACJI) kliknij opcję **Integration** (Integracja).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-integration.png)

7. W sekcji **MANAGE AUTHENTICATION** (ZARZĄDZANIE UWIERZYTELNIANIEM) kliknij opcję **SAML Single Sign-on** (Logowanie jednokrotne SAML).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml.png)

8. Na stronie **SAML Setup** (Konfigurowanie protokołu SAML) w sekcji **NetSuite Configuration** (Konfiguracja NetSuite) wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Wybierz opcję **PRIMARY AUTHENTICATION METHOD** (PODSTAWOWA METODA UWIERZYTELNIANIA).

    b. W polu oznaczonym jako **SAMLV2 IDENTITY PROVIDER METADATA** (METADANE DOSTAWCY TOŻSAMOŚCI SAMLV2) wybierz opcję **UPLOAD IDP METADATA FILE** (PRZEKAŻ PLIK METADANYCH DOSTAWCY TOŻSAMOŚCI). Następnie kliknij przycisk **Browse** (Przeglądaj), aby przekazać plik metadanych, który został pobrany z witryny Azure Portal.

    d. Kliknij przycisk **Prześlij**.

9. W aplikacji NetSuite kliknij pozycję **Setup** (Konfiguracja), następnie przejdź do pozycji **Company** (Firma) i kliknij przycisk **Company Information** (Informacje o firmie) w górnym menu nawigacji.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-account-id.png)

    b. Ze strony **Company Information** (Informacje o firmie) skopiuj wartość **ACCOUNT ID** (IDENTYFIKATOR KONTA), znajdującą się w prawej kolumnie.

    d. Wklej wartość **Identyfikator konta** skopiowaną z konta aplikacji NetSuite w polu **Wartość atrybutu** w usłudze Azure AD. 

10. Zanim użytkownicy będą mogli wykonywać logowanie jednokrotne do aplikacji NetSuite, należy im wcześniej przypisać odpowiednie uprawnienia w tej aplikacji. Postępuj zgodnie z poniższymi instrukcjami, aby przypisać te uprawnienia.

    a. W górnym menu nawigacyjnym kliknij opcję **Setup** (Konfiguracja).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    b. W menu nawigacji po lewej stronie wybierz opcję **Users/Roles** (Użytkownicy/Role), a następnie kliknij przycisk **Manage Roles** (Zarządzaj rolami).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-roles.png)

    d. Kliknij opcję **New Role** (Nowa rola).

    d. Wpisz nazwę nowej roli w polu **Name**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kliknij pozycję **Zapisz**.

    f. W menu u góry kliknij pozycję **Permissions** (Uprawnienia). Następnie kliknij pozycję **Setup** (Konfiguracja).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-sso.png)

    g. Wybierz opcję **SAML Single Sign-on** (Logowanie jednokrotne SAML), a następnie kliknij przycisk **Add** (Dodaj).

    h. Kliknij pozycję **Zapisz**.

    i. W górnym menu nawigacyjnym kliknij pozycję **Setup** (Konfiguracja), następnie kliknij pozycję **Setup Manager** (Menedżer instalacji).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    j. W menu nawigacji po lewej stronie wybierz opcję **Users/Roles** (Użytkownicy/Role), a następnie kliknij pozycję **Manage Users** (Zarządzaj użytkownikami).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Wybierz użytkownika testowego. Następnie kliknij przycisk **Edit** (Edytuj) i przejdź do karty **Access** (Dostęp).

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-edit-user.png)

    l. W oknie dialogowym Roles (Role) przypisz odpowiednią rolę, która została przez Ciebie utworzone.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kliknij pozycję **Zapisz**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji NetSuite.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **NetSuite**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz **NetSuite** i wybierz odpowiednią pozycję.

    ![Link NetSuite na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-netsuite-test-user"></a>Tworzenie użytkownika testowego aplikacji NetSuite

W tej sekcji w aplikacji NetSuite tworzony jest użytkownik o nazwie Britta Simon. Aplikacja NetSuite obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji NetSuite, zostanie utworzony po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka NetSuite w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji NetSuite, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](NetSuite-provisioning-tutorial.md)

