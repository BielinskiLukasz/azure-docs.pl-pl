---
title: 'Samouczek: integracja Azure Active Directory z usługą MyWorkDrive | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: c733873a5545bb01f03abd8178ed2f371e2dd3aa
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074083"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Samouczek: integracja MyWorkDrive z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę MyWorkDrive z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi MyWorkDrive z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do MyWorkDrive.
* Zezwól użytkownikom na automatyczne logowanie się do usługi MyWorkDrive przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) MyWorkDrive.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. MyWorkDrive obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-myworkdrive-from-the-gallery"></a>Dodawanie MyWorkDrive z galerii

Aby skonfigurować integrację programu MyWorkDrive z usługą Azure AD, musisz dodać MyWorkDrive z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **MyWorkDrive** w polu wyszukiwania.
1. Wybierz pozycję **MyWorkDrive** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą MyWorkDrive przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w MyWorkDrive.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą MyWorkDrive, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-myworkdrive-sso)** w usłudze MyWorkDrive, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz użytkownika testowego MyWorkDrive](#create-myworkdrive-test-user)** , aby uzyskać odpowiednik Britta Simon w MyWorkDrive, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **MyWorkDrive** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu usługi **dostawcy tożsamości** , na stronie **Podstawowa konfiguracja protokołu SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Wprowadź nazwę hosta serwera MyWorkDrive firmy: np.
    > 
    > Adres URL odpowiedzi: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Adres URL logowania:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Skontaktuj się z [zespołem pomocy technicznej MyWorkDrive](mailto:support@myworkdrive.com) , jeśli nie masz pewności, jak skonfigurować własną nazwę hosta i certyfikat SSL dla tych wartości.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** do Schowka.

    ![Link pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Konfigurowanie logowania jednokrotnego MyWorkDrive

1. Aby zautomatyzować konfigurację w programie MyWorkDrive, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup MyWorkDrive** , aby skierować do aplikacji MyWorkDrive. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi MyWorkDrive. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-4.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować MyWorkDrive, w innym oknie przeglądarki sieci Web Zaloguj się do MyWorkDrive jako administrator zabezpieczeń.

1. Na serwerze MyWorkDrive w panelu administratora kliknij pozycję **Enterprise** i wykonaj następujące czynności:

    ![Administrator](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Włącz **Logowanie jednokrotne SAML/ADFS**.

    b. Wybierz pozycję **SAML — Azure AD**

    d. W polu tekstowym **adres URL metadanych federacji aplikacji platformy Azure** wklej wartość **adresu URL metadanych federacji aplikacji** skopiowanego z Azure Portal.

    d. Kliknij pozycję **Zapisz**

    > [!NOTE]
    > Aby uzyskać dodatkowe informacje, zobacz artykuł dotyczący [pomocy technicznej usługi Azure AD MyWorkDrive](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi MyWorkDrive.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **MyWorkDrive**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-myworkdrive-test-user"></a>Utwórz użytkownika testowego MyWorkDrive

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w MyWorkDrive. Aby dodać użytkowników na platformie MyWorkDrive, Pracuj z [zespołem pomocy technicznej MyWorkDrive](mailto:support@myworkdrive.com) . Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka MyWorkDrive w panelu dostępu należy automatycznie zalogować się do MyWorkDrive, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)