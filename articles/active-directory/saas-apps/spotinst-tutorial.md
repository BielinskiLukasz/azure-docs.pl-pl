---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Spotinst | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0bffdf439a192fb10fe695fbfa18e8c7abf8077
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57846688"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Spotinst

W tym samouczku dowiesz się, jak zintegrować Spotinst w usłudze Azure Active Directory (Azure AD).

Integrowanie Spotinst z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Spotinst.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Spotinst (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Spotinst, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Spotinst logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Spotinst z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-spotinst-from-the-gallery"></a>Dodawanie Spotinst z galerii
Aby skonfigurować integrację Spotinst w usłudze Azure AD, należy dodać Spotinst z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Spotinst z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Spotinst**, wybierz opcję **Spotinst** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Spotinst na liście wyników](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Spotinst w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Spotinst do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Spotinst musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Spotinst, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego Spotinst](#create-a-spotinst-test-user)**  — aby odpowiednikiem Britta Simon w Spotinst połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Spotinst.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Spotinst, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Spotinst** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. Na **Spotinst domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w trybie zainicjował dostawcy tożsamości należy wykonać następujące czynności:

    ![Spotinst domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**.

    b. W **tan przekaźnika** pole tekstowe, wpisz wartość: `<ID>`

    c. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, w **adres URL logowania** pole tekstowe, wpisz adres URL: `https://console.spotinst.com`

    > [!NOTE]
    > Wartość stanu przekazywania nie jest prawdziwe. Wartość stanu przekazywania zostaną zaktualizowane o wartość tan przekaźnika rzeczywistą zostało wyjaśnione w dalszej części tego samouczka.

4. Aplikacja Spotinst oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład dla niego.

    ![Konfigurowanie logowania jednokrotnego](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    e. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/spotinst-tutorial/tutorial_general_400.png)

8. W oknie przeglądarki internetowej innej, zaloguj się do Spotinst jako Administrator zabezpieczeń.

9. Kliknij pozycję **ikony użytkownika** w prawej górnej części ekranu i kliknij przycisk **ustawienia**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. Kliknij pozycję **zabezpieczeń** u góry, a następnie wybierz pozycję **dostawców tożsamości** i wykonaj następujące czynności:

    ![Spotinst zabezpieczeń](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopiuj **tan przekaźnika** wartości dla swojego wystąpienia, a następnie wklej je **tan przekaźnika** polu tekstowym w **Spotinst domena i adresy URL** sekcji w witrynie Azure portal.

    b. Kliknij przycisk **PRZEGLĄDAJ** można przekazać pliku xml metadanych, który został pobrany z witryny Azure portal

    c. Kliknij przycisk **SAVE** (Zapisz).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/spotinst-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/spotinst-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/spotinst-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/spotinst-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-spotinst-test-user"></a>Tworzenie użytkownika testowego Spotinst

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Spotinst.

1. Po skonfigurowaniu aplikacji w **SP** tryb intiated, wykonaj następujące czynności:

   a. W oknie przeglądarki internetowej innej, zaloguj się do Spotinst jako Administrator zabezpieczeń.

   b. Kliknij pozycję **ikony użytkownika** w prawej górnej części ekranu i kliknij przycisk **ustawienia**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Kliknij przycisk **użytkowników** i wybierz **Dodaj użytkownika**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. W sekcji Dodaj użytkownika wykonaj następujące czynności:

    ![Ustawienia Spotinst](./media/spotinst-tutorial/adduser2.png)

    * W **imię i nazwisko** polu tekstowym Wprowadź pełną nazwę użytkownika, takich jak **BrittaSimon**.

    * W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon\@contoso.com**.

    * Wybierz swoje szczegóły specyficzne dla organizacji **kont, roli konta i roli organizacji**.

2. Po skonfigurowaniu aplikacji w **tożsamości** intiated trybie miejsca jest nie czynność do wykonania dla Ciebie w tej sekcji. Spotinst obsługę just-in-time, który jest domyślnie włączona. Nowy użytkownik jest tworzony podczas próby dostępu Spotinst, jeśli go jeszcze nie istnieje.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Spotinst.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Spotinst, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Spotinst**.

    ![Link Spotinst na liście aplikacji](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Spotinst w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Spotinst.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

