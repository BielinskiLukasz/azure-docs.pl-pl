---
title: Łączenie z usługą Office 365 Outlook
description: Automatyzowanie zadań i przepływów pracy, które zarządzają pocztą e-mail, kontaktami i kalendarzami w programie Office 365 Outlook przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 07/27/2020
tags: connectors
ms.openlocfilehash: e0e152b3c0e10c34bc6213ddf867b79c58d1d767
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832981"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Zarządzanie pocztą e-mail, kontaktami i kalendarzami w programie Outlook usługi Office 365 przy użyciu usługi Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika pakietu Office 365 Outlook](/connectors/office365connector/)można tworzyć zautomatyzowane zadania i przepływy pracy, które zarządzają kontem pakietu Office 365, tworząc Aplikacje logiki. Na przykład można zautomatyzować następujące zadania:

* Pobieranie, wysyłanie i odpowiadanie na wiadomości e-mail. 
* Planowanie spotkań w kalendarzu.
* Dodawanie i edytowanie kontaktów. 

Możesz użyć dowolnego wyzwalacza, aby uruchomić przepływ pracy, na przykład po nadejściu nowej wiadomości e-mail, po zaktualizowaniu elementu kalendarza lub gdy zdarzenie występuje w usłudze różnicowej, na przykład w usłudze Salesforce. Możesz użyć akcji, które reagują na zdarzenie wyzwalacza, na przykład Wyślij wiadomość e-mail lub Utwórz nowe wydarzenie w kalendarzu. 

> [!NOTE]
> Aby zautomatyzować zadania dla @outlook.com konta usługi lub @hotmail.com , użyj [łącznika Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

* [Konto pakietu Office 365](https://www.office.com/)

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta programu Outlook w usłudze Office 365. Aby uruchomić przepływ pracy przy użyciu wyzwalacza programu Outlook pakietu Office 365, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby dodać akcję programu Outlook pakietu Office 365 do przepływu pracy, aplikacja logiki musi już mieć wyzwalacz.

## <a name="add-a-trigger"></a>Dodawanie wyzwalacza

[Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) to zdarzenie, które uruchamia przepływ pracy w aplikacji logiki. Ta przykładowa aplikacja logiki używa wyzwalacza sondowania, który sprawdza każde zaktualizowane zdarzenie w kalendarzu na koncie e-mail na podstawie określonego interwału i częstotliwości.

1. W [Azure Portal](https://portal.azure.com)Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W polu wyszukiwania wprowadź `office 365 outlook` jako filtr. Ten przykład wybiera **, gdy wkrótce zostanie uruchomione nadchodzące wydarzenie**.
   
   ![Wybierz wyzwalacz, aby uruchomić aplikację logiki](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Jeśli zostanie wyświetlony monit o zalogowanie się, podaj poświadczenia pakietu Office 365, aby aplikacja logiki mogła łączyć się z Twoim kontem. W przeciwnym razie, jeśli połączenie już istnieje, podaj informacje o właściwościach wyzwalacza.

   > [!NOTE]
   > Twoje połączenie nie wygasa, dopóki nie zostanie odwołane, nawet jeśli zmienisz poświadczenia logowania. Aby uzyskać więcej informacji, zobacz [konfigurowalne okresy istnienia tokenu w Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Ten przykład wybiera kalendarz sprawdzany przez wyzwalacz, na przykład:

   ![Skonfiguruj właściwości wyzwalacza](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. W wyzwalaczu Ustaw wartości **częstotliwości** i **interwałów** . Aby dodać inne dostępne właściwości wyzwalacza, takie jak **strefa czasowa**, wybierz te właściwości z listy **Dodaj nowy parametr** .

   Na przykład, jeśli chcesz, aby wyzwalacz sprawdzał kalendarz co 15 minut, ustaw **częstotliwość** na **minutę**i ustaw **Interwał** na `15` . 

   ![Ustawianie częstotliwości i interwału dla wyzwalacza](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Teraz Dodaj akcję, która jest uruchamiana po uruchomieniu wyzwalacza. Na przykład można dodać akcję Twilio **Wyślij wiadomość** , która wysyła tekst, gdy zdarzenie kalendarza zostanie uruchomione w ciągu 15 minut.

## <a name="add-an-action"></a>Dodawanie akcji

[Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest operacją uruchomioną przez przepływ pracy w aplikacji logiki. Ta przykładowa aplikacja logiki tworzy nowy kontakt w pakiecie Office 365 Outlook. Aby utworzyć kontakt, można użyć danych wyjściowych z innego wyzwalacza lub akcji. Na przykład załóżmy, że aplikacja logiki używa wyzwalacza Dynamics 365, **gdy rekord zostanie utworzony**. Można dodać akcję **Utwórz kontakt** programu Outlook dla pakietu Office 365 i użyć danych wyjściowych z wyzwalacza usługi Salesforce, aby utworzyć nowy kontakt.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Aby dodać akcję jako ostatni krok w przepływie pracy, wybierz pozycję **nowy krok**. 

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między tymi krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź `office 365 outlook` jako filtr. W tym przykładzie wybrano opcję **Utwórz kontakt**.

   ![Wybierz akcję do uruchomienia w aplikacji logiki](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Jeśli zostanie wyświetlony monit o zalogowanie się, podaj poświadczenia pakietu Office 365, aby aplikacja logiki mogła łączyć się z Twoim kontem. W przeciwnym razie, jeśli połączenie już istnieje, podaj informacje o właściwościach akcji.

   > [!NOTE]
   > Twoje połączenie nie wygasa, dopóki nie zostanie odwołane, nawet jeśli zmienisz poświadczenia logowania. Aby uzyskać więcej informacji, zobacz [konfigurowalne okresy istnienia tokenu w Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Ten przykład wybiera folder Kontakty, w którym akcja tworzy nowy kontakt, na przykład:

   ![Konfigurowanie właściwości akcji](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Aby dodać inne dostępne właściwości akcji, wybierz te właściwości z listy **Dodaj nowy parametr** .

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne dotyczące tego łącznika, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/office365/). 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)