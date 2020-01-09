---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Amazon Web Services (AWS) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługami Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 758b14a53927e00a5546147f8d67e556b45acc3c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689776"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Amazon Web Services (AWS)

W tym samouczku dowiesz się, jak zintegrować usługę Amazon Web Services (AWS) z usługą Azure Active Directory (Azure AD). Podczas integrowania Amazon Web Services (AWS) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Amazon Web Services (AWS).
* Zezwól użytkownikom na automatyczne logowanie do Amazon Web Services (AWS) przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagram relacji usług Azure AD i AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Można skonfigurować wiele identyfikatorów dla wielu wystąpień. Przykład:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Za pomocą tych wartości usługa Azure AD usuwa wartość **#** i wysyła poprawną wartość `https://signin.aws.amazon.com/saml` jako adres URL odbiorców w tokenie SAML.

Zalecamy to podejście z następujących powodów:

- Każda aplikacja zapewnia unikatowy certyfikat x509. Każde wystąpienie wystąpienia aplikacji AWS może następnie mieć inną datę wygaśnięcia certyfikatu, która może być zarządzana na poszczególnych kontach AWS. Ogólnie Przerzucanie certyfikatów jest w tym przypadku łatwiejsze.

- Możesz włączyć Inicjowanie obsługi użytkowników przy użyciu aplikacji AWS w usłudze Azure AD, a następnie Nasza usługa pobiera wszystkie role z tego konta AWS. Nie trzeba ręcznie dodawać ani aktualizować ról usług AWS dla aplikacji.

- Właściciela aplikacji można przypisać osobno dla aplikacji. Ta osoba może zarządzać aplikacją bezpośrednio w usłudze Azure AD.

> [!Note]
> Upewnij się, że używasz tylko aplikacji w galerii.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi AWS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi Amazon Web Services (AWS) obsługują logowanie jednokrotne inicjowane przez **dostawcę usługi i dostawcę tożsamości**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii

Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego, konta szkoły lub konto Microsoft osobistych.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przegląd Azure Active Directory wybierz pozycję **aplikacje przedsiębiorstwa** > **wszystkie aplikacje**.
1. Wybierz pozycję **Nowa aplikacja** , aby dodać aplikację.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Web Services (AWS)** w polu wyszukiwania.
1. Wybierz pozycję **Amazon Web Services (AWS)** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Amazon Web Services (AWS)

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Amazon Web Services (AWS) przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Amazon Web Services (AWS).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Amazon Web Services (AWS), wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Amazon Web Services (AWS) logowanie JEDNOkrotne](#configure-amazon-web-services-aws-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** , aby uzyskać odpowiednik B. Simon w Amazon Web Services (AWS), który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
    1. **[Jak skonfigurować Inicjowanie obsługi administracyjnej w Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Amazon Web Services (AWS)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana, a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, wybierając pozycję **Zapisz**.

1. W przypadku konfigurowania więcej niż jednego wystąpienia podaj wartość identyfikatora. W drugim wystąpieniu, użyj następującego formatu, łącznie ze znakiem **#** , aby określić unikatową wartość SPN.

    `https://signin.aws.amazon.com/saml#2`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **konfigurowanie Amazon Web Services (AWS)** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przegląd Azure Active Directory wybierz pozycję **użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do Amazon Web Services (AWS).

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przegląd Azure Active Directory wybierz pozycję **aplikacje przedsiębiorstwa** > **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Amazon Web Services (AWS)** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-amazon-web-services-aws-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Amazon Web Services (AWS)

1. W innym oknie przeglądarki Zaloguj się do witryny firmy AWS jako administrator.

2. Wybierz pozycję **AWS Home**.

    ![Zrzut ekranu przedstawiający witrynę firmy AWS z wyróżnioną ikoną główną AWS][11]

3. Wybierz pozycję **Zarządzanie tożsamościami i dostępem**.

    ![Zrzut ekranu przedstawiający stronę usług AWS Services z wyróżnioną pozycją IAM][12]

4. Wybierz pozycję **dostawcy tożsamości** > **Utwórz dostawcę**.

    ![Zrzut ekranu przedstawiający stronę mapy][13]

5. Na stronie **Konfiguruj dostawcę** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający konfigurację dostawcy][14]

    a. W obszarze **Typ dostawcy**wybierz pozycję **SAML**.

    b. W obszarze **Nazwa dostawcy**wpisz nazwę dostawcy (na przykład: *WAAD*).

    d. Aby przekazać pobrany **plik metadanych** z Azure Portal, wybierz pozycję **Wybierz plik**.

    d. Wybierz **Następny krok**.

6. Na stronie **Weryfikowanie informacji o dostawcy** wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający Weryfikowanie informacji o dostawcy, z wyróżnioną pozycją Utwórz][15]

