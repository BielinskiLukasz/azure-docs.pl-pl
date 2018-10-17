---
title: Interfejsy API protokołu HTTP w funkcje trwałe - Azure
description: Dowiedz się, jak implementować interfejsy API protokołu HTTP w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 4c5f99ed9d20076e3e25ebca261253e576572786
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354261"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Interfejsy API protokołu HTTP w funkcje trwałe (usługa Azure Functions)

Rozszerzenie zadań trwałe ujawnia zestaw interfejsów API protokołu HTTP, który może służyć do wykonywania następujących zadań:

* Pobierz stan wystąpienia aranżacji.
* Wysyła zdarzenie do wystąpienia aranżacji oczekiwania.
* Zakończ uruchomione wystąpienie aranżacji.


Każdy z tych interfejsów API protokołu HTTP jest operacja elementu webhook stosowana, który jest obsługiwany bezpośrednio przez rozszerzenie trwałe zadań. Nie są specyficzne dla dowolnej funkcji w aplikacji funkcji.

> [!NOTE]
> Operacje te można również wywołać bezpośrednio przy użyciu interfejsów API zarządzania wystąpienia na [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy. Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Odnajdywanie adresu URL interfejsu API HTTP

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy ujawnia [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) interfejsu API, który może służyć do generowania ładunków odpowiedzi HTTP zawierająca łącza do wszystkich obsługiwanych operacji. Oto przykład funkcji wyzwalacza HTTP, który pokazuje, jak używać tego interfejsu API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Ta funkcja przykład generuje następujące dane JSON w odpowiedzi. Typ danych wszystkich pól jest `string`.

| Pole             |Opis                           |
|-------------------|--------------------------------------|
| id                |Identyfikator wystąpienia aranżacji. |
| statusQueryGetUri |Adres URL stanu wystąpienia aranżacji. |
| sendEventPostUri  |"Zgłoś zdarzenie" adres URL wystąpienia aranżacji. |
| terminatePostUri  |"Terminate" adres URL wystąpienia aranżacji. |
| rewindPostUri     |"Przewijanie" adres URL wystąpienia aranżacji. |

Poniżej przedstawiono przykładową odpowiedź:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Format adresu URL elementu webhook mogą się różnić w zależności od posiadanej wersji programu host usługi Azure Functions. Powyższy przykład dotyczy hostów Azure Functions w wersji 2.0.

## <a name="async-operation-tracking"></a>Operacji asynchronicznej śledzenia

Odpowiedź HTTP wymienionych wcześniej ułatwia Implementowanie długotrwałych HTTP asynchronicznych interfejsów API za pomocą funkcje trwałe. To jest czasami określane jako *sondowania wzorca odbiorców*. Przepływ klient/serwer działa w następujący sposób:

1. Klient wysyła żądanie HTTP, aby rozpocząć proces długotrwałe, takich jak funkcja orkiestratora.
2. Wyzwalacz elementu docelowego protokołu HTTP, zwraca odpowiedź HTTP 202 z `Location` nagłówek `statusQueryGetUri` wartość.
3. Klient sonduje adresu URL w `Location` nagłówka. Kontynuuje odpowiedzi HTTP 202 z `Location` nagłówka.
4. Gdy wystąpienie kończy (lub nie powiedzie się), punkt końcowy w `Location` zwraca wartość nagłówka HTTP 200.

Ten protokół umożliwia koordynowanie długotrwałe procesy z klientami zewnętrznymi lub usług, które obsługują sondowanie punktu końcowego HTTP i postępowanie `Location` nagłówka. Podstawowe elementy już są wbudowane w interfejsów API protokołu HTTP usługi Functions trwałe.

> [!NOTE]
> Domyślnie wszystkie działania oparte na protokole HTTP, dostarczone przez [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) obsługi wzorca standardowych operacji asynchronicznej. Ta funkcja umożliwia osadzanie trwałe funkcję długo działających w ramach przepływu pracy aplikacji logiki. Szczegółowe informacje na temat aplikacji logiki obsługę wzorców asynchronicznych HTTP można znaleźć w [dokumentacja akcji i wyzwalaczy przepływu pracy usługi Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Dokumentacja interfejsu API HTTP

Wszystkie interfejsy API protokołu HTTP implementowany przez rozszerzenie wypełnij następujące parametry. Typ danych wszystkich parametrów jest `string`.

| Parametr  | Typ parametru  | Opis |
|------------|-----------------|-------------|
| instanceId | Adres URL             | Identyfikator wystąpienia aranżacji. |
| taskHub    | Ciąg zapytania    | Nazwa [Centrum zadań](durable-functions-task-hubs.md). Jeśli nie zostanie określony, przyjmowana jest nazwa Centrum zadania bieżącej aplikacji funkcji. |
| połączenie | Ciąg zapytania    | **Nazwa** parametrów połączenia dla konta magazynu. Jeśli nie zostanie określony, przyjmowana jest domyślne parametry połączenia dla aplikacji funkcji. |
| systemKey  | Ciąg zapytania    | Klucz autoryzacji wymaganych do wywołania interfejsu API. |
| showHistory| Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiono `true`, historię wykonywania aranżacji zostaną uwzględnione w ładunku odpowiedzi.| 
| showHistoryOutput| Ciąg zapytania    | Parametr opcjonalny. Jeśli ustawiono `true`, wyjściem działania zostaną uwzględnione w historii wykonywania aranżacji.| 
| createdTimeFrom  | Ciąg zapytania    | Parametr opcjonalny. Jeśli zostanie określony, filtruje listę zwracane wystąpienia, które zostały utworzone na lub po podanej sygnaturze czasowej ISO8601.|
| createdTimeTo    | Ciąg zapytania    | Parametr opcjonalny. Jeśli zostanie określony, filtruje listę zwracane wystąpienia, które zostały utworzone w lub przed podaną sygnaturą czasową ISO8601.|
| runtimeStatus    | Ciąg zapytania    | Parametr opcjonalny. Jeśli zostanie określony, filtry listę wystąpień zwrócona na podstawie ich stanu środowiska uruchomieniowego. Aby wyświetlić listę wartości stanu środowiska uruchomieniowego możliwe, zobacz [zapytań wystąpień](durable-functions-instance-management.md) tematu. |

`systemKey` jest klucz autoryzacji wygenerowany automatycznie przez hosta usługi Azure Functions. W szczególności nieograniczony dostęp do rozszerzenia zadań trwałe interfejsów API i można zarządzać w taki sam sposób jak [inne klucze autoryzacji](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Najprostszym sposobem odnajdywanie `systemKey` wartość przy użyciu `CreateCheckStatusResponse` API wymienionych wcześniej.

Kilka następnych sekcji obejmują określonych interfejsów API protokołu HTTP obsługiwane przez rozszerzenie i podają przykłady sposobu ich użycia.

### <a name="get-instance-status"></a>Pobierz stan wystąpienia

Pobiera stan wystąpienia określonego aranżacji.

#### <a name="request"></a>Żądanie

Funkcje wersji 1.0 format żądania jest następująca:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Format funkcje w wersji 2.0 ma te same parametry, ale ma nieco inny prefiks adresu URL:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Odpowiedź

Mogą być zwracane kilka wartości Kod stanu to możliwe.

* **HTTP 200 (OK)**: określone wystąpienie jest w stanie ukończone.
* **HTTP 202 (zaakceptowano)**: określone wystąpienie jest w toku.
* **HTTP 400 (nieprawidłowe żądanie)**: określone wystąpienie nie powiodło się lub został zakończony.
* **HTTP 404 (nie znaleziono)**: określone wystąpienie nie istnieje lub nie rozpoczęło działanie.
* **HTTP 500 (wewnętrzny błąd serwera)**: określone wystąpienie nie powiodło się z powodu nieobsługiwanego wyjątku.

Ładunek odpowiedzi dla **HTTP 200** i **HTTP 202** przypadkach jest obiektem JSON przy użyciu następujących pól:

| Pole           | Typ danych | Opis |
|-----------------|-----------|-------------|
| runtimeStatus   | ciąg    | Stan środowiska uruchomieniowego wystąpienia. Wartości to *systemem*, *oczekujące*, *anulowane*, *zwolniony*, *Ukończone*. |
| wejście           | JSON      | Dane JSON, używane do inicjowania wystąpienia. |
| customStatus    | JSON      | Dane JSON, używane na potrzeby stan niestandardowej aranżacji. To pole jest `null` Jeśli nie zostanie ustawiona. |
| output          | JSON      | Dane wyjściowe JSON wystąpienia. To pole jest `null` Jeśli wystąpienie nie jest w stanie ukończone. |
| wartością createdTime     | ciąg    | Podczas tworzenia tego wystąpienia. Używa ISO 8601 rozszerzone notacji. |
| lastUpdatedTime | ciąg    | Czas, jaką utrwalone ostatniego wystąpienia. Używa ISO 8601 rozszerzone notacji. |
| historyEvents   | JSON      | Tablica JSON, zawierającą historię wykonywania aranżacji. To pole jest `null` chyba że `showHistory` parametr ciągu zapytania jest równa `true`.  | 

Oto przykład ładunek odpowiedzi, tym aranżacji wykonywania historii i działania dane wyjściowe (sformatowane, aby zwiększyć czytelność):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** Odpowiedz zawiera też **lokalizacji** nagłówek odpowiedzi, który odwołuje się do tego samego adresu URL jako `statusQueryGetUri` pola wymienionych wcześniej.


### <a name="get-all-instances-status"></a>Pobierz stan dla wszystkich wystąpień

Możesz także zbadać stan dla wszystkich wystąpień. Usuń `instanceId` w żądaniu "Pobierz stan wystąpienia". Parametry są takie same jak "Pobierz stan wystąpienia". 

Jest to jedno należy pamiętać, że `connection` i `code` są opcjonalne. Jeśli uwierzytelnianie anonimowe dla funkcji, a następnie kod nie jest wymagane.
Jeśli nie chcesz używać różnych obiektów blob parametry połączenia magazynu innego niż zdefiniowane w ustawieniach aplikacji AzureWebJobsStorage, można bezpiecznie zignorować parametr ciągu zapytania połączenia.

#### <a name="request"></a>Żądanie

Funkcje wersji 1.0 format żądania jest następująca:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Format funkcje w wersji 2.0 ma te same parametry, ale nieco inny prefiks adresu URL: 

```http
GET /runtime/webhooks/durabletask/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="request-with-filters"></a>Żądania z filtrami

Można filtrować żądania.

Funkcje wersji 1.0 format żądania jest następująca:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}&createdTimeFrom={createdTimeFrom}&createdTimeTo={createdTimeTo}&runtimeStatus={runtimeStatus,runtimeStatus,...}
```

Format funkcje w wersji 2.0 ma te same parametry, ale nieco inny prefiks adresu URL: 

```http
GET /runtime/webhooks/durableTask/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}&createdTimeFrom={createdTimeFrom}&createdTimeTo={createdTimeTo}&runtimeStatus={runtimeStatus,runtimeStatus,...}
```

#### <a name="response"></a>Odpowiedź

Oto przykład ładunków odpowiedzi, takich jak stan aranżacji (sformatowane, aby zwiększyć czytelność):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Ta operacja może być bardzo kosztowna pod względem operacji We/Wy do usługi Azure Storage, jeśli jest dostępnych wiele wierszy w tabeli wystąpień. Szczegółowe informacje na temat wystąpienia tabeli znajdują się w [wydajności i skali w funkcje trwałe (usługi Azure Functions)](https://docs.microsoft.com/azure/azure-functions/durable-functions-perf-and-scale#instances-table) dokumentacji.
> 


### <a name="raise-event"></a>Wywołaj zdarzenie

Wysyła powiadomienie o zdarzeniu do uruchomionego wystąpienia aranżacji.

#### <a name="request"></a>Żądanie

Funkcje wersji 1.0 format żądania jest następująca:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Format funkcje w wersji 2.0 ma te same parametry, ale ma nieco inny prefiks adresu URL:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Żądania, że parametry dla tego interfejsu API to domyślny zestaw wspomniano wcześniej, a także następujące parametry unikatowy:

| Pole       | Typ parametru  | TType danych | Opis |
|-------------|-----------------|-----------|-------------|
| EventName   | Adres URL             | ciąg    | Nazwa zdarzenia, które czeka na wystąpienie docelowe aranżacji. |
| {content}   | Zawartość żądania | JSON      | Ładunek zdarzenia w formacie JSON. |

#### <a name="response"></a>Odpowiedź

Mogą być zwracane kilka wartości Kod stanu to możliwe.

* **HTTP 202 (zaakceptowano)**: zostaje zgłoszone zdarzenie zostało zaakceptowane do przetwarzania.
* **HTTP 400 (złe żądanie)**: zawartość żądania nie jest typu `application/json` lub nie jest prawidłowym plikiem JSON.
* **HTTP 404 (nie znaleziono)**: nie można odnaleźć określonego wystąpienia.
* **HTTP 410 (Gone)**: określone wystąpienie zostało ukończone lub nie powiodło się i nie można przetworzyć zdarzeń zostaje zgłoszone.

Oto przykładowe żądanie, która wysyła ciągu JSON `"incr"` do wystąpienia oczekiwania na zdarzenie o nazwie **operacji**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

### <a name="terminate-instance"></a>Zakończenie wystąpienia

Kończy uruchomionego wystąpienia aranżacji.

#### <a name="request"></a>Żądanie

Funkcje wersji 1.0 format żądania jest następująca:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Format funkcje w wersji 2.0 ma te same parametry, ale ma nieco inny prefiks adresu URL:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Parametry dla tego interfejsu API to domyślny zestaw wspomniano wcześniej, a także określić następujący parametr unikatowy żądań.

| Pole       | Typ parametru  | Typ danych | Opis |
|-------------|-----------------|-----------|-------------|
| reason      | Ciąg zapytania    | ciąg    | Opcjonalny. Przyczyna zakończenia wystąpienia aranżacji. |

#### <a name="response"></a>Odpowiedź

Mogą być zwracane kilka wartości Kod stanu to możliwe.

* **HTTP 202 (zaakceptowano)**: otrzymała żądanie zostało zaakceptowane do przetwarzania.
* **HTTP 404 (nie znaleziono)**: nie można odnaleźć określonego wystąpienia.
* **HTTP 410 (Gone)**: określone wystąpienie zostało ukończone lub nie powiodło się.

Oto przykładowe żądanie, kończy uruchomionego wystąpienia, która określa przyczynę **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="rewind-instance-preview"></a>Przewiń do tyłu wystąpienia (wersja zapoznawcza)

Przywraca wystąpienia orchestration nie powiodło się, do stanu uruchomienia przez odtworzenie najnowszych operacje zakończone niepowodzeniem.

#### <a name="request"></a>Żądanie

Funkcje wersji 1.0 format żądania jest następująca:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Format funkcje w wersji 2.0 ma te same parametry, ale ma nieco inny prefiks adresu URL:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Parametry dla tego interfejsu API to domyślny zestaw wspomniano wcześniej, a także określić następujący parametr unikatowy żądań.

| Pole       | Typ parametru  | Typ danych | Opis |
|-------------|-----------------|-----------|-------------|
| reason      | Ciąg zapytania    | ciąg    | Opcjonalny. Przyczyna przewijanie wystąpienia aranżacji. |

#### <a name="response"></a>Odpowiedź

Mogą być zwracane kilka wartości Kod stanu to możliwe.

* **HTTP 202 (zaakceptowano)**: przewijanie żądanie zostało zaakceptowane do przetwarzania.
* **HTTP 404 (nie znaleziono)**: nie można odnaleźć określonego wystąpienia.
* **HTTP 410 (Gone)**: określone wystąpienie zostało ukończone lub został zakończony.

Oto przykładowe żądanie, przechodzi zakończone niepowodzeniem wystąpienie, która określa przyczynę **stałej**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Informacje o sposobie obsługi błędów](durable-functions-error-handling.md)
