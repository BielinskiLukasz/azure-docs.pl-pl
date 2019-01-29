---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Fieldglass | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Fieldglass.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2510195f-d5b1-4684-b3da-283fb8619df2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: cf0cb36e157aeb0e74c530fc8d0fac7f355832d0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193067"
---
# <a name="tutorial-azure-active-directory-integration-with-fieldglass"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Fieldglass

W tym samouczku dowiesz się, jak zintegrować Fieldglass w usłudze Azure Active Directory (Azure AD).

Integrowanie Fieldglass z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Fieldglass
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Fieldglass (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Fieldglass, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Fieldglass logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Fieldglass z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-fieldglass-from-the-gallery"></a>Dodawanie Fieldglass z galerii
Aby skonfigurować integrację Fieldglass w usłudze Azure AD, należy dodać Fieldglass z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Fieldglass z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Fieldglass**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/fieldglass-tutorial/tutorial_fieldglass_search.png)

1. W panelu wyników wybierz **Fieldglass**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/fieldglass-tutorial/tutorial_fieldglass_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Fieldglass w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Fieldglass do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Fieldglass musi można ustanowić.

W Fieldglass, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Fieldglass, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Fieldglass](#creating-a-fieldglass-test-user)**  — aby odpowiednikiem Britta Simon w Fieldglass połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Fieldglass.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Fieldglass, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Fieldglass** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/fieldglass-tutorial/tutorial_fieldglass_samlbase.png)

1. Na **Fieldglass domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/fieldglass-tutorial/tutorial_fieldglass_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL jako `https://www.fieldglass.com` lub zgodne ze wzorcem:  `https://<company name>.fgvms.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:
    | |
    |--|
    | `https://www.fieldglass.net/<company name>`|
    | `https://<company name>.fgvms.com/<company name>`|

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołu pomocy technicznej Fieldglass](https://www.fieldglass.com/customer-support) do uzyskania tych wartości.
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/fieldglass-tutorial/tutorial_fieldglass_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/fieldglass-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Fieldglass** , kliknij przycisk **skonfigurować Fieldglass** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/fieldglass-tutorial/tutorial_fieldglass_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **Fieldglass** stronie, musisz wysłać pobrany **Certificate(Base64)** i **adres URL wylogowania, identyfikator jednostki SAML** do [ Zespołu pomocy technicznej Fieldglass](https://www.fieldglass.com/customer-support). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/fieldglass-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/fieldglass-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/fieldglass-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/fieldglass-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-fieldglass-test-user"></a>Tworzenie użytkownika testowego Fieldglass

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w FieldGlass. Skontaktuj się z Twojego [zespołu pomocy technicznej Fieldglass](https://www.fieldglass.com/customer-support) Aby dodać użytkowników w ramach konta Fieldglass.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Fieldglass.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Fieldglass, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Fieldglass**.

    ![Konfigurowanie logowania jednokrotnego](./media/fieldglass-tutorial/tutorial_fieldglass_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Fieldglass w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Fieldglass.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fieldglass-tutorial/tutorial_general_01.png
[2]: ./media/fieldglass-tutorial/tutorial_general_02.png
[3]: ./media/fieldglass-tutorial/tutorial_general_03.png
[4]: ./media/fieldglass-tutorial/tutorial_general_04.png

[100]: ./media/fieldglass-tutorial/tutorial_general_100.png

[200]: ./media/fieldglass-tutorial/tutorial_general_200.png
[201]: ./media/fieldglass-tutorial/tutorial_general_201.png
[202]: ./media/fieldglass-tutorial/tutorial_general_202.png
[203]: ./media/fieldglass-tutorial/tutorial_general_203.png

