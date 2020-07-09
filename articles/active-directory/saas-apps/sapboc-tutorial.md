---
title: 'Samouczek: integracja Azure Active Directory z chmurą SAP Analytics Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i chmurą SAP Analytics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbe6aed8ae0403f6ab4a7f9e0ba8b9a2c5d61617
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86168562"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Samouczek: integracja chmury SAP Analytics z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować chmurę z usługą SAP Analytics z usługą Azure Active Directory (Azure AD). Gdy integrujesz chmurę SAP Analytics z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do chmury SAP Analytics.
* Zezwól użytkownikom na automatyczne logowanie do chmury SAP Analytics przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne w chmurze usługi SAP Analytics.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SAP Analytics w chmurze obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Dodawanie chmury SAP Analytics z galerii

Aby skonfigurować integrację chmury SAP Analytics z usługą Azure AD, musisz dodać chmurę analizy SAP z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **chmura analizy SAP** .
1. Wybierz pozycję **chmura analizy SAP** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą chmury SAP Analytics przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze analizy SAP.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą chmury SAP Analytics, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj usługę SAP Analytics — logowanie JEDNOkrotne w chmurze](#configure-sap-analytics-cloud-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Tworzenie użytkownika testowego chmury SAP Analytics](#create-sap-analytics-cloud-test-user)** — aby dysponować odpowiednikiem B. Simon w chmurze analizy SAP, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji w **chmurze SAP Analytics** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Wartości tych adresów URL są tylko na potrzeby demonstracyjne. Zaktualizuj wartości rzeczywistym adresem URL logowania i adresem URL identyfikatora. Aby uzyskać adres URL logowania, skontaktuj się z [zespołem pomocy technicznej usługi SAP Analytics Cloud Client](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Możesz uzyskać adres URL identyfikatora, pobierając metadane chmury SAP Analytics z konsoli administracyjnej. Jest to wyjaśnione w dalszej części tego samouczka.

4. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie chmury SAP Analytics** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Konfigurowanie logowania jednokrotnego w chmurze SAP Analytics

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny usługi SAP Analytics w chmurze jako administrator.

2. Wybierz pozycję **menu**  >  **System**  >  **Administracja**systemu.
    
    ![Wybierz pozycję Menu (Menu), wybierz pozycję System (System), a następnie wybierz pozycję Administration (Administracja)](./media/sapboc-tutorial/config1.png)

3. Na karcie **Security** (Zabezpieczenia) wybierz ikonę **edycji** (ikona ołówka).
    
    ![Na karcie Security (Zabezpieczenia) wybierz ikonę edycji (ikona ołówka)](./media/sapboc-tutorial/config2.png)  

4. W obszarze **Authentication Method** (Metoda uwierzytelniania) wybierz pozycję **SAML Single Sign-On (SSO)** (Logowanie jednokrotne protokołu SAML).

    ![Wybierz pozycję SAML Single Sign-On (SSO) (Logowanie jednokrotne protokołu SAML) jako metodę uwierzytelniania](./media/sapboc-tutorial/config3.png)  

5. Aby pobrać metadane dostawcy usług (Krok 1.), wybierz pozycję **Download** (Pobierz). W pliku metadanych znajdź i skopiuj wartość elementu **entityID**. W witrynie Azure Portal w oknie dialogowym **Podstawowa konfiguracja protokołu SAML** wklej wartość w polu **Identyfikator**.

    ![Skopiuj i wklej wartość elementu entityID](./media/sapboc-tutorial/config4.png)  

6. Aby przekazać metadane dostawcy usług (Krok 2.) do pliku, który został pobrany z witryny Azure Portal, w obszarze **Upload Identity Provider metadata** (Przekazywanie metadanych dostawcy tożsamości) wybierz pozycję **Upload** (Przekaż).  

    ![W obszarze Upload Identity Provider metadata (Przekazywanie metadanych dostawcy tożsamości) wybierz pozycję Upload (Przekaż)](./media/sapboc-tutorial/config5.png)

7. Z listy **User Attribute** (Atrybut użytkownika) wybierz atrybut użytkownika (Krok 3.), którego chcesz użyć na potrzeby implementacji. Ten atrybut użytkownika jest mapowany na dostawcę tożsamości. Aby wprowadzić atrybut niestandardowy na stronie użytkownika, użyj opcji **Custom SAML Mapping** (Niestandardowe mapowanie protokołu SAML). Możesz również wybrać wartość **Email** (Adres e-mail) lub **USER ID** (Identyfikator użytkownika) jako atrybut użytkownika. W tym przykładzie wybraliśmy wartość **Email** (Adres e-mail), ponieważ oświadczenie identyfikatora użytkownika zamapowano z atrybutem **userprincipalname** w sekcji **Atrybuty i oświadczenia użytkownika** w witrynie Azure Portal. Zapewnia to unikatowy adres e-mail użytkownika, który jest wysyłany do aplikacji w chmurze SAP Analytics przy każdej pomyślnej odpowiedzi SAML.

    ![Wybierz atrybut użytkownika](./media/sapboc-tutorial/config6.png)

8. Aby zweryfikować konto za pomocą dostawcy tożsamości (Krok 4.) w polu **Login Credential (Email)** (Poświadczenia danych logowania — adres e-mail) wprowadź adres e-mail użytkownika. Następnie wybierz pozycję **Verify Account** (Sprawdź konto). System dodaje poświadczenia logowania do konta użytkownika.

    ![Wprowadź adres e-mail, a następnie wybierz pozycję Verify Account (Sprawdź konto)](./media/sapboc-tutorial/config7.png)

9. Wybierz ikonę **zapisywania**.

    ![Ikona zapisywania](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury analizy SAP.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **chmura Analytics Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-sap-analytics-cloud-test-user"></a>Tworzenie użytkownika testowego chmury SAP Analytics

Aby można było zalogować się do chmury SAP Analytics, użytkownicy usługi Azure AD muszą być obsługiwani w chmurze analizy SAP. W chmurze analizy SAP Inicjowanie obsługi jest zadaniem ręcznym.

Aby aprowizować konto użytkownika:

1. Zaloguj się do firmowej witryny usługi SAP Analytics w chmurze jako administrator.

2. Wybierz **menu**  >  **Security**  >  **Użytkownicy**zabezpieczeń.

    ![Dodawanie pracownika](./media/sapboc-tutorial/user1.png)

3. Na stronie **Users** (Użytkownicy) wybierz pozycję **+**, aby dodać szczegółowe informacje dotyczące nowego użytkownika. 

    ![Strona dodawania użytkowników](./media/sapboc-tutorial/user4.png)

    Następnie wykonaj następujące kroki:

    a. W polu **Identyfikator użytkownika** wprowadź identyfikator użytkownika, na przykład **B**.

    b. W polu **imię** Wprowadź imię i nazwisko użytkownika, na przykład **B**.

    c. W polu **LAST NAME** (Nazwisko) wprowadź nazwisko użytkownika, np. **Simon**.

    d. W polu **Nazwa wyświetlana** wprowadź pełną nazwę użytkownika, na przykład **B. Simon**.

    e. W polu **E-MAIL** (Adres e-mail) wprowadź adres e-mail użytkownika, np. `b.simon@contoso.com`.

    f. Na stronie **Select Roles** (Wybieranie ról) wybierz odpowiednią rolę użytkownika, a następnie wybierz pozycję **OK**.

      ![Wybierz rolę](./media/sapboc-tutorial/user3.png)

    przykład Wybierz ikonę **zapisywania**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi SAP Analytics w panelu dostępu należy automatycznie zalogować się do chmury analizy SAP, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

