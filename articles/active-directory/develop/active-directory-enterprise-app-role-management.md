---
title: Konfigurowanie żądania roli dla aplikacji usługi Azure AD w przedsiębiorstwie | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować rolę w tokenie SAML dla aplikacji dla przedsiębiorstw w Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 702c64fbc4eda2af7132a2117c1b09ca0bf41cff
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918597"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Instrukcje: Konfigurowanie roszczeń ról wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw

Korzystając z Azure Active Directory (Azure AD), można dostosować typ wystąpienia dla żądania roli w tokenie odpowiedzi, który otrzymasz po zatwierdzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja usługi Azure AD z konfiguracją katalogu.
- Subskrypcja z włączonym logowaniem jednokrotnym (SSO). Musisz skonfigurować Logowanie jednokrotne za pomocą aplikacji.

## <a name="when-to-use-this-feature"></a>Kiedy używać tej funkcji

Jeśli aplikacja oczekuje, że role niestandardowe mają być przesyłane do odpowiedzi SAML, należy użyć tej funkcji. Można utworzyć dowolną liczbę ról, które mają być przesyłane z powrotem z usługi Azure AD do aplikacji.

## <a name="create-roles-for-an-application"></a>Tworzenie ról dla aplikacji

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz ikonę **Azure Active Directory** .

    ![Ikona Azure Active Directory][1]

2. Wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie wybierz pozycję **wszystkie aplikacje**.

    ![Okienko aplikacje dla przedsiębiorstw][2]

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk "Nowa aplikacja"][3]

