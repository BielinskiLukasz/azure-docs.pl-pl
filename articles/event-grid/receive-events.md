---
title: "Odbieranie zdarzeń z siatki zdarzeń Azure do punktu końcowego HTTP"
description: "Opisuje sposób weryfikacji punktu końcowego HTTP, a następnie odbierają i deserializować zdarzenia z usługi Azure Event siatki"
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 02/16/2018
ms.author: babanisa
ms.openlocfilehash: da4d1aff45c39d8b4ce7cdd003dd4af35dcd33b3
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
---
# <a name="receive-events-to-an-http-endpoint"></a>Odbieranie zdarzeń do punktu końcowego HTTP

W tym artykule opisano sposób [weryfikacji punktu końcowego HTTP](security-authentication.md#webhook-event-delivery) odbieranie zdarzeń z subskrypcji zdarzeń i otrzymywanie i deserializować zdarzenia. W tym artykule używa funkcji platformy Azure dla celów demonstracyjnych, jednak zastosować te same pojęcia niezależnie od tego, gdzie jest hostowana aplikacja.

> [!NOTE]
> Jest **silnie** zalecane używanie [wyzwalacz siatki zdarzeń](../azure-functions/functions-bindings-event-grid.md) podczas wyzwalania funkcji platformy Azure z siatki zdarzeń. Użycie ogólnego elementu WebHook wyzwalacza w tym miejscu jest demonstrative.

## <a name="prerequisites"></a>Wymagania wstępne

* Konieczne będzie aplikacji funkcji z [HTTP wyzwalane — funkcja](../azure-functions/functions-create-generic-webhook-triggered-function.md)

## <a name="add-dependencies"></a>Dodaj zależności

Jeśli tworzysz .net, [Dodawanie zależności](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) do funkcji dla `Microsoft.Azure.EventGrid` [pakietu Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Zestawy SDK dla innych języków są dostępne za pośrednictwem [publikowanie zestawów SDK](./sdk-overview.md#publish-sdks) odwołania. Te pakiety zawierać modeli dla typów natywnych zdarzeń, takich jak `EventGridEvent`, `StorageBlobCreatedEventData`, i `EventHubCaptureFileCreatedEventData`.

Aby to zrobić, kliknij łącze "Wyświetl pliki" w funkcji Azure (prawe okienko większości w portalu Azure functions), a następnie utwórz plik o nazwie pliku project.json. Dodaj następującą zawartość do `project.json` plik i zapisać go: 
 
 ```json
{ 
  "frameworks": { 
    "net46":{ 
      "dependencies": { 
        "Microsoft.Azure.EventGrid": "1.1.0-preview" 
      } 
    } 
   } 
} 
```

![Dodany pakiet Nuget](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Sprawdzanie poprawności punktu końcowego

Pierwszą rzeczą, którą chcemy, aby zrobić to obsłużyć `Microsoft.EventGrid.SubscriptionValidationEvent` zdarzenia. Za każdym razem, gdy utworzeniu nowej subskrypcji zdarzeń siatki zdarzeń wysyła zdarzenie sprawdzania poprawności do punktu końcowego z `validationCode` w ładunku danych. Punkt końcowy jest wymagany do echo tej w treści odpowiedzi na [okazać się punkt końcowy jest prawidłowy i należące do Ciebie](security-authentication.md#webhook-event-delivery). Jeśli używasz [wyzwalacz siatki zdarzeń](../azure-functions/functions-bindings-event-grid.md) zamiast elementu WebHook wyzwoleniu funkcja weryfikacji punktu końcowego jest już obsługiwane.

Do obsługi sprawdzania poprawności subskrypcji, należy użyć poniższego kodu:

```csharp
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 

    log.Info($"C# HTTP trigger function begun");  
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent"; 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}"); 
            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }             
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
} 

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
}; 
```

### <a name="test-validation-response"></a>Test weryfikacji odpowiedzi

Testowanie funkcji odpowiedzi weryfikacji wklejając zdarzenie próbkowania w polu testu dla funkcji:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Gdy klikniesz przycisk Uruchom, dane wyjściowe powinny być 200 OK i `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` w treści:

![Sprawdzanie poprawności odpowiedzi](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Obsługa zdarzeń magazynu obiektów Blob

Firma Microsoft może teraz rozszerzenia funkcji obsługi `Microsoft.Storage.BlobCreated`:

```cs
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 
    log.Info($"C# HTTP trigger function begun");  
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated"; 
 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>(); 
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}"); 
        }    
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};
```

### <a name="test-blob-created-event-handling"></a>Obsługi zdarzeń utworzony obiekt Blob testu

Testowanie nowej funkcji funkcji poprzez umieszczenie [zdarzenia magazynu obiektów Blob](./event-schema-blob-storage.md#example-event) w polu testu i uruchomiona:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Powinny pojawić się dane wyjściowe adresu URL obiektu blob w dzienniku funkcji:

![Dziennik wyjścia](./media/receive-events/blob-event-response.png)

Można również przeprowadzić test ten limit na żywo, tworząc konto magazynu obiektów Blob lub ogólnego przeznaczenia V2 konta magazynu (GPv2), [subskrypcji Dodawanie i zdarzenia](../storage/blobs/storage-blob-event-quickstart.md)i ustawienie punkt końcowy adres URL funkcji:

![Adres URL funkcji](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Obsługa zdarzeń niestandardowych

Ponadto umożliwia raz rozszerzenia funkcji, dzięki czemu można również obsługiwać zdarzeń niestandardowych. Możemy dodać własne zdarzenia kontrolę `Contoso.Items.ItemReceived`. Końcowe kod powinien wyglądać następująco:

```cs
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 

class ContosoItemReceivedEventData 
{ 
    public string id { get; set; }     
    public string message { get; set; }   
    public string time { get; set; }
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated"; 
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";
 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}"); 
            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>(); 
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}"); 
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>(); 
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}"); 
        } 
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
}

```

```javascript
var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Obsługa zdarzeń niestandardowych testu

Na koniec testu, że funkcja uszkodzonego teraz może obsłużyć danego typu zdarzenia niestandardowego:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Może także przetestować tę funkcję na żywo przez [wysyłania niestandardowych zdarzeń z CURL z portalu](./custom-event-quickstart-portal.md) lub [zamieszczając do niestandardowego tematu](./post-to-custom-topic.md) przy użyciu usługi lub aplikacji, która umożliwia OPUBLIKOWANIE punktu końcowego, takie jak [Postman](https://www.getpostman.com/). Tworzenie niestandardowego tematu i subskrypcji zdarzeń z punktem końcowym Ustaw jako adres URL funkcji.

## <a name="next-steps"></a>Kolejne kroki

* Eksploruj [administracyjny siatki zdarzeń platformy Azure i publikowanie zestawów SDK](./sdk-overview.md)
* Dowiedz się, jak [post do niestandardowego tematu](./post-to-custom-topic.md)
* Wypróbuj jedną z szczegółowe samouczki siatki zdarzeń i funkcje, takie jak [zmiana rozmiaru obrazów przekazany do magazynu obiektów Blob](resize-images-on-storage-blob-upload-event.md)