7. Wybierz pozycję **role** > **Utwórz rolę**.

    ![Zrzut ekranu strony ról][16]

8. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Zrzut ekranu przedstawiający stronę tworzenie roli][19]

    a. W obszarze **Wybierz typ zaufanej jednostki**wybierz pozycję **SAML 2,0 Federation**.

    b. W obszarze **Wybierz dostawcę saml 2,0**wybierz wcześniej utworzony **dostawca SAML** (na przykład: *WAAD*).

    d. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Wybierz pozycję **Dalej: uprawnienia**.

9. W oknie dialogowym **Dołącz zasady uprawnień** Dołącz odpowiednie zasady dla swojej organizacji. Następnie wybierz kolejno pozycje **Dalej: przegląd**.  

    ![Zrzut ekranu przedstawiający okno dialogowe zasady dołączania uprawnień][33]

10. W oknie dialogowym **Przegląd** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe przegląd][34]

    a. W polu **Nazwa roli**wprowadź nazwę roli.

    b. W polu **Opis roli**wprowadź opis.

    d. Wybierz pozycję **Utwórz rolę**.

    d. Utwórz dowolną liczbę ról zgodnie z potrzebami i zamapuj je na dostawcę tożsamości.

11. Użyj poświadczeń konta usługi AWS, aby pobrać role z konta AWS w ramach aprowizacji użytkowników usługi Azure AD. W tym celu otwórz stronę główną konsoli usług AWS.

12. Wybierz pozycję **usługi**. W obszarze **zabezpieczenia, tożsamość & zgodność**, wybierz pozycję **IAM**.

    ![Zrzut ekranu przedstawiający stronę główną konsoli programu AWS z wyróżnionymi usługami i usługą IAM](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. W sekcji IAM wybierz pozycję **zasady**.

    ![Zrzut ekranu sekcji IAM z wyróżnionymi zasadami](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Utwórz nowe zasady, wybierając pozycję **Utwórz zasady** , aby pobrać role z konta AWS w ramach aprowizacji użytkowników usługi Azure AD.

    ![Zrzut ekranu przedstawiający stronę tworzenie roli z wyróżnionymi zasadami tworzenia zasad](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Utwórz własne zasady, aby pobrać wszystkie role z kont AWS.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad z wyróżnioną pozycją JSON](./media/amazon-web-service-tutorial/policy1.png)

    a. W obszarze **Tworzenie zasad**wybierz kartę **JSON** .

    b. W dokumencie zasad Dodaj następujący kod JSON:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    d. Wybierz pozycję **Przejrzyj zasady** , aby sprawdzić poprawność zasad.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad](./media/amazon-web-service-tutorial/policy5.png)

16. Zdefiniuj nowe zasady.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad z wyróżnionymi polami nazwa i opis](./media/amazon-web-service-tutorial/policy2.png)

    a. W obszarze **Nazwa**wprowadź **AzureAD_SSOUserRole_Policy**.

    b. W polu **Opis**wprowadź następujące zasady umożliwią **pobranie ról z kont usługi AWS**.

    d. Wybierz pozycję **Utwórz zasady**.

17. Utwórz nowe konto użytkownika w usłudze IAM AWS.

    a. W konsoli AWS IAM wybierz pozycję **Użytkownicy**.

    ![Zrzut ekranu przedstawiający konsolę AWS IAM z wyróżnionymi użytkownikami](./media/amazon-web-service-tutorial/policy3.png)

    b. Aby utworzyć nowego użytkownika, wybierz pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawiający przycisk Dodaj użytkownika](./media/amazon-web-service-tutorial/policy4.png)

    d. W sekcji **Dodawanie użytkownika** :

    ![Zrzut ekranu przedstawiający stronę Dodawanie użytkownika z wyróżnioną nazwą użytkownika i typem dostępu](./media/amazon-web-service-tutorial/adduser1.png)

    * Podaj nazwę użytkownika: **AzureADRoleManager**.

    * W polu Typ dostępu wybierz pozycję **dostęp programistyczny**. Dzięki temu użytkownik może wywołać interfejsy API i pobrać role z konta AWS.

    * Wybierz pozycję **następne uprawnienia**.

18. Utwórz nowe zasady dla tego użytkownika.

    ![Zrzut ekranu przedstawiający Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser2.png)

    a. Wybierz pozycję **Dołącz istniejące zasady bezpośrednio**.

    b. Wyszukaj nowo utworzone zasady w sekcji **AzureAD_SSOUserRole_Policy** filtru.

    d. Wybierz zasady, a następnie wybierz kolejno pozycje **Dalej: przegląd**.

