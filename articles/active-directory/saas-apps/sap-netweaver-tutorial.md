---
title: 'Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6cce23194025a68eec055fe45b806c3d28434a1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880496"
---
# <a name="tutorial-integrate-sap-netweaver-with-azure-active-directory"></a>Samouczek: Integracja oprogramowania SAP NetWeaver z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować oprogramowanie SAP NetWeaver z usługą Azure Active Directory (Azure AD). Po zintegrowaniu integracji oprogramowania SAP NetWeaver z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rozwiązania SAP NetWeaver.
* Zezwól użytkownikom na automatyczne logowanie do oprogramowania SAP NetWeaver przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w systemie SAP NetWeaver.
* Oprogramowanie SAP NetWeaver w wersji co najmniej 7.20

## <a name="scenario-description"></a>Opis scenariusza

Rozwiązanie SAP NetWeaver obsługuje zarówno protokół **SAML** (**SSO zainicjowany przez usługę Sp**), jak i **OAuth**. W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

> [!NOTE]
> Skonfiguruj aplikację w języku SAML lub OAuth zgodnie z wymaganiami organizacji. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Dodawanie oprogramowania SAP NetWeaver z galerii

Aby skonfigurować integrację oprogramowania SAP NetWeaver z usługą Azure AD, należy dodać oprogramowanie SAP NetWeaver z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP NetWeaver** w polu wyszukiwania.
1. Wybierz pozycję **SAP NetWeaver** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla oprogramowania SAP NetWeaver

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP NetWeaver przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu SAP NetWeaver.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP NetWeaver, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj rozwiązanie SAP NetWeaver przy użyciu protokołu SAML](#configure-sap-netweaver-using-saml)** , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAP NetWeaver](#create-sap-netweaver-test-user)** , aby miał odpowiednik B. Simon w oprogramowaniu SAP NetWeaver, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj logowanie](#test-sso)** jednokrotne, aby sprawdzić, czy konfiguracja działa.
1. **[Skonfiguruj rozwiązanie SAP NetWeaver na potrzeby uwierzytelniania OAuth](#configure-sap-netweaver-for-oauth)** , aby skonfigurować ustawienia protokołu OAuth po stronie aplikacji.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP NetWeaver, wykonaj następujące kroki:

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy SAP NetWeaver jako administrator

2. Upewnij się, że usługi **http** i **https** są aktywne i że w kodzie transakcji **SMICM** są przypisane odpowiednie porty.

3. Zaloguj się do klienta biznesowego systemu SAP system (T01), w którym jest wymagane logowanie jednokrotne i aktywuj zarządzanie sesjami zabezpieczeń protokołu HTTP.

    a. Przejdź do sekcji **SICF_SESSIONS** kodu transakcji. Pokazuje ona wszystkie odpowiednie parametry profilu z bieżącymi wartościami. Wyglądają one podobnie do poniższych:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Dostosuj te parametry zgodnie z wymaganiami organizacji — powyższe parametry są tu podane jedynie jako przykład.

    b. W razie potrzeby skoryguj parametry w profilu wystąpienie/domyślny system SAP i uruchom ponownie system SAP.

    c. Kliknij dwukrotnie odpowiedni klient, aby włączyć sesję zabezpieczeń protokołu HTTP.

    ![Link pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktywuj poniższe usługi SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Przejdź do kodu transakcji **SAML2** w kliencie biznesowym systemu SAP [T01/122]. Spowoduje to otwarcie interfejsu użytkownika w przeglądarce. W tym przykładzie dla klienta biznesowego systemu SAP przyjmujemy wartość 122.

    ![Link pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Podaj nazwę użytkownika i hasło, które należy wprowadzić w interfejsie użytkownika, a następnie kliknij pozycję **Edit** (Edytuj).

    ![Link pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. W polu **Provider Name** (Nazwa dostawcy) zastąp ciąg T01122 ciągiem `http://T01122` i kliknij pozycję **Save** (Zapisz).

    > [!NOTE]
    > Domyślna nazwa dostawcy jest uznawana `<sid><client>` za format `<protocol>://<name>`, ale usługa Azure AD oczekuje nazwy w formacie, zaleca się, aby zachować nazwę dostawcy `https://<sid><client>` , tak aby zezwalała na konfigurowanie wielu aparatów ABAP SAP NetWeaver w usłudze Azure AD.

    ![Link pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Generowanie metadanych dostawcy usługi** — po skonfigurowaniu ustawień **dostawcy lokalnego** i **zaufanych dostawców** w interfejsie użytkownika SAML 2.0 następnym krokiem będzie wygenerowanie pliku metadanych dostawcy usług (który zawiera wszystkie ustawienia, kontekst uwierzytelniania i inne konfiguracje w systemie SAP). Gdy plik zostanie wygenerowany, należy przekazać go w usłudze Azure AD.

    ![Link pobierania certyfikatu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Przejdź do **karty dostawcy lokalnego**.

    b. Kliknij pozycję **Metadata** (Metadane).

    c. Zapisz wygenerowany **plik XML metadanych** na komputerze i przekaż go w sekcji **Podstawowa konfiguracja SAML** , aby automatycznie wypełnić wartości **identyfikatorów** i **adresów URL odpowiedzi** w Azure Portal.

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SAP NetWeaver** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. Kliknij przycisk **Przekaż plik metadanych** , aby przekazać **plik metadanych dostawcy usług**, który został uzyskany wcześniej.

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    c. Po pomyślnym przekazaniu pliku metadanych wartości **Identyfikator** i **Adres URL odpowiedzi** zostaną automatycznie wypełnione w polach tekstowych sekcji **Podstawowa konfiguracja protokołu SAML**, jak pokazano poniżej:

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Zaobserwowaliśmy, że niektórzy klienci zgłaszają błąd polegający na tym, że adres URL odpowiedzi skonfigurowany dla ich wystąpienia jest nieprawidłowy. Jeśli wystąpi taki błąd, możesz użyć następującego skryptu programu PowerShell jako obejścia w celu ustawienia prawidłowego adresu URL odpowiedzi dla wystąpienia:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > Identyfikator obiektu ServicePrincipal musisz najpierw ustawić samodzielnie lub możesz również przekazać go w tym miejscu.

1. Aplikacja SAP NetWeaver oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

13. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()** .

    c. Z listy **Parametr 1** wybierz pozycję **user.userprinicipalname**.

    d. Kliknij polecenie **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie rozwiązania SAP NetWeaver** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do rozwiązania SAP NetWeaver.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **SAP NetWeaver**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-sap-netweaver-using-saml"></a>Konfigurowanie rozwiązania SAP NetWeaver przy użyciu protokołu SAML

1. Zaloguj się do systemu SAP i przejdź do kodu transakcji SAML2. Spowoduje to otwarcie nowego okna przeglądarki z ekranem konfiguracji protokołu SAML.

2. Aby skonfigurować punkty końcowe dla zaufanego dostawcy tożsamości (Azure AD), przejdź na kartę **Trusted Providers** (Zaufani dostawcy).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Naciśnij pozycję **Add** (Dodaj) i wybierz polecenie **Upload Metadata File** (Przekaż plik metadanych) z menu kontekstowego.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Przekaż plik metadanych, który został pobrany z witryny Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Na następnym ekranie wpisz nazwę aliasu. Na przykład aadsts i naciśnij przycisk **dalej** , aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Upewnij się, że w polu **Digest Algorithm** (Algorytm porządkowania ) znajduje się wartość **SHA-256** i że nie są wymagane żadne zmiany, a następnie naciśnij pozycję **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Na stronie **Single Sign-On Endpoints** (Punkty końcowe logowania jednokrotnego) użyj opcji **HTTP POST** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Na stronie **Single Logout Endpoints** (Punkty końcowe wylogowania jednokrotnego) wybierz opcję **HTTPRedirect** i kliknij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Na stronie **Artifact Endpoints** (Punkty końcowe artefaktu) naciśnij przycisk **Next** (Dalej), aby kontynuować.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Na stronie **Authentication Requirements** (Wymagania dotyczące uwierzytelniania) kliknij przycisk **Finish** (Zakończ).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Przejdź do karty **Trusted Provider (Zaufany dostawca)**  > **Identity Federation (Federacja tożsamości)** (na dole ekranu). Kliknij pozycję **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Kliknij przycisk **Add** (Dodaj) na karcie **Identity Federation** (Federacja tożsamości) (dolne okno).

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. W oknie podręcznym wybierz opcję **nieokreślone** w **obsługiwanych formatach NameID** , a następnie kliknij przycisk OK.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Zauważ, że wartości **źródła identyfikatora użytkownika** i **trybu mapowania identyfikatora użytkownika** określają połączenie między użytkownikiem systemu SAP i oświadczeniem usługi Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenariusz: mapowanie użytkownika systemu SAP na użytkownika usługi Azure AD.

    a. Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenariusz: wybieranie identyfikatora użytkownika systemu SAP na podstawie skonfigurowanego adresu e-mail w rozwiązaniu SU01. W tym przypadku identyfikator poczty e-mail należy skonfigurować w rozwiązaniu su01 dla każdego użytkownika, który wymaga logowania jednokrotnego.

    a.  Zrzut ekranu przedstawiający szczegóły identyfikatora nazwy w systemie SAP.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Zrzut ekranu przedstawiający wymagane oświadczenia z usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Kliknij przycisk **Zapisz**, a następnie kliknij przycisk **Włącz**, aby włączyć dostawcę tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/configuration1.png)

16. Po wyświetleniu monitu kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Tworzenie użytkownika testowego oprogramowania SAP NetWeaver

    W tej sekcji utworzysz użytkownika o nazwie B. Simon w oprogramowaniu SAP NetWeaver. Aby dodać użytkownika na platformie SAP NetWeaver, skontaktuj się z zespołem ekspertów ds. systemu SAP w swojej organizacji lub z odpowiednim partnerem firmy SAP.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

1. Po aktywowaniu usługi Azure AD jako dostawcy tożsamości spróbuj uzyskać dostęp do poniższego adresu URL, aby sprawdzić działanie logowania jednokrotnego (nie zostanie wyświetlony monit o nazwę użytkownika i hasło)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (lub) użyj poniższego adresu URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Zastąp ciąg „sapurl” faktyczną nazwą hosta systemu SAP.

2. Powyższy adres URL powinien przenieść Cię na pokazany poniżej ekran. Jeśli poniższa strona zostanie wyświetlona, oznacza to, że konfiguracja logowania jednokrotnego usługi Azure AD została przeprowadzona pomyślnie.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/testingsso.png)

3. Jeśli zostanie wyświetlony monit o nazwę użytkownika i hasło, zdiagnozuj problem, włączając śledzenie przy użyciu poniższego adresu URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Konfigurowanie oprogramowania SAP NetWeaver na potrzeby uwierzytelniania OAuth

1. Proces udokumentowanych rozwiązań SAP jest dostępny w lokalizacji: [Tworzenie zakresu i uwierzytelnianie OAuth 2,0 usługi bramy NetWeaver](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Przejdź do SPRO i Znajdź pozycję **Aktywuj i obsługuj usługi**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth01.png)

3. W tym przykładzie chcemy połączyć usługę OData: `DAAG_MNGGRP` z uwierzytelnianiem OAuth na potrzeby rejestracji jednokrotnej usługi Azure AD. Użyj nazwy usługi technicznej Wyszukaj usługę `DAAG_MNGGRP` i aktywuj ją, jeśli jeszcze nie jest aktywna, lub `green` Wyszukaj stan na karcie węzły zapory ICF). Upewnij się, że alias systemowy (podłączony system wewnętrznej bazy danych, gdzie usługa jest rzeczywiście uruchomiona) jest poprawny.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth02.png)

    * Następnie kliknij pozycję przycisk **uwierzytelniania OAuth** na górnym pasku przycisków `scope` i przypisz (Zachowaj nazwę domyślną jako oferowaną).

4. W naszym przykładzie zakres jest `DAAG_MNGGRP_001`generowany na podstawie nazwy usługi przez automatyczne dodanie numeru. Można `/IWFND/R_OAUTH_SCOPES` użyć raportu, aby zmienić nazwę zakresu lub utworzyć ręcznie.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Message `soft state status is not supported` — można zignorować, ponieważ nie ma problemu. Więcej informacji można znaleźć [tutaj](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Tworzenie użytkownika usługi dla klienta OAuth 2,0

1. OAuth2 używa elementu `service ID` , aby uzyskać token dostępu dla użytkownika końcowego w jego imieniu. Ważne ograniczenie przez projektowanie protokołu OAuth: `OAuth 2.0 Client ID` musi być taka sama jak `username` w przypadku logowania przy użyciu klienta OAuth 2,0 podczas żądania tokenu dostępu. W związku z tym w naszym przykładzie będziemy rejestrować klienta protokołu OAuth 2,0 o nazwie KLIENT1, a w systemie SAP musi istnieć użytkownik o tej samej nazwie (KLIENT1), a użytkownik zostanie skonfigurowany do użycia przez określoną aplikację. 

2. Podczas rejestrowania klienta OAuth korzystamy z programu `SAML Bearer Grant type`.

    >[!NOTE]
    >Aby uzyskać więcej informacji, zapoznaj się z artykułem rejestracja klienta OAuth 2,0 dla typu dotacji elementu SAML w [tym miejscu](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01/Utwórz użytkownika KLIENT1 jako `System type` i Przypisz hasło, Zapisz go jako wymagające podania poświadczeń do programisty interfejsu API, który powinien go nagrać przy użyciu nazwy użytkownika do kodu wywołującego. Nie należy przypisywać profilu ani roli.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Rejestrowanie nowego identyfikatora klienta OAuth 2,0 przy użyciu Kreatora tworzenia

1. Aby zarejestrować nowy **SOAUTH2**Transaction **klienta OAuth 2,0** . Zostanie wyświetlona informacja o klientach OAuth 2,0, które zostały już zarejestrowane. Wybierz pozycję **Utwórz** , aby uruchomić kreatora dla nowego klienta OAuth o nazwie "CLIENT1" w tym przykładzie.

2. Przejdź do kodu T: **SOAUTH2** i podaj opis, a następnie kliknij przycisk **dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth05.png)

3. Wybierz już dodany **SAML2 dostawcy tożsamości — Azure AD** z listy rozwijanej i Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth06.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth07.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth08.png)

4. Kliknij pozycję **Dodaj** w obszarze przypisanie zakresu, aby dodać wcześniej utworzony zakres:`DAAG_MNGGRP_001`

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth09.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sapnetweaver-tutorial/oauth10.png)

5. Kliknij przycisk **Zakończ**.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)