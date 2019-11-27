---
title: Monitory w Durable Functions — Azure
description: Dowiedz się, jak zaimplementować Monitor stanu przy użyciu rozszerzenia Durable Functions Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 9c8edf5e8fb32160280a1ce9bff827c2e3fa14f8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232859"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scenariusz monitorowania w przykładowym monitorze Durable Functions-Pogoda

Wzorzec monitora odnosi się do elastycznego procesu *cyklicznego* w przepływie pracy — na przykład sondowania do momentu spełnienia określonych warunków. W tym artykule opisano przykład, który używa [Durable Functions](durable-functions-overview.md) do wdrożenia monitorowania.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Ten przykład służy do monitorowania bieżących warunków pogodowych lokalizacji i powiadamiania użytkownika przez program SMS, gdy Skies są jasne. Aby sprawdzić Pogoda i wysyłać alerty, można użyć zwykłej funkcji wyzwalanej przez czasomierz. Jednak jeden problem z tym podejściem to **Zarządzanie okresem istnienia**. Jeśli należy wysłać tylko jeden alert, Monitor musi wyłączyć się po wykryciu jasnej pogody. Wzorzec monitorowania może zakończyć własne wykonywanie, między innymi korzyści:

* Monitory są uruchamiane w odstępach czasu, a nie harmonogramy: wyzwalacz czasomierza *jest uruchamiany* co godzinę; Monitor *czeka* jedną godzinę między akcjami. Akcje monitora nie nakładają się, chyba że zostanie określony, co może być ważne w przypadku długotrwałych zadań.
* Monitory mogą mieć dynamiczne interwały: czas oczekiwania można zmienić w zależności od pewnego warunku.
* Monitory mogą kończyć się, gdy jakiś warunek zostanie spełniony lub zostanie przerwany przez inny proces.
* Monitory mogą przyjmować parametry. W przykładzie pokazano, jak ten sam proces monitorowania pogody można zastosować do każdej żądanej lokalizacji i numeru telefonu.
* Monitory są skalowalne. Ponieważ każdy monitor jest wystąpieniem aranżacji, można utworzyć wiele monitorów bez konieczności tworzenia nowych funkcji lub definiowania dalszych kodów.
* Monitory można łatwo zintegrować z większymi przepływami pracy. Monitor może być jedną sekcją bardziej złożonej funkcji aranżacji lub [aranżacją podrzędną](durable-functions-sub-orchestrations.md).

## <a name="configuring-twilio-integration"></a>Konfigurowanie integracji Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurowanie integracji podziemnej pogody

Ten przykład obejmuje użycie podziemnego interfejsu API Pogoda do sprawdzenia bieżących warunków pogodowych dla lokalizacji.

Pierwszym z nich jest konto podziemne. Możesz go utworzyć bezpłatnie w [https://www.wunderground.com/signup](https://www.wunderground.com/signup). Po utworzeniu konta należy uzyskać klucz interfejsu API. Możesz to zrobić, odwiedzając [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1), a następnie wybierając pozycję Ustawienia klucza. Plan dewelopera Stratus jest bezpłatny i wystarczający do uruchomienia tego przykładu.

Gdy masz klucz interfejsu API, Dodaj następujące **ustawienie aplikacji** do aplikacji funkcji.

| Nazwa ustawienia aplikacji | Opis wartości |
| - | - |
| **WeatherUndergroundApiKey**  | Klucz interfejsu API sieci podziemnej Pogoda. |

## <a name="the-functions"></a>Funkcje

W tym artykule wyjaśniono następujące funkcje w przykładowej aplikacji:

* `E3_Monitor`: funkcja programu Orchestrator, która okresowo wywołuje `E3_GetIsClear`. Wywołuje `E3_SendGoodWeatherAlert`, jeśli `E3_GetIsClear` zwraca wartość true.
* `E3_GetIsClear`: funkcja działania, która sprawdza bieżące warunki pogodowe dla lokalizacji.
* `E3_SendGoodWeatherAlert`: funkcja działania, która wysyła wiadomość SMS za pośrednictwem Twilio.

W poniższych sekcjach opisano konfigurację i kod, który jest używany C# do obsługi skryptów i języka JavaScript. Kod dla projektowania programu Visual Studio znajduje się na końcu artykułu.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Aranżacja monitorowania pogody (Visual Studio Code i Azure Portal przykładowy kod)

Funkcja **E3_Monitor** używa standardowego pliku *Function. JSON* dla funkcji programu Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Oto kod implementujący funkcję:

### <a name="c-script"></a>C#Napisy

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Ta funkcja programu Orchestrator wykonuje następujące akcje:

1. Pobiera **MonitorRequest** składa się z *lokalizacji* do monitorowania oraz *numeru telefonu* , do którego wyśle powiadomienie SMS.
2. Określa czas wygaśnięcia monitora. Przykład używa zakodowanej wartości zwięzłości.
3. Wywołuje **E3_GetIsClear** , aby określić, czy w żądanym miejscu znajdują się jasne Skies.
4. Jeśli pogoda jest jasne, program wywołuje **E3_SendGoodWeatherAlert** , aby wysłać powiadomienie SMS do żądanego numeru telefonu.
5. Tworzy trwały czasomierz w celu wznowienia aranżacji przy następnym interwale sondowania. Przykład używa zakodowanej wartości zwięzłości.
6. Kontynuuje działanie, dopóki `CurrentUtcDateTime` (.NET) lub `currentUtcDateTime` (JavaScript) przejdzie do czasu wygaśnięcia monitora lub zostanie wysłany alert programu SMS.

Jednocześnie można uruchomić wiele wystąpień programu Orchestrator, wysyłając wiele **MonitorRequests**. Można określić lokalizację do monitorowania i numer telefonu, na który ma zostać wysłany alert programu SMS.

## <a name="strongly-typed-data-transfer-net-only"></a>Transfer danych o jednoznacznie określonym typie (tylko platforma .NET)

Program Orchestrator wymaga wielu fragmentów danych, więc [udostępnione obiekty poco](../functions-reference-csharp.md#reusing-csx-code) są używane do transferu danych z silną C# ilością C# w i skryptu:  
[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

Przykładowy kod JavaScript używa zwykłych obiektów JSON jako parametrów.

## <a name="helper-activity-functions"></a>Funkcje działania pomocnika

Podobnie jak w przypadku innych próbek, funkcje działania pomocnika są regularnymi funkcjami, które używają powiązań wyzwalacza `activityTrigger`. Funkcja **E3_GetIsClear** pobiera bieżące warunki pogodowe przy użyciu podziemnego interfejsu API Pogoda i określa, czy ta wartość jest wyczyszczona. *Funkcja. JSON* jest definiowana w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

A oto implementacja. Podobnie jak w przypadku POCOs używanym do transferowania danych, logiki do obsługi wywołania interfejsu API i analizy JSON odpowiedzi jest abstrakcją klasy udostępnionej w C#. Można go znaleźć jako część [przykładowego kodu programu Visual Studio](#run-the-sample).

### <a name="c-script"></a>C#Napisy

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

Funkcja **E3_SendGoodWeatherAlert** używa powiązania Twilio do wysyłania wiadomości SMS z powiadomieniem użytkownika końcowego, że jest to dobry czas na przeprowadzenie. Jego *Funkcja Function. JSON* jest prosta:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

A Oto kod, który wysyła wiadomość SMS:

### <a name="c-script"></a>C#Napisy

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Korzystając z funkcji wyzwalanych przez protokół HTTP zawartych w przykładzie, można uruchomić aranżację, wysyłając następujące żądanie HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Wystąpienie **E3_Monitor** uruchamia i bada bieżące warunki pogodowe dla żądanej lokalizacji. Jeśli pogoda jest jasne, wywołuje funkcję działania w celu wysłania alertu; w przeciwnym razie ustawia czasomierz. Gdy czasomierz wygaśnie, aranżacja zostanie wznowiona.

Działanie aranżacji można zobaczyć, przeglądając dzienniki funkcji w portalu Azure Functions.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

Aranżacja zostanie [zakończona](durable-functions-instance-management.md) po osiągnięciu limitu czasu lub wykryciu Skies. Można również użyć `TerminateAsync` (.NET) lub `terminate` (JavaScript) wewnątrz innej funkcji lub wywołać **terminatePostUri** http post elementu webhook, do którego odwołuje się odpowiedź 202 powyżej, zastępując `{text}` z przyczyną zakończenia:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Przykładowy kod programu Visual Studio

Oto aranżacja jako pojedynczy C# plik w projekcie programu Visual Studio:

> [!NOTE]
> Musisz zainstalować pakiet NuGet `Microsoft.Azure.WebJobs.Extensions.Twilio`, aby uruchomić przykładowy kod poniżej.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazuje, jak używać Durable Functions do monitorowania stanu źródła zewnętrznego przy użyciu [trwałych czasomierzy](durable-functions-timers.md) i logiki warunkowej. Następny przykład pokazuje, jak używać zdarzeń zewnętrznych i [trwałych czasomierzy](durable-functions-timers.md) do obsługi interakcji człowieka.

> [!div class="nextstepaction"]
> [Uruchamianie przykładu interakcji człowieka](durable-functions-phone-verification.md)
