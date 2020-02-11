---
title: Wywoływanie punktów końcowych HTTP i HTTPS
description: Wysyłaj żądania wychodzące do punktów końcowych HTTP i HTTPS za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 9c1b2af8d06c9466ed6c82308de941b43510238a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117960"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Wysyłaj wywołania wychodzące do punktów końcowych HTTP lub HTTPS za pomocą Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanego wyzwalacza http lub akcji można tworzyć automatyczne zadania i przepływy pracy, które regularnie wysyłają żądania do dowolnego punktu końcowego http lub https. Aby w zamian odbierać przychodzące wywołania HTTP lub HTTPS i odpowiadać na nie, użyj wbudowanego [wyzwalacza lub akcji odpowiedzi](../connectors/connectors-native-reqres.md).

Na przykład można monitorować punkt końcowy usługi dla witryny sieci Web, sprawdzając ten punkt końcowy zgodnie z określonym harmonogramem. Gdy w tym punkcie końcowym wystąpi określone zdarzenie, takie jak witryna sieci Web, zdarzenie wyzwala przepływ pracy aplikacji logiki i uruchamia określone akcje.

Aby sprawdzić lub *sondować* punkt końcowy zgodnie z regularnym harmonogramem, można użyć wyzwalacza http jako pierwszego kroku w przepływie pracy. Dla każdego sprawdzenia wyzwalacz wysyła wywołanie lub *żądanie* do punktu końcowego. Odpowiedź punktu końcowego określa, czy przepływ pracy aplikacji logiki zostanie uruchomiony. Wyzwalacz przekazuje zawartość z odpowiedzi na akcje w aplikacji logiki.

Możesz użyć akcji HTTP jako dowolnego innego kroku w przepływie pracy, aby wywołać punkt końcowy w razie potrzeby. Odpowiedź punktu końcowego określa, jak działają pozostałe akcje przepływu pracy.

Na podstawie możliwości docelowego punktu końcowego łącznik protokołu HTTP obsługuje Transport Layer Security (TLS) w wersjach 1,0, 1,1 i 1,2. Logic Apps negocjuje z punktem końcowym przy użyciu najwyższej obsługiwanej wersji. Jeśli na przykład punkt końcowy obsługuje 1,2, łącznik najpierw używa 1,2. W przeciwnym razie łącznik używa następnej najwyższej obsługiwanej wersji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL docelowego punktu końcowego, który ma zostać wywołany.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

* Aplikacja logiki, z której ma zostać wywołany docelowy punkt końcowy. Aby rozpocząć pracę z wyzwalaczem HTTP, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji HTTP, uruchom aplikację logiki z dowolnym wyzwalaczem, który chcesz. Ten przykład używa wyzwalacza HTTP jako pierwszego kroku.

## <a name="add-an-http-trigger"></a>Dodawanie wyzwalacza HTTP

Ten wbudowany wyzwalacz wykonuje wywołanie HTTP do określonego adresu URL dla punktu końcowego i zwraca odpowiedź.

1. Zaloguj się do [Azure portal](https://portal.azure.com). Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź ciąg "http" jako filtr. Z listy **wyzwalacze** Wybierz wyzwalacz **http** .

   ![Wybieranie wyzwalacza HTTP](./media/connectors-native-http/select-http-trigger.png)

   Ten przykład zmienia nazwę wyzwalacza na "wyzwalacz HTTP", tak aby krok miał bardziej opisową nazwę. Ponadto przykład później dodaje akcję HTTP, a obie nazwy muszą być unikatowe.

1. Podaj wartości [parametrów wyzwalacza http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) , które mają zostać uwzględnione w wywołaniu docelowego punktu końcowego. Skonfiguruj Cykl dla tego, jak często wyzwalacz ma sprawdzać docelowy punkt końcowy.

   ![Wprowadź parametry wyzwalacza HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   W przypadku wybrania typu uwierzytelniania innego niż **none**ustawienia uwierzytelniania różnią się w zależności od wybranej opcji. Aby uzyskać więcej informacji o typach uwierzytelniania dostępnych dla protokołu HTTP, zobacz następujące tematy:

   * [Dodawanie uwierzytelniania do wywołań wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Uwierzytelnianie dostępu do zasobów za pomocą tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md)

1. Aby dodać inne dostępne parametry, Otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki przy użyciu akcji uruchamianych podczas uruchamiania wyzwalacza.

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="add-an-http-action"></a>Dodaj akcję HTTP

Ta wbudowana akcja powoduje wywołanie HTTP do określonego adresu URL dla punktu końcowego i zwraca odpowiedź.

1. Zaloguj się do [Azure portal](https://portal.azure.com). Otwórz aplikację logiki w Projektancie aplikacji logiki.

   Ten przykład używa wyzwalacza HTTP jako pierwszego kroku.

1. W kroku, w którym chcesz dodać akcję HTTP, wybierz pozycję **nowy krok**.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź ciąg "http" jako filtr. Z listy **Akcje** wybierz akcję **http** .

   ![Wybieranie akcji HTTP](./media/connectors-native-http/select-http-action.png)

   Ten przykład zmienia nazwę akcji na "Akcja HTTP", aby krok miał bardziej opisową nazwę.

1. Podaj wartości [parametrów akcji http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) , które mają zostać uwzględnione w wywołaniu docelowego punktu końcowego.

   ![Wprowadź parametry akcji HTTP](./media/connectors-native-http/http-action-parameters.png)

   W przypadku wybrania typu uwierzytelniania innego niż **none**ustawienia uwierzytelniania różnią się w zależności od wybranej opcji. Aby uzyskać więcej informacji o typach uwierzytelniania dostępnych dla protokołu HTTP, zobacz następujące tematy:

   * [Dodawanie uwierzytelniania do wywołań wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Uwierzytelnianie dostępu do zasobów za pomocą tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md)

1. Aby dodać inne dostępne parametry, Otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="content-with-multipartform-data-type"></a>Zawartość z typem wieloczęściowym/formularzem danych

Aby obsłużyć zawartość, która ma `multipart/form-data` typu w żądaniach HTTP, można dodać obiekt JSON, który zawiera atrybuty `$content-type` i `$multipart` do treści żądania HTTP przy użyciu tego formatu.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Załóżmy na przykład, że masz aplikację logiki, która wysyła żądanie HTTP POST dla pliku programu Excel do witryny sieci Web za pomocą interfejsu API tej witryny, który obsługuje typ `multipart/form-data`. Oto, jak ta akcja może wyglądać następująco:

![Wieloczęściowe dane formularza](./media/connectors-native-http/http-action-multipart.png)

Poniżej znajduje się ten sam przykład pokazujący definicję JSON akcji HTTP w źródłowej definicji przepływu pracy:

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji na temat wyzwalaczy i parametrów akcji, zobacz następujące sekcje:

* [Parametry wyzwalacza HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametry akcji HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Szczegóły danych wyjściowych

Poniżej znajduje się więcej informacji na temat danych wyjściowych wyzwalacza HTTP lub akcji, która zwraca te informacje:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| nagłówki | obiekt | Nagłówki żądania |
| jednostce | obiekt | JSON — Obiekt | Obiekt z zawartością treści z żądania |
| kod stanu | int | Kod stanu z żądania |
|||

| Kod stanu | Opis |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Nieprawidłowe żądanie |
| 401 | Brak autoryzacji |
| 403 | Forbidden |
| 404 | Nie znaleziono |
| 500 | Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |
|||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
