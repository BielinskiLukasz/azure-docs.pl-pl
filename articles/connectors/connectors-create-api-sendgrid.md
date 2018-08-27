---
title: Łączenie z usługą SendGrid z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które wysyłanie wiadomości e-mail i zarządzanie listami adresowymi w SendGrid za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: c8747210a77879d551e323a7c0e46a9ab013fa3f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887193"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Wysyłanie wiadomości e-mail i zarządzanie listami adresowymi w SendGrid za pomocą usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik usługi SendGrid można utworzyć zautomatyzowanym zadaniom i przepływów pracy rozpoczynających wysyłanie wiadomości e-mail, a także zarządzania listami adresatów, na przykład:

* Wyślij wiadomość e-mail.
* Dodaj adresatów, do listy.
* GET, Dodaj i Zarządzaj globalne pomijanie.

Akcje usługi SendGrid w aplikacjach logiki służy do wykonywania tych zadań. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji usługi SendGrid. 

Ten łącznik udostępnia tylko akcje, aby uruchomić aplikację logiki, użyj oddzielnych wyzwalacza, takich jak **cyklu** wyzwalacza. Na przykład należy regularnie dodawać odbiorców do listy, możesz wysłać wiadomość e-mail o adresatów oraz list za pomocą łącznika usługi Office 365 Outlook lub łącznik usługi Outlook.com.
Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* A [konta SendGrid](https://www.sendgrid.com/) i [klucz interfejsu API usługi SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Klucz interfejsu API autoryzuje aplikację logiki, aby utworzyć połączenie i dostęp do Twojego konta SendGrid.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SendGrid. Aby użyć akcji usługi SendGrid, uruchomić swoją aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-sendgrid"></a>Łączenie z usługą SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Wybierz ścieżkę: 

   * W ostatnim kroku, której chcesz dodać akcję, wybierz **nowy krok**. 

     — lub —

   * Między krokami, które chcesz dodać akcję wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "sendgrid" jako filtr. W obszarze listy akcji wybierz akcję, którą chcesz.

1. Podaj nazwę połączenia. 

1. Wprowadź klucz interfejsu API usługi SendGrid, a następnie wybierz **Utwórz**.

1. Podaj odpowiednie szczegóły wybranej akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/sendgrid/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)