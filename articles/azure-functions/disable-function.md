---
title: Jak wyłączyć funkcje w Azure Functions
description: Dowiedz się, jak wyłączyć i włączyć funkcje w Azure Functions.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: ee701e8df8faddef9bbdb16e7a1048c4dc2e40a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83848743"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Jak wyłączyć funkcje w Azure Functions

W tym artykule wyjaśniono, jak wyłączyć funkcję w Azure Functions. Aby *wyłączyć* funkcję oznacza, że środowisko uruchomieniowe zignoruje automatyczny wyzwalacz, który jest zdefiniowany dla funkcji. Dzięki temu można zapobiec uruchamianiu określonej funkcji bez zatrzymywania całej aplikacji funkcji.

Zalecanym sposobem wyłączenia funkcji jest użycie ustawienia aplikacji w formacie `AzureWebJobs.<FUNCTION_NAME>.Disabled` . To ustawienie aplikacji można utworzyć i zmodyfikować na wiele sposobów, w tym za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/) oraz z karty **zarządzanie** funkcją w [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Po wyłączeniu funkcji wyzwalanej przez protokół HTTP przy użyciu metod opisanych w tym artykule punkt końcowy może nadal być dostępny, gdy działa na komputerze lokalnym.  

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure można użyć [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) polecenie, aby utworzyć i zmodyfikować ustawienie aplikacji. Następujące polecenie wyłącza funkcję o nazwie `QueueTrigger` przez utworzenie ustawienia aplikacji o nazwie `AzureWebJobs.QueueTrigger.Disabled` Ustaw ją na `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Aby ponownie włączyć funkcję, należy ponownie uruchomić to samo polecenie o wartości `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Korzystanie z portalu

Można również użyć przycisków **Włącz** i **Wyłącz** na stronie **Przegląd** funkcji. Te przyciski działają przez tworzenie i usuwanie `AzureWebJobs.<FUNCTION_NAME>.Disabled` ustawień aplikacji.

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

> [!NOTE]  
> Funkcja testowania zintegrowanego z portalem ignoruje `Disabled` ustawienie. Oznacza to, że wyłączona funkcja nadal działa po uruchomieniu z okna **testowego** w portalu. 

## <a name="other-methods"></a>Inne metody

Mimo że metoda ustawienia aplikacji jest zalecana dla wszystkich języków i wszystkich wersji środowiska uruchomieniowego, istnieje kilka innych sposobów wyłączania funkcji. Te metody, które różnią się w zależności od języka i wersji środowiska uruchomieniowego, są utrzymywane na potrzeby zgodności z poprzednimi wersjami 

### <a name="c-class-libraries"></a>Biblioteki klas języka C#

W funkcji biblioteki klas można również użyć `Disable` atrybutu, aby zapobiec wyzwoleniu funkcji. Można użyć atrybutu bez konstruktora, jak pokazano w następującym przykładzie:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Atrybut bez parametru konstruktora wymaga ponownego skompilowania i ponownego wdrożenia projektu w celu zmiany stanu wyłączenia funkcji. Bardziej elastycznym sposobem korzystania z tego atrybutu jest dołączenie parametru konstruktora, który odwołuje się do ustawienia aplikacji logicznej, jak pokazano w następującym przykładzie:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Ta metoda pozwala włączać i wyłączać funkcję przez zmianę ustawienia aplikacji, bez ponownej kompilacji lub ponownego wdrożenia. Zmiana ustawienia aplikacji powoduje, że aplikacja funkcji zostanie ponownie uruchomiona, więc zmiana stanu wyłączenia zostanie natychmiast rozpoznana.

> [!IMPORTANT]
> Ten `Disabled` atrybut jest jedynym sposobem wyłączenia funkcji biblioteki klas. Wygenerowany *function.jsw* pliku dla funkcji biblioteki klas nie jest przeznaczony do edycji bezpośrednio. Jeśli edytujesz ten plik, nie `disabled` ma to żadnego wpływu na właściwość.
>
> To samo dotyczy przełącznika **stanu funkcji** na karcie **Zarządzanie** , ponieważ działa przez zmianę *function.jsw* pliku.
>
> Należy również pamiętać, że w portalu może wskazywać, że funkcja jest wyłączona, gdy nie jest.

### <a name="functions-1x---scripting-languages"></a>Functions 1. x — Języki obsługi skryptów

W wersji 1. x można także użyć `disabled` właściwości *function.jsw* pliku, aby poinformować, że środowisko uruchomieniowe nie wyzwala funkcji. Ta metoda działa tylko w przypadku języków skryptów, takich jak skrypt C# i JavaScript. `disabled`Właściwość może mieć wartość `true` lub na nazwę ustawienia aplikacji:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
lub 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

W drugim przykładzie funkcja jest wyłączona, gdy istnieje ustawienie aplikacji o nazwie IS_DISABLED i jest ustawione na `true` lub 1.

>[!IMPORTANT]  
>Portal używa teraz ustawień aplikacji do wyłączenia funkcji v1. x. Gdy ustawienia aplikacji powodują konflikt z function.jsw pliku, może wystąpić błąd. Należy usunąć `disabled` Właściwość z function.jsw pliku, aby uniknąć błędów. 


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano wyłączanie wyzwalaczy automatycznych. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md).
