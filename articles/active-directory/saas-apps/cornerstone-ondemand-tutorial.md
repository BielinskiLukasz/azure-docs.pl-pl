---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z OnDemandiem kamień Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Cornerstone OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 363b5b2dc2891166f779c98c6de1487bc45047a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z OnDemandą kamień

W tym samouczku dowiesz się, jak zintegrować OnDemand Kamieńal z usługą Azure Active Directory (Azure AD). Gdy integrujesz OnDemand kamień z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do OnDemandu kamień.
* Zezwól użytkownikom na automatyczne logowanie do OnDemandu kamień z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) OnDemand.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Cornerstone OnDemand obsługuje logowanie jednokrotne inicjowane przez **SP**
* Aplikacja Cornerstone OnDemand obsługuje [zautomatyzowane inicjowanie obsługi użytkowników](cornerstone-ondemand-provisioning-tutorial.md)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Dodawanie aplikacji Cornerstone OnDemand z galerii

Aby skonfigurować integrację aplikacji Cornerstone OnDemand z usługą Azure AD, należy dodać aplikację Cornerstone OnDemand z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **kamień OnDemand** w polu wyszukiwania.
1. Wybierz pozycję **kamień węgielny OnDemand** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cornerstone-ondemand"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla OnDemand kamień

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą OnDemand kamień przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w OnDemandach.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą OnDemandi, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Konfigurowanie ustawień logowania JEDNOkrotnego OnDemand](#configure-cornerstone-ondemand-sso)** na stronie aplikacji.
    1. **[Utwórz użytkownika OnDemand testowego](#create-cornerstone-ondemand-test-user)** , aby dysponować odpowiednikiem B. Simon w kamień węgielny OnDemand, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **OnDemand** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company>.csod.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `https://<company>.csod.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając rzeczywistego adresu URL logowania i identyfikatora. Skontaktuj się z [zespołem pomocy technicznej aplikacji Cornerstone OnDemand Client](mailto:moreinfo@csod.com), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Ustaw OnDemand węgielal** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do OnDemand kamień.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **kamień OnDemand**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-cornerstone-ondemand-sso"></a>Konfigurowanie samoOnDemandowego logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Cornerstone OnDemand**, należy wysłać pobrany **certyfikat (Base64)** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej aplikacji Cornerstone OnDemand](mailto:moreinfo@csod.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-cornerstone-ondemand-test-user"></a>Tworzenie użytkownika testowego aplikacji Cornerstone OnDemand

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w OnDemand kamień. Aplikacja Cornerstone OnDemand obsługuje automatyczne inicjowanie obsługi użytkowników, które jest domyślnie włączone. Więcej szczegółów dotyczących konfigurowania automatycznej aprowizacji użytkowników można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/saas-apps/cornerstone-ondemand-provisioning-tutorial).

**Jeśli musisz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

Aby skonfigurować Inicjowanie obsługi użytkowników, Wyślij informacje o użytkowniku usługi Azure AD, który chcesz udostępnić do [zespołu pomocy technicznej OnDemand](mailto:moreinfo@csod.com).

>[!NOTE]
>Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników i interfejsów API OnDemand.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Cornerstone OnDemand w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Cornerstone OnDemand, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj OnDemande węgielne z usługą Azure AD](https://aad.portal.azure.com)
