---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z dokumentami w dokumentach Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i dokumentów.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf25ce318415a8aa36bca8d4cd7380e4e8e67ca
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743455"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z dokumentami

W tym samouczku dowiesz się, jak zintegrować dokumenty z usługą Azure Active Directory (Azure AD). Podczas integrowania dokumentów z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do dokumentów w usłudze.
* Umożliwia użytkownikom automatyczne logowanie się do dokumentów w dokumentach sieci przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Dokumenty w programie obsługuje zainicjowanie rejestracji jednokrotnej w programie **SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Dodawanie dokumentów z galerii

Aby skonfigurować integrację dokumentów w usłudze Azure AD, musisz dodać dokumenty z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **dokumenty** w polu wyszukiwania.
1. Wybierz pozycję **dokumenty** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla dokumentów

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą dokumentów sieci przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w dokumentach sieciowych.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą dokumentów, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj rejestrację jednokrotną dla dokumentów](#configure-netdocuments-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz dokumenty w postaci użytkownika testowego](#create-netdocuments-test-user)** , aby dysponować odpowiednikiem B. Simon w dokumentach sieci połączonych z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **integracja aplikacji sieci** Web Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu adresu URL logowania i adresu URL odpowiedzi. Identyfikator repozytorium to wartość rozpoczynająca się od **urzędu certyfikacji** , po którym następuje 8 kodów znaków skojarzonych z repozytorium dokumentów. Aby uzyskać więcej informacji, możesz sprawdzić dokument dotyczący [obsługi tożsamości federacyjnych w dokumentach](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) . Możesz też skontaktować się z [zespołem pomocy technicznej klienta](https://support.netdocuments.com/hc/) , aby uzyskać te wartości, jeśli masz problemy z konfiguracją przy użyciu powyższych informacji. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja sieci Documents oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja sieciową oczekuje, że **NameIdentifier** mają być mapowane przy użyciu **IDPracownika** lub innych roszczeń, które mają zastosowanie do Twojej organizacji jako **NameIdentifier**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i Zmień mapowanie atrybutu.

    ![image](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie dokumentów** sieci skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do dokumentów w usłudze.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **dokumenty**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-netdocuments-sso"></a>Konfigurowanie rejestracji jednokrotnej dokumentów w dokumentach

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny dokumentów w organizacji jako administrator.

2. Przejdź do pozycji **Administracja**.

3. Kliknij przycisk **Dodaj i Usuń użytkowników i grupy**.
   
    ![Repozytorium](./media/netdocuments-tutorial/ic795047.png "Repozytorium")

4. Kliknij pozycję **Konfiguruj zaawansowane opcje uwierzytelniania**.
    
    ![Skonfiguruj zaawansowane opcje uwierzytelniania](./media/netdocuments-tutorial/ic795048.png "Skonfiguruj zaawansowane opcje uwierzytelniania")

5. W oknie dialogowym **Tożsamość federacyjna** wykonaj następujące czynności:
   
    ![Tożsamość federacyjna](./media/netdocuments-tutorial/ic795049.png "Tożsamość federacyjna")
   
    a. Jako **Typ federacyjnego serwera tożsamości**wybierz **Active Directory Federation Services**.
   
    b. Kliknij pozycję **Wybierz plik**, aby przekazać pobrany plik metadanych pobrany z Azure Portal.
   
    c. Kliknij przycisk **OK**.

### <a name="create-netdocuments-test-user"></a>Tworzenie użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD logowanie się w dokumentach, muszą one być obsługiwane w dokumentach.  
W przypadku dokumentów, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmowej **dokumentów** w organizacji jako administrator.

2. W menu u góry kliknij pozycję **Admin** (Administrator).
   
    ![Administrator](./media/netdocuments-tutorial/ic795051.png "Administrator")

3. Kliknij przycisk **Dodaj i Usuń użytkowników i grupy**.
   
    ![Repozytorium](./media/netdocuments-tutorial/ic795047.png "Repozytorium")

4. W polu tekstowym **adres e-mail** wpisz adres e-mail prawidłowego konta Azure Active Directory, które chcesz udostępnić, a następnie kliknij przycisk **Dodaj użytkownika**.
   
    ![Adres e-mail](./media/netdocuments-tutorial/ic795053.png "Adres e-mail")
   
    >[!NOTE]
    >Właściciel konta Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim staną się aktywne. Do aprowizacji Azure Active Directory kont użytkowników można używać innych narzędzi do tworzenia kont użytkowników i interfejsów API dostarczonych przez dokumenty.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka dokumenty w panelu dostępu należy automatycznie zalogować się do dokumentów, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj dokumenty w usłudze Azure AD](https://aad.portal.azure.com/)

