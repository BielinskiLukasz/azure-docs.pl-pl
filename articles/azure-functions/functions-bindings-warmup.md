---
title: Azure Functions wyzwalacz rozgrzewania
description: Dowiedz się, jak używać wyzwalacza rozgrzewania w Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: Azure Functions, Functions, przetwarzanie zdarzeń, rozgrzewania, zimny start, Premium, Dynamic COMPUTE, architektura bezserwerowa
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: 108294e3f125da9fb009eb0a85585dab026c8d01
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933334"
---
# <a name="azure-functions-warm-up-trigger"></a>Wyzwalacz rozgrzewania Azure Functions

W tym artykule opisano sposób pracy z wyzwalaczem rozgrzewania w Azure Functions. Wyzwalacz rozgrzewania jest obsługiwany tylko w przypadku aplikacji funkcji działających w ramach [planu Premium](functions-premium-plan.md). Wyzwalacz rozgrzewania jest wywoływany po dodaniu wystąpienia w celu skalowania uruchomionej aplikacji funkcji. Wyzwalacza rozgrzewania można użyć do wstępnego ładowania zależności niestandardowych podczas [procesu wstępnego rozgrzewania](./functions-premium-plan.md#pre-warmed-instances) , dzięki czemu funkcje są gotowe do natychmiastowego uruchamiania przetwarzania żądań. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Wymagany jest pakiet NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) w wersji **3.0.5 lub nowszej** . Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz rozgrzewania umożliwia zdefiniowanie funkcji, która będzie uruchamiana w nowym wystąpieniu, gdy zostanie dodana do uruchomionej aplikacji. Możesz użyć funkcji rozgrzewania, aby otworzyć połączenia, Załaduj zależności lub uruchomić dowolną inną logikę niestandardową, zanim aplikacja zacznie odbierać ruch. 

Wyzwalacz rozgrzewania jest przeznaczony do tworzenia współużytkowanych zależności, które będą używane przez inne funkcje w aplikacji. [Zobacz przykłady zależności udostępnionych tutaj](./manage-connections.md#client-code-examples).

Należy zauważyć, że wyzwalacz rozgrzewania jest wywoływany tylko podczas operacji skalowania w poziomie, a nie w trakcie ponownego uruchamiania lub w innych uruchomieniach bez skalowania. Musisz się upewnić, że logika może ładować wszystkie wymagane zależności bez użycia wyzwalacza rozgrzewania. Ładowanie z opóźnieniem jest dobrym wzorcem do osiągnięcia tego celu.

## <a name="trigger---example"></a>Wyzwalacz — przykład

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która będzie uruchamiana w każdym nowym wystąpieniu, gdy zostanie dodana do aplikacji. Atrybut wartości zwracanej nie jest wymagany.


* Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.
* Aby użyć rozgrzewania jako funkcji biblioteki klas .NET, upewnij się, że masz odwołanie do pakietu **Microsoft. Azure. WebJobs. Extensions > = 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Komentarze symboli zastępczych pokazują, gdzie w aplikacji należy zadeklarować i zainicjować współdzielone zależności. 
[Więcej informacji na temat udostępnionych zależności znajdziesz tutaj](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)


Poniższy przykład przedstawia wyzwalacz rozgrzewania w pliku *Function. JSON* oraz [ C# funkcję skryptu](functions-reference-csharp.md) , która będzie uruchamiana w każdym nowym wystąpieniu, gdy zostanie dodana do aplikacji.

Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto *function.json* pliku:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Kod C# skryptu, który wiąże się z `HttpRequest`:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia wyzwalacz rozgrzewania w pliku *Function. JSON* oraz [funkcję języka JavaScript](functions-reference-node.md) , która będzie uruchamiana w każdym nowym wystąpieniu, gdy zostanie dodana do aplikacji.

Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto *function.json* pliku:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Poniższy przykład przedstawia wyzwalacz rozgrzewania w pliku *Function. JSON* i [funkcję języka Python](functions-reference-python.md) , która będzie uruchamiana w każdym nowym wystąpieniu, gdy zostanie dodana do aplikacji.

Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto *function.json* pliku:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

Poniższy przykład przedstawia wyzwalacz rozgrzewania w pliku *Function. JSON* oraz [funkcje języka Java](functions-reference-java.md) , które będą uruchamiane w każdym nowym wystąpieniu po dodaniu go do aplikacji.

Funkcja musi mieć nazwę ```warmup``` (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto *function.json* pliku:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Oto kodu Java:

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [ C# bibliotekach klas](functions-dotnet-class-library.md)atrybut `WarmupTrigger` jest dostępny w celu skonfigurowania funkcji.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W tym przykładzie pokazano, jak używać atrybutu [rozgrzewania](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Należy pamiętać, że funkcja musi być wywoływana ```Warmup``` i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Aby uzyskać pełny przykład, zobacz [przykład wyzwalacza](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Wyzwalacz rozgrzewania nie jest obsługiwany w języku Java jako atrybut.

---

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `WarmupTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type** | nd.| Wymagane — musi być ustawiony na `warmupTrigger`. |
| **direction** | nd.| Wymagane — musi być ustawiony na `in`. |
| **Nazwa** | nd.| Wymagane — nazwa zmiennej używana w kodzie funkcji.|

## <a name="trigger---usage"></a>Wyzwalacz — użycie

Żadne dodatkowe informacje nie są dostarczane do funkcji wyzwalanej przez funkcję rozgrzewania, gdy jest wywoływana.

## <a name="trigger---limits"></a>Wyzwalacze — limity

* Wyzwalacz rozgrzewania jest dostępny tylko dla aplikacji uruchomionych w ramach [planu Premium](./functions-premium-plan.md).
* Wyzwalacz rozgrzewania jest wywoływany tylko podczas operacji skalowania w górę, a nie w trakcie ponownego uruchamiania lub w innych uruchomieniach bez skalowania. Musisz się upewnić, że logika może ładować wszystkie wymagane zależności bez użycia wyzwalacza rozgrzewania. Ładowanie z opóźnieniem jest dobrym wzorcem do osiągnięcia tego celu.
* Nie można wywołać wyzwalacza rozgrzewania, gdy wystąpienie jest już uruchomione.
* Dla każdej aplikacji funkcji może istnieć tylko jedna funkcja wyzwalacza rozgrzewania.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
