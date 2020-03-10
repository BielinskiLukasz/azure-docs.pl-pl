---
title: Odbieranie wywołań HTTPS i odpowiadanie na nie
description: Obsługa żądań HTTPS i zdarzeń w czasie rzeczywistym przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 01/14/2020
tags: connectors
ms.openlocfilehash: 0949e50c5a4993dfbcc83b41ef01d2cea82350a8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386521"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>Odbieraj przychodzące wywołania HTTPS i odpowiadaj na nie przy użyciu Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanego wyzwalacza żądań lub akcji odpowiedzi można tworzyć automatyczne zadania i przepływy pracy, które odbierają i reagują na przychodzące żądania HTTPS. Na przykład możesz mieć aplikację logiki:

* Odbieraj żądania HTTPS dotyczące danych i odpowiadaj na nie w lokalnej bazie danych.
* Wyzwalanie przepływu pracy po wystąpieniu zewnętrznego zdarzenia elementu webhook.
* Odbieraj i odpowiadaj na wywołanie HTTPS z innej aplikacji logiki.

> [!NOTE]
> Wyzwalacz żądania obsługuje *tylko* Transport Layer Security (TLS) 1,2 dla wywołań przychodzących. Wywołania wychodzące nadal obsługują protokoły TLS 1,0, 1,1 i 1,2. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemu z protokołem TLS 1,0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Jeśli widzisz błędy uzgadniania protokołu SSL, upewnij się, że korzystasz z protokołu TLS 1,2. W przypadku wywołań przychodzących Oto obsługiwane mechanizmy szyfrowania:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [zarejestrować się w celu uzyskania bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [aplikacji logiki](../logic-apps/logic-apps-overview.md). Jeśli dopiero zaczynasz tworzyć aplikacje logiki, Dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Dodaj wyzwalacz żądania

Ten wbudowany wyzwalacz tworzy ręcznie możliwy do przełączenia punkt końcowy HTTPS, który może odbierać *tylko* przychodzące żądania HTTPS. Po wystąpieniu tego zdarzenia wyzwalacz uruchamia i uruchamia aplikację logiki. Aby uzyskać więcej informacji na temat podstawowej definicji JSON wyzwalacza i sposobu wywoływania tego wyzwalacza, zobacz [Typ wyzwalacza żądania](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) oraz [przepływy pracy wywołania, wyzwalacza lub zagnieżdżania z punktami końcowymi http w Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

1. Zaloguj się do [Azure portal](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Gdy zostanie otwarty projektant aplikacji logiki, w polu wyszukiwania wprowadź ciąg "żądanie HTTP" jako filtr. Z listy Wyzwalacze wybierz wyzwalacz **po odebraniu żądania HTTP** , który jest pierwszym krokiem w przepływie pracy aplikacji logiki.

   ![Wybierz wyzwalacz żądania](./media/connectors-native-reqres/select-request-trigger.png)

   Wyzwalacz żądania zawiera następujące właściwości:

   ![Wyzwalacz żądania](./media/connectors-native-reqres/request-trigger.png)

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagane | Opis |
   |---------------|--------------------|----------|-------------|
   | **ADRES URL POST PROTOKOŁU HTTP** | dawaj | Tak | Adres URL punktu końcowego, który jest generowany po zapisaniu aplikacji logiki i jest używany do wywoływania aplikacji logiki |
   | **Schemat JSON treści żądania** | `schema` | Nie | Schemat JSON, który opisuje właściwości i wartości w treści żądania przychodzącego |
   |||||

1. W polu **schemat JSON treści żądania** opcjonalnie wprowadź schemat JSON, który opisuje treść w żądaniu przychodzącym, na przykład:

   ![Przykładowy schemat JSON](./media/connectors-native-reqres/provide-json-schema.png)

   Projektant używa tego schematu do generowania tokenów dla właściwości w żądaniu. Dzięki temu aplikacja logiki może analizować, wykorzystywać i przekazywać dane z żądania za pośrednictwem wyzwalacza do przepływu pracy.

   Oto przykładowy schemat:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Po wprowadzeniu schematu JSON Projektant wyświetla przypomnienie, aby uwzględnić w żądaniu nagłówek `Content-Type` i ustawić wartość tego nagłówka na `application/json`. Aby uzyskać więcej informacji, zobacz temat [Obsługa typów zawartości](../logic-apps/logic-apps-content-type.md).

   ![Przypomnienie o uwzględnieniu nagłówka "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Oto, jak wygląda ten nagłówek w formacie JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Aby wygenerować schemat JSON, który jest oparty na oczekiwanym ładunku (danych), można użyć narzędzia, takiego jak [JSONSchema.NET](https://jsonschema.net)lub wykonać następujące czynności:

   1. W wyzwalaczu żądania wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

      ![Generuj schemat na podstawie ładunku](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Wprowadź przykładowy ładunek i wybierz pozycję **gotowe**.

      ![Generuj schemat na podstawie ładunku](./media/connectors-native-reqres/enter-payload.png)

      Oto przykład ładunku:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Aby określić dodatkowe właściwości, Otwórz listę **Dodaj nowy parametr** i wybierz parametry, które chcesz dodać.

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagane | Opis |
   |---------------|--------------------|----------|-------------|
   | **Metoda** | `method` | Nie | Metoda, która musi być używana przez żądanie przychodzące do wywoływania aplikacji logiki |
   | **Ścieżka względna** | `relativePath` | Nie | Ścieżka względna parametru, który może zostać zaakceptowany przez adres URL punktu końcowego aplikacji logiki |
   |||||

   Ten przykład dodaje właściwość **metody** :

   ![Dodaj parametr metody](./media/connectors-native-reqres/add-parameters.png)

   Właściwość **Metoda** zostanie wyświetlona w wyzwalaczu, aby można było wybrać metodę z listy.

   ![Select — metoda](./media/connectors-native-reqres/select-method.png)

1. Teraz Dodaj kolejną akcję w następnym kroku w przepływie pracy. W obszarze wyzwalacza wybierz pozycję **Następny krok** , aby znaleźć akcję, którą chcesz dodać.

   Na przykład można odpowiedzieć na żądanie, [dodając akcję odpowiedzi](#add-response), której można użyć do zwrócenia dostosowanej odpowiedzi i opisanej w dalszej części tego tematu.

   Aplikacja logiki utrzymuje otwarte żądanie przychodzące tylko przez jedną minutę. Przy założeniu, że przepływ pracy aplikacji logiki zawiera akcję odpowiedzi, jeśli aplikacja logiki nie zwróci odpowiedzi po upływie tego czasu, aplikacja logiki zwróci `504 GATEWAY TIMEOUT` do obiektu wywołującego. W przeciwnym razie, jeśli aplikacja logiki nie zawiera akcji odpowiedzi, aplikacja logiki natychmiast zwróci odpowiedź `202 ACCEPTED` do obiektu wywołującego.

1. Gdy skończysz, Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

   Ten krok generuje adres URL służący do wysyłania żądania wyzwalającego aplikację logiki. Aby skopiować ten adres URL, wybierz ikonę kopiowania obok adresu URL.

   ![Adres URL używany do wyzwalania aplikacji logiki](./media/connectors-native-reqres/generated-url.png)

1. Aby wyzwolić aplikację logiki, Wyślij HTTP POST do wygenerowanego adresu URL. Można na przykład użyć narzędzia, takiego jak [Poster](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Wyjściowe wyzwalacza

Poniżej znajduje się więcej informacji na temat danych wyjściowych wyzwalacza żądania:

| Nazwa właściwości JSON | Typ danych | Opis |
|--------------------|-----------|-------------|
| `headers` | Obiekt | Obiekt JSON, który opisuje nagłówki z żądania |
| `body` | Obiekt | Obiekt JSON, który opisuje zawartość treści z żądania |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Dodaj akcję odpowiedzi

Możesz użyć akcji odpowiedzi, aby odpowiedzieć na ładunek (dane) do przychodzącego żądania HTTPS, ale tylko w aplikacji logiki, która jest wyzwalana przez żądanie HTTPS. Akcję odpowiedzi można dodać w dowolnym momencie w przepływie pracy. Aby uzyskać więcej informacji na temat podstawowej definicji JSON dla tego wyzwalacza, zobacz [Typ akcji odpowiedź](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Aplikacja logiki utrzymuje otwarte żądanie przychodzące tylko przez jedną minutę. Przy założeniu, że przepływ pracy aplikacji logiki zawiera akcję odpowiedzi, jeśli aplikacja logiki nie zwróci odpowiedzi po upływie tego czasu, aplikacja logiki zwróci `504 GATEWAY TIMEOUT` do obiektu wywołującego. W przeciwnym razie, jeśli aplikacja logiki nie zawiera akcji odpowiedzi, aplikacja logiki natychmiast zwróci odpowiedź `202 ACCEPTED` do obiektu wywołującego.

1. W Projektancie aplikacji logiki w kroku, w którym chcesz dodać akcję odpowiedzi, wybierz pozycję **nowy krok**.

   Na przykład przy użyciu wyzwalacza żądania z wcześniejszych wersji:

   ![Dodaj nowy krok](./media/connectors-native-reqres/add-response.png)

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między tymi krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź "odpowiedź" jako filtr, a następnie wybierz akcję **odpowiedź** .

   ![Wybierz akcję odpowiedzi](./media/connectors-native-reqres/select-response-action.png)

   Wyzwalacz żądania jest zwinięty w tym przykładzie dla uproszczenia.

1. Dodaj wszystkie wartości, które są wymagane dla komunikatu odpowiedzi. 

   W niektórych polach kliknięcie wewnątrz nich spowoduje otwarcie listy zawartości dynamicznej. Następnie można wybrać tokeny reprezentujące dostępne dane wyjściowe z poprzednich kroków w przepływie pracy. Właściwości ze schematu określonego w poprzednim przykładzie pojawiają się teraz na liście zawartości dynamicznej.

   Na przykład dla pola **nagłówki** Dołącz `Content-Type` jako nazwę klucza, a następnie ustaw wartość klucza na `application/json` jak wspomniano wcześniej w tym temacie. Dla pola **treść** możesz wybrać pozycję wyzwalanie danych wyjściowych z listy zawartości dynamicznej.

   ![Szczegóły akcji odpowiedzi](./media/connectors-native-reqres/response-details.png)

   Aby wyświetlić nagłówki w formacie JSON, wybierz polecenie **Przełącz do widoku tekstu**.

   ![Nagłówki — Przełącz do widoku tekstu](./media/connectors-native-reqres/switch-to-text-view.png)

   Poniżej znajduje się więcej informacji na temat właściwości, które można ustawić w akcji odpowiedzi. 

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagane | Opis |
   |---------------|--------------------|----------|-------------|
   | **Kod stanu** | `statusCode` | Tak | Kod stanu do zwrócenia w odpowiedzi |
   | **Nagłówki** | `headers` | Nie | Obiekt JSON, który opisuje jeden lub więcej nagłówków do uwzględnienia w odpowiedzi |
   | **Treść** | `body` | Nie | Treść odpowiedzi |
   |||||

1. Aby określić dodatkowe właściwości, takie jak schemat JSON dla treści odpowiedzi, Otwórz listę **Dodaj nowy parametr** i wybierz parametry, które chcesz dodać.

1. Gdy skończysz, Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki

* [Łączniki dla Logic Apps](../connectors/apis-list.md)
