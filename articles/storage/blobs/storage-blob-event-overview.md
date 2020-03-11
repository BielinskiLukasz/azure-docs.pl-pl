---
title: Reagowanie na zdarzenia usługi Azure Blob Storage | Microsoft Docs
description: Zasubskrybuj zdarzenia usługi Blob Storage przy użyciu usługi Azure Event Grid.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081913"
---
# <a name="reacting-to-blob-storage-events"></a>Reagowanie na zdarzenia magazynu obiektów BLOB

Zdarzenia usługi Azure Storage umożliwiają aplikacjom reagowanie na zdarzenia, takie jak tworzenie i usuwanie obiektów BLOB. Robi to bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania.

Zdarzenia są wypychane przy użyciu [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak Azure Functions, Azure Logic Apps lub nawet do własnego odbiornika http. W najlepszej części płacisz tylko za to, czego używasz.

Magazyn obiektów BLOB wysyła zdarzenia do Event Grid, które zapewniają niezawodne dostarczanie zdarzeń do aplikacji dzięki rozbudowanym zasadom ponawiania prób i wykorzystaniu utraconych wiadomości.

Typowe scenariusze zdarzeń magazynu obiektów BLOB obejmują przetwarzanie obrazów lub wideo, indeksowanie wyszukiwania lub wszelkie przepływy pracy zorientowane na pliki. Asynchroniczne przekazywanie plików to doskonałe dopasowanie do zdarzeń. Gdy zmiany są rzadko wykonywane, ale scenariusz wymaga natychmiastowej reakcji, Architektura oparta na zdarzeniach może być szczególnie wydajna.

Jeśli chcesz wypróbować tę usługę teraz, zobacz dowolny z tych artykułów szybkiego startu:

|Jeśli chcesz użyć tego narzędzia:    |Zobacz ten artykuł: |
|--|-|
|Portalu Azure    |[Szybki Start: kierowanie zdarzeń magazynu obiektów BLOB do punktu końcowego sieci Web za pomocą Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Program PowerShell    |[Szybki Start: kierowanie zdarzeń magazynu do punktu końcowego w sieci Web przy użyciu programu PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfejs wiersza polecenia platformy Azure    |[Szybki Start: kierowanie zdarzeń magazynu do punktu końcowego sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Aby zapoznać się z szczegółowymi przykładami odwołującymi się do zdarzeń magazynu obiektów BLOB za pomocą usługi Azure Functions, zobacz następujące artykuły:

- [Samouczek: Użyj zdarzeń Azure Data Lake Storage Gen2, aby zaktualizować tabelę różnicową](data-lake-storage-events.md)danych.
- [Samouczek: Automatyzowanie zmiany rozmiarów załadowanych obrazów przy użyciu Event Grid](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Tylko konta magazynu typu **StorageV2 (ogólnego przeznaczenia w wersji 2)** i **BlobStorage** obsługują integrację zdarzeń. **Magazyn (genral cel v1)** nie *obsługuje integracji* z programem Event Grid.

## <a name="the-event-model"></a>Model zdarzenia

Event Grid używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Ten obraz przedstawia relację między wydawcami zdarzeń, subskrypcjami zdarzeń i programami obsługi zdarzeń.

![Model Event Grid](./media/storage-blob-event-overview/event-grid-functional-model.png)

Najpierw Zasubskrybuj punkt końcowy do zdarzenia. Następnie po wyzwoleniu zdarzenia usługa Event Grid wyśle dane dotyczące tego zdarzenia do punktu końcowego.

Zobacz artykuł [schemat zdarzeń magazynu obiektów BLOB](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) , aby wyświetlić:

> [!div class="checklist"]
> * Pełna lista zdarzeń magazynu obiektów blob oraz sposób wyzwalania poszczególnych zdarzeń.
> * Przykład danych wysyłanych przez Event Grid dla każdego z tych zdarzeń.
> * Przeznaczenie poszczególnych par klucz-wartość, które pojawiają się w danych.

## <a name="filtering-events"></a>Filtrowanie zdarzeń

Zdarzenia obiektów BLOB [można filtrować](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) według typu zdarzenia, nazwy kontenera lub nazwy obiektu, który został utworzony/usunięty. Filtry w Event Grid są zgodne z początkiem lub końcem podmiotu, dlatego zdarzenia z odpowiednim podmiotem przejdą do subskrybenta.

Aby dowiedzieć się więcej na temat stosowania filtrów, zobacz [filtrowanie zdarzeń dla Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

W temacie zdarzeń magazynu obiektów BLOB jest stosowany format:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Aby dopasować wszystkie zdarzenia dla konta magazynu, można pozostawić puste filtry podmiotu.

Aby dopasować zdarzenia z obiektów BLOB utworzonych w zestawie kontenerów, które współużytkują prefiks, Użyj filtru `subjectBeginsWith`, takiego jak:

```
/blobServices/default/containers/containerprefix
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, Użyj filtru `subjectBeginsWith`, takiego jak:

```
/blobServices/default/containers/containername/
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, udostępniając prefiks nazwy obiektu BLOB, Użyj filtru `subjectBeginsWith`, takiego jak:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Aby dopasować zdarzenia z obiektów BLOB utworzonych w określonym kontenerze, udostępniając sufiks obiektu BLOB, Użyj filtru `subjectEndsWith`, takiego jak ". log" lub ". jpg". Aby uzyskać więcej informacji, zobacz temat [Event Grid pojęcia](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Praktyki związane z zużywaniem zdarzeń

Aplikacje, które obsługują zdarzenia magazynu obiektów blob, powinny spełniać kilka zalecanych praktyk:
> [!div class="checklist"]
> * Ponieważ można skonfigurować wiele subskrypcji do kierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie przyjmować zdarzeń z określonego źródła, ale w celu sprawdzenia, czy pochodzi ona z konta magazynu, którego oczekujesz.
> * Podobnie Sprawdź, czy typ zdarzenia jest przygotowana do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwanymi typami.
> * Ponieważ komunikaty mogą trafiać po pewnym opóźnieniu, Użyj pól ETag, aby zrozumieć, czy informacje o obiektach są nadal aktualne. Aby dowiedzieć się, jak używać pola ETag, zobacz [Zarządzanie współbieżnością w magazynie obiektów BLOB](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * Ponieważ komunikaty mogą się pojawiać poza kolejnością, Użyj pól programu Sequencer do zrozumienia kolejności zdarzeń dla każdego określonego obiektu. Pole Sequencer jest wartością ciągu reprezentującą logiczną sekwencję zdarzeń dla każdej konkretnej nazwy obiektu BLOB. Można użyć standardowego porównania ciągów, aby zrozumieć względną sekwencję dwóch zdarzeń dla tej samej nazwy obiektu BLOB.
> * Użyj pola blobtype, aby zrozumieć, jaki typ operacji jest dozwolony dla obiektu BLOB, a także typy bibliotek klientów, które powinny być używane w celu uzyskania dostępu do obiektu BLOB. Prawidłowe wartości to `BlockBlob` lub `PageBlob`. 
> * Użyj pola adresu URL z konstruktorami `CloudBlockBlob` i `CloudAppendBlob`, aby uzyskać dostęp do obiektu BLOB.
> * Ignoruj pola, które nie są zrozumiałe. Ta metoda pomaga w zachowaniu odporności na nowe funkcje, które mogą zostać dodane w przyszłości.
> * Jeśli chcesz się upewnić, że zdarzenie **Microsoft. Storage. BlobCreated** jest wyzwalane tylko wtedy, gdy blokowy obiekt BLOB jest całkowicie zatwierdzony, przefiltruj zdarzenie dla wywołań interfejsu API REST `CopyBlob`, `PutBlob`, `PutBlockList` lub `FlushWithClose`. Te wywołania interfejsu API wyzwalają zdarzenie **Microsoft. Storage. BlobCreated** tylko wtedy, gdy dane są w pełni zatwierdzone do blokowego obiektu BLOB. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń dla Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i nadaj zdarzeniom magazynu obiektów blob:

- [Event Grid — informacje](../../event-grid/overview.md)
- [Kierowanie zdarzeń magazynu obiektów BLOB do niestandardowego internetowego punktu końcowego](storage-blob-event-quickstart.md)
