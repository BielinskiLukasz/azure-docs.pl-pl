---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77202127"
---
Można użyć następujących typów parametrów dla powiązania danych wejściowych obiektu BLOB:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>jedno</sup>
* `CloudBlockBlob`<sup>jedno</sup>
* `CloudPageBlob`<sup>jedno</sup>
* `CloudAppendBlob`<sup>jedno</sup>

<sup>1</sup> wymaga powiązania `direction` "Inout" w *funkcji Function. JSON* lub `FileAccess.ReadWrite` w bibliotece klas języka C#.

Jeśli próbujesz powiązać z jednym z typów zestawu SDK magazynu i otrzymasz komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string` lub `Byte[]` jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie typu `Stream` lub. `CloudBlockBlob` Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) wcześniej w tym artykule.
