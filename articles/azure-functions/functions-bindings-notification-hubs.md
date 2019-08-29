---
title: Notification Hubs powiązania Azure Functions
description: Dowiedz się, jak używać powiązań centrum powiadomień platformy Azure w Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 7538e47a1d0bed0c72ff5ed467c98828cc9c18ba
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086640"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hubs powiązanie danych wyjściowych dla Azure Functions

W tym artykule wyjaśniono, jak wysyłać powiadomienia wypychane za pomocą powiązań [usługi Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) w programie Azure Functions. Azure Functions obsługuje powiązania wyjściowe dla Notification Hubs.

Należy skonfigurować usługę Azure Notification Hubs dla usługi powiadomień platformy (PNS), która ma być używana. Aby dowiedzieć się, jak uzyskać powiadomienia wypychane w aplikacji klienckiej z Notification Hubs, zobacz [wprowadzenie do Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) i wybierz docelową platformę klienta z listy rozwijanej w górnej części strony.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Firma Google ma [przestarzałe Google Cloud Messaging (GCM) na korzyść usługi Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). To powiązanie danych wyjściowych nie obsługuje FCM. Aby wysyłać powiadomienia przy użyciu programu FCM, należy użyć [interfejsu API Firebase](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) bezpośrednio w funkcji lub użyć [powiadomień dotyczących szablonów](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania Notification Hubs są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) w wersji 1. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

To powiązanie nie jest dostępne w funkcjach 2. x.

## <a name="example---template"></a>Przykład — szablon

Wysyłane powiadomienia mogą być natywnymi powiadomieniami lub [powiadomieniami o szablonach](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Natywne powiadomienia są przeznaczone dla określonej platformy klienta zgodnie z `platform` konfiguracją we właściwości powiązania danych wyjściowych. Powiadomienia szablonu mogą służyć do kierowania wielu platform.   

Zobacz przykład specyficzny dla języka:

* [C#parametr out dla skryptu](#c-script-template-example---out-parameter)
* [C#skrypt — asynchroniczny](#c-script-template-example---asynchronous)
* [C#skrypt — JSON](#c-script-template-example---json)
* [C#typy bibliotek skryptów](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C#Przykładowy parametr szablonu skryptu

Ten przykład wysyła powiadomienie dla [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , która zawiera `message` symbol zastępczy w szablonie.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>C#przykładowy szablon skryptu — asynchroniczny

W przypadku korzystania z kodu asynchronicznego parametry out są niedozwolone. W tym przypadku użyj `IAsyncCollector` do zwrócenia powiadomienia dotyczącego szablonu. Poniższy kod stanowi asynchroniczny przykład kodu. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C#przykładowy szablon skryptu — JSON

Ten przykład wysyła powiadomienie dotyczące `message` [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierającej symbol zastępczy w szablonie przy użyciu prawidłowego ciągu JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#przykład szablonu skryptu — typy bibliotek

Ten przykład pokazuje, jak używać typów zdefiniowanych w [bibliotece Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>F#przykład szablonu

Ten przykład wysyła powiadomienie dla `location` [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierającej i `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Przykład szablonu JavaScript

Ten przykład wysyła powiadomienie dla `location` [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierającej i `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Przykład — natywny APN

Ten C# przykładowy skrypt pokazuje, jak wysłać natywne powiadomienie usługi APNs. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Przykład — WNS natywny

Ten C# przykładowy skrypt pokazuje, jak używać typów zdefiniowanych w [bibliotece Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) do wysyłania wyskakujących powiadomień natywnych WNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Atrybuty

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) .

Parametry i właściwości konstruktora atrybutu są opisane w sekcji [konfiguracji](#configuration) .

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i w `NotificationHub` atrybucie:

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** |Nie dotyczy| Musi być ustawiona na wartość "notificationHub". |
|**direction** |Nie dotyczy| Musi być równa "out". | 
|**name** |Nie dotyczy| Nazwa zmiennej używana w kodzie funkcji dla komunikatu centrum powiadomień. |
|**tagExpression** |**TagExpression** | Wyrażenia tagów umożliwiają określenie, że powiadomienia mają być dostarczane do zestawu urządzeń, które zostały zarejestrowane w celu otrzymywania powiadomień pasujących do wyrażenia tagu.  Aby uzyskać więcej informacji, zobacz [Routing i wyrażenia tagów](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nazwa zasobu centrum powiadomień w Azure Portal. |
|**połączenia** | **ConnectionStringSetting** | Nazwa ustawienia aplikacji, która zawiera Notification Hubs parametry połączenia.  Parametry połączenia muszą być ustawione na wartość *DefaultFullSharedAccessSignature* dla centrum powiadomień. Zobacz [Konfiguracja parametrów połączenia](#connection-string-setup) w dalszej części tego artykułu.|
|**platform** | **Platformach** | Właściwość platform wskazuje platformę klienta, której dotyczą Twoje powiadomienia. Domyślnie, jeśli właściwość platformy zostanie pominięta z powiązania danych wyjściowych, można użyć powiadomień szablonów dla wszystkich platform skonfigurowanych w centrum powiadomień platformy Azure. Aby uzyskać więcej informacji o używaniu szablonów na ogół do wysyłania powiadomień międzyplatformowych za pomocą Centrum powiadomień platformy Azure, zobacz [Szablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Po ustawieniu **platforma** musi mieć jedną z następujących wartości: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Aby uzyskać więcej informacji na temat konfigurowania centrum powiadomień dla usługi APNS i otrzymywania powiadomień w aplikacji klienckiej, zobacz [wysyłanie powiadomień wypychanych do systemu iOS przy użyciu usługi Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Aby uzyskać więcej informacji na temat konfigurowania centrum powiadomień dla usługi ADM i otrzymywania powiadomień w aplikacji Kindle, zobacz [wprowadzenie with Notification Hubs for Kindle Apps](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;[Usługi powiadomień wypychanych systemu Windows](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) ukierunkowane na platformy Windows. Windows Phone 8,1 i nowsze są również obsługiwane przez WNS. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Notification Hubs dla aplikacji platformy uniwersalnej systemu Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Usługa powiadomień wypychanych firmy Microsoft](/previous-versions/windows/apps/ff402558(v=vs.105)). Ta platforma obsługuje Windows Phone 8 i starszych Windows Phone platform. Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs w Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>przykład pliku Function. JSON

Oto przykład powiązania Notification Hubs w pliku *Function. JSON* .

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Konfiguracja parametrów połączenia

Aby użyć powiązania danych wyjściowych centrum powiadomień, należy skonfigurować parametry połączenia dla centrum. Możesz wybrać istniejące centrum powiadomień lub utworzyć nowe po prawej stronie na karcie *integracja* w Azure Portal. Parametry połączenia można również skonfigurować ręcznie. 

Aby skonfigurować parametry połączenia z istniejącym centrum powiadomień:

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum powiadomień, wybierz pozycję **zasady dostępu**i wybierz przycisk Kopiuj obok zasad **DefaultFullSharedAccessSignature** . Spowoduje to skopiowanie parametrów połączenia dla zasad DefaultFullSharedAccessSignatureymi do centrum powiadomień. Te parametry połączenia umożliwiają funkcji wysyłanie komunikatów powiadomień do centrum.
    ![Kopiowanie parametrów połączenia centrum powiadomień](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Przejdź do aplikacji funkcji w Azure Portal, wybierz pozycję **Ustawienia aplikacji**, Dodaj klucz, taki jak **MyHubConnectionString**, wklej skopiowany *DefaultFullSharedAccessSignature* dla centrum powiadomień jako wartość, a następnie kliknij przycisk  **Zapisz**.

Nazwa tego ustawienia aplikacji ma wartość ustawienia połączenie wyjściowe powiązania w *funkcji Function. JSON* lub atrybut .NET. Zapoznaj się z [sekcją konfiguracji](#configuration) wcześniejszą w tym artykule.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Tematy pomocy |
|---|---|
| Centrum powiadomień | [Przewodnik obsługi](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

