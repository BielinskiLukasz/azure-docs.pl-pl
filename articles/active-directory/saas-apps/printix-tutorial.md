---
title: 'Samouczek: integracja Azure Active Directory z usługą Printix | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e883833f7998c073b574c892ed5c7777e01faab4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "62111459"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Samouczek: integracja Azure Active Directory z usługą Printix

W tym samouczku dowiesz się, jak zintegrować usługę Printix z usługą Azure Active Directory (Azure AD).

Integracja Printix z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Printix
- Możesz umożliwić użytkownikom automatyczne uzyskiwanie zalogowanych do Printix (Logowanie jednokrotne) przy użyciu kont usługi Azure AD
- Kontami można zarządzać w jednej centralnej lokalizacji — Azure Portal

Jeśli chcesz dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Printix, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja z włączonym logowaniem jednokrotnym w usłudze Printix

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska w wersji próbnej usługi Azure AD, możesz w [tym miejscu](https://azure.microsoft.com/pricing/free-trial/)uzyskać miesięczną wersję próbną.

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku przetestujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Printix z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD

## <a name="adding-printix-from-the-gallery"></a>Dodawanie Printix z galerii
Aby skonfigurować integrację programu Printix z usługą Azure AD, musisz dodać Printix z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Printix z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacji dla przedsiębiorstw**. Następnie przejdź do **wszystkich aplikacji**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Printix**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/tutorial_printix_search.png)

1. W panelu wyników wybierz pozycję **Printix**, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD
W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Printix na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby logowanie jednokrotne działało, usługa Azure AD musi znać, co odpowiedni użytkownik w Printix, jest użytkownikiem w usłudze Azure AD. Innymi słowy, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Printix.

W programie Printix Przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość parametru **username** , aby określić relację łącza.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Printix, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configuring-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Printix](#creating-a-printix-test-user)** — Aby uzyskać odpowiednik Britta Simon w Printix, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)** — aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie rejestracji](#testing-single-sign-on)** jednokrotnej — aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne w usłudze Azure AD w Azure Portal i skonfiguruj Logowanie jednokrotne w aplikacji Printix.

**Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Printix, wykonaj następujące czynności:**

1. W Azure Portal na stronie integracja aplikacji **Printix** kliknij pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. W oknie dialogowym **Logowanie** jednokrotne wybierz pozycję **tryb** jako **Logowanie oparte na protokole SAML** , aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. W sekcji **domena i adresy URL Printix** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_url.png)

    W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca:`https://<subdomain>.printix.net`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta Printix](mailto:support@printix.net) , aby uzyskać wartość. 
 
1. W sekcji **certyfikat podpisywania SAML** kliknij pozycję **metadane XML** , a następnie Zapisz plik metadanych na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_general_400.png)

1. Zaloguj się do dzierżawy Printix jako administrator.

1. W menu u góry kliknij ikonę w prawym górnym rogu, a następnie wybierz pozycję "**uwierzytelnianie**".
   
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_06.png)

1. Na karcie **Konfiguracja** wybierz opcję **Włącz uwierzytelnianie Azure/Office 365**
   
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_07.png)

1. Na karcie **Azure** wprowadź adres URL metadanych Federacji do pola tekstowego "**dokument metadanych Federacji**". 

    Dołącz plik XML metadanych, który został pobrany z usługi Azure AD do [zespołu pomocy technicznej Printix](mailto:support@printix.net). Następnie przekażą plik XML i podają adres URL metadanych Federacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Kliknij przycisk "**test**", a następnie kliknij przycisk**OK**, jeśli test zakończył się pomyślnie.
   
     Po kliknięciu przycisku **Testuj** zostanie wyświetlona strona usługi Azure Active Directory. "Test zakończył się pomyślnie" oznacza, że po wprowadzeniu poświadczeń konta testowego platformy Azure zostanie wyświetlony komunikat "Ustawienia przetestowane OK". Następnie kliknij przycisk **OK** .
   
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_09.png)

1. Kliknij przycisk **Zapisz** na stronie "**uwierzytelnianie**".


> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji ** Konfiguracja** w dolnej części strony. Więcej informacji na temat funkcji osadzonej dokumentacji można znaleźć tutaj: [Dokumentacja usługi Azure AD Embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **Azure Portal**w okienku nawigacji po lewej stronie kliknij ikonę **Azure Active Directory** .

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do pozycji **Użytkownicy i grupy** , a następnie kliknij pozycję **Wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/create_aaduser_02.png) 

1. Aby otworzyć okno dialogowe **użytkownika** , kliknij przycisk **Dodaj** w górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/create_aaduser_03.png) 

1. Na stronie **użytkownika** wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/create_aaduser_04.png) 

    a. W polu tekstowym **Nazwa** wpisz **BrittaSimon**.

    b. W polu tekstowym **Nazwa użytkownika** wpisz **adres e-mail** BrittaSimon.

    c. Wybierz pozycję **Pokaż hasło** i Zapisz wartość **hasła**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-printix-test-user"></a>Tworzenie użytkownika testowego Printix

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w Printix. Printix obsługuje obsługę just-in-Time, która jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Nowy użytkownik zostanie utworzony podczas próby uzyskania dostępu do Printix, jeśli jeszcze nie istnieje. 

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Printix.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do Printix, wykonaj następujące czynności:**

1. W Azure Portal Otwórz widok aplikacje, a następnie przejdź do widoku katalogu i przejdź do pozycji **aplikacje przedsiębiorstwa** , a następnie kliknij pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście Aplikacje wybierz pozycję **Printix**.

    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_app.png) 

1. W menu po lewej stronie kliknij pozycję **Użytkownicy i grupy**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Add** (Dodaj). Następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodaj przypisanie** .

    ![Przypisz użytkownika][203]

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **Wybierz** w oknie dialogowym **Użytkownicy i grupy** .

1. Kliknij przycisk **Assign (Przypisz** ) w oknie dialogowym **Dodaj przypisanie** .
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Printix w panelu dostępu należy automatycznie zalogować się do aplikacji Printix.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

