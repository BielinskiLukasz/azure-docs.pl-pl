---
title: Aktualizowanie informacji o grupach z portalu Moje aplikacje — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak wyświetlać i aktualizować informacje związane z grupami, w tym wyświetlanie własnych grup, tworzenie nowych grup, wyświetlanie grup, do których należysz, i dołączanie do dowolnych grup, które nie są już częścią.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a11b43d040dec838de350c23614ae42b6756ec6e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383170"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>Aktualizowanie informacji o grupach z portalu My Apps

Możesz użyć swojego konta służbowego z portalem **Moje aplikacje** oparte na sieci Web, aby wyświetlać i uruchamiać wiele aplikacji opartych na chmurze w organizacji, aktualizować niektóre informacje o profilu i koncie, wyświetlać informacje o **grupach** i wykonywać  **przeglądy dostępu** do aplikacji i grup. Jeśli nie masz dostępu do portalu **Moje aplikacje** , musisz skontaktować się z pomocą techniczną, aby uzyskać odpowiednie uprawnienia.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat sposobu konfigurowania i zarządzania aplikacjami opartymi na chmurze w [dokumentacji zarządzania aplikacjami](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Wyświetlanie informacji o grupach

Jeśli administrator udzielił Ci uprawnień do wyświetlania kafelka **grupy** , możesz:

- **Jako członek grupy.** Wyświetl szczegóły lub pozostaw dowolną grupę.

- **Jako właściciel grupy.** Wyświetlanie szczegółów, tworzenie nowej grupy, Dodawanie lub usuwanie elementów członkowskich lub usuwanie grupy.

### <a name="to-view-your-groups-information"></a>Aby wyświetlić informacje o grupach

1. Zaloguj się do konta służbowego.

2. Otwórz przeglądarkę internetową i przejdź do https://myapps.microsoft.com lub Użyj linku dostarczonego przez organizację. Na przykład może nastąpić przekierowanie do dostosowanej strony organizacji, https://myapps.microsoft.com/contoso.com na przykład.

    Zostanie wyświetlona strona **aplikacje** przedstawiająca wszystkie aplikacje oparte na chmurze należące do organizacji i dostępne do użycia.

    ![Strona aplikacje w portalu Moje aplikacje](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Wybierz kafelek **grupy** , aby wyświetlić informacje związane z grupą.

    ![Strona grup z członkami i grupami należącymi do grupy](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Na podstawie uprawnień możesz użyć strony **grupy** do:

    - **Przejrzyj posiadane grupy.** Wyświetl informacje o wszystkich grupach należących do organizacji w obszarze **grupy I** obszary. Wybranie określonej nazwy grupy zapewnia więcej szczegółów na temat grupy, w tym typ grupy, liczbę elementów członkowskich, zasady dołączania i listę aktywnych członków.

    - **Utwórz nową grupę.** Utwórz nową grupę za pomocą użytkownika jako właściciela z obszaru **grupy I do własnych grup** . Szczegółowe instrukcje znajdują się w sekcji [Tworzenie nowej grupy](#create-a-new-group) w tym artykule.

    - **Edytuj istniejącą grupę.** Edytuj szczegóły dla dowolnych własnych grup. Aby uzyskać szczegółowe instrukcje, zobacz sekcję [Edytowanie istniejącej grupy](#edit-an-existing-group) w tym artykule.

    - **Dodaj lub Usuń członków.** Dodaj lub Usuń elementy członkowskie dla posiadanych grup. Aby uzyskać szczegółowe instrukcje, zobacz sekcję [Dodawanie lub usuwanie elementu członkowskiego](#add-or-remove-a-member) w tym artykule.

    - **Odnów grupę Office 365.** Jeśli Twoja organizacja zezwala na to, możesz odnowić grupy programu Office 365. Aby uzyskać szczegółowe instrukcje, zobacz sekcję [Odnów grupę Office 365](#renew-an-office-365-group) w tym artykule. 

    - **Usuń grupę.** Usuń wszystkie należące do siebie grupy. Aby uzyskać szczegółowe instrukcje, zobacz sekcję [Usuwanie grupy](#delete-a-group) w tym artykule.

    - **Przejrzyj grupy, których jesteś częścią.** Wyświetl nazwy wszystkich grup, których jesteś członkiem z **grup** , do których należysz. Wybranie określonej nazwy grupy zapewnia więcej szczegółów na temat grupy, w tym typ grupy, liczbę elementów członkowskich, zasady dołączania i listę aktywnych członków.

    - **Dołącz do grupy.** Dołącz do istniejącej grupy, dla której nie jesteś jeszcze członkiem z **grup, w** których prowadzisz. Aby uzyskać szczegółowe instrukcje, zobacz sekcję [Przyłącz do istniejącej grupy](#join-an-existing-group).

## <a name="create-a-new-group"></a>Utwórz nową grupę

1. Na stronie **grupy** wybierz pozycję **Utwórz grupę** w obszarze **grupy I własne** .

    Zostanie wyświetlone pole **Utwórz grupę** .

    ![Utwórz pole grupy](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Wprowadź wymagane informacje:

    - **Typ grupy:**

        - **Bezpieczeństwo.** Używany do zarządzania dostępem członków i komputerów do udostępnionych zasobów dla grupy użytkowników. Na przykład można utworzyć grupę zabezpieczeń dla konkretnych zasad zabezpieczeń. W ten sposób można jednocześnie udzielić zestawu uprawnień wszystkim członkom zamiast udzielania uprawnień każdemu członkowi osobno.

        - **Office 365.** Zapewnia możliwości współpracy przez udostępnienie członkom współużytkowanej skrzynki pocztowej, kalendarza, plików, witryny programu SharePoint i innych. Ta opcja umożliwia także udzielenie dostępu do grupy osobom spoza organizacji.

    - **Nazwa grupy.** Dodaj nazwę grupy, która ma znaczenie i którą łatwo zapamiętać.

    - **Opis grupy (opcjonalnie).** Do grupy możesz dodać opcjonalny opis.

    - **Zasady grupy.** Wybierz opcję, aby umożliwić wszystkim dołączenie do grupy lub zezwalanie na dodawanie elementów członkowskich tylko właścicielowi grupy.

3. Wybierz pozycję **Utwórz**.

    Nowa grupa zostanie utworzona za pomocą użytkownika jako właściciel i zostanie wyświetlona na liście Twoich **grup** . Ponieważ jesteś właścicielem, ta grupa jest również wyświetlana na liście **grupy**

## <a name="edit-an-existing-group"></a>Edytowanie istniejącej grupy

Po utworzeniu grupy można edytować jej szczegóły, w tym aktualizując dowolne z istniejących informacji.

### <a name="to-edit-your-details"></a>Aby edytować szczegóły

1. Wybierz grupę, którą chcesz edytować, na stronie **grupy** , a następnie wybierz pozycję **Edytuj szczegóły** na *&lt;stronie Group_Name&gt;* .

    Zostanie wyświetlone okno **Edytuj szczegóły** i można zaktualizować informacje dodane podczas pierwszego tworzenia grupy.

2. Wprowadź wszystkie zmiany, a następnie wybierz pozycję **Aktualizuj**.

## <a name="add-or-remove-a-member"></a>Dodawanie lub usuwanie elementu członkowskiego

Możesz dodawać lub usuwać elementy członkowskie dla dowolnych własnych grup.

### <a name="to-add-or-remove-a-member"></a>Aby dodać lub usunąć członka

1. Wybierz grupę, do której chcesz dodać członków, a następnie wybierz **+** ją *&lt;na stronie Group_Name&gt;* .

    ![Dodawanie elementu członkowskiego grupy z wyróżnionym znakiem +](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Wyszukaj element członkowski, który chcesz dodać, w polu **Dodaj członków** , a następnie wybierz pozycję **Dodaj**.

    ![Dodawanie pola członków z nowym członkiem do dodania](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Zaproszenie jest wysyłane do nowego członka, aby rozpocząć dostęp do aplikacji w organizacji.

3. Jeśli element członkowski został dodany przez pomyłkę lub jeśli członek opuścił organizację, możesz usunąć element członkowski, wybierając pozycję **Usuń element członkowski** obok nazwy elementu członkowskiego na *&lt;stronie Group_Name&gt;* .

    ![Usuń element członkowski z wyróżnionym linkiem usuwania](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Odnów grupę Office 365

Jeśli Twoja organizacja zezwala na to, możesz odnowić grupę Office 365, rozszerzając datę wygaśnięcia.

### <a name="to-renew-a-group"></a>Aby odnowić grupę

1. Wybierz grupę pakietu Office 365, którą chcesz odnowić, a następnie wybierz pozycję **Odnów grupę**.

    ![Odnów grupę pakietu Office 365, rozszerzając datę wygaśnięcia](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Kliknij przycisk **OK** , aby zamknąć komunikat potwierdzający.

    Po odświeżeniu strony zobaczysz zaktualizowane daty **ostatniego odnowienia** i **ważności grupy** .

## <a name="delete-a-group"></a>Usuwanie grupy

W dowolnym momencie możesz usunąć dowolną własną grupę. Jeśli jednak usuniesz grupę przez pomyłkę, musisz ją utworzyć i ponownie dodać członków.

### <a name="to-delete-the-group"></a>Aby usunąć grupę

1. Wybierz grupę, którą chcesz trwale usunąć, a następnie wybierz pozycję **Usuń grupę** na *&lt;stronie Group_Name&gt;* .

    ![Strona < Group_name > z wyróżnionym linkiem Usuń grupę](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. W komunikacie potwierdzenia wybierz pozycję **tak** .

    Grupa zostanie trwale usunięta.

## <a name="join-an-existing-group"></a>Dołącz do istniejącej grupy

Możesz dołączyć już istniejącą grupę ze strony **grupy** .

### <a name="to-join-or-leave-a-group"></a>Aby dołączyć lub opuścić grupę

1. Na stronie **grupy** wybierz pozycję **Dołącz do grupy** w obszarze **grupy, w której się** znajduje.

    Zostanie wyświetlona strona **grupy sprzężeń** .

    ![Strona Dołącz grupy z wyróżnionym przyciskiem Dołącz grupę](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Na stronie **dołączanie grup** wybierz nazwę grupy, do której chcesz dołączyć, Wyświetl szczegóły skojarzonej grupy, a następnie, jeśli grupa jest dostępna, wybierz pozycję **Dołącz do grupy**.

    Jeśli grupa wymaga zatwierdzenia członkostwa w grupie, użytkownik zostanie poproszony o podanie uzasadnienia biznesowego, w którym należy dołączyć do grupy, a następnie wybrać pozycję **Żądaj**. Jeśli grupa nie wymaga zatwierdzenia, zostanie natychmiast dodana jako element członkowski, a grupa pojawia się na liście Twoich **grup** .

3. Jeśli dołączysz grupę przez pomyłkę lub jeśli nie musisz już jej częścią, możesz wybrać nazwę grupy na stronie Dołączanie **grup** , a następnie wybrać pozycję **Opuść grupę**.

    ![Strona Dołącz grupy z wyróżnionym przyciskiem Opuść grupę](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>Następne kroki

- [Dostęp do aplikacji i korzystanie z nich w portalu My Apps](my-apps-portal-end-user-access.md).

- [Zmień informacje o profilu](my-apps-portal-end-user-update-profile.md).

- [Wykonaj własne przeglądy dostępu](my-apps-portal-end-user-access-reviews.md).
