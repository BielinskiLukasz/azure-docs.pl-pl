---
title: 'Samouczek: Azure Active Directory integrację z platformą projektowi Civic Innovation | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i platformą projektowi Civic Innovation.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496826"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Samouczek: Integruj platformę projektowi Civic Innovation z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować platformę projektowi Civic Innovation z usługą Azure Active Directory (Azure AD). Gdy integrujesz platformę projektowi Civic Innovation z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do platformy projektowi Civic Innovation.
* Zezwól użytkownikom na automatyczne logowanie do platformy projektowi Civic Innovation przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej bezpłatnej wersji próbnej [](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) platformy projektowi Civic Innovation.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma projektowi Civic Innovation obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**





## <a name="adding-civic-platform-from-the-gallery"></a>Dodawanie platformy projektowi Civic Innovation z galerii

Aby skonfigurować integrację platformy projektowi Civic Innovation z usługą Azure AD, musisz dodać platformę projektowi Civic Innovation z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **projektowi Civic Innovation platformę** w polu wyszukiwania.
1. Wybierz pozycję **platforma projektowi Civic Innovation** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą platformy projektowi Civic Innovation przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na platformie projektowi Civic Innovation.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą platformy projektowi Civic Innovation, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania JEDNOkrotnego platformy projektowi Civic Innovation](#configure-civic-platform-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego platformy projektowi Civic Innovation](#create-civic-platform-test-user)** , aby dysponować odpowiednikiem B. Simon na platformie projektowi Civic Innovation, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **platformy projektowi Civic Innovation** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.accela.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `civicplatform.accela.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta platformy projektowi Civic Innovation](mailto:skale@accela.com) , aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/copy-metadataurl.png)

1. Przejdź do **Azure Active Directory** > **rejestracje aplikacji** w usłudze Azure AD, wybierz aplikację.

1. Skopiuj **Identyfikator katalogu (dzierżawy)** i Zapisz go w Notatniku.

    ![Skopiuj katalog (identyfikator dzierżawy) i Zapisz go w kodzie aplikacji](media/civic-platform-tutorial/directoryid.png)

1. Skopiuj **Identyfikator aplikacji** i Zapisz go w Notatniku.

   ![Kopiowanie identyfikatora aplikacji (klienta)](media/civic-platform-tutorial/applicationid.png)

1. Przejdź do **Azure Active Directory** > **rejestracje aplikacji** w usłudze Azure AD, wybierz aplikację. Wybierz pozycję **certyfikaty &** wpisy tajne.

1. Wybierz pozycję wpisy **tajne klienta — > nowego klucza tajnego klienta**.

1. Podaj opis klucza tajnego i czas trwania. Po zakończeniu wybierz pozycję **Dodaj**.

   > [!NOTE]
   > Po zapisaniu klucza tajnego klienta zostanie wyświetlona wartość wpisu tajnego klienta. Skopiuj tę wartość, ponieważ nie można pobrać klucza później.

   ![Skopiuj wartość klucza tajnego, ponieważ nie można pobrać jej później](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Konfigurowanie logowania jednokrotnego dla platformy projektowi Civic Innovation

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny w chmurze Atlassian jako administrator.

1. Kliknij pozycję **standardowe opcje**.

    ![Link pobierania certyfikatu](media/civic-platform-tutorial/standard-choices.png)

1. Utwórz standardową **ssoconfig**wyboru.

1. Wyszukaj **ssoconfig** i Prześlij.

    ![Link pobierania certyfikatu](media/civic-platform-tutorial/sso-config.png)

1. Rozwiń SSOCONFIG, klikając czerwoną kropkę.

    ![Link pobierania certyfikatu](media/civic-platform-tutorial/sso-config01.png)

1. Podaj informacje o konfiguracji dotyczące logowania jednokrotnego w następującym kroku:

    ![Link pobierania certyfikatu](media/civic-platform-tutorial/sso-config02.png)

    1. W polu  **Identyfikator aplikacji** wprowadź wartość identyfikatora, która została skopiowana z Azure Portal.

    1. W polu **clientSecret** wprowadź wartość **klucza tajnego** , która została skopiowana z Azure Portal.

    1. W polu **directoryId** wprowadź wartość **identyfikatora katalogu (dzierżawy)** , która została skopiowana z Azure Portal.

    1. Wprowadź idpName. Np `Azure`.:

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do platformy projektowi Civic Innovation.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **platforma projektowi Civic Innovation**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-civic-platform-test-user"></a>Utwórz użytkownika testowego platformy projektowi Civic Innovation

W tej sekcji utworzysz użytkownika o nazwie B. Simon na platformie projektowi Civic Innovation. Współpracuj z zespołem pomocy technicznej platformy projektowi Civic Innovation, aby dodać użytkowników w [zespole obsługi klienta platformy projektowi Civic Innovation](mailto:skale@accela.com). Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka platforma projektowi Civic Innovation w panelu dostępu należy automatycznie zalogować się do platformy projektowi Civic Innovation, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

