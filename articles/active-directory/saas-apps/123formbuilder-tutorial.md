---
title: 'Samouczek: Integracja usługi Azure Active Directory z 123-formbuilderem samouczycieli | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logajną jednokrotną usługi 123FormBuilder.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 947a9d632089b18f6b950c5eecbcb74d061f32eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274214"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z logiem logaj firmy 123FormBuilder

W tym samouczku dowiesz się, jak zintegrować 123FormBuilder SSO z usługą Azure Active Directory (Azure AD). Po zintegrowaniu 123FormBuilder SSO z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do 123FormBuilder SSO.
* Włącz automatyczne logowanie użytkowników do 123 FormBuilder logowanie logowanie za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* 123FormBuilder SSO jednokrotnego logowania (Logowanie jednokrotne) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* 123FormBuilder SSO obsługuje **sp i IDP** zainicjowane SSO.
* 123FormBuilder SSO obsługuje **just in time** użytkownika inicjowania obsługi administracyjnej.
* Po skonfigurowaniu 123FormBuilder SSO można wymusić kontrolę sesji, która chroni eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>Dodawanie 123FormBuilder SSO z galerii

Aby skonfigurować integrację 123FormBuilder SSO do usługi Azure AD, należy dodać 123FormBuilder SSO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **123FormBuilder SSO** w polu wyszukiwania.
1. Wybierz **123FormBuilder SSO** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla logowania jednokrotnego programu 123FormBuilder

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą 123 FormBuilder SSO przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w 123FormBuilder SSO.

Aby skonfigurować i przetestować sytą usługę Azure AD za pomocą 123FormBuilder SSO, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj 123FormBuilder SSO](#configure-123formbuilder-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz 123FormBuilder SSO test użytkownika](#create-123formbuilder-sso-test-user)** — mieć odpowiednik B.Simon w 123FormBuilder SSO, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **logowania jednokrotnego 123formBuilder** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego.**
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** Jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Musisz zaktualizować te wartości, używając rzeczywistych adresów URL i identyfikatora, co zostało opisane w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **kod XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie 123FormBuilder SSO** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do logowania jednokrotnego 123FormBuilder.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **123FormBuilder SSO**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-123formbuilder-sso"></a>Konfigurowanie 123-owego systemu SSO programu 123FormBuilder

1. Aby skonfigurować logowanie jednokrotne po stronie **logowania jednokrotnego 123FormBuilder,** przejdź do [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/123formbuilder-tutorial/submit.png) 

    a. W polu tekstowym **Email (Adres e-mail)** wpisz adres e-mail użytkownika, na przykład `B.Simon@Contoso.com`.

    b. Kliknij pozycję **Upload (Przekazywanie)** i przejdź do pobranego pliku XML metadanych, który został pobrany z witryny Azure Portal.

    d. Kliknij pozycję **SUBMIT FORM (PRZEŚLIJ FORMULARZ)**.

2. Na stronie **Microsoft Azure AD - Single sign-on - Configure App Settings (Konfigurowanie ustawień aplikacji usługi Microsoft Azure AD — logowanie jednokrotne)** wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/123formbuilder-tutorial/url3.png)

    a. Jeśli chcesz skonfigurować aplikację w **trybie inicjowanym przez dostawcę tożsamości**, skopiuj wartość **IDENTIFIER (IDENTYFIKATOR)** dla swojego wystąpienia, a następnie wklej ją w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    b. Jeśli chcesz skonfigurować aplikację w **trybie inicjowanym przez dostawcę tożsamości**, skopiuj wartość **REPLY URL (ADRES URL ODPOWIEDZI)** dla swojego wystąpienia, a następnie wklej ją w polu tekstowym **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    d. Jeśli chcesz skonfigurować aplikację w **trybie inicjowanym przez dostawcę tożsamości**, skopiuj wartość **SIGN ON URL (ADRES URL LOGOWANIA)** dla swojego wystąpienia, a następnie wklej ją w polu tekstowym **Adres URL logowania** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

### <a name="create-123formbuilder-sso-test-user"></a>Tworzenie użytkownika testowego sytomowego systemu SSO 123FormBuilder

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w 123FormBuilder SSO. 123 FormBuilder SSO obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w 123FormBuilder SSO, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka logującego jednośliże firmy 123FormBuilder w Panelu dostępu należy automatycznie zalogować się do 123FormBuilder SSO, dla którego skonfigurowano logującą jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj 123-owy sytmierz usługi 123FormBuilder z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
