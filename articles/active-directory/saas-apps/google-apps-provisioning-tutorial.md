---
title: 'Samouczek: Konfigurowanie usługi G Suite do automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w usłudze G Suite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 5ecf8ca6e6790b218216aed9a6ff82c8a7ac9dd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85250149"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi G Suite do automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w systemie G Suite i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w usłudze [G Suite](https://gsuite.google.com/) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md). 

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Łącznik pakietu G Suite został niedawno zaktualizowany o 2019 października. Zmiany wprowadzone do łącznika G Suite obejmują:
>
> * Dodano obsługę dodatkowych atrybutów użytkowników i grup usługi G Suite.
> * Zaktualizowano nazwy atrybutów docelowych G Suite, aby były zgodne z informacjami zdefiniowanymi w [tym miejscu](https://developers.google.com/admin-sdk/directory).
> * Zaktualizowano domyślne mapowania atrybutów.

## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze G Suite
> * Usuń użytkowników w usłudze G Suite, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD a programem G Suite
> * Udostępnianie grup i członkostw w grupach w usłudze G Suite
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial) jednokrotne do pakietu G Suite (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* [Dzierżawa G Suite](https://gsuite.google.com/pricing.html)
* Konto użytkownika w pakiecie G Suite z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługami Azure AD i G Suite](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi G Suite do obsługi aprowizacji w usłudze Azure AD

Przed skonfigurowaniem pakietu G dla automatycznej aprowizacji użytkowników w usłudze Azure AD należy włączyć Inicjowanie obsługi Standard scim w usłudze G Suite.

1. Zaloguj się do [konsoli administracyjnej G Suite](https://admin.google.com/) przy użyciu konta administratora, a następnie wybierz pozycję **zabezpieczenia**. Jeśli łącze nie jest widoczne, może być ukryte w menu **Więcej kontrolek** w dolnej części ekranu.

    ![Zabezpieczenia w usłudze G Suite](./media/google-apps-provisioning-tutorial/gapps-security.png)

2. Na stronie **zabezpieczenia** wybierz pozycję **Dokumentacja interfejsu API**.

    ![Interfejs API usługi G Suite](./media/google-apps-provisioning-tutorial/gapps-api.png)

3. Wybierz pozycję **Włącz dostęp do interfejsu API**.

    ![Włączono interfejs API usługi G Suite](./media/google-apps-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Dla każdego użytkownika, który zamierzasz udostępnić do usługi G Suite, jego nazwa użytkownika w usłudze Azure AD **musi** być powiązana z domeną niestandardową. Na przykład nazwy użytkowników, które wyglądają jak bob@contoso.onmicrosoft.com nie są akceptowane przez pakiet G Suite. Z drugiej strony bob@contoso.com zostanie zaakceptowany. Istniejącą domenę użytkownika można zmienić, postępując zgodnie z instrukcjami znajdującymi się [tutaj](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

4. Po dodaniu i zweryfikowaniu żądanych domen niestandardowych za pomocą usługi Azure AD należy zweryfikować je ponownie z pakietem G Suite. Aby sprawdzić domeny w usłudze G Suite, zapoznaj się z następującymi krokami:

    a. W [konsoli administracyjnej programu G Suite](https://admin.google.com/)wybierz pozycję **domeny**.

    ![Domeny pakietu G Suite](./media/google-apps-provisioning-tutorial/gapps-domains.png)

    b. Wybierz pozycję **Dodaj domenę lub alias domeny**.

    ![Dodaj domenę zestawu G Suite](./media/google-apps-provisioning-tutorial/gapps-add-domain.png)

    c. Wybierz pozycję **Dodaj inną domenę**, a następnie wpisz nazwę domeny, którą chcesz dodać.

    ![G Suite Dodaj kolejną](./media/google-apps-provisioning-tutorial/gapps-add-another.png)

    d. Wybierz pozycję **Kontynuuj i sprawdź własność domeny**. Następnie postępuj zgodnie z instrukcjami, aby sprawdzić, czy jesteś właocicielem nazwy domeny. Aby uzyskać wyczerpujące instrukcje dotyczące weryfikowania domeny za pomocą usługi Google, zobacz [Sprawdzanie własności lokacji](https://support.google.com/webmasters/answer/35179).

    e. Powtórz powyższe kroki dla wszystkich dodatkowych domen, które mają zostać dodane do usługi G Suite.

5. Następnie określ konto administratora, które ma być używane do zarządzania aprowizacji użytkowników w usłudze G Suite. Przejdź do **ról administratora**.

    ![Administrator pakietu G Suite](./media/google-apps-provisioning-tutorial/gapps-admin.png)

6. W przypadku **roli administratora** tego konta Edytuj **uprawnienia** dla tej roli. Upewnij się, że wszystkie **uprawnienia administratora interfejsu API** są włączone, aby można było używać tego konta do obsługi administracyjnej.

    ![Uprawnienia administratora w usłudze G Suite](./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Krok 3. Dodaj pakiet G Suite z galerii aplikacji usługi Azure AD

Dodaj pakiet G Suite z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą do usługi G Suite. Jeśli wcześniej skonfigurowano usługę G Suite dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do zestawu G Suite należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w usłudze G Suite 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym interfejsu API katalogu usługi G, zapoznaj się z [interfejsem API katalogu](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla pakietu G Suite w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**. Użytkownicy będą musieli zalogować się do portal.azure.com i nie będą mogli używać aad.portal.azure.com

    ![Blok Aplikacje dla przedsiębiorstw](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![Blok Wszystkie aplikacje](./media/google-apps-provisioning-tutorial/all-applications.png)

2. Na liście Aplikacje wybierz pozycję **G Suite**.

    ![Link do usługi G Suite na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi** . kliknij pozycję **Rozpocznij**.

    ![Karta aprowizacji](common/provisioning.png)

      ![Wprowadzenie — blok](./media/google-apps-provisioning-tutorial/get-started.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** kliknij pozycję **Autoryzuj**. Nastąpi przekierowanie do okna dialogowego autoryzacji Google w nowym oknie przeglądarki.

      ![G Suite — Autoryzuj](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. Potwierdź, że chcesz nadać uprawnienia usługi Azure AD, aby wprowadzić zmiany w dzierżawie w usłudze G Suite. Wybierz pozycję **Zaakceptuj**.

     ![Uwierzytelnianie dzierżawy w usłudze G Suite](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z pakietem G Suite. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi G Suite ma uprawnienia administratora, a następnie spróbuj ponownie. Następnie spróbuj ponownie wykonać krok **Autoryzuj** .

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Udostępnij Azure Active Directory użytkownikom**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD z usługą G Suite w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w usłudze G Suite dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że interfejs API usługi G Suite obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |primaryEmail|String|
   |relacje. [Type EQ "Manager"]. Value|String|
   |Nazwa. rodzina|String|
   |Nazwa. imię|String|
   |wieszon|String|
   |externalIds. [Type EQ "Custom"]. Value|String|
   |externalIds. [typ EQ "organizacja"]. wartość|String|
   |adres. [Type EQ "Work"]. Country|String|
   |adres. [wpisz EQ "Work"]. streetAddress|String|
   |adres. [Type EQ "Work"]. region|String|
   |adres. [typ EQ "Work"]. locale|String|
   |adres. [Type EQ "Work"]. KodPocztowy|String|
   |Zamów. [Type EQ "Work"]. Address|String|
   |przedsiębiorstwa. [Type EQ "Work"]. Department|String|
   |przedsiębiorstwa. [Type EQ "Work"]. title|String|
   |numer telefonu. [Type EQ "Work"]. Value|String|
   |numer telefonu. [Type EQ "Mobile"]. Value|String|
   |numer telefonu. [Type EQ "work_fax"]. Value|String|
   |Zamów. [Type EQ "Work"]. Address|String|
   |przedsiębiorstwa. [Type EQ "Work"]. Department|String|
   |przedsiębiorstwa. [Type EQ "Work"]. title|String|
   |numer telefonu. [Type EQ "Work"]. Value|String|
   |numer telefonu. [Type EQ "Mobile"]. Value|String|
   |numer telefonu. [Type EQ "work_fax"]. Value|String|
   |adres. [Type EQ "Home"]. Country|String|
   |adres. [Type EQ "Home"]. sformatowana|String|
   |adres. [Type EQ "Home"]. locale|String|
   |adres. [Type EQ "Home"]. KodPocztowy|String|
   |adres. [Type EQ "Home"]. region|String|
   |adres. [Type EQ "Home"]. streetAddress|String|
   |adres. [Type EQ "Other"]. Country|String|
   |adres. [Type EQ "Other"]. sformatowane|String|
   |adres. [Type EQ "Other"]. locale|String|
   |adres. [Type EQ "Other"]. KodPocztowy|String|
   |adres. [Type EQ "Other"]. region|String|
   |adres. [Type EQ "Other"]. streetAddress|String|
   |adres. [wpisz EQ "Work"]. sformatowane|String|
   |changePasswordAtNextLogin|String|
   |Zamów. [Type EQ "Home"]. Address|String|
   |Zamów. [Type EQ "Other"]. Address|String|
   |externalIds. [typ EQ "konto"]. wartość|String|
   |externalIds. [Type EQ "Custom"]. CustomType|String|
   |externalIds. [typ EQ "klient"]. wartość|String|
   |externalIds. [Type EQ "login_id"]. Value|String|
   |externalIds. [typ EQ "Sieć"]. wartość|String|
   |płeć. typ|String|
   |GeneratedImmutableId|String|
   |Identyfikator|String|
   |ISP. [Type EQ "Home"]. Protocol|String|
   |ISP. [Type EQ "Other"]. protokół|String|
   |ISP. [Type EQ "Work"]. protokół|String|
   |includeInGlobalAddressList|String|
   |ipWhitelisted|String|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. costCenter|String|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. Dział|String|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. domena|String|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. fullTimeEquivalent|String|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. Lokalizacja|String|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. Name|String|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. symbol|String|
   |przedsiębiorstwa. [Type EQ "Szkoła"]. title|String|
   |przedsiębiorstwa. [wpisz EQ "Work"]. costCenter|String|
   |przedsiębiorstwa. [Type EQ "Work"]. domena|String|
   |przedsiębiorstwa. [wpisz EQ "Work"]. fullTimeEquivalent|String|
   |przedsiębiorstwa. [typ EQ "Work"]. Lokalizacja|String|
   |przedsiębiorstwa. [wpisz EQ "Work"]. Name|String|
   |przedsiębiorstwa. [Type EQ "Work"]. symbol|String|
   |OrgUnitPath|String|
   |numer telefonu. [Type EQ "Home"]. Value|String|
   |numer telefonu. [Type EQ "Other"]. Value|String|
   |zaufany. [Type EQ "Home"]. Value|String|
   |zaufany. [Type EQ "Other"]. Value|String|
   |zaufany. [Type EQ "Work"]. Value|String|
   

10. W sekcji **mapowania** wybierz pozycję **Udostępnij Azure Active Directory grupy**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD z usługą G Suite w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usłudze G Suite dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |poczta e-mail|String|
      |Elementy członkowskie|String|
      |name|String|
      |description|String|

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla usługi G Suite, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do zbioru G Suite, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

> [!NOTE]
> Jeśli użytkownicy mają już istniejące konto osobiste/konsumencki przy użyciu adresu e-mail użytkownika usługi Azure AD, może to spowodować pewne problemy, które można rozwiązać za pomocą narzędzia Google transfer przed przeprowadzeniem synchronizacji katalogów.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
