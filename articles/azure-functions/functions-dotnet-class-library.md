---
title: Dokumentacja C# dla deweloperów Azure Functions
description: Dowiedz się, jak opracowywać C#Azure Functions przy użyciu programu.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: 388b389cca7c3e820ea3ccfd37a2a93ccd476b31
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254636"
---
# <a name="azure-functions-c-developer-reference"></a>Dokumentacja C# dla deweloperów Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ten artykuł stanowi wprowadzenie do tworzenia Azure Functions przy użyciu C# programu w bibliotekach klas platformy .NET.

Azure Functions obsługuje C# Języki C# programowania skryptów i. Jeśli szukasz wskazówek dotyczących [korzystania C# z programu w Azure Portal](functions-create-function-app-portal.md), zobacz temat [ C# Dokumentacja dla deweloperów skryptów (. CSX)](functions-reference-csharp.md).

W tym artykule przyjęto założenie, że zostały już przeczytane następujące artykuły:

* [Przewodnik dla deweloperów Azure Functions](functions-reference.md)
* [Azure Functions Visual Studio 2019 Tools](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Projekt biblioteki klas funkcji

W programie Visual Studio szablon projektu **Azure Functions** tworzy projekt biblioteki C# klas, który zawiera następujące pliki:

* plik [host. JSON](functions-host-json.md) — przechowuje ustawienia konfiguracji, które mają wpływ na wszystkie funkcje w projekcie podczas uruchamiania lokalnego lub na platformie Azure.
* [Local. Settings. JSON](functions-run-local.md#local-settings-file) — przechowuje ustawienia aplikacji i parametry połączenia, które są używane podczas uruchamiania lokalnego. Ten plik zawiera wpisy tajne i nie jest publikowany w aplikacji funkcji na platformie Azure. Zamiast tego [Dodaj ustawienia aplikacji do aplikacji funkcji](functions-develop-vs.md#function-app-settings).

Podczas kompilowania projektu, struktura folderów, która wygląda podobnie do następującego przykładu, jest generowana w katalogu danych wyjściowych kompilacji:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Ten katalog jest wdrażany w aplikacji funkcji na platformie Azure. Rozszerzenia powiązań wymagane w [wersji 2. x](functions-versions.md) środowiska uruchomieniowego funkcji są [dodawane do projektu jako pakiety NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Proces kompilacji tworzy plik *Function. JSON* dla każdej funkcji. Plik *Function. JSON* nie jest przeznaczony do edycji bezpośrednio. Nie można zmienić konfiguracji powiązań ani wyłączyć funkcji, edytując ten plik. Aby dowiedzieć się, jak wyłączyć funkcję, zobacz [How to disable](disable-function.md#functions-2x---c-class-libraries)Functions.

## <a name="methods-recognized-as-functions"></a>Metody rozpoznawane jako funkcje

W bibliotece klas funkcja jest metodą statyczną z `FunctionName` atrybutem i wyzwalaczem, jak pokazano w następującym przykładzie:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Ten `FunctionName` atrybut oznacza metodę jako punkt wejścia funkcji. Nazwa musi być unikatowa w obrębie projektu, zaczynać się od litery i zawierać tylko litery, cyfry `_`, i `-`, maksymalnie 127 znaków. Szablony projektów często tworzą metodę o nazwie `Run`, ale nazwa metody może być dowolną prawidłową C# nazwą metody.

Atrybut wyzwalacza określa typ wyzwalacza i wiąże dane wejściowe z parametrem metody. Przykładowa funkcja jest wyzwalana przez komunikat w kolejce, a komunikat kolejki jest przesyłany do metody w `myQueueItem` parametrze.

## <a name="method-signature-parameters"></a>Parametry sygnatury metody

Sygnatura metody może zawierać parametry inne niż używane z atrybutem wyzwalacza. Poniżej przedstawiono niektóre dodatkowe parametry, które można uwzględnić:

* [Powiązania wejściowe i wyjściowe](functions-triggers-bindings.md) oznaczone jako takie przez dekorowania nazwy je z atrybutami.  
* Parametr `ILogger` lub `TraceWriter` ([wersja 1. x-Only](functions-versions.md#creating-1x-apps)) do [rejestrowania](#logging).
* Parametr służący do [bezpiecznego zamykania.](#cancellation-tokens) `CancellationToken`
* Parametry [wyrażeń powiązań](./functions-bindings-expressions-patterns.md) w celu uzyskania metadanych wyzwalacza.

Kolejność parametrów w sygnaturze funkcji nie ma znaczenia. Na przykład można umieścić parametry wyzwalacza przed lub po innych powiązaniach i można umieścić parametr rejestratora przed lub po parametrach parametrów lub powiązania.

### <a name="output-binding-example"></a>Przykład powiązania danych wyjściowych

Poniższy przykład modyfikuje poprzednią wartość, dodając powiązanie kolejki wyjściowej. Funkcja zapisuje komunikat kolejki, który wyzwala funkcję do nowej kolejki komunikatu w innej kolejce.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Szczegółowe artykuły referencyjne (na przykład[kolejki magazynu](functions-bindings-storage-queue.md)) wyjaśniają, które typy parametrów mogą być używane z atrybutami wyzwalacza, danych wejściowych lub wyjściowych.

### <a name="binding-expressions-example"></a>Przykład wyrażeń powiązań

Poniższy kod pobiera nazwę kolejki do monitorowania z poziomu aplikacji i pobiera w `insertionTime` parametrach czas utworzenia komunikatu w kolejce.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Funkcja AutoGenerate. JSON

Proces kompilacji tworzy plik *Function. JSON* w folderze funkcji w folderze Build. Jak wspomniano wcześniej, ten plik nie jest przeznaczony do edycji bezpośrednio. Nie można zmienić konfiguracji powiązań ani wyłączyć funkcji, edytując ten plik. 

Celem tego pliku jest udostępnienie informacji kontrolerowi skalowania do użycia w celu [skalowania w ramach planu zużycia](functions-scale.md#how-the-consumption-and-premium-plans-work). Z tego powodu plik zawiera tylko informacje wyzwalacza, a nie dane wejściowe i wyjściowe.

Wygenerowany plik *Function. JSON* zawiera `configurationSource` właściwość, która instruuje środowisko uruchomieniowe, aby korzystało z atrybutów platformy .NET dla powiązań, a nie z konfiguracji *Function. JSON* . Oto przykład:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

Generowanie pliku *Function. JSON* jest wykonywane przez pakiet NuGet [\.funkcji Microsoft .NET\.\.SDK](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Ten sam pakiet jest używany zarówno w wersji 1. x, jak i 2. x środowiska uruchomieniowego usługi Functions. Platforma docelowa to odróżnia projekt 1. x z projektu 2. x. Poniżej przedstawiono odpowiednie części plików *. csproj* , które pokazują różne platformy docelowe i ten sam `Sdk` pakiet:

**Funkcje 1. x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Funkcje 2. x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Między zależnościami `Sdk` pakietów są wyzwalacze i powiązania. Projekt 1. x odwołuje się do 1. x wyzwalaczy i powiązań, ponieważ te wyzwalacze i powiązania są przeznaczone dla .NET Framework, natomiast wyzwalacze 2. x i powiązania są przeznaczone dla platformy .NET Core.

Pakiet `Sdk` jest również zależny od [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json), a pośrednio w [windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Te zależności zadbają o to, aby projekt używał wersji tych pakietów, które działają z wersją środowiska uruchomieniowego funkcji, której dotyczy projekt. Na przykład `Newtonsoft.Json` ma wersję 11 dla .NET Framework 4.6.1, ale środowisko uruchomieniowe Functions, które jest elementem docelowym .NET Framework 4.6.1 `Newtonsoft.Json` jest zgodne z 9.0.1. Dlatego kod funkcji w tym projekcie musi również używać `Newtonsoft.Json` 9.0.1.

Kod `Microsoft.NET.Sdk.Functions` źródłowy jest dostępny w repozytorium GitHub [usługi Azure\-Functions\-\-vs\-Build SDK](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Wersja środowiska uruchomieniowego

Program Visual Studio używa [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) do uruchamiania projektów funkcji. Podstawowe narzędzia to interfejs wiersza polecenia dla środowiska uruchomieniowego usługi Functions.

Jeśli instalujesz podstawowe narzędzia przy użyciu programu npm, które nie ma wpływu na podstawową wersję narzędzi używaną przez program Visual Studio. W przypadku środowiska uruchomieniowego usługi Functions w wersji 1. x program Visual Studio przechowuje podstawowe wersje narzędzi w programie *%USERPROFILE%\AppData\Local\Azure.Functions.CLI* i używa w tym miejscu najnowszej wersji. W przypadku funkcji 2. x podstawowe narzędzia są zawarte w rozszerzeniu **narzędzia Azure Functions i Web Jobs Tools** . Dla obu 1. x i 2. x można sprawdzić, jaka wersja jest używana w danych wyjściowych konsoli podczas uruchamiania projektu funkcji:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Obsługiwane typy powiązań

Każde powiązanie ma własne obsługiwane typy; na przykład atrybut wyzwalacza obiektu BLOB może być stosowany do parametru ciągu, poco parametru, `CloudBlockBlob` parametru lub dowolnego innego obsługiwanego typu. [Artykuł dotyczący powiązań powiązań obiektów BLOB](functions-bindings-storage-blob.md#trigger---usage) zawiera listę wszystkich obsługiwanych typów parametrów. Aby uzyskać więcej informacji, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md) oraz [dokumenty referencyjne powiązań dla każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Powiązanie z wartością zwracaną metody

Można użyć wartości zwracanej metody dla powiązania danych wyjściowych, stosując atrybut do wartości zwracanej przez metodę. Aby zapoznać się z przykładami, zobacz [wyzwalacze i powiązania](./functions-bindings-return-value.md). 

Użyj wartości zwracanej tylko wtedy, gdy pomyślne wykonanie funkcji zawsze powoduje, że wartość zwracana zostanie przekazana do powiązania danych wyjściowych. W przeciwnym razie `ICollector` Użyj `IAsyncCollector`lub, jak pokazano w poniższej sekcji.

## <a name="writing-multiple-output-values"></a>Zapisywanie wielu wartości wyjściowych

Aby zapisać wiele wartości do powiązania danych wyjściowych lub jeśli pomyślne wywołanie funkcji może nie spowodować, że wszystko jest przekazywane do powiązania danych wyjściowych, użyj [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) typów lub. [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) Te typy są kolekcjami tylko do zapisu, które są zapisywane do powiązania danych wyjściowych, gdy metoda zostanie zakończona.

Ten przykład zapisuje wiele komunikatów w kolejce w tej samej kolejce `ICollector`przy użyciu:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Rejestrowanie

Aby zalogować dane wyjściowe do dzienników przesyłania C#strumieniowego w programie, należy dołączyć argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Zalecamy, aby ją `log`nazwać, jak w poniższym przykładzie:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Unikaj `Console.Write` używania w Azure Functions. Aby uzyskać więcej informacji, zobacz [Zapisywanie dzienników C# w funkcjach](functions-monitoring.md#write-logs-in-c-functions) w artykule **monitor Azure Functions** .

## <a name="async"></a>Asynchroniczne

Aby wykonać funkcję [asynchroniczną](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), należy użyć `async` `Task` słowa kluczowego i zwrócić obiekt.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Nie można używać `out` parametrów w funkcjach asynchronicznych. W przypadku powiązań wyjściowych Użyj zamiast niego [wartości zwracanej funkcji](#binding-to-method-return-value) lub [obiektu modułu zbierającego](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Tokeny anulowania

Funkcja może akceptować parametr [CancellationToken](/dotnet/api/system.threading.cancellationtoken) , który umożliwia systemowi operacyjnemu powiadomienie Twojego kodu, gdy funkcja zostanie zakończona. Możesz użyć tego powiadomienia, aby upewnić się, że funkcja nie kończy się nieoczekiwanie w sposób, który opuszcza dane w stanie niespójnym.

Poniższy przykład pokazuje, jak sprawdzić nieoczekiwane zakończenie działania funkcji.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Zmienne środowiskowe

Aby uzyskać zmienną środowiskową lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable`, jak pokazano w poniższym przykładzie kodu:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Ustawienia aplikacji mogą być odczytywane ze zmiennych środowiskowych zarówno podczas tworzenia lokalnego, jak i uruchamiania na platformie Azure. Podczas tworzenia lokalnie ustawienia aplikacji pochodzą z `Values` kolekcji w pliku *Local. Settings. JSON* . W obu środowiskach — lokalnie i na platformie `GetEnvironmentVariable("<app setting name>")` Azure, pobiera wartość nazwanego ustawienia aplikacji. Na przykład w przypadku uruchamiania lokalnego "Moja witryna Name" zostanie zwrócona, jeśli plik *Local. Settings. JSON* zawiera `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

Właściwość [System. Configuration. ConfigurationManager. AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) jest alternatywnym interfejsem API do uzyskiwania wartości ustawień aplikacji, ale zalecamy użycie `GetEnvironmentVariable` metody, jak pokazano tutaj.

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W C# programie i innych językach .NET można użyć wzorca powiązania [bezwzględnego](https://en.wikipedia.org/wiki/Imperative_programming) , w przeciwieństwie do [deklaratywnych](https://en.wikipedia.org/wiki/Declarative_programming) powiązań w atrybutach. Bezwzględne powiązanie jest przydatne, gdy parametry powiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Za pomocą tego wzorca można powiązać z obsługiwanymi powiązaniami wejściowymi i wyjściowymi na bieżąco w kodzie funkcji.

Zdefiniuj bezwzględne powiązanie w następujący sposób:

- **Nie** dołączaj atrybutu do podpisu funkcji dla żądanych, bezwzględnych powiązań.
- Przekaż parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) wejściowy lub [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Użyj następującego C# wzorca, aby wykonać powiązanie danych.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`jest atrybutem platformy .NET, który definiuje powiązanie, `T` i jest typem wejściowym lub wyjściowym obsługiwanym przez ten typ powiązania. `T`nie może być `out` typem parametru (na przykład `out JObject`). Na przykład powiązanie danych wyjściowych tabeli Mobile Apps obsługuje [sześć typów wyjściowych](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale można używać tylko [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [IAsyncCollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) z bezwzględnym wiązaniem.

### <a name="single-attribute-example"></a>Przykład pojedynczego atrybutu

Poniższy przykładowy kod tworzy [powiązanie danych wyjściowych obiektu blob magazynu](functions-bindings-storage-blob.md#output) z ścieżką obiektu BLOB, która jest zdefiniowana w czasie wykonywania, a następnie zapisuje ciąg do obiektu BLOB.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[Obiekt blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definiuje powiązanie danych wejściowych lub wyjściowych [magazynu obiektów BLOB](functions-bindings-storage-blob.md) , a parametr [TextWriter](/dotnet/api/system.io.textwriter) jest obsługiwanym typem powiązania.

### <a name="multiple-attribute-example"></a>Przykład wielu atrybutów

W poprzednim przykładzie jest pobierane ustawienie aplikacji dla głównych parametrów połączenia konta magazynu aplikacji funkcji (co to jest `AzureWebJobsStorage`). Możesz określić niestandardowe ustawienie aplikacji do użycia dla konta magazynu, dodając [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazując tablicę atrybutów do `BindAsync<T>()`. Użyj parametru `Binder` , a nie `IBinder`.  Na przykład:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Wyzwalacze i powiązania 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących Azure Functions](functions-best-practices.md)
