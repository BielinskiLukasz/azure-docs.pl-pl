---
title: Schemat zdarzeń Azure Event Grid
description: Opisuje właściwości i schemat, które są obecne dla wszystkich zdarzeń. Zdarzenia składają się z zestawu pięciu wymaganych właściwości ciągu i wymaganego obiektu danych.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244838"
---
# <a name="azure-event-grid-event-schema"></a>Schemat zdarzeń Azure Event Grid

W tym artykule opisano właściwości i schemat, które są obecne dla wszystkich zdarzeń. Zdarzenia składają się z zestawu pięciu wymaganych właściwości ciągu i wymaganego obiektu danych. Właściwości są wspólne dla wszystkich zdarzeń z dowolnego wydawcy. Obiekt danych ma właściwości, które są specyficzne dla każdego wydawcy. W przypadku tematów systemowych te właściwości są specyficzne dla dostawcy zasobów, na przykład Azure Storage lub Azure Event Hubs.

Źródła zdarzeń wysyłają zdarzenia do Azure Event Grid w tablicy, które mogą mieć kilka obiektów zdarzeń. Podczas ogłaszania zdarzeń w temacie Event Grid tablica może mieć łączny rozmiar do 1 MB. Każde zdarzenie w tablicy jest ograniczone do 64 KB (ogólna dostępność) lub 1 MB (wersja zapoznawcza). Jeśli zdarzenie lub tablica jest większe niż limity rozmiaru, otrzymasz **ładunek odpowiedzi 413 za duży**.

> [!NOTE]
> Zdarzenie o rozmiarze do 64 KB jest objęte ogólnym dostępnością Umowa dotycząca poziomu usług (SLA). Obsługa zdarzeń o rozmiarze do 1 MB jest obecnie dostępna w wersji zapoznawczej. Zdarzenia powyżej 64 KB są naliczone w przyrostach 64 KB. 

Event Grid wysyła zdarzenia do subskrybentów w tablicy, która ma pojedyncze zdarzenie. Takie zachowanie może ulec zmianie w przyszłości.

Schemat JSON dla zdarzenia Event Grid i każdego ładunku danych wydawcy platformy Azure można znaleźć w [magazynie schematów zdarzeń](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Schemat zdarzeń

W poniższym przykładzie przedstawiono właściwości, które są używane przez wszystkich wydawców zdarzeń:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Na przykład schemat opublikowany dla zdarzenia usługi Azure Blob Storage to:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Wszystkie zdarzenia mają takie same dane jak najwyższego poziomu:

| Właściwość | Typ | Wymagany | Opis |
| -------- | ---- | -------- | ----------- |
| temat | ciąg | Nie, ale jeśli ta wartość jest uwzględniona, musi dokładnie odpowiadać Event Grid tematu Azure Resource Manager identyfikator. Jeśli nie jest uwzględniony, Event Grid będzie sygnaturą zdarzenia. | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Event Grid udostępnia tę wartość. |
| subject | ciąg | Yes | Ścieżka zdefiniowana przez program Publisher do tematu zdarzenia. |
| eventType | ciąg | Yes | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Yes | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| id | ciąg | Yes | Unikatowy identyfikator zdarzenia. |
| data | obiekt | Nie | Dane zdarzenia specyficzne dla dostawcy zasobów. |
| dataVersion | ciąg | Nie, ale zostanie opatrzona pustą wartością. | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Niewymagane, ale jeśli jest uwzględnione, muszą być zgodne ze schematem Event Grid `metadataVersion` dokładnie (obecnie tylko `1`). Jeśli nie jest uwzględniony, Event Grid będzie sygnaturą zdarzenia. | Wersja schematu metadanych zdarzenia. Event Grid definiuje schemat właściwości najwyższego poziomu. Event Grid udostępnia tę wartość. |

Aby dowiedzieć się więcej o właściwościach w obiekcie danych, zobacz Źródło zdarzenia:

* [Subskrypcje platformy Azure (operacje zarządzania)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupy zasobów (operacje zarządzania)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Usługa Azure Signal](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

W przypadku niestandardowych tematów Wydawca zdarzeń określa obiekt danych. Dane najwyższego poziomu powinny mieć te same pola co standardowe zdarzenia zdefiniowane przez zasób.

Podczas publikowania zdarzeń w niestandardowych tematach, należy utworzyć tematy dotyczące wydarzeń, które ułatwią subskrybentom dowiedzieć się, czy zainteresują się wydarzeniem. Subskrybenci używają podmiotu do filtrowania i kierowania zdarzeń. Rozważ podanie ścieżki do miejsca wystąpienia zdarzenia, dzięki czemu Subskrybenci mogą filtrować według segmentów tej ścieżki. Ścieżka umożliwia subskrybentom Zawężanie lub szerokie filtrowanie zdarzeń. Na przykład jeśli podano trzy ścieżki segmentu, takie jak `/A/B/C` w temacie, subskrybenci mogą filtrować według pierwszego segmentu `/A`, aby uzyskać obszerny zestaw zdarzeń. Subskrybenci uzyskują zdarzenia z podmiotami takimi jak `/A/B/C` lub `/A/D/E`. Inni Subskrybenci mogą filtrować według `/A/B`, aby uzyskać węższy zestaw zdarzeń.

Czasami podmiot wymaga więcej szczegółów na temat tego, co się stało. Na przykład Wydawca **kont magazynu** udostępnia temat `/blobServices/default/containers/<container-name>/blobs/<file>` po dodaniu pliku do kontenera. Subskrybent może filtrować według ścieżki `/blobServices/default/containers/testcontainer`, aby uzyskać wszystkie zdarzenia dla tego kontenera, ale nie inne kontenery na koncie magazynu. Subskrybent może również filtrować lub kierować sufiksem `.txt`, aby działał tylko z plikami tekstowymi.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
