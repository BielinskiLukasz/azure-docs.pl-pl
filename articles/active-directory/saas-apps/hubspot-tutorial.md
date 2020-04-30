---
title: 'Samouczek: integracja Azure Active Directory z usługą HubSpot | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68944447"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Samouczek: integracja Azure Active Directory z usługą HubSpot

W tym samouczku dowiesz się, jak zintegrować usługę HubSpot z usługą Azure Active Directory (Azure AD).

Integracja HubSpot z usługą Azure AD zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do usługi HubSpot.
* Użytkownicy mogą być automatycznie zalogowani w usłudze HubSpot przy użyciu kont usługi Azure AD (Logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS (Software as a Service) z usługą Azure AD, zobacz [Logowanie jednokrotne do aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą HubSpot, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Subskrypcja HubSpot z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku należy skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD w środowisku testowym i zintegrować usługę HubSpot z usługą Azure AD.

Program HubSpot obsługuje następujące funkcje:

* **Logowanie jednokrotne zainicjowane przez program SP**
* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**

## <a name="add-hubspot-in-the-azure-portal"></a>Dodaj HubSpot w Azure Portal

Aby zintegrować usługę HubSpot z usługą Azure AD, musisz dodać HubSpot do listy zarządzanych aplikacji SaaS.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Opcja Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **aplikacje** > dla przedsiębiorstw**wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.

    ![Opcja nowej aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **HubSpot**. W wynikach wyszukiwania wybierz pozycję **HubSpot**, a następnie wybierz pozycję **Dodaj**.

    ![HubSpot na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą HubSpot na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w HubSpot.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi HubSpot, należy wykonać następujące bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystanie z tej funkcji. |
| **[Konfigurowanie logowania jednokrotnego HubSpot](#configure-hubspot-single-sign-on)** | Konfiguruje ustawienia logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testuje Logowanie jednokrotne w usłudze Azure AD dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD. |
| **[Tworzenie użytkownika testowego HubSpot](#create-a-hubspot-test-user)** | Tworzy odpowiednik Britta Simon w HubSpot, który jest połączony z reprezentacją usługi Azure AD użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz Logowanie jednokrotne w usłudze Azure AD za pomocą HubSpot w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)w okienku integracja aplikacji **HubSpot** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML** lub **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć okienko **podstawowe ustawienia protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W okienku **podstawowe konfiguracje języka SAML** , aby skonfigurować *tryb zainicjowany dostawcy tożsamości*, wykonaj następujące czynności:

    1. W polu **Identyfikator** wprowadź adres URL, który ma następujący wzorzec: https:\//API.HubSpot.com/login-API/V1/SAML/login?portalId =\<identyfikator\>klienta.

    1. W polu **adres URL odpowiedzi** wprowadź adres URL, który ma następujący wzorzec: https:\//API.HubSpot.com/login-API/V1/SAML/ACS?portalId =\<identyfikator\>klienta.

    ![HubSpot domenę i adresy URL Logowanie jednokrotne](common/idp-intiated.png)

    > [!NOTE]
    > Aby sformatować adresy URL, można również odwołać się do wzorców przedstawionych w okienku podstawowe informacje o **konfiguracji SAML** w Azure Portal.

1. Aby skonfigurować aplikację w trybie *inicjowania programu Sp* :

    1. Wybierz pozycję **Ustaw dodatkowe adresy URL**.

    1. W polu **adres URL logowania** wprowadź wartość **https\/:/App.HubSpot.com/login**.

    ![Opcja Ustaw dodatkowe adresy URL](common/metadata-upload-additional-signon.png)

1. W okienku **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** obok pozycji **certyfikat (base64)**. Wybierz opcję pobierania w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu (base64)](common/certificatebase64.png)

1. W sekcji **Konfiguracja HubSpot** Skopiuj następujące adresy URL zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurowanie logowania jednokrotnego HubSpot

1. Otwórz nową kartę w przeglądarce i zaloguj się na swoim koncie administratora HubSpot.

1. Wybierz ikonę **Ustawienia** w prawym górnym rogu strony.

    ![Ikona Ustawienia w HubSpot](./media/hubspot-tutorial/config1.png)

1. Wybierz pozycję **Ustawienia domyślne konta**.

    ![Opcja ustawienia domyślne konta w HubSpot](./media/hubspot-tutorial/config2.png)

1. Przewiń w dół do sekcji **zabezpieczenia** , a następnie wybierz pozycję **Konfiguruj**.

    ![Opcja konfiguracji w HubSpot](./media/hubspot-tutorial/config3.png)

1. W sekcji **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności:

    1. W polu **adres URL odbiorców (identyfikator jednostki dostawcy usługi)** wybierz pozycję **Kopiuj** , aby skopiować wartość. W Azure Portal w okienku **podstawowe konfiguracje języka SAML** wklej wartość w polu **Identyfikator** .

    1. W polu **adres URL logowania, ACS, adresat lub przekierowanie** wybierz opcję **Kopiuj** , aby skopiować wartość. W Azure Portal w okienku **podstawowe konfiguracje języka SAML** wklej wartość w polu **adres URL odpowiedzi** .

    1. W HubSpot, w polu **Identyfikator dostawcy tożsamości lub adres URL wystawcy** , wklej wartość **identyfikatora usługi Azure AD** skopiowaną w Azure Portal.

    1. W HubSpot, w polu **adres URL** logowania jednokrotnego dostawcy tożsamości, wklej wartość **adresu URL loginu** skopiowanego w Azure Portal.

    1. W Notatniku systemu Windows Otwórz pobrany plik certyfikatu (base64). Wybierz i skopiuj zawartość pliku. Następnie w HubSpot wklej go w polu **certyfikat X. 509** .

    1. Wybierz pozycję **Verify** (Weryfikuj).

        ![Sekcja Konfigurowanie logowania jednokrotnego w HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i wszyscy użytkownicy](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Opcja nowy użytkownik](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **brittasimon\@\<>-firmowej domeny.\< rozszerzenie\>**. Na przykład **brittasimon\@contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** . Zapisz wartość wyświetlaną w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

    ![Okienko użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji przyznano Britta Simon dostęp do usługi HubSpot, dzięki czemu może on korzystać z logowania jednokrotnego na platformie Azure.

1. W Azure Portal wybierz pozycję **aplikacje** > dla przedsiębiorstw**wszystkie aplikacje** > **HubSpot**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **HubSpot**.

    ![HubSpot na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników. Wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W okienku **Dodaj przypisanie** wybierz pozycję **Przypisz**.

### <a name="create-a-hubspot-test-user"></a>Tworzenie użytkownika testowego HubSpot

Aby umożliwić użytkownikom logowanie się do usługi Azure AD, użytkownik musi być zainicjowany w HubSpot. W HubSpot, Inicjowanie obsługi jest zadaniem ręcznym.

Aby zainicjować obsługę administracyjną konta użytkownika w programie HubSpot:

1. Zaloguj się do witryny firmy HubSpot jako administrator.

1. Wybierz ikonę **Ustawienia** w prawym górnym rogu strony.

    ![Ikona Ustawienia w HubSpot](./media/hubspot-tutorial/config1.png)

1. Wybierz pozycję **użytkownicy & zespoły**.

    ![Opcja Użytkownicy & zespoły w HubSpot](./media/hubspot-tutorial/user1.png)

1. Wybierz pozycję **Utwórz użytkownika**.

    ![Opcja tworzenia użytkownika w HubSpot](./media/hubspot-tutorial/user2.png)

1. W polu **Dodaj e-mail addess (ES)** wprowadź adres e-mail użytkownika w formacie brittasimon\@contoso.com, a następnie wybierz przycisk **dalej**.

    ![Pole Dodaj adresy e-mail w sekcji Tworzenie użytkowników w HubSpot](./media/hubspot-tutorial/user3.png)

1. W sekcji **Tworzenie użytkowników** wybierz każdą kartę. Na każdej karcie Ustaw odpowiednie opcje i uprawnienia dla użytkownika. Następnie wybierz przycisk **dalej**.

    ![Karty w sekcji Tworzenie użytkowników w HubSpot](./media/hubspot-tutorial/user4.png)

1. Aby wysłać zaproszenie do użytkownika, wybierz pozycję **Wyślij**.

    ![Opcja wysyłania w HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Użytkownik jest aktywowany po zaakceptowaniu zaproszenia przez użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po skonfigurowaniu logowania jednokrotnego, gdy wybierzesz pozycję **HubSpot** w portalu My Apps, nastąpi automatyczne zalogowanie do HubSpot. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się z następującymi artykułami:

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
