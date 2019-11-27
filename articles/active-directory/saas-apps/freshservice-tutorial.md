---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Freshservice | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Freshservice

W tym samouczku dowiesz się, jak zintegrować usługę Freshservice z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Freshservice z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Freshservice.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Freshservice przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Freshservice.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Freshservice obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-freshservice-from-the-gallery"></a>Dodawanie aplikacji Freshservice z galerii

Aby skonfigurować integrację aplikacji Freshservice z usługą Azure AD, musisz dodać aplikację Freshservice z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Freshservice** w polu wyszukiwania.
1. Wybierz pozycję **Freshservice** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Freshservice

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Freshservice przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Freshservice.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Freshservice, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-freshservice-sso)** w usłudze Freshservice, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Freshservice](#create-freshservice-test-user)** , aby dysponować odpowiednikiem B. Simon w Freshservice, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Freshservice** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<democompany>.freshservice.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając rzeczywistego adresu URL logowania i identyfikatora. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Freshservice](https://support.freshservice.com/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Freshservice wymaga odcisku palca SHA-256 do uzyskania pracy z logowaniem jednokrotnym. Aby uzyskać odcisk palca SHA-256, wykonaj następujące czynności:

    ![Linii](./media/freshservice-tutorial/ic790821.png "Linii")

    1. Otwórz [link](https://www.samltool.com/fingerprint.php) w innej przeglądarce sieci Web.

    1. Otwórz plik pobrany certyfikat (base64) w Notatniku i wklej zawartość w polu tekstowym **certyfikat X. 509** .

    1. Dla algorytmu wybierz pozycję **SHA256** z listy rozwijanej.

    1. Kliknij pozycję **Oblicz odcisk palca**.

    1. Kliknij ikonę kopiowania, aby skopiować wygenerowany **odcisk palca** i zapisać go na komputerze.

1. W sekcji **Konfigurowanie Freshservice** na **Azure Portal**skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Freshservice.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Freshservice**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-freshservice-sso"></a>Konfigurowanie logowania jednokrotnego Freshservice

1. Aby zautomatyzować konfigurację w programie Freshservice, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup Freshservice** , aby skierować do aplikacji Freshservice. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Freshservice. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Freshservice, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Freshservice jako administrator i wykonaj następujące czynności:

4. W menu u góry kliknij pozycję **Admin** (Administrator).

    ![Administratora](./media/freshservice-tutorial/ic790814.png "Administrator")

5. W obszarze **Customer Portal** (Portal klienta) kliknij pozycję **Security** (Zabezpieczenia).

    ![Bezpieczeństwo](./media/freshservice-tutorial/ic790815.png "Bezpieczeństwo")

6. W sekcji **Security** (Zabezpieczenia) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/freshservice-tutorial/ic790816.png "Logowanie jednokrotne")

    a. Włącz przełącznik **Single Sign On** (Logowanie jednokrotne).

    b. Wybierz pozycję **SAML SSO** (Logowanie jednokrotne SAML).

    c. W polu tekstowym **SAML Login URL** (Adres URL logowania SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym **odcisk palca certyfikatu zabezpieczeń** wklej wartość **odcisku palca** , która została wygenerowana wcześniej.

    f. Kliknij pozycję **Zapisz**

### <a name="create-freshservice-test-user"></a>Tworzenie użytkownika testowego aplikacji Freshservice

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze FreshService, muszą one być obsługiwane w usłudze FreshService. W przypadku aplikacji FreshService jest to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny **FreshService** jako administrator.

2. W menu u góry kliknij pozycję **Admin** (Administrator).

    ![Administratora](./media/freshservice-tutorial/ic790814.png "Administrator")

3. W sekcji **User Management** (Zarządzanie użytkownikami) kliknij pozycję **Requesters** (Inicjatorzy żądania).

    ![Jednostek żądających](./media/freshservice-tutorial/ic790818.png "Jednostek żądających")

4. Kliknij przycisk **New Requester** (Nowy inicjator żądania).

    ![Nowi osoby żądające](./media/freshservice-tutorial/ic790819.png "Nowi osoby żądające")

5. W sekcji **New Requester** (Nowy inicjator żądania) wykonaj następujące kroki:

    ![Nowy obiekt żądający](./media/freshservice-tutorial/ic790820.png "Nowy obiekt żądający")  

    a. W polach tekstowych **First Name** (Imię) i **Email** (Adres e-mail) wprowadź atrybuty imienia i adresu e-mail prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

    b. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.
    >  

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników FreshService i interfejsów API udostępnionych przez usługę FreshService.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka FreshService w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji FreshService, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Freshservice z usługą Azure AD](https://aad.portal.azure.com/)