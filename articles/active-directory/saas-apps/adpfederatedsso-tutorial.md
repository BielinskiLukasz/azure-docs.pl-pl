---
title: 'Samouczek: integracja usługi Azure Active Directory z rozwiązaniem ADP | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rozwiązaniem ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.openlocfilehash: 487c9cb145000b92a4aa664ea2bd159026104b6b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065158"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem ADP

Z tego samouczka dowiesz się, jak zintegrować rozwiązanie ADP z usługą Azure Active Directory (Azure AD).
Zintegrowanie rozwiązania ADP z usługą Azure AD zapewnia następujące korzyści:

* Możliwość kontrolowania dostępu do rozwiązania ADP za pomocą usługi Azure AD.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do rozwiązania ADP (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z rozwiązaniem ADP potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja rozwiązania ADP z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie ADP obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-adp-from-the-gallery"></a>Dodawanie rozwiązania ADP z galerii

Aby skonfigurować integrację rozwiązania ADP z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązanie ADP z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ADP**, wybierz pozycję **ADP** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Pozycja ADP na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z rozwiązaniem ADP, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem rozwiązania ADP.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z rozwiązaniem ADP, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w rozwiązaniu ADP](#configure-adp-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego rozwiązania ADP](#create-adp-test-user)** — aby w rozwiązaniu ADP istniał odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z rozwiązaniem ADP, wykonaj następujące kroki:

1. W witrynie Azure Portal na stronie integracji aplikacji **ADP** kliknij kartę  **Właściwości**, a następnie wykonaj następujące kroki: 

    ![Właściwości logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ustaw pole **Możliwe logowanie użytkowników** na wartość **Tak**.

    b. Skopiuj wartość pola **Adres URL dostępu użytkownika** i wklej ją w **sekcji Adres URL logowania jednokrotnego**, co zostało opisane w dalszej części tego samouczka.

    d. Ustaw pole **Wymagane przypisanie użytkownika** na wartość **Tak**.

    d. Ustaw pole **Widoczne dla użytkowników** na wartość **Nie**.

2. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **ADP** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

3. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

5. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL rozwiązania ADP na potrzeby logowania jednokrotnego](common/idp-identifier.png)

    W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://fed.adp.com`

6. Rozwiązanie ADP oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**. Nazwą oświadczenia będzie zawsze **„PersonImmutableID”**, a jego wartość będzie mapowana na element **employeeid**. 

    Mapowanie użytkowników z usługi Azure AD na rozwiązanie ADP będzie odbywać w ramach elementu **employeeid**, ale można je zamapować na inną wartość zgodnie z ustawieniami aplikacji. Dlatego prosimy o skontaktowanie się najpierw z [zespołem pomocy technicznej rozwiązania ADP](https://www.adp.com/contact-us/overview.aspx) w celu użycia prawidłowego identyfikatora użytkownika i zamapowania tej wartości z oświadczeniem **„PersonImmutableID”**.

    ![image](common/edit-attribute.png)

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:
    
    | Name (Nazwa) | Atrybut źródłowy | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

    > [!NOTE] 
    > Przed skonfigurowaniem asercji SAML należy skontaktować się z [zespołem pomocy technicznej rozwiązania ADP](https://www.adp.com/contact-us/overview.aspx) i zażądać wartości unikatowego atrybutu identyfikatora użytkownika dla dzierżawy. Ta wartość jest wymagana do skonfigurowania oświadczenia niestandardowego na potrzeby aplikacji. 

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w rozwiązaniu ADP

Aby skonfigurować logowanie jednokrotne po stronie rozwiązania **ADP**, należy przekazać pobrany **plik XML metadanych** w [witrynie internetowej rozwiązania ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ten proces może potrwać kilka dni.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurowanie usług ADP pod kątem dostępu federacyjnego

>[!Important]
> Twoi pracownicy, dla których wymagany jest dostęp federacyjny do usług ADP, muszą zostać przypisani do aplikacji usługi ADP, co wiąże się z koniecznością ponownego przypisania użytkowników do określonej usługi ADP.
Po otrzymaniu potwierdzenia od przedstawiciela firmy ADP skonfiguruj swoje usługi ADP, a następnie przypisz użytkowników lub zarządzaj nimi, aby kontrolować ich dostęp do określonej usługi ADP.

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ADP**, wybierz pozycję **ADP** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Pozycja ADP na liście wyników](common/search-new-app.png)

5. W witrynie Azure Portal na stronie integracji aplikacji **ADP** kliknij kartę  **Właściwości**, a następnie wykonaj następujące kroki:  

    ![Połączone właściwości logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ustaw pole **Możliwe logowanie użytkowników** na wartość **Tak**.

    b.  Ustaw pole **Wymagane przypisanie użytkownika** na wartość **Tak**.

    d.  Ustaw pole **Widoczne dla użytkowników** na wartość **Tak**.

6. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **ADP** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

7. W oknie dialogowym **Wybierz metodę logowania jednokrotnego** w polu **Tryb** wybierz pozycję **Połączony**. w celu połączenia aplikacji z rozwiązaniem **ADP**.

    ![Połączone logowanie jednokrotne](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Przejdź do sekcji **Konfigurowanie adresu URL logowania**, a następnie wykonaj następujące kroki:

    ![Właściwość logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Wklej wartość pola **Adres URL dostępu użytkownika**, która została skopiowana z przedstawionej powyżej **karty właściwości** (z głównej aplikacji ADP).
                                                             
    b. Poniżej przedstawiono 5 aplikacji, które obsługują różne **adresy URL stanu przekaźnika**. Należy ręcznie dołączyć odpowiednią wartość **adresu URL stanu przekaźnika** dla określonej aplikacji w polu **Adres URL dostępu użytkownika**.
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Zapisz** zmiany.

10. Po otrzymaniu potwierdzenia od przedstawiciela firmy ADP rozpocznij testowanie przez jednego lub dwóch użytkowników.

    a. Przypisz kilku użytkowników do aplikacji usługi ADP, aby przetestować dostęp federacyjny.

    b. Test zostanie zakończony pomyślnie, gdy użytkownik uzyska dostęp do aplikacji usługi ADP w galerii i będzie mógł uzyskać dostęp do usługi ADP.
 
11. Jako potwierdzenie pomyślnego wykonania testu przypisz federacyjną usługę ADP do poszczególnych użytkowników lub grup użytkowników, co zostało wyjaśnione w dalszej części tego samouczka, i przekaż o tym informacje swoim pracownikom.
 
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do rozwiązania ADP.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **ADP**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz **ADP** i wybierz odpowiednią pozycję.

    ![Link do rozwiązania ADP na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-adp-test-user"></a>Tworzenie użytkownika testowego rozwiązania ADP

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w rozwiązaniu ADP. Skontaktuj się z [zespołem pomocy technicznej rozwiązania ADP](https://www.adp.com/contact-us/overview.aspx) w celu dodania użytkowników w ramach konta rozwiązania ADP. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ADP w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania ADP, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

