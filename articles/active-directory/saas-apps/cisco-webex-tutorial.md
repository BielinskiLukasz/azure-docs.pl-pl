---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu spotkań Cisco WebEx | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i spotkaniami Cisco WebEx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6aab04826a3c06b595859c0f41f658b6e5d3432
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562295"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu spotkań Cisco WebEx

W tym samouczku dowiesz się, jak zintegrować spotkania Cisco WebEx z usługą Azure Active Directory (Azure AD). W przypadku integrowania spotkań Cisco WebEx z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do spotkań Cisco WebEx.
* Zezwól użytkownikom na automatyczne logowanie do spotkań Cisco WebEx z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w systemie Cisco WebEx.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Spotkania Cisco WebEx obsługują usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

* Spotkania Cisco WebEx wspierają Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Dodawanie spotkań Cisco WebEx z galerii

Aby skonfigurować integrację spotkań Cisco WebEx w usłudze Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS spotkania Cisco WebEx z galerii.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **spotkania Cisco WebEx** .
1. Wybierz pozycję **spotkania Cisco WebEx** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla spotkań Cisco WebEx

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą spotkań Cisco WebEx przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach spotkań Cisco WebEx.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu spotkań Cisco WebEx, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj spotkania Cisco WebEx logowanie](#configure-cisco-webex-meetings-sso)** jednokrotne — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego spotkań Cisco WebEx](#create-cisco-webex-meetings-test-user)** , aby dysponować odpowiednikiem B. Simon w ramach spotkań Cisco WebEx, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji aplikacji do **spotkań Cisco WebEx** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** Przekaż pobrany plik **metadanych dostawcy usług** i skonfiguruj aplikację w trybie inicjowania **dostawcy tożsamości** , wykonując następujące czynności:

    >[!Note]
    >Zostanie wyświetlony plik metadanych dostawcy usług, który został wyjaśniony w dalszej części artykułu Konfigurowanie logowania jednokrotnego w usłudze **Cisco WebEx** . 

    a. Kliknij przycisk **przekazywania pliku metadanych**.

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    c. Po pomyślnym ukończeniu przekazywania pliku metadanych dostawcy usług wartości **Identyfikator** i **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** zostaną wypełnione automatycznie:

5. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , wykonaj następujące czynności:
    
    W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca:`https://<customername>.webex.com`

5. Aplikacja do spotkań Cisco WebEx oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

6. Oprócz powyższych, aplikacja do spotkań Cisco WebEx oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W sekcji oświadczenia użytkownika w oknie dialogowym atrybuty użytkownika wykonaj następujące kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli: 

    | Name | Atrybut źródłowy|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   Identyfikator UID    | user.mail |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij polecenie **Zapisz**.

4. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na stronie **Konfigurowanie spotkań Cisco WebEx** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz funkcję B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do spotkań Cisco WebEx.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **spotkania Cisco WebEx**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurowanie spotkań Cisco WebEx Logowanie jednokrotne

1. Przejdź do `https://<customername>.webex.com/admin` adresu URL z poświadczeniami administracyjnymi.

2. Przejdź do obszaru **typowe ustawienia lokacji** i przejdź do **konfiguracji rejestracji**jednokrotnej.
 
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Na stronie **Administracja WebEx** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Wybierz pozycję **SAML 2,0** jako **Protokół federacyjny**.

    b. Kliknij link **Importuj metadane SAML** , aby przekazać plik metadanych, który został pobrany z Azure Portal.

    c. Kliknij przycisk **Eksportuj** , aby pobrać plik metadanych dostawcy usług i przekazać go w sekcji **podstawowe konfiguracje języka SAML** na Azure Portal.

    d. W polu tekstowym **AuthContextClassRef** wpisz `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` i, jeśli chcesz włączyć uwierzytelnianie wieloskładnikowe przy użyciu usługi Azure AD wpisz dwie wartości, takie jak`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Wybierz pozycję **Autotworzenie konta**.

    >[!NOTE]
    >Aby włączyć Inicjowanie obsługi klienta **just-in-Time** , należy sprawdzić, czy **Funkcja autouzupełniania konta**jest włączona. Oprócz atrybutów tokenów SAML należy je przesłać do odpowiedzi SAML.

    f. Kliknij polecenie **Zapisz**.

    >[!NOTE]
    >Ta konfiguracja jest tylko dla klientów korzystających z WebEx UserID w formacie poczty e-mail.

### <a name="create-cisco-webex-meetings-test-user"></a>Utwórz użytkownika testowego spotkań Cisco WebEx

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w ramach spotkań Cisco WebEx. W przypadku spotkań Cisco WebEx jest dostępna obsługa **just-in-Time** , która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w spotkaniach Cisco WebEx, zostanie utworzony nowy, gdy spróbujesz uzyskać dostęp do spotkań Cisco WebEx.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka spotkań Cisco WebEx w panelu dostępu należy automatycznie zalogować się do spotkań Cisco WebEx, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługi ServiceNow z usługą Azure AD](https://aad.portal.azure.com)