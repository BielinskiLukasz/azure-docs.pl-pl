---
title: Dodawanie i uruchamianie fragmentów kodu przy użyciu kodu wbudowanego
description: Dowiedz się, jak tworzyć i uruchamiać fragmenty kodu przy użyciu wbudowanych akcji kodu dla zautomatyzowanych zadań i przepływów pracy tworzonych przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75453491"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Dodawanie i uruchamianie fragmentów kodu przy użyciu kodu śródwierszowego w Azure Logic Apps

Gdy chcesz uruchomić fragment kodu wewnątrz aplikacji logiki, możesz dodać wbudowaną akcję **kodu wbudowanego** jako krok w przepływie pracy aplikacji logiki. Ta akcja działa najlepiej, gdy chcesz uruchomić kod, który pasuje do tego scenariusza:

* Działa w języku JavaScript. Więcej języków już wkrótce.
* Kończy działanie w ciągu pięciu sekund lub mniej.
* Obsługuje dane o rozmiarze do 50 MB.
* Nie wymaga pracy z [akcjami **zmiennych** ](../logic-apps/logic-apps-create-variables-store-values.md), które nie są jeszcze obsługiwane.
* Używa środowiska Node. js w wersji 8.11.1. Aby uzyskać więcej informacji, zobacz [standardowe obiekty wbudowane](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > `require()` Funkcja nie jest obsługiwana przez **wewnętrzną akcję kodu** do uruchamiania języka JavaScript.

Ta akcja uruchamia fragment kodu i zwraca dane wyjściowe z tego fragmentu jako token nazwany **wynik**, którego można użyć w kolejnych akcjach w aplikacji logiki. W przypadku innych scenariuszy, w których chcesz utworzyć funkcję dla kodu, wypróbuj [Tworzenie i wywoływanie funkcji platformy Azure](../logic-apps/logic-apps-azure-functions.md) w aplikacji logiki.

W tym artykule Przykładowa aplikacja logiki jest wyzwalana po nadejściu nowej wiadomości e-mail w ramach konta Office 365 Outlook. Fragment kodu wyodrębnia i zwraca adresy e-mail, które są wyświetlane w treści wiadomości e-mail.

![Przykład — Omówienie](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której chcesz dodać fragment kodu, w tym wyzwalacz. Jeśli nie masz aplikacji logiki, zobacz [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Przykładowa aplikacja logiki w tym temacie używa tego wyzwalacza programu Outlook pakietu Office 365: **po nadejściu nowej wiadomości e-mail**

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) połączone z aplikacją logiki

  > [!NOTE]
  > Upewnij się, że używasz konta integracji, które jest odpowiednie dla przypadku użycia lub scenariusza. Na przykład konta integracji w [warstwie Bezpłatna](../logic-apps/logic-apps-pricing.md#integration-accounts) są przeznaczone tylko dla scenariuszy i obciążeń poznawczych, a nie scenariuszy produkcyjnych, są ograniczone do użycia i przepływności i nie są obsługiwane przez umowę dotyczącą poziomu usług (SLA). Inne warstwy ponoszą koszty, ale obejmują obsługę umów SLA, oferują większą przepływność i mają wyższe limity. Dowiedz się więcej o [warstwach](../logic-apps/logic-apps-pricing.md#integration-accounts), [cenach](https://azure.microsoft.com/pricing/details/logic-apps/)i [limitach](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)konta integracji.

## <a name="add-inline-code"></a>Dodawanie kodu wbudowanego

1. Jeśli jeszcze tego nie zrobiono, w [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W projektancie Dodaj akcję **kodu wbudowanego** do lokalizacji, która ma być w przepływie pracy aplikacji logiki.

   * Aby dodać akcję na końcu przepływu pracy, wybierz pozycję **nowy krok**.

   * Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy nad strzałkę, która łączy te kroki. Wybierz znak plus (**+**), a następnie wybierz pozycję **Dodaj akcję**.

   Ten przykład dodaje akcję **kodu wbudowanego** w wyzwalaczu programu Outlook pakietu Office 365.

   ![Dodaj nowy krok](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź "kod wbudowany" jako filtr. Z listy Akcje wybierz tę akcję: **Wykonaj kod JavaScript**

   ![Wybierz pozycję "wykonaj kod JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Akcja pojawia się w Projektancie i zawiera jakiś domyślny przykładowy kod, łącznie z instrukcją Return.

   ![Akcja kodu śródwierszowego z domyślnym przykładowym kodem](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. W polu **kod** Usuń przykładowy kod i wprowadź kod, który chcesz uruchomić. Napisz kod, który został umieszczony wewnątrz metody, ale bez definiowania sygnatury metody. 

   Po wpisaniu rozpoznanego słowa kluczowego zostanie wyświetlona lista autouzupełniania, aby można było wybrać spośród dostępnych słów kluczowych, na przykład:

   ![Lista autouzupełniania słów kluczowych](./media/logic-apps-add-run-inline-code/auto-complete.png)

   W tym przykładowym fragmencie kodu najpierw jest tworzona zmienna, która przechowuje *wyrażenie regularne*, które określa wzorzec do dopasowania w tekście wejściowym. Następnie kod tworzy zmienną, która przechowuje dane treści wiadomości e-mail z wyzwalacza.

   ![Tworzenie zmiennych](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Aby wyniki z wyzwalacza i poprzednich akcji były łatwiejsze w odwołaniu, wyświetlana jest lista zawartości dynamicznej, gdy kursor znajduje się wewnątrz pola **kod** . Na potrzeby tego przykładu lista zawiera dostępne wyniki wyzwalacza, w tym token **treści** , który można teraz wybrać.

   Po wybraniu tokenu **treści** akcja kodu wbudowanego rozwiązuje token do `workflowContext` obiektu, który odwołuje się do wartości `Body` właściwości wiadomości e-mail:

   ![Wybierz wynik](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   W polu **kod** fragment kodu może użyć obiektu tylko `workflowContext` do odczytu jako dane wejściowe. Ten obiekt ma właściwości podrzędne, które dają kodowi dostęp do wyników wyzwalacza i poprzednich akcji w przepływie pracy.
   Aby uzyskać więcej informacji, zapoznaj się z sekcją w dalszej części tego tematu: [wyzwalacz odwołania i wyniki akcji w kodzie](#workflowcontext).

   > [!NOTE]
   >
   > Jeśli fragment kodu odwołuje się do nazw akcji, które używają operatora kropki (.), należy dodać te nazwy akcji do [parametru **Actions** ](#add-parameters). Te odwołania muszą również zawierać nazwy akcji z nawiasami kwadratowymi ([]) i cudzysłowem, na przykład:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Akcja kodu śródwierszowego nie wymaga `return` instrukcji, ale wyniki z `return` instrukcji są dostępne dla odwołania w późniejszych akcjach za pośrednictwem tokenu **wynikowego** . 
   Na przykład fragment kodu zwraca wynik, wywołując `match()` funkcję, która znajduje dopasowań w treści wiadomości e-mail względem wyrażenia regularnego. Akcja **redagowania** używa tokenu **wynik** do odwoływania się do wyników akcji kodu wbudowanego i tworzy pojedynczy wynik.

   ![Ukończona aplikacja logiki](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Gdy skończysz, Zapisz aplikację logiki.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Wyzwalacz odwołania i wyniki akcji w kodzie

`workflowContext` Obiekt ma tę strukturę, która obejmuje właściwości `actions`, `trigger`i `workflow` .

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Ta tabela zawiera więcej informacji o tych właściwościach:

| Właściwość | Typ | Opis |
|----------|------|-------|
| `actions` | Kolekcja obiektów | Obiekty wynikowe z akcji, które są uruchamiane przed uruchomieniem fragmentu kodu. Każdy obiekt ma parę *klucz-wartość* , gdzie klucz jest nazwą akcji, a wartość jest równoważna wywołaniu [funkcji Actions ()](../logic-apps/workflow-definition-language-functions-reference.md#actions) w `@actions('<action-name>')`. Nazwa akcji używa tej samej nazwy akcji, która jest używana w źródłowej definicji przepływu pracy, która zastępuje spacje ("") w nazwie akcji znakami podkreślenia (_). Ten obiekt zapewnia dostęp do wartości właściwości akcji z bieżącego przebiegu wystąpienia przepływu pracy. |
| `trigger` | Obiekt | Obiekt wynikowy z wyzwalacza i równoważny do wywołania [funkcji wyzwalacza ()](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Ten obiekt zapewnia dostęp do wartości właściwości wyzwalacza z bieżącego uruchomienia wystąpienia przepływu pracy. |
| `workflow` | Obiekt | Obiekt przepływu pracy i jego odpowiednik wywołujący [funkcję przepływu pracy ()](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Ten obiekt zapewnia dostęp do wartości właściwości przepływu pracy, takich jak nazwa przepływu pracy, identyfikator przebiegu itd., z bieżącego uruchomienia wystąpienia przepływu pracy. |
|||

Na przykład w tym temacie `workflowContext` obiekt zawiera te właściwości, do których Twój kod może uzyskać dostęp:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Dodaj parametry

W niektórych przypadkach może być konieczne jawne wymaganie, aby Akcja **kodu wbudowanego** zawierała wyniki z wyzwalacza lub określonych akcji, do których kod odwołuje się jako zależności przez dodanie parametrów **wyzwalacza** lub **akcji** . Ta opcja jest przydatna w scenariuszach, w których odwołania do nie są Znalezione w czasie wykonywania.

> [!TIP]
> Jeśli planujesz ponowne użycie kodu, Dodaj odwołania do właściwości przy użyciu pola **kod** , dzięki czemu kod zawiera rozpoznane odwołania do tokenów, zamiast dodawać wyzwalacz lub akcje jako jawne zależności.

Załóżmy na przykład, że masz kod odwołujący się do wyniku **SelectedOption** z akcji **Wyślij wiadomość e-mail** dotyczącą zatwierdzenia dla łącznika Office 365 Outlook. Podczas tworzenia aparat Logic Apps analizuje swój kod, aby określić, czy odwołuje się do żadnego wyzwalacza, czy wyników akcji i automatycznie uwzględnia te wyniki. W czasie wykonywania należy uzyskać błąd, że wyzwalacz lub wynik odwołania, którego dotyczy odwołanie, nie jest dostępny w `workflowContext` określonym obiekcie, można dodać ten wyzwalacz lub akcję jako jawną zależność. W tym przykładzie należy dodać parametr **Actions** i określić, że akcja **kodu wbudowanego** jawnie uwzględnia wynik z akcji **Wyślij wiadomość e-mail** dotyczącą zatwierdzenia.

Aby dodać te parametry, Otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry:

   ![Dodaj parametry](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametr | Opis |
   |-----------|-------------|
   | **Akcje** | Uwzględnij wyniki z poprzednich akcji. Zobacz [Dołącz wyniki akcji](#action-results). |
   | **Wyzwalacz** | Uwzględnij wyniki z wyzwalacza. Zobacz [Dołączanie wyników wyzwalacza](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Uwzględnij wyniki wyzwalacza

W przypadku wybrania opcji **wyzwalacze**zostanie wyświetlony monit z pytaniem, czy mają być uwzględniane wyniki wyzwalacza.

* Z listy **wyzwalacza** wybierz pozycję **tak**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Uwzględnij wyniki akcji

Jeśli wybierzesz pozycję **Akcje**, zostanie wyświetlony monit o akcje, które chcesz dodać. Jednak przed rozpoczęciem dodawania akcji potrzebna jest wersja nazwy akcji, która jest wyświetlana w definicji podstawowego przepływu pracy aplikacji logiki.

* Ta funkcja nie obsługuje zmiennych, pętli i indeksów iteracji.

* Nazwy w definicji przepływu pracy aplikacji logiki używają znaku podkreślenia (_), a nie spacji.

* W przypadku nazw akcji, które używają operatora kropki (.), Uwzględnij te operatory, na przykład:

  `My.Action.Name`

1. Na pasku narzędzi projektanta wybierz **Widok kod**, a następnie wyszukaj wewnątrz `actions` atrybutu Nazwa akcji.

   Na przykład `Send_approval_email_` jest nazwą JSON akcji **Wyślij wiadomość e-mail** dotyczącą zatwierdzenia.

   ![Znajdź nazwę akcji w formacie JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Aby powrócić do widoku projektanta, na pasku narzędzi Widok kodu wybierz opcję **Projektant**.

1. Aby dodać pierwszą akcję, w polu **działania element-1** wprowadź nazwę JSON akcji.

   ![Wprowadź pierwszą akcję](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Aby dodać kolejną akcję, wybierz pozycję **Dodaj nowy element**.

## <a name="reference"></a>Dokumentacja

Aby uzyskać więcej informacji o strukturze i składni akcji **Wykonaj kod JavaScript** w źródłowej definicji przepływu pracy aplikacji logiki przy użyciu języka definicji przepływu pracy, zapoznaj się z [sekcją odwołania](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)tej akcji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat łączników dla Azure Logic Apps](../connectors/apis-list.md)
