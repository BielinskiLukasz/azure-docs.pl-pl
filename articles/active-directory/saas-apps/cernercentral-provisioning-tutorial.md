---
title: 'Samouczek: Inicjowanie obsługi przez użytkownika dla programu CERN (środkowe) w usłudze Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać użytkownikom listę w centrum CERN.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ed04d8fdcc2d79c66e2ebc53c737c78664e4621
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058320"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Cerner Central dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w ramach programu Cerner Central i Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do listy użytkowników w centrum CERN.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawca centralny dla programu CERN

> [!NOTE]
> Azure Active Directory integruje się z programem Cerner Central przy użyciu protokołu [Standard scim](http://www.simplecloud.info/) .

## <a name="assigning-users-to-cerner-central"></a>Przypisywanie użytkowników do programu Cerner Central

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD. 

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do centrum CERN. Po ustaleniu tych użytkowników można przypisać je do programu Cerner Central, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Ważne porady dotyczące przypisywania użytkowników do programu Cerner Central

* Zaleca się, aby jeden użytkownik usługi Azure AD był przypisany do programu Cerner Central w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Po zakończeniu wstępnego testowania dla jednego użytkownika, program Cerner zaleca przypisanie całej listy użytkowników, którzy mają dostęp do dowolnego rozwiązania CERN (nie tylko program CERN), aby mógł zostać zainicjowany do spisu użytkownika firmy CERN.  Inne rozwiązania CERN wykorzystują tę listę użytkowników w spisie użytkowników.

* Podczas przypisywania użytkownika do programu Cerner Central, należy wybrać rolę **użytkownika** w oknie dialogowym przypisania. Użytkownicy z rolą "dostęp domyślny" są wykluczeni z aprowizacji.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurowanie aprowizacji użytkowników w centralnym programie CERN

Ta sekcja przeprowadzi Cię przez proces łączenia usługi Azure AD z kontem użytkownika programu CERN w centrum danych firmy CERN przy użyciu interfejsu API aprowizacji Standard scim konta użytkownika i konfigurowania usługi aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w centrum danych CERN na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla programu Cerner Central, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje uzupełniają się wzajemnie. Aby uzyskać więcej informacji, zobacz [centralny samouczek wylogowania](cernercentral-tutorial.md)jednokrotnego dla programu CERN.

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi konta użytkownika do programu Cerner Central w usłudze Azure AD:

Aby zapewnić obsługę kont użytkowników w programie Cerner Central, należy zażądać centralnego konta systemowego firmy CERN od CERN i wygenerować token okaziciela OAuth, którego usługa Azure AD może używać do nawiązywania połączenia z punktem końcowym usługi CERN Standard scim. Zaleca się również, aby integracja była przeprowadzana w środowisku piaskownicy CERN przed wdrożeniem do produkcji.

1. Pierwszym krokiem jest upewnienie się, że osoby zarządzające programem CERN i integracją usługi Azure AD mają konto CernerCare, które jest wymagane do uzyskania dostępu do dokumentacji niezbędnej do wykonania instrukcji. W razie potrzeby Użyj poniższych adresów URL, aby utworzyć konta CernerCare w każdym odpowiednim środowisku.

   * Rozwiązaniahttps://sandboxcernercare.com/accounts/create

   * Narzędzihttps://cernercare.com/accounts/create  

2. Następnie należy utworzyć konto systemowe dla usługi Azure AD. Skorzystaj z poniższych instrukcji, aby zażądać konta systemowego w piaskownicy i środowiskach produkcyjnych.

   * Wskazówekhttps://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Rozwiązaniahttps://sandboxcernercentral.com/system-accounts/

   * Narzędzihttps://cernercentral.com/system-accounts/

3. Następnie Wygeneruj token okaziciela OAuth dla każdego konta systemowego. Aby to zrobić, postępuj zgodnie z poniższymi instrukcjami.

   * Wskazówekhttps://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Rozwiązaniahttps://sandboxcernercentral.com/system-accounts/

   * Narzędzihttps://cernercentral.com/system-accounts/

4. Na koniec należy uzyskać identyfikatory obszaru spisu użytkownika dla środowisk piaskownicy i produkcyjnych w narzędziu CERN, aby ukończyć konfigurację. Aby uzyskać informacje o tym, jak uzyskać ten temat https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM, zobacz:. 

5. Teraz można skonfigurować usługę Azure AD w celu udostępniania kont użytkowników w programie CERN. Zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

6. Jeśli już skonfigurowano program CERN (Centraler) do logowania jednokrotnego, Wyszukaj wystąpienie elementu CERN w centrali przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj dla programu **Cerner Central** w galerii aplikacji. Wybierz pozycję CERN (centralne) z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

7. Wybierz wystąpienie programu Cerner Central, a następnie wybierz kartę **Inicjowanie obsługi** .

8. Ustaw **tryb aprowizacji** na **automatyczny**.

   ![Centralne Inicjowanie obsługi przez CERN](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Wypełnij następujące pola w obszarze **poświadczenia administratora**:

   * W polu **adres URL dzierżawy** wprowadź adres URL w formacie poniżej, zastępując tekst "User-Field-ID" identyfikatorem obszaru, który został pobrany w kroku #4.

    > Rozwiązaniahttps://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Narzędzihttps://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * W polu **token tajny** wprowadź token okaziciela OAuth wygenerowany w kroku #3 a następnie kliknij pozycję **Testuj połączenie**.

   * Na stronie upperright portalu powinna zostać wyświetlona powiadomienie o powodzeniu.

1. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , a następnie zaznacz pole wyboru poniżej.

1. Kliknij przycisk **Zapisz**.

1. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkowników i grup, które mają być synchronizowane z usługi Azure AD do programu Cerner Central. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników i grup w programie Cerner Central for Updates. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługę Azure AD Provisioning dla programu Cerner Central, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

1. Kliknij przycisk **Zapisz**.

Spowoduje to rozpoczęcie synchronizacji początkowej dla wszystkich użytkowników i/lub grup przypisanych do programu Cerner Central w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji centralnej programu CERN.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Program Cerner Central: publikowanie danych tożsamości przy użyciu usługi Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Samouczek: Konfigurowanie centrum CERN dla logowania jednokrotnego za pomocą Azure Active Directory](cernercentral-tutorial.md)
* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
