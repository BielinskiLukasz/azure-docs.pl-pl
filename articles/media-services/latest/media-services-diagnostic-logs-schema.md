---
title: Schematy dzienników diagnostycznych Azure Media Services — platforma Azure
description: W tym artykule przedstawiono schematy dzienników diagnostycznych Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 2f5fcf4d9106bf37bcc81388e48afe689f4ef4d6
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896056"
---
# <a name="diagnostic-logs-schemas"></a>Schematy dzienników diagnostycznych

[Azure monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które ułatwiają zrozumienie sposobu działania aplikacji. Można monitorować Media Services dzienników diagnostycznych oraz tworzyć alerty i powiadomienia dotyczące zebranych metryk i dzienników. Możesz wysyłać dzienniki do usługi [Azure Storage](https://azure.microsoft.com/services/storage/), przesyłać strumieniowo do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i eksportować je do [log Analytics](https://azure.microsoft.com/services/log-analytics/)lub korzystać z usług innych firm.

Aby uzyskać szczegółowe informacje, zobacz [Azure monitor metryki](../../azure-monitor/platform/data-platform.md) i [Azure monitor dzienników diagnostycznych](../../azure-monitor/platform/resource-logs-overview.md).

W tym artykule opisano Media Services schematy dzienników diagnostycznych.

## <a name="top-level-diagnostic-logs-schema"></a>Schemat dzienników diagnostycznych najwyższego poziomu

Aby uzyskać szczegółowy opis schematu dzienników diagnostycznych najwyższego poziomu, zobacz [obsługiwane usługi, schematy i kategorie dla dzienników diagnostycznych platformy Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schemat dziennika dostarczania kluczy

### <a name="properties"></a>Właściwości

Te właściwości są specyficzne dla schematu dziennika dostarczania kluczy.

|Nazwa|Opis|
|---|---|
|keyId|Identyfikator żądanego klucza.|
|KeyType|Może to być jedna z następujących wartości: "Clear" (bez szyfrowania), "FairPlay", "PlayReady" lub "Widevine".|
|policyName|Nazwa Azure Resource Manager zasad.|
|Obiektu TokenType|Typ tokenu.|
|statusMessage|Komunikat o stanie.|

### <a name="examples"></a>Przykłady

Właściwości schematu żądań dostarczenia klucza.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Następne kroki

[Monitorowanie metryk Media Services i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md)
