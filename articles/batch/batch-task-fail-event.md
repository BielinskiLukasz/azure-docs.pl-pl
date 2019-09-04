---
title: Zdarzenie niepowodzenia zadania Azure Batch | Microsoft Docs
description: Odwołanie do zdarzenia niepowodzenia zadania usługi Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: lahugh
ms.openlocfilehash: ea33153c1d231444205a30a09b338f1922641424
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258198"
---
# <a name="task-fail-event"></a>Zdarzenie niepowodzenia zadania

 To zdarzenie jest emitowane, gdy zadanie kończy się niepowodzeniem z powodu błędu. Obecnie wszystkie kody zakończenia inne niż zero są uznawane za niepowodzenia. To zdarzenie będzie emitowane *jako uzupełnienie* zdarzenia ukończenia zadania i może służyć do wykrywania, kiedy zadanie zakończyło się niepowodzeniem.


 Poniższy przykład przedstawia treść zdarzenia niepowodzenia zadania.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|`jobId`|String|Identyfikator zadania zawierającego zadanie.|
|`id`|String|Identyfikator zadania.|
|`taskType`|String|Typ zadania. Może to być "JobManager" wskazujący, że jest to zadanie Menedżera zadań lub "użytkownika" wskazujące, że nie jest to zadanie Menedżera zadań. To zdarzenie nie jest emitowane w przypadku zadań związanych z przygotowaniem zadania, zadań wydania zadania lub uruchamiania zadań.|
|`systemTaskVersion`|Int32|Jest to wewnętrzny licznik ponownych prób dla zadania. Wewnętrznie usługa Batch może ponowić zadanie, aby uwzględnić przejściowe problemy. Te problemy mogą obejmować wewnętrzne błędy planowania lub próby odzyskania z węzłów obliczeniowych w nieprawidłowym stanie.|
|[`nodeInfo`](#nodeInfo)|Typ złożony|Zawiera informacje o węźle obliczeniowym, na którym uruchomiono zadanie.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Typ złożony|Określa, że zadanie jest zadaniem z wieloma wystąpieniami wymagającymi wielu węzłów obliczeniowych.  Aby [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) uzyskać szczegółowe informacje, zobacz.|
|[`constraints`](#constraints)|Typ złożony|Ograniczenia wykonywania, które są stosowane do tego zadania.|
|[`executionInfo`](#executionInfo)|Typ złożony|Zawiera informacje o wykonywaniu zadania.|

###  <a name="nodeInfo"></a>nodeInfo

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|`poolId`|String|Identyfikator puli, w której uruchomiono zadanie.|
|`nodeId`|String|IDENTYFIKATOR węzła, w którym uruchomiono zadanie.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Liczba węzłów obliczeniowych wymaganych przez zadanie.|

###  <a name="constraints"></a>powiązanych

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maksymalna liczba prób wykonania zadania. Usługa Batch ponawia próbę wykonania zadania, jeśli jego kod zakończenia jest różny od zera.<br /><br /> Należy zauważyć, że ta wartość kontroluje liczbę ponownych prób. Usługa Batch spróbuje wykonać zadanie jeden raz, a następnie ponowić próbę wykonania tego limitu. Jeśli na przykład maksymalna liczba ponownych prób wynosi 3, zadanie wsadowe próbuje wykonać zadania do 4 razy (jedna początkowa próba i 3 ponownych prób).<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi 0, usługa Batch nie wykonuje żadnych ponownych prób wykonywania zadań.<br /><br /> Jeśli maksymalna liczba ponownych prób wynosi-1, usługa Batch ponawia zadania bez ograniczeń.<br /><br /> Wartość domyślna to 0 (brak ponownych prób).|


###  <a name="executionInfo"></a>executionInfo

|Nazwa elementu|Type|Uwagi|
|------------------|----------|-----------|
|`startTime`|DateTime|Godzina, o której uruchomiono zadanie. "Uruchomiona" odpowiada stanie **uruchomienia** , więc jeśli zadanie Określa pliki zasobów lub pakiety aplikacji, czas rozpoczęcia odzwierciedla czas, w którym zadanie rozpoczęło pobieranie lub wdrażanie.  Jeśli zadanie zostało ponownie uruchomione lub ponowione, jest to Ostatnia godzina, o której uruchomiono zadanie.|
|`endTime`|DateTime|Godzina ukończenia zadania.|
|`exitCode`|Int32|Kod zakończenia zadania.|
|`retryCount`|Int32|Liczba ponownych prób wykonania zadania przez usługę Batch. Zadanie zostanie ponowione, jeśli zostanie zakończone z niezerowym kodem zakończenia, do określonego MaxTaskRetryCount.|
|`requeueCount`|Int32|Liczba uruchomień zadania w kolejce przez usługę Batch w wyniku żądania użytkownika.<br /><br /> Gdy użytkownik usuwa węzły z puli (zmieniając rozmiar lub zmniejszając pulę) lub gdy zadanie jest wyłączone, użytkownik może określić, że uruchomione zadania w węzłach będą ponownie umieszczane w kolejce do wykonania. Ta liczba śledzi, ile razy zadanie zostało ponownie poddane kolejce z tych przyczyn.|