19. Zapoznaj się z zasadami dla dołączonego użytkownika.

    ![Zrzut ekranu przedstawiający stronę Dodawanie użytkownika z wyróżnioną pozycją Utwórz użytkownika](./media/amazon-web-service-tutorial/adduser3.png)

    a. Przejrzyj nazwę użytkownika, typ dostępu i zasady zamapowane do użytkownika.

    b. Wybierz pozycję **Utwórz użytkownika**.

20. Pobierz poświadczenia użytkownika.

    ![Zrzut ekranu przedstawiający Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser4.png)

    a. Skopiuj **identyfikator klucza dostępu** i **klucz dostępu wpisu tajnego**.

    b. Wprowadź te poświadczenia w sekcji aprowizacji użytkowników usługi Azure AD, aby pobrać role z konsoli AWS.

    d. Wybierz polecenie **Zamknij**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Jak skonfigurować Inicjowanie obsługi administracyjnej w Amazon Web Services (AWS)

1. W portalu zarządzania usługi Azure AD w aplikacji AWS przejdź do obszaru **udostępniania**.

    ![Zrzut ekranu aplikacji AWS z wyróżnioną obsługą administracyjną](./media/amazon-web-service-tutorial/provisioning.png)

2. Wprowadź odpowiednio klucz dostępu i wpis tajny w polach **clientsecret** i **Secret** .

    ![Zrzut ekranu przedstawiający okno dialogowe poświadczenia administratora](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Podaj klucz dostępu użytkownika usług AWS w polu **clientsecret**.

    b. Podaj wpis tajny użytkownika usług AWS w polu **Secret Token** (Token wpisu tajnego).

    d. Wybierz pozycję **Testuj połączenie**.

    d. Zapisz ustawienie, wybierając pozycję **Zapisz**.

3. W sekcji **Ustawienia** w obszarze **stan aprowizacji**wybierz pozycję **włączone**. Następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający sekcję ustawień z wyróżnioną pozycją](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Usługa aprowizacji będzie importować tylko role z AWS do usługi Azure AD. Ta usługa nie będzie dostarczać użytkowników i grup z usługi Azure AD z powrotem do AWS.

### <a name="create-amazon-web-services-aws-test-user"></a>Tworzenie użytkownika testowego usług Amazon Web Services (AWS)

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w Amazon Web Services (AWS). Usługi Amazon Web Services (AWS) nie wymagają utworzenia użytkownika w swoim systemie logowania jednokrotnego, więc w tym miejscu nie trzeba wykonywać żadnych działań.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Amazon Web Services (AWS) na panelu dostępu powinno nastąpić automatyczne zalogowanie do usług Amazon Web Services (AWS), dla których skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="known-issues"></a>Znane problemy

 * W sekcji dotyczącej **aprowizacji** w podsekcji **mapowania** jest wyświetlana wartość "ładowanie..." i nigdy nie wyświetla mapowań atrybutów. Jedyną obsługiwaną obsługą przepływu pracy jest importowanie ról z AWS do usługi Azure AD w celu wyboru podczas przypisywania użytkowników lub grup. Mapowania atrybutów dla tego elementu są wstępnie określone i nie można ich konfigurować.

 * Sekcja **Aprowizacja** obsługuje wprowadzanie tylko jednego zestawu poświadczeń jednocześnie dla jednej dzierżawy usług AWS. Wszystkie zaimportowane role są zapisywane we właściwości `appRoles` [obiektu`servicePrincipal`](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) usługi Azure AD dla dzierżawy AWS.

   Wiele dzierżawców AWS (reprezentowane przez `servicePrincipals`) można dodać do usługi Azure AD z galerii w celu aprowizacji. Istnieje jednak znany problem, ale nie można automatycznie zapisać wszystkich zaimportowanych ról z wielu AWS `servicePrincipals` używanych do aprowizacji w pojedynczym `servicePrincipal` używanym do logowania jednokrotnego.

   Aby obejść ten obejście, można użyć [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do wyodrębnienia wszystkich `appRoles` zaimportowanych do poszczególnych AWS `servicePrincipal`, w których skonfigurowano Inicjowanie obsługi administracyjnej. Następnie można dodać te ciągi ról do AWS `servicePrincipal`, w którym skonfigurowano Logowanie jednokrotne.

* Role muszą spełniać następujące wymagania, aby można było zaimportować je z AWS do usługi Azure AD:

  * Role muszą mieć dokładnie jeden dostawca SAML zdefiniowany w AWS

  * Połączona długość roli ARN i SAML-Provider ARN dla importowanej roli nie może zawierać więcej niż 119 znaków

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Amazon Web Services (AWS) z usługą Azure AD](https://aad.portal.azure.com/)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png