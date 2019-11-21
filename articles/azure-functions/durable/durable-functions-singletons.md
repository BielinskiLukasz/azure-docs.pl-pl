---
title: Pojedyncze dla Durable Functions — Azure
description: Jak użyć pojedynczych w rozszerzeniu Durable Functions Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 8c12e0ab854bb2b5764dd326e3f1649202f6f16b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232801"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Pojedyncze Koordynatory w Durable Functions (Azure Functions)

W przypadku zadań w tle często trzeba upewnić się, że tylko jedno wystąpienie określonego programu Orchestrator działa w danym momencie. Ten rodzaj zachowań można zapewnić w [Durable Functions](durable-functions-overview.md) przez przypisanie określonego identyfikatora wystąpienia do koordynatora podczas jego tworzenia.

## <a name="singleton-example"></a>Pojedynczy przykład

Poniższy przykład pokazuje funkcję wyzwalacza HTTP, która tworzy pojedynczą aranżację zadania w tle. Kod gwarantuje, że istnieje tylko jedno wystąpienie dla określonego identyfikatora wystąpienia.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> Poprzedni C# kod jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć atrybutu `OrchestrationClient` zamiast atrybutu `DurableClient` i należy użyć typu parametru `DurableOrchestrationClient` zamiast `IDurableOrchestrationClient`. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

Poniżej przedstawiono plik function.json:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Poniżej przedstawiono kod JavaScript:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Domyślnie identyfikatory wystąpień są losowo generowanymi identyfikatorami GUID. W poprzednim przykładzie jednak identyfikator wystąpienia jest przesyłany w danych trasy z adresu URL. Kod wywołuje `GetStatusAsync`(C#) lub `getStatus` (JavaScript), aby sprawdzić, czy wystąpienie o określonym identyfikatorze jest już uruchomione. Jeśli takie wystąpienie nie jest uruchomione, zostanie utworzone nowe wystąpienie o tym IDENTYFIKATORze.

> [!NOTE]
> W tym przykładzie istnieje potencjalny warunek wyścigu. Jeśli dwa wystąpienia **HttpStartSingle** wykonywane współbieżnie, oba wywołania funkcji będą zgłaszać sukces, ale w rzeczywistości zostanie uruchomione tylko jedno wystąpienie aranżacji. W zależności od wymagań może to mieć niepożądane skutki uboczne. Z tego powodu ważne jest, aby upewnić się, że żadne dwa żądania nie mogą wykonać tej funkcji wyzwalacza współbieżnie.

Szczegóły implementacji funkcji programu Orchestrator nie mają znaczenia. Może to być zwykła funkcja programu Orchestrator, która uruchamia się i kończy, lub może być taka, która działa w nieskończoność (czyli [aranżacja Eternal](durable-functions-eternal-orchestrations.md)). Ważnym punktem jest to, że w danym momencie jest uruchomione tylko jedno wystąpienie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat natywnych funkcji protokołu HTTP dla aranżacji](durable-functions-http-features.md)
