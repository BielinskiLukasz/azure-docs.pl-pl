---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z serwerem Tableau | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i serwerem Tableau.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e95143da96ce9891c3820479e536e9ea6a18617
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861362"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z serwerem Tableau

W tym samouczku dowiesz się, jak zintegrować serwer Tableau z usługą Azure Active Directory (Azure AD). W przypadku integrowania serwera Tableau z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do serwera Tableau.
* Zezwól użytkownikom na automatyczne logowanie do serwera Tableau przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) na serwerze Tableau.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Serwer Tableau obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Dodawanie serwera Tableau z galerii

Aby skonfigurować integrację serwera Tableau z usługą Azure AD, musisz dodać serwer Tableau z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Tableau Server** w polu wyszukiwania.
1. Wybierz pozycję **serwer Tableau** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla serwera Tableau

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą serwera Tableau przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na serwerze Tableau.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą serwera Tableau, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne serwera Tableau](#configure-tableau-server-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego serwera Tableau](#create-tableau-server-test-user)** , aby dysponować odpowiednikiem B. Simon na serwerze Tableau, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **serwera Tableau** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Poprzednie wartości nie są wartościami rzeczywistymi. Zaktualizuj wartości z rzeczywistym adresem URL i identyfikatorem na stronie konfiguracji serwera Tableau, która została omówiona w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie serwera Tableau** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do serwera Tableau.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **serwer Tableau**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-tableau-server-sso"></a>Konfigurowanie logowania jednokrotnego serwera Tableau

1. Aby skonfigurować Logowanie jednokrotne dla aplikacji, musisz zalogować się do dzierżawy serwera Tableau jako administrator.

2. Na karcie **Konfiguracja** wybierz pozycję **tożsamość użytkownika & dostęp**, a następnie wybierz kartę Metoda **uwierzytelniania** .

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na stronie **Konfiguracja** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. W obszarze **Metoda uwierzytelniania**wybierz pozycję SAML.

    b. Zaznacz pole wyboru **Włącz uwierzytelnianie SAML dla serwera**.

    c. Zwrotny adres URL serwera Tableau — adres URL, do którego będą uzyskiwać dostęp użytkownicy serwera <http://tableau_server>Tableau, na przykład. Użycie `http://localhost` nie jest zalecane. Użycie adresu URL z końcowym ukośnikiem (na przykład `http://tableau_server/`) nie jest obsługiwane. Skopiuj **adres URL zwrotnego serwera Tableau** i wklej go w celu **zalogowania się do adresu URL** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    d. Identyfikator jednostki SAML — identyfikator jednostki jednoznacznie identyfikuje instalację serwera Tableau w dostawcy tożsamości. Możesz tutaj wprowadzić adres URL serwera Tableau, jeśli chcesz, ale nie musi to być adres URL serwera Tableau. Skopiuj **Identyfikator jednostki SAML** i wklej go do pola tekstowego **identyfikatora** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    e. Kliknij **plik metadanych XML pobierania** i otwórz go w aplikacji Edytor tekstu. Znajdź adres URL usługi Konsumenckej potwierdzenia przy użyciu protokołu HTTP POST i indeksu 0 i skopiuj adres URL. Teraz wklej je do pola tekstowego **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    f. Znajdź plik metadanych Federacji pobrany z Azure Portal, a następnie Przekaż go w **pliku metadanych SAML dostawcy tożsamości**.

    g. Wprowadź nazwy atrybutów, których dostawcy tożsamości używa do przechowywania nazw użytkowników, nazw wyświetlanych i adresów e-mail.

    h. Kliknij polecenie **Zapisz**.

    > [!NOTE]
    > Klient musi przekazać dowolny certyfikat z konfiguracji SSO protokołu SAML serwera Tableau i zostanie zignorowany w przepływie logowania jednokrotnego. Jeśli potrzebujesz pomocy przy konfigurowaniu protokołu SAML na serwerze Tableau, zapoznaj się z tym artykułem [Konfigurowanie protokołu SAML](https://help.tableau.com/current/server/en-gb/saml_config_steps_tsm_ui.htm).

### <a name="create-tableau-server-test-user"></a>Utwórz użytkownika testowego serwera Tableau

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon na serwerze Tableau. Musisz udostępnić wszystkim użytkownikom na serwerze Tableau.

Ta nazwa użytkownika powinna być zgodna z wartością skonfigurowaną w niestandardowym atrybucie usługi Azure AD **nazwy użytkownika**. Przy prawidłowym mapowaniu należy wykonać integrację, aby skonfigurować Logowanie jednokrotne w usłudze Azure AD.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z administratorem serwera Tableau w organizacji.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka serwer Tableau w panelu dostępu należy automatycznie zalogować się do serwera Tableau, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj serwer Tableau z usługą Azure AD](https://aad.portal.azure.com/)