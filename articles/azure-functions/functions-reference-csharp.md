---
title: Informacje C# dotyczące deweloperów skryptów Azure Functions
description: Dowiedz się, jak opracowywać C# Azure Functions przy użyciu skryptu.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 3ac9d8d64e4f16a4d6268606e723b14e32d8c16e
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261783"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Dokumentacja C# dla deweloperów skryptów Azure Functions (CSX)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Ten artykuł stanowi wprowadzenie do tworzenia Azure Functions przy użyciu C# skryptu ( *. CSX*).

Azure Functions obsługuje C# Języki C# programowania skryptów i. Jeśli szukasz wskazówek dotyczących [używania C# w projekcie biblioteki klas programu Visual Studio](functions-develop-vs.md), zobacz [ C# Dokumentacja dewelopera](functions-dotnet-class-library.md).

W tym artykule przyjęto założenie, że został już odczytany [Przewodnik deweloperów Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Jak działa CSX

Środowisko C# skryptu dla Azure Functions jest oparte na [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Przepływy danych do C# funkcji za pośrednictwem argumentów metody. Nazwy argumentów są określone w `function.json` pliku, a istnieją wstępnie zdefiniowane nazwy do uzyskiwania dostępu do takich elementów jak Rejestrator funkcji i tokeny anulowania.

Format *. CSX* umożliwia pisanie mniej "standardowych" i skoncentrowanie się na pisaniu tylko C# funkcji. Zamiast zawijać wszystko w przestrzeni nazw i klasie, wystarczy zdefiniować `Run` metodę. Dołącz wszystkie odwołania do zestawów i przestrzenie nazw na początku pliku w zwykły sposób.

Pliki *. CSX* aplikacji funkcji są kompilowane po zainicjowaniu wystąpienia. Ten krok kompilacji oznacza, jak zimne uruchomienie może trwać dłużej C# dla funkcji skryptów w C# porównaniu z bibliotekami klas. Ten krok kompilacji ma również na C# celu edytowanie funkcji skryptów w Azure Portal, podczas gdy C# biblioteki klas nie są.

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektu C# skryptu wygląda następująco:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Istnieje udostępniony plik [host. JSON](functions-host-json.md) , który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma własny plik kodu (. CSX) i plik konfiguracji powiązania (Function. JSON).

Rozszerzenia powiązań wymagane w [wersji 2. x](functions-versions.md) środowiska uruchomieniowego funkcji są zdefiniowane w `extensions.csproj` pliku z rzeczywistymi plikami `bin` biblioteki w folderze. Podczas programowania lokalnego należy [zarejestrować rozszerzenia powiązań](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w Azure Portal Rejestracja jest wykonywana.

## <a name="binding-to-arguments"></a>Powiązanie z argumentami

Dane wejściowe lub wyjściowe są powiązane z parametrem funkcji C# skryptu za pośrednictwem `name` właściwości w pliku konfiguracyjnym *Function. JSON* . W poniższym przykładzie przedstawiono plik *Function. JSON* i plik *Run. CSX* dla funkcji wyzwalanej przez kolejkę. Parametr, który odbiera dane z komunikatu kolejki, jest nazwany `myQueueItem` , ponieważ jest to wartość `name` właściwości.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

Instrukcja została omówiona [w dalszej części tego artykułu.](#referencing-external-assemblies) `#r`

## <a name="supported-types-for-bindings"></a>Obsługiwane typy powiązań

Każde powiązanie ma własne obsługiwane typy; na przykład wyzwalacz obiektu BLOB może być używany z parametrem String, parametrem poco, `CloudBlockBlob` parametrem lub dowolnym innym obsługiwanym typem. [Artykuł dotyczący powiązań powiązań obiektów BLOB](functions-bindings-storage-blob.md#trigger---usage) zawiera listę wszystkich obsługiwanych typów parametrów dla wyzwalaczy obiektów BLOB. Aby uzyskać więcej informacji, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md) oraz [dokumenty referencyjne powiązań dla każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Odwoływanie klas niestandardowych

Jeśli musisz użyć niestandardowej klasy POCO (Custom CLR Object), możesz dołączyć definicję klasy wewnątrz tego samego pliku lub umieścić ją w osobnym pliku.

W poniższym przykładzie przedstawiono przykład *Run. CSX* , który zawiera definicję klasy poco.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Klasa POCO musi mieć zdefiniowaną metodę pobierającą i setter dla każdej właściwości.

## <a name="reusing-csx-code"></a>Używanie kodu. CSX

Można użyć klas i metod zdefiniowanych w innych plikach *. CSX* w pliku *Run. CSX* . W tym celu należy użyć `#load` dyrektyw w pliku *Run. CSX* . W poniższym przykładzie procedura rejestrowania o `MyLogger` nazwie jest udostępniana w elemencie webrejestratoru *. CSX* i załadowana do polecenia  `#load` Run. CSX przy użyciu dyrektywy:

Przykład *Run. CSX*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Przykładowy kod *CSX*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Użycie pliku Shared *. CSX* jest typowym wzorcem, gdy chcesz silnie wpisywać dane przesyłane między funkcjami przy użyciu obiektu poco. W poniższym uproszczonym przykładzie wyzwalacz http i wyzwalacz kolejki współdzielą obiekt poco o nazwie `Order` , aby silnie wpisać dane zamówienia:

Przykład *Run. CSX* dla wyzwalacza http:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Przykład *Run. CSX* dla wyzwalacza kolejki:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Przykład *Order. CSX*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

Możesz użyć ścieżki względnej z `#load` dyrektywą:

* `#load "mylogger.csx"`ładuje plik znajdujący się w folderze funkcji.
* `#load "loadedfiles\mylogger.csx"`ładuje plik znajdujący się w folderze w folderze funkcji.
* `#load "..\shared\mylogger.csx"`ładuje plik znajdujący się w folderze na tym samym poziomie co folder funkcji, czyli bezpośrednio w katalogu *wwwroot*.

Dyrektywa `#load` działa tylko z plikami *. CSX* , a nie z plikami *. cs* .

## <a name="binding-to-method-return-value"></a>Powiązanie z wartością zwracaną metody

Można użyć wartości zwracanej metody dla powiązania danych wyjściowych przy użyciu nazwy `$return` w pliku *Function. JSON*. Aby zapoznać się z przykładami, zobacz [wyzwalacze i powiązania](./functions-bindings-return-value.md).

Użyj wartości zwracanej tylko wtedy, gdy pomyślne wykonanie funkcji zawsze powoduje, że wartość zwracana zostanie przekazana do powiązania danych wyjściowych. W przeciwnym razie `ICollector` Użyj `IAsyncCollector`lub, jak pokazano w poniższej sekcji.

## <a name="writing-multiple-output-values"></a>Zapisywanie wielu wartości wyjściowych

Aby zapisać wiele wartości do powiązania danych wyjściowych lub jeśli pomyślne wywołanie funkcji może nie spowodować, że wszystko jest przekazywane do powiązania danych wyjściowych, użyj [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) typów lub. [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) Te typy są kolekcjami tylko do zapisu, które są zapisywane do powiązania danych wyjściowych, gdy metoda zostanie zakończona.

Ten przykład zapisuje wiele komunikatów w kolejce w tej samej kolejce `ICollector`przy użyciu:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Rejestrowanie

Aby zalogować dane wyjściowe do dzienników przesyłania C#strumieniowego w programie, należy dołączyć argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Zalecamy, aby ją `log`nazwać. Unikaj `Console.Write` używania w Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Aby uzyskać informacje na temat nowszej struktury rejestrowania, której można użyć zamiast `TraceWriter`programu, zobacz [Zapisywanie dzienników C# w funkcjach](functions-monitoring.md#write-logs-in-c-functions) w artykule **monitor Azure Functions** .

## <a name="async"></a>Asynchroniczne

Aby wykonać funkcję [asynchroniczną](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), należy użyć `async` `Task` słowa kluczowego i zwrócić obiekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Nie można używać `out` parametrów w funkcjach asynchronicznych. W przypadku powiązań wyjściowych Użyj zamiast niego [wartości zwracanej funkcji](#binding-to-method-return-value) lub [obiektu modułu zbierającego](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Tokeny anulowania

Funkcja może akceptować parametr [CancellationToken](/dotnet/api/system.threading.cancellationtoken) , który umożliwia systemowi operacyjnemu powiadomienie Twojego kodu, gdy funkcja zostanie zakończona. Możesz użyć tego powiadomienia, aby upewnić się, że funkcja nie kończy się nieoczekiwanie w sposób, który opuszcza dane w stanie niespójnym.

Poniższy przykład pokazuje, jak sprawdzić nieoczekiwane zakończenie działania funkcji.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
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
```

## <a name="importing-namespaces"></a>Importowanie przestrzeni nazw

Jeśli zachodzi konieczność zaimportowania przestrzeni nazw, można to zrobić w zwykły `using` sposób z klauzulą.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Następujące przestrzenie nazw są automatycznie importowane i dlatego są opcjonalne:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Odwołujące się do zestawów zewnętrznych

W przypadku zestawów struktury Dodaj odwołania za pomocą `#r "AssemblyName"` dyrektywy.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Następujące zestawy są automatycznie dodawane przez środowisko hostingu Azure Functions:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Następujące zestawy mogą być przywoływane przez prostą nazwę (na przykład `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Odwołujące się do zestawów niestandardowych

Aby odwołać się do zestawu niestandardowego, można użyć zestawu *udostępnionego* lub zestawu *prywatnego* :

* Zestawy udostępnione są współużytkowane przez wszystkie funkcje w aplikacji funkcji. Aby odwołać się do zestawu niestandardowego, Przekaż zestaw do folderu o `bin` nazwie w [folderze głównym aplikacji funkcji](functions-reference.md#folder-structure) (wwwroot).

* Zestawy prywatne są częścią danego kontekstu funkcji i obsługują ładowanie bezpośredniego różnych wersji. Zestawy prywatne należy przekazać do `bin` folderu w katalogu funkcji. Odwołuje się do zestawów przy użyciu nazwy pliku, `#r "MyAssembly.dll"`takiej jak.

Informacje o sposobach przekazywania plików do folderu funkcji znajdują się w sekcji [Zarządzanie pakietami](#using-nuget-packages).

### <a name="watched-directories"></a>Monitorowane katalogi

Katalog zawierający plik skryptu funkcji jest automatycznie oglądany pod kątem zmian w zestawach. Aby obejrzeć zmiany zestawu w innych katalogach, Dodaj je do `watchDirectories` listy w pliku [host. JSON](functions-host-json.md).

## <a name="using-nuget-packages"></a>Korzystanie z pakietów NuGet
Aby używać pakietów NuGet w funkcji 2. x C# , Przekaż plik *. proj* do folderu funkcji w systemie plików aplikacji funkcji. Poniżej znajduje się przykładowy plik *Function. proj* , który dodaje odwołanie do *Microsoft. ProjectOxford.* *1.1.0*w wersji:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    
    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Aby użyć niestandardowego źródła danych NuGet, Określ źródło w pliku *NuGet. config* w katalogu głównym aplikacja funkcji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zachowania NuGet](/nuget/consume-packages/configuring-nuget-behavior). 

> [!NOTE]
> W funkcjach 1 C# . x pakiety NuGet są przywoływane przy użyciu pliku *Project. JSON* zamiast pliku *Function. proj* .

W przypadku funkcji 1. x zamiast tego należy użyć pliku *Project. JSON* . Oto przykładowy plik *Project. JSON* : 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Korzystanie z pliku Function. proj

1. Otwórz funkcję w Azure Portal. Na karcie Dzienniki są wyświetlane dane wyjściowe instalacji pakietu.
2. Aby przekazać plik *Function. proj* , użyj jednej z metod opisanych w artykule [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate) w temacie Informacje dotyczące deweloperów Azure Functions.
3. Po przekazaniu pliku *Function. proj* zobaczysz dane wyjściowe podobne do następującego przykładu w dzienniku przesyłania strumieniowego funkcji:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Zmienne środowiskowe

Aby uzyskać zmienną środowiskową lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable`, jak pokazano w poniższym przykładzie kodu:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
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
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W C# programie i innych językach .NET można użyć [wzorca powiązania,](https://en.wikipedia.org/wiki/Imperative_programming) a nie do powiązań [deklaratywnych](https://en.wikipedia.org/wiki/Declarative_programming) w *funkcji Function. JSON*. Bezwzględne powiązanie jest przydatne, gdy parametry powiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Za pomocą tego wzorca można powiązać z obsługiwanymi powiązaniami wejściowymi i wyjściowymi na bieżąco w kodzie funkcji.

Zdefiniuj bezwzględne powiązanie w następujący sposób:

- **Nie** dołączaj wpisu w pliku *Function. JSON* dla żądanych bezwzględnych powiązań.
- Przekaż parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) wejściowy lub [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Użyj następującego C# wzorca, aby wykonać powiązanie danych.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`jest atrybutem platformy .NET, który definiuje powiązanie `T` i jest typem wejściowym lub wyjściowym obsługiwanym przez ten typ powiązania. `T`nie może być `out` typem parametru (na przykład `out JObject`). Na przykład powiązanie danych wyjściowych tabeli Mobile Apps obsługuje [sześć typów wyjściowych](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale można używać tylko [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [IAsyncCollector\<> t](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) dla `T`.

### <a name="single-attribute-example"></a>Przykład pojedynczego atrybutu

Poniższy przykładowy kod tworzy [powiązanie danych wyjściowych obiektu blob magazynu](functions-bindings-storage-blob.md#output) z ścieżką obiektu BLOB, która jest zdefiniowana w czasie wykonywania, a następnie zapisuje ciąg do obiektu BLOB.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[Obiekt blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definiuje powiązanie danych wejściowych lub wyjściowych [magazynu obiektów BLOB](functions-bindings-storage-blob.md) , a parametr [TextWriter](/dotnet/api/system.io.textwriter) jest obsługiwanym typem powiązania.

### <a name="multiple-attribute-example"></a>Przykład wielu atrybutów

W poprzednim przykładzie jest pobierane ustawienie aplikacji dla głównych parametrów połączenia konta magazynu aplikacji funkcji (co to jest `AzureWebJobsStorage`). Możesz określić niestandardowe ustawienie aplikacji do użycia dla konta magazynu, dodając [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazując tablicę atrybutów do `BindAsync<T>()`. Użyj parametru `Binder` , a nie `IBinder`.  Na przykład:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

Poniższa tabela zawiera listę atrybutów .NET dla każdego typu powiązania i pakietów, w których zostały zdefiniowane.

> [!div class="mx-codeBreakAll"]
> | Powiązanie | Atrybut | Dodawanie odwołania |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Kolejka magazynu | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Obiekt blob magazynu | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabela magazynu | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o najlepszych rozwiązaniach dotyczących Azure Functions](functions-best-practices.md)
