---
title: Wyrażenia Azure Functions i wzorce powiązań
description: Dowiedz się, jak tworzyć różne wyrażenia powiązań Azure Functions na podstawie wspólnych wzorców.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: db6f4f938b1555091dc51e310d4d31f96f93200c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097352"
---
# <a name="azure-functions-binding-expression-patterns"></a>Wzorce wyrażeń powiązań Azure Functions

Jedną z najbardziej zaawansowanych funkcji [wyzwalaczy i powiązań](./functions-triggers-bindings.md) jest *wyrażenie wiążące*. W pliku *Function. JSON* oraz w parametrach i kodzie funkcji można użyć wyrażeń, które rozwiązują wartości z różnych źródeł.

Większość wyrażeń jest identyfikowana przez zapakowanie ich w nawiasy klamrowe. Na przykład, w funkcji wyzwalacza kolejki, `{queueTrigger}` jest rozpoznawana jako tekst komunikatu w kolejce. Jeśli właściwość dla powiązania danych wyjściowych obiektu BLOB `container/{queueTrigger}` ma wartość, a funkcja jest wyzwalana przez `HelloWorld`komunikat w kolejce, tworzony jest obiekt BLOB o nazwie `HelloWorld`. `path`

Typy wyrażeń powiązań

* [Ustawienia aplikacji](#binding-expressions---app-settings)
* [Nazwa pliku wyzwalacza](#trigger-file-name)
* [Metadane wyzwalacza](#trigger-metadata)
* [Ładunki JSON](#json-payloads)
* [Nowy identyfikator GUID](#create-guids)
* [Bieżąca data i godzina](#current-time)

## <a name="binding-expressions---app-settings"></a>Wyrażenia powiązań — ustawienia aplikacji

Najlepszym rozwiązaniem jest zarządzanie kluczami tajnymi i ciągami połączeń przy użyciu ustawień aplikacji, a nie plików konfiguracji. Pozwala to ograniczyć dostęp do tych kluczy tajnych i umożliwia bezpieczne przechowywanie plików, takich jak *Function. JSON* , w repozytoriach kontroli źródła publicznego.

Ustawienia aplikacji są również przydatne, gdy chcesz zmienić konfigurację w oparciu o środowisko. Na przykład w środowisku testowym może być konieczne monitorowanie innej kolejki lub kontenera magazynu obiektów BLOB.

Wyrażenia powiązań ustawień aplikacji są identyfikowane inaczej od innych wyrażeń powiązania: są one opakowane w znaki procentowe, a nie nawiasy klamrowe. Na przykład jeśli ścieżka powiązania danych wyjściowych obiektu `%Environment%/newblob.txt` BLOB jest `Environment` i wartość ustawienia aplikacji `Development`to `Development` , obiekt BLOB zostanie utworzony w kontenerze.

Gdy funkcja działa lokalnie, wartości ustawień aplikacji pochodzą z pliku *Local. Settings. JSON* .

Należy zauważyć, `connection` że właściwość wyzwalacze i powiązania jest szczególnym przypadkiem i automatycznie rozpoznaje wartości jako ustawienia aplikacji, bez znaków procentu. 

Poniższy przykład to wyzwalacz usługi Azure queue storage, który używa ustawienia `%input-queue-name%` aplikacji do zdefiniowania kolejki do wyzwalania.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Możesz użyć tego samego podejścia w bibliotekach klas:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Nazwa pliku wyzwalacza

Wyzwalacz `path` dla obiektu BLOB może być wzorcem, który umożliwia odwoływanie się do nazwy obiektu BLOB wyzwalającego w innych powiązaniach i kodzie funkcji. Wzorzec może również obejmować kryteria filtrowania, które określają, które obiekty blob mogą wyzwalać wywołanie funkcji.

Na przykład w poniższym powiązaniu `path` wyzwalacza obiektu BLOB wzorzec jest `sample-images/{filename}`, który tworzy wyrażenie powiązania o nazwie `filename`:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Wyrażenia `filename` można następnie użyć w powiązaniu danych wyjściowych, aby określić nazwę tworzonego obiektu BLOB:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Kod funkcji ma dostęp do tej samej wartości przy użyciu `filename` jako nazwa parametru:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Ta sama możliwość używania wyrażeń i wzorców powiązań ma zastosowanie do atrybutów w bibliotekach klas. W poniższym przykładzie parametry konstruktora atrybutów są te same `path` wartości, jak w przypadku poprzedniej *funkcji. Przykłady JSON* : 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Można również tworzyć wyrażenia dla części nazwy pliku, takiej jak rozszerzenie. Aby uzyskać więcej informacji na temat używania wyrażeń i wzorców w ciągu ścieżki obiektu BLOB, zobacz [odwołanie do powiązania obiektu blob magazynu](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadane wyzwalacza

Oprócz ładunku danych dostarczonego przez wyzwalacz (na przykład zawartości komunikatu kolejki, który wyzwolił funkcję), wiele wyzwalaczy zapewnia dodatkowe wartości metadanych. Te wartości mogą być używane jako parametry wejściowe w C# i F# lub `context.bindings` właściwości obiektu w języku JavaScript. 

Na przykład wyzwalacz usługi Azure queue storage obsługuje następujące właściwości:

* QueueTrigger — wyzwalanie zawartości komunikatu, jeśli prawidłowy ciąg
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Te wartości metadanych są dostępne we właściwościach pliku *Function. JSON* . Załóżmy na przykład, że używasz wyzwalacza kolejki, a komunikat kolejki zawiera nazwę obiektu BLOB, który ma zostać odczytany. W pliku *Function. JSON* można użyć `queueTrigger` właściwości Metadata we właściwości obiektu BLOB `path` , jak pokazano w następującym przykładzie:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Szczegóły właściwości metadanych każdego wyzwalacza są opisane w odpowiednim artykule referencyjnym. Aby zapoznać się z przykładem, zobacz [metadane wyzwalacza kolejki](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentacja jest również dostępna na karcie **integracja** w portalu, w sekcji **Dokumentacja** pod obszarem Konfiguracja powiązania.  

## <a name="json-payloads"></a>Ładunki JSON

Gdy ładunek wyzwalacza to JSON, można odwołać się do jego właściwości w konfiguracji dla innych powiązań w tej samej funkcji i w kodzie funkcji.

W poniższym przykładzie przedstawiono plik *Function. JSON* dla funkcji webhook, która otrzymuje nazwę obiektu BLOB w formacie JSON: `{"BlobName":"HelloWorld.txt"}`. Powiązanie danych wejściowych obiektu BLOB odczytuje obiekt BLOB, a powiązanie danych wyjściowych HTTP zwraca zawartość obiektu BLOB w odpowiedzi HTTP. Zwróć uwagę, że powiązanie danych wejściowych obiektu BLOB Pobiera nazwę obiektu BLOB, odwołując`"path": "strings/{BlobName}"`się bezpośrednio do `BlobName` właściwości ()

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Aby można było działać w C# i F#, potrzebna jest Klasa, która definiuje pola do deserializacji, jak w poniższym przykładzie:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

W języku JavaScript deserializacja JSON jest wykonywana automatycznie.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Notacja kropka

Jeśli niektóre właściwości w ładunku JSON są obiektami z właściwościami, można odwoływać się do nich bezpośrednio przy użyciu notacji kropkowej. Załóżmy na przykład, że kod JSON wygląda następująco:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Można odwołać się bezpośrednio `FileName` do `BlobName.FileName`programu. W tym formacie `path` json właściwość w poprzednim przykładzie będzie wyglądać następująco:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

W C#programie wymagane są dwie klasy:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Tworzenie identyfikatorów GUID

Wyrażenie `{rand-guid}` powiązania tworzy identyfikator GUID. Następująca ścieżka obiektu BLOB w `function.json` pliku tworzy obiekt BLOB o nazwie takiej jak *50710cb5-84b9-4d87-9d83-a03d6976a682. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Bieżący czas

Wyrażenie `DateTime` powiązania jest rozpoznawane `DateTime.UtcNow`jako. Następująca ścieżka obiektu BLOB w `function.json` pliku tworzy obiekt BLOB o nazwie *2018 r-02-16T17-59 -55z. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W C# programie i innych językach .NET można użyć wzorca powiązania bezwzględnego, w przeciwieństwie do deklaratywnych powiązań w *funkcji Function. JSON* i atrybutów. Bezwzględne powiązanie jest przydatne, gdy parametry powiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Aby dowiedzieć się więcej, zobacz [ C# informacje dotyczące deweloperów](functions-dotnet-class-library.md#binding-at-runtime) lub [ C# informacje dotyczące deweloperów skryptów](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Korzystanie z wartości zwracanej przez funkcję platformy Azure](./functions-bindings-return-value.md)