4. W polu wyszukiwania wpisz nazwę aplikacji, a następnie wybierz aplikację z panelu wyników. Wybierz przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja na liście wyników](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po dodaniu aplikacji przejdź do strony **Właściwości** i skopiuj identyfikator obiektu.

    ![Strona właściwości](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otwórz [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie i wykonaj następujące czynności:

    a. Zaloguj się do witryny programu Graph Explorer przy użyciu konta administratora globalnego lub współadministratora dzierżawy.

    b. Potrzebujesz wystarczających uprawnień do tworzenia ról. Wybierz pozycję **Modyfikuj uprawnienia** , aby uzyskać uprawnienia.

      ![Przycisk "Modyfikuj uprawnienia"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    d. Wybierz następujące uprawnienia z listy (jeśli nie masz tych jeszcze) i wybierz pozycję **Modyfikuj uprawnienia**.

      ![Lista uprawnień i przycisk Modyfikuj uprawnienia](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Rola Administrator aplikacji w chmurze i administrator aplikacji nie będą działały w tym scenariuszu, ponieważ potrzebujemy uprawnień administratora globalnego do odczytu i zapisu w katalogu.

    d. Zaakceptuj zgodę. Użytkownik jest ponownie zalogowany do systemu.

    e. Zmień wersję na **beta**i Pobierz listę jednostek usługi z dzierżawy za pomocą następującego zapytania:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Jeśli używasz wielu katalogów, postępuj zgodnie z tym wzorcem: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Eksplorator grafu — okno dialogowe z zapytaniem dotyczącym pobierania jednostek usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Już trwa proces uaktualniania interfejsów API, aby klienci mogli zobaczyć zakłócenia w usłudze.

    f. Z listy pobranych nazw głównych usług Pobierz tę, którą chcesz zmodyfikować. Możesz również użyć kombinacji klawiszy Ctrl + F, aby przeszukać aplikację ze wszystkich wymienionych nazw podmiotów usługi. Wyszukaj identyfikator obiektu skopiowanego ze strony **Właściwości** , a następnie użyj następującego zapytania, aby uzyskać dostęp do jednostki usługi:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Zapytanie dotyczące pobierania jednostki usługi, którą należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Wyodrębnij Właściwość **appRoles** z obiektu jednostki usługi.

      ![Szczegóły właściwości appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Jeśli używasz aplikacji niestandardowej (a nie aplikacji portalu Azure Marketplace), zobaczysz dwie role domyślne: User i msiam_access. W przypadku aplikacji Marketplace msiam_access jest jedyną rolą domyślną. Nie musisz wprowadzać żadnych zmian w rolach domyślnych.

    h. Generuj nowe role dla swojej aplikacji.

      Poniższy kod JSON jest przykładem obiektu **appRoles** . Utwórz podobny obiekt, aby dodać role, które mają być używane w aplikacji.

      ```
      {
         "appRoles": [
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "msiam_access",
              "displayName": "msiam_access",
              "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
              "isEnabled": true,
              "origin": "Application",
              "value": null
          },
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "Administrators Only",
              "displayName": "Admin",
              "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
              "isEnabled": true,
              "origin": "ServicePrincipal",
              "value": "Administrator"
          }
      ]
      }
      ```

      > [!Note]
      > Nowe role można dodawać tylko po msiam_access dla operacji patch. Ponadto możesz dodać dowolną liczbę ról, ile potrzebujesz w organizacji. Usługa Azure AD wyśle wartość tych ról jako wartość żądania w odpowiedzi SAML. Aby wygenerować wartości identyfikatora GUID dla identyfikatora nowych ról, użyj narzędzi sieci Web, takich jak [Ta](https://www.guidgenerator.com/)

    i. Wróć do Eksploratora grafów i Zmień metodę z **Get** na **patch**. Należy zastosować poprawki do obiektu jednostki usługi, aby uzyskać odpowiednie role przez zaktualizowanie właściwości **appRoles** , takiej jak pokazana w poprzednim przykładzie. Wybierz pozycję **Uruchom zapytanie** , aby wykonać operację patch. Komunikat o powodzeniu potwierdza utworzenie roli.

      ![Operacja patch z komunikatem o powodzeniu](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po zastosowaniu poprawki jednostki usługi z większą liczbą ról można przypisać użytkowników do odpowiednich ról. Użytkowników można przypisywać, przechodząc do portalu i przechodząc do aplikacji. Wybierz kartę **Użytkownicy i grupy** . Na tej karcie znajduje się lista wszystkich użytkowników i grup, które zostały już przypisane do aplikacji. Nowych użytkowników można dodawać na nowe role. Możesz również wybrać istniejącego użytkownika i wybrać pozycję **Edytuj** , aby zmienić rolę.

    ![Karta "Użytkownicy i grupy"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Aby przypisać rolę do dowolnego użytkownika, wybierz nową rolę i wybierz przycisk **Przypisz** w dolnej części strony.

    ![Okienko "Edytuj przypisanie" i okienko "Wybieranie roli"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Aby wyświetlić nowe role, należy odświeżyć sesję w Azure Portal.

8. Zaktualizuj tabelę **atrybutów** , aby zdefiniować niestandardowe mapowanie tego żądania roli.

9. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | ----------------|
    | Nazwa roli  | user.assignedroles |

    >[!NOTE]
    >Jeśli rola roli ma wartość null, usługa Azure AD nie będzie wysyłać tej wartości do tokenu i jest to ustawienie domyślne dla każdego projektu.

    a. Kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika & oświadczenia** .

      ![Przycisk "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. W oknie dialogowym **Zarządzanie oświadczeniami użytkowników** Dodaj ATRYBUT tokenu SAML, klikając pozycję **Dodaj nowe oświadczenie**.

      ![Przycisk "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Okienko "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    d. W polu **Nazwa** wpisz nazwę atrybutu zgodnie z wymaganiami. Ten przykład używa **nazwy roli** jako nazwy żądania.

    d. Pozostaw puste pole **obszar nazw** .

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Wybierz pozycję **Zapisz**.

10. Aby przetestować aplikację przy użyciu logowania jednokrotnego, która jest inicjowana przez dostawcę tożsamości, zaloguj się do [panelu dostępu](https://myapps.microsoft.com) i wybierz kafelek aplikacji. W tokenie SAML należy zobaczyć wszystkie przypisane role dla użytkownika o podanej nazwie.

## <a name="update-an-existing-role"></a>Aktualizuj istniejącą rolę

Aby zaktualizować istniejącą rolę, wykonaj następujące czynności:

1. Otwórz [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Zaloguj się do witryny programu Graph Explorer przy użyciu konta administratora globalnego lub współadministratora dzierżawy.

3. Zmień wersję na **beta**i Pobierz listę jednostek usługi z dzierżawy za pomocą następującego zapytania:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, postępuj zgodnie z tym wzorcem: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Eksplorator grafu — okno dialogowe z zapytaniem dotyczącym pobierania jednostek usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Z listy pobranych nazw głównych usług Pobierz tę, którą chcesz zmodyfikować. Możesz również użyć kombinacji klawiszy Ctrl + F, aby przeszukać aplikację ze wszystkich wymienionych nazw podmiotów usługi. Wyszukaj identyfikator obiektu skopiowanego ze strony **Właściwości** , a następnie użyj następującego zapytania, aby uzyskać dostęp do jednostki usługi:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Zapytanie dotyczące pobierania jednostki usługi, którą należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Wyodrębnij Właściwość **appRoles** z obiektu jednostki usługi.

    ![Szczegóły właściwości appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Aby zaktualizować istniejącą rolę, wykonaj następujące czynności.

    ![Treść żądania dla "PATCH" z wyróżnioną "Description" i "DisplayName"](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Zmień metodę z **Get** na **patch**.

    b. Skopiuj istniejące role i wklej je w obszarze **treści żądania**.

    d. Zaktualizuj wartość roli przez zaktualizowanie opisu roli, wartości roli lub nazwy wyświetlanej roli zgodnie z wymaganiami.

    d. Po zaktualizowaniu wszystkich wymaganych ról wybierz pozycję **Uruchom zapytanie**.

## <a name="delete-an-existing-role"></a>Usuń istniejącą rolę

Aby usunąć istniejącą rolę, wykonaj następujące czynności:

1. Otwórz [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

2. Zaloguj się do witryny programu Graph Explorer przy użyciu konta administratora globalnego lub współadministratora dzierżawy.

3. Zmień wersję na **beta**i Pobierz listę jednostek usługi z dzierżawy za pomocą następującego zapytania:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, postępuj zgodnie z tym wzorcem: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Eksplorator grafu — okno dialogowe z zapytaniem dotyczącym pobierania listy jednostek usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Z listy pobranych nazw głównych usług Pobierz tę, którą chcesz zmodyfikować. Możesz również użyć kombinacji klawiszy Ctrl + F, aby przeszukać aplikację ze wszystkich wymienionych nazw podmiotów usługi. Wyszukaj identyfikator obiektu skopiowanego ze strony **Właściwości** , a następnie użyj następującego zapytania, aby uzyskać dostęp do jednostki usługi:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Zapytanie dotyczące pobierania jednostki usługi, którą należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Wyodrębnij Właściwość **appRoles** z obiektu jednostki usługi.

    ![Szczegóły właściwości appRoles z obiektu jednostki usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Aby usunąć istniejącą rolę, wykonaj następujące czynności.

    ![Treść żądania dla elementu "PATCH" z wartością IsEnabled ustawioną na wartość false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Zmień metodę z **Get** na **patch**.

    b. Skopiuj istniejące role z aplikacji i wklej je w obszarze **treści żądania**.

    d. Dla roli, która ma zostać usunięta, ustaw wartość **false** dla opcji **IsEnabled** .

    d. Wybierz pozycję **Uruchom zapytanie**.

    > [!NOTE]
    > Upewnij się, że masz rolę msiam_access, a identyfikator jest zgodny w wygenerowanej roli.

7. Po wyłączeniu roli Usuń ten blok roli z sekcji **appRoles** . Zachowaj metodę jako **poprawkę**i wybierz pozycję **Uruchom zapytanie**.

8. Po uruchomieniu kwerendy rola zostanie usunięta.

    > [!NOTE]
    > Aby można było usunąć tę rolę, należy ją wyłączyć.

## <a name="next-steps"></a>Następne kroki

Dodatkowe kroki można znaleźć w [dokumentacji aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
