---
title: 'Samouczek: Konfigurowanie powiększenia automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w celu powiększania.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: cd832a9dfec4680222d2c985f49aba499a56aaac
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062784"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie powiększenia automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w powiększeniu i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do powiększania.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa powiększenia](https://zoom.us/pricing)
* Konto użytkownika w powiększeniu z uprawnieniami administratora

## <a name="add-zoom-from-the-gallery"></a>Dodaj powiększenie z galerii

Przed skonfigurowaniem powiększenia dla automatycznej aprowizacji użytkowników w usłudze Azure AD należy dodać powiększenie z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać powiększenie z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **powiększenie** **, wybierz pozycję Powiększ w** panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja Zoom na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Przypisywanie użytkowników do powiększania

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do powiększenia. Po ustaleniu tych użytkowników i/lub grup możesz powiększyć, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Ważne porady dotyczące przypisywania użytkowników do powiększania

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do powiększenia w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do powiększania należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Konfigurowanie automatycznego aprowizacji użytkowników w celu powiększania 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w celu powiększania w zależności od przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję jednokrotnego logowania opartego na protokole SAML, postępując zgodnie z instrukcjami podanymi w [samouczku Powiększ logowanie](zoom-tutorial.md)jednokrotne. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurowanie automatycznego aprowizacji użytkowników w celu powiększania w usłudze Azure AD

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **powiększenie**.

    ![Link aplikacji Zoom na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://api.zoom.us/scim` w **adresie URL dzierżawy**. Aby pobrać **klucz tajny** konta powiększenia, postępuj zgodnie z instrukcjami opisanymi w sekcji Krok 6.

6. Zaloguj się do [konsoli administracyjnej powiększenia](https://zoom.us/signin). Przejdź do okna **zaawansowane > powiększenie dla deweloperów** w lewym okienku nawigacji.

    ![Powiększ integracje](media/zoom-provisioning-tutorial/zoom01.png)

    Przejdź do obszaru **Zarządzanie** w prawym górnym rogu strony. 

    ![Zainstaluj powiększenie](media/zoom-provisioning-tutorial/zoom02.png)

    Przejdź do utworzonej aplikacji usługi Azure AD. 
    
    ![Powiększ aplikację](media/zoom-provisioning-tutorial/zoom03.png)

    W okienku nawigacji po lewej stronie wybierz pozycję **poświadczenia aplikacji** .

    ![Powiększ aplikację](media/zoom-provisioning-tutorial/zoom04.png)

    Pobierz poniższą wartość tokenu JWT i wprowadź ją w polu **token tajny** w usłudze Azure AD. Jeśli potrzebujesz nowego tokenu bez wygaśnięcia, musisz zmienić konfigurację czasu wygaśnięcia, który będzie automatycznie generować nowy token. 

    ![Zainstaluj powiększenie](media/zoom-provisioning-tutorial/zoom05.png)

7. Po zapełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie ze powiększeniem. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto powiększenia ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij przycisk **Save** (Zapisz).

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby powiększyć**.

    ![Powiększ mapowania użytkowników](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby powiększyć sekcję **Mapowanie atrybutu** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w powiększeniu dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.
    
     ![Powiększ mapowania użytkowników](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć powiększanie usługi Azure AD Provisioning, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .
    
    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz powiększyć, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem działań aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning przy powiększeniu.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Powiększenie nie obsługuje aprowizacji dla grup.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
