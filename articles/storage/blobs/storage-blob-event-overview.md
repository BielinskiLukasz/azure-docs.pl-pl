---
title: Reagowanie na zdarzenia usługi Azure Blob storage | Dokumentacja firmy Microsoft
description: Zasubskrybuj zdarzenia usługi Blob Storage przy użyciu usługi Azure Event Grid.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 68d722338562d21d59dd720250a62b8603c8af43
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076302"
---
# <a name="reacting-to-blob-storage-events"></a>Reagowanie na zdarzenia usługi Blob storage

Zdarzenia usługi Azure Storage umożliwiają aplikacjom reagować na tworzenie i usuwanie obiektów blob za pomocą nowoczesnych architektur bez użycia serwera. Robi to bez konieczności skomplikowanego kodu lub sondowania kosztowne i nieefektywna usług.  Zamiast tego zdarzenia są przekazywane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) dla subskrybentów, takie jak [usługi Azure Functions](https://azure.microsoft.com/services/functions/), [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet własne odbiornika http niestandardowego, a tylko płatność za rzeczywiste użycie. 

Typowe scenariusze zdarzenia magazynu obiektów Blob obejmują przetwarzanie obrazu lub filmu wideo, indeksowanie wyszukiwania lub przepływ pracy korzystający z pliku.  Przekazywanie plików asynchroniczne są doskonałe rozwiązanie dla zdarzenia.  Gdy zmiany są rzadkie, ale dany scenariusz wymaga natychmiastowej reakcji, oparte na zdarzeniach architektury może być szczególnie wydajne.

Dostępność dla zdarzenia magazynu jest powiązana z usługi Event Grid [dostępności](../../event-grid/overview.md) i będą dostępne w innych regionach, jak usługa Event Grid. Przyjrzyj się [zdarzenia usługi Route Blob storage do niestandardowego sieci web punktu końcowego — interfejs wiersza polecenia](storage-blob-event-quickstart.md) lub [zdarzenia usługi Route Blob storage do niestandardowego sieci web punktu końcowego - PowerShell](storage-blob-event-quickstart-powershell.md) potrzeby krótkiego przykładu. 

![Model siatki zdarzeń](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Konta usługi Blob Storage
Zdarzenia usługi blob storage są dostępne w [konta usługi Blob storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) i [kont magazynu ogólnego przeznaczenia v2](../common/storage-account-options.md#general-purpose-v2-accounts). Konta **ogólnego przeznaczenia w wersji 2 (GPv2)** to konta magazynu, które obsługują wszystkie funkcje wszystkich usług magazynu, w tym usług Blobs, Files, Queues i Tables. **Konto usługi Blob Storage** to specjalne konto magazynu służące do przechowywania danych niestrukturalnych w formie obiektów blob w usłudze Azure Storage. Konta usługi Blob Storage przypominają konta magazynu ogólnego przeznaczenia i udostępniają wszystkie używane obecnie funkcje doskonałej trwałości, dostępności, skalowalności i wydajności, łącznie z pełną spójnością interfejsu API na potrzeby blokowych obiektów blob i obiektów blob dołączania. W przypadku aplikacji wymagających tylko magazynu obiektów blokowych lub uzupełnialnych obiektów blob zalecamy używanie kont usługi Blob Storage. 

## <a name="available-blob-storage-events"></a>Dostępności zdarzenia usługi Blob storage
Korzysta z usługi Event grid [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów.  Subskrypcje zdarzeń magazyn obiektów blob może zawierać dwa typy zdarzeń:  

> |Nazwa zdarzenia|Opis|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Wywoływane, gdy obiekt blob zostanie utworzony lub zastąpiony przez `PutBlob`, `PutBlockList`, lub `CopyBlob` operacji|
> |`Microsoft.Storage.BlobDeleted`|Wywoływane, gdy obiekt blob jest usuwane przez `DeleteBlob` operacji|

## <a name="event-schema"></a>Schemat zdarzeń
Zdarzenia usługi blob storage zawiera wszystkie informacje potrzebne do reagowania na zmiany w danych.  Można zidentyfikować zdarzenia magazynu obiektów Blob, ponieważ właściwość Typ zdarzenia, który rozpoczyna się od "Microsoft.Storage". Dodatkowe informacje na temat użycia właściwości zdarzeń usługi Event Grid jest udokumentowany w [schematu zdarzeń usługi Event Grid](../../event-grid/event-schema.md).  

> |Właściwość|Typ|Opis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |temat|ciąg|Pełny identyfikator usługi Azure Resource Manager konta magazynu, który emituje zdarzenia.|
> |Temat|ciąg|Ścieżka względna zasobu do obiektu, który jest przedmiotem zdarzenia, korzystając z tych samych rozszerzony format usługi Azure Resource Manager, której używamy w celu opisania kont magazynu, usługi i kontenery dla RBAC platformy Azure.  Ten format obejmuje zachowywanie nazwa obiektu blob.|
> |eventTime|ciąg|Data/Godzina, który wygenerowania zdarzenia w formacie ISO 8601|
> |Typ zdarzenia|ciąg|"Microsoft.Storage.BlobCreated" lub "Microsoft.Storage.BlobDeleted"|
> |Identyfikator|ciąg|Unikatowy identyfikator, jeśli to zdarzenie|
> |dataVersion|ciąg|Wersja schematu dla obiektu danych.|
> |metadataVersion|ciąg|Wersja schematu właściwości najwyższego poziomu.|
> |dane|obiekt|Zbieranie danych zdarzeń specyficznych dla magazynu obiektów blob|
> |data.contentType|ciąg|Typ zawartości obiektu blob, ponieważ do zwrócenia w nagłówku Content-Type z obiektu blob|
> |data.contentLength|numer|Rozmiar obiektu blob, tak jak w liczbę całkowitą reprezentującą liczbę bajtów, ponieważ do zwrócenia w nagłówku Content-Length z obiektu blob.  Wysyłane z BlobCreated zdarzenia, ale nie z BlobDeleted.|
> |data.url|ciąg|Adres url obiektu, który jest przedmiotem zdarzenia|
> |data.eTag|ciąg|Element etag obiektu, gdy to zdarzenie wyzwolone.  Nie jest dostępna dla zdarzenia BlobDeleted.|
> |data.api|ciąg|Nazwa operacji interfejsu api, która wyzwoliła tego zdarzenia. W przypadku zdarzeń BlobCreated ta wartość jest "PutBlob", "PutBlockList" lub "CopyBlob". W przypadku zdarzeń BlobDeleted ta wartość jest "DeleteBlob". Te wartości są takie same nazwy interfejsu api, które znajdują się w dziennikach diagnostycznych usługi Azure Storage. Zobacz [rejestrowane komunikaty o stanie i operacje](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|ciąg|Nieprzezroczysty Ciąg reprezentującą logiczne sekwencję zdarzeń o dowolną nazwę określonego obiektu blob.  Użytkownicy mogą używać standardowego porównywania ciągów zrozumienie względne sekwencji dwa zdarzenia w tej samej nazwy obiektu blob.|
> |data.requestId|ciąg|Identyfikator generowanych przez usługi żądania dla operacji interfejsu API usługi storage. Może służyć do odnoszą się do usługi Azure Storage diagnostyczne dzienniki, korzystając z pola "żądanie id-header" w dziennikach i zwracany jest zainicjowanie wywołanie interfejsu API w nagłówku "x-ms-request-id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|ciąg|Identyfikator żądania dostarczonych przez klienta do przechowywania operacji interfejsu API. Może służyć do odnoszą się do dzienników diagnostycznych usługi Azure Storage, korzystając z pola "client-request-id" w dziennikach i mogą otrzymywać żądania klientów przy użyciu nagłówek "x-ms klient request-id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
> |data.storageDiagnostics|obiekt|Dane diagnostyczne od czasu do czasu dołączane przez usługę Azure Storage. Jeśli jest obecny, mają być ignorowane przez odbiorców zdarzeń.|
|data.blobType|ciąg|Typ obiektu blob. Prawidłowe wartości to "BlockBlob" lub "PageBlob".| 

Oto przykład zdarzenia BlobCreated:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Aby uzyskać więcej informacji, zobacz [schemat zdarzenia magazynu obiektów Blob](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtrowanie zdarzeń
Subskrypcje zdarzeń obiektów blob można filtrować na podstawie typ zdarzenia i według nazwy kontenera i nazwa obiektu blob obiektu, który został utworzony lub usunąć.  Filtry można zastosować do subskrypcji zdarzeń albo podczas [tworzenia](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) subskrypcji zdarzeń lub [w późniejszym czasie](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Filtry tematu usługi Event Grid pracę, sądząc po "rozpoczyna się od" a "kończy się na" dopasowania, tak aby zdarzeń za pomocą dopasowywania podmiotu są dostarczane do subskrybenta. 

Temat zdarzenia usługi Blob storage używany format:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Aby dopasować wszystkich zdarzeń dla konta magazynu, można pozostawić filtry podmiotu puste.

Aby dopasować zdarzenia z obiektów blob utworzonych w zestawie kontenerów udostępnianie prefiksu, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containerprefix
```

Aby dopasować zdarzenia utworzone w określonym kontenerze obiektów blob, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containername/
```

Aby dopasować zdarzenia z obiektów blob utworzonych w określonym kontenerze udostępnianie prefiks nazwy obiektu blob, użyj `subjectBeginsWith` filtrowania, takie jak:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Aby dopasować zdarzenia z obiektów blob utworzonych w kontenerze określonym udostępnianie sufiks obiektów blob, użyj `subjectEndsWith` filtru, takie jak "log" lub ".jpg". Aby uzyskać więcej informacji, zobacz [Event Grid — pojęcia](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Wskazówki dotyczące używania zdarzenia
Aplikacje, które obsługują zdarzenia usługi Blob storage, należy wykonać kilka zalecanych rozwiązań:
> [!div class="checklist"]
> * Zgodnie z wieloma subskrypcjami można skonfigurować w celu kierowanie zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, nie przyjęto założenie, że są zdarzenia z konkretnego źródła, ale Sprawdź tematu wiadomości, aby upewnić się, że pochodzi on z konta magazynu, które są oczekiwane.
> * Podobnie upewnij się, że zdarzenia jest przygotowany do procesów, a nie należy zakładać, że wszystkie zdarzenia, które otrzymujesz będzie typów, których oczekujesz.
> * Nadejścia nowych wiadomości mogą poza kolejnością i po pewne opóźnienie, należy użyć pól element etag, aby zrozumieć, jeśli Twoje informacje, informacje o obiektach są nadal aktualne.  Ponadto użyć pola program sequencer, aby zrozumieć kolejności zdarzeń dla dowolnego określonego obiektu.
> * Użyj pola blobType, aby zrozumieć, jakiego typu operacje są dozwolone w obiekcie blob, a typy biblioteki klienckiej, która powinna być używana do dostępu do obiektu blob. Prawidłowe wartości to albo `BlockBlob` lub `PageBlob`. 
> * Pole adresu url za pomocą `CloudBlockBlob` i `CloudAppendBlob` z konstruktorów mają dostęp do obiektu blob.
> * Ignoruj pola, których nie rozumie. Praktyka ta będzie zapewnić Ci odporne na błędy do nowych funkcji, które mogłyby zostać dodane w przyszłości.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Event Grid i wypróbuj zdarzeń usługi Blob storage:

- [Event Grid — informacje](../../event-grid/overview.md)
- [Kierowanie magazynu obiektów Blob zdarzeń do niestandardowego internetowego punktu końcowego](storage-blob-event-quickstart.md)
