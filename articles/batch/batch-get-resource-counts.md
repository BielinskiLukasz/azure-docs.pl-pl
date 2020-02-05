---
title: Liczba stanów zadań i węzłów — Azure Batch | Microsoft Docs
description: Policz stan Azure Batch zadań i węzłów obliczeniowych, aby pomóc w zarządzaniu i monitorowaniu rozwiązań wsadowych.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a7b58e96918d26851812aa96c18043121c081e94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023926"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorowanie rozwiązań wsadowych przez liczenie zadań i węzłów według stanu

Do monitorowania i zarządzania rozwiązaniami Azure Batch o dużej skali potrzebne są dokładne liczby zasobów w różnych stanach. Azure Batch zapewnia wydajne operacje, aby uzyskać te liczniki dla *zadań* wsadowych i *węzłów obliczeniowych*. Użyj tych operacji zamiast potencjalnie czasochłonnych zapytań dotyczących list, które zwracają szczegółowe informacje o dużych kolekcjach zadań lub węzłów.

* [Licznik zadania pobierania][rest_get_task_counts] pobiera zagregowaną liczbę aktywnych, uruchomionych i wykonanych zadań w ramach zadania oraz zadań, które zakończyły się powodzeniem lub niepowodzeniem. 

  Zliczając zadania w poszczególnych stanach, można łatwiej wyświetlać postęp zadania dla użytkownika lub wykrywać nieoczekiwane opóźnienia lub błędy, które mogą mieć wpływ na zadanie. Liczba zadań pobierania jest dostępna w ramach interfejsu API usługi Batch w wersji 2017 -06-01.5.1 oraz powiązanych zestawów SDK i narzędzi.

* [Liczba węzłów puli listy][rest_get_node_counts] Pobiera liczbę węzłów obliczeniowych dedykowanych i o niskim priorytecie w każdej puli, które znajdują się w różnych stanach: Tworzenie, bezczynne, offline, zastępujące, ponowne uruchamianie, odtwarzanie obrazu, uruchamianie i inne. 

  Zliczając węzły w każdym stanie, można określić, kiedy mają być potrzebne zasoby obliczeniowe do uruchamiania zadań, i zidentyfikować potencjalne problemy związane z pulami. Liczby węzłów puli list są dostępne w ramach interfejsu API usługi Batch w wersji 2018 r -03-01.6.1 oraz powiązanych zestawów SDK i narzędzi.

Jeśli używasz wersji usługi, która nie obsługuje operacji licznika zadań ani liczby węzłów, zamiast tego użyj zapytania listy, aby obliczyć te zasoby. Należy również użyć zapytania listy, aby uzyskać informacje o innych zasobach wsadowych, takich jak aplikacje, pliki i zadania. Aby uzyskać więcej informacji na temat stosowania filtrów do wyświetlania zapytań, zobacz [Tworzenie zapytań w celu wydajnego wyświetlania listy zasobów usługi Batch](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Liczba stanów zadań

Operacja Pobierz licznik zadań zlicza zadania według następujących stanów:

- **Aktywne** — zadanie, które jest kolejkowane i możliwe do uruchomienia, ale nie jest aktualnie przypisane do węzła obliczeniowego. Zadanie jest również `active`, jeśli jest [zależne od zadania nadrzędnego](batch-task-dependencies.md) , które nie zostało jeszcze ukończone. 
- **Uruchomienie** — zadanie, które zostało przypisane do węzła obliczeniowego, ale nie zostało jeszcze ukończone. Zadanie jest zliczane jako `running`, gdy jego stan jest `preparing` lub `running`, jak wskazano w polu [Pobierz informacje o operacji zadania][rest_get_task] .
- **Ukończono** — zadanie, które nie jest już do uruchomienia, ponieważ zostało zakończone pomyślnie, lub zakończone niepowodzeniem, a także wyczerpuje limit ponownych prób. 
- **Powodzenie** — zadanie, którego wynikiem jest wykonanie zadania, jest `success`. Partia zadań określa, czy zadanie zakończyło się powodzeniem czy niepowodzeniem, sprawdzając Właściwość `TaskExecutionResult` właściwości [executionInfo][rest_get_exec_info] .
- **Nie powiodło się** Zadanie, którego wynikiem jest wykonanie zadania, jest `failure`.

Poniższy przykład kodu platformy .NET pokazuje, jak pobrać liczbę zadań według stanu: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Aby uzyskać liczniki zadań dla zadania, można użyć podobnego wzorca dla REST i innych obsługiwanych języków. 

> [!NOTE]
> Wersje interfejsu API usługi Batch przed 2018 r -08-01.7.0 również zwracają Właściwość `validationStatus` w odpowiedzi Get Task Counts. Ta właściwość wskazuje, czy partia sprawdza liczbę stanów w celu zapewnienia spójności z Stanami zgłoszonymi w interfejsie API zadań listy. Wartość `validated` wskazuje tylko, że partia sprawdzana pod kątem spójności co najmniej raz dla danego zadania. Wartość właściwości `validationStatus` nie wskazuje, czy liczniki, które pobierają liczby zadań zwracają, są aktualne.
>

## <a name="node-state-counts"></a>Liczba Stanów węzłów

Operacja zliczania węzłów puli listy zlicza węzły obliczeniowe według następujących stanów w każdej puli. Oddzielne liczby agregacji są dostępne dla węzłów dedykowanych i węzłów o niskim priorytecie w każdej puli.

- **Tworzenie** — przydzieloną przez platformę Azure maszynę wirtualną, która nie została jeszcze uruchomiona w celu dołączenia do puli.
- **Bezczynny** — dostępny węzeł obliczeniowy, który aktualnie nie wykonuje zadania.
- **LeavingPool** — węzeł, który opuszcza pulę, ponieważ został jawnie usunięty przez użytkownika lub w przypadku zmiany rozmiarów puli lub skalowania automatycznego.
- **Offline** — węzeł, którego nie można użyć do zaplanowania nowych zadań przez partię.
- **Zastępujący** — węzeł o niskim priorytecie, który został usunięty z puli, ponieważ platforma Azure ODZYSKAł maszynę wirtualną. Węzeł `preempted` może zostać ponownie zainicjowany, gdy będzie dostępna zastępowanie pojemności maszyny wirtualnej o niskim priorytecie.
- Ponowny **rozruch** — węzeł, który jest uruchamiany ponownie.
- Ponowne tworzenie **obrazu** — węzeł, na którym jest instalowany system operacyjny.
- **Uruchamianie** — węzeł, w którym działa jedno lub więcej zadań (innych niż zadanie podrzędne).
- **Uruchamianie** — węzeł, w którym jest uruchamiana usługa Batch. 
- **StartTaskFailed** — węzeł, w którym [zadanie uruchomieniowe][rest_start_task] nie powiodło się i wyczerpuje wszystkie ponowne próby, i na którym `waitForSuccess` jest ustawiona w zadaniu startowym. Nie można użyć węzła do uruchamiania zadań.
- **Nieznany** — węzeł, który utracił kontakt z usługą Batch i którego stan nie jest znany.
- **Niezdatny do użytku** — węzeł, którego nie można użyć do wykonania zadania z powodu błędów.
- **WaitingForStartTask** — węzeł, w którym uruchomiono zadanie uruchomieniowe, ale `waitForSuccess` jest ustawiony, a zadanie uruchamiania nie zostało ukończone.

Poniższy C# fragment kodu przedstawia sposób wyświetlania listy liczników dla wszystkich pul na bieżącym koncie:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Poniższy C# fragment kodu przedstawia sposób wyświetlania listy liczb węzłów dla danej puli na bieżącym koncie.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Możesz użyć podobnego wzorca dla REST i innych obsługiwanych języków, aby uzyskać liczbę węzłów dla pul.
 
## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zasadach działania i funkcjach usługi Batch, zobacz temat [Omówienie funkcji usługi Batch](batch-api-basics.md). W tym artykule omówiono podstawowe zasoby usługi Batch, takie jak pule, węzły obliczeniowe, zadania i zadania, a także omówiono funkcje usług.

* Aby uzyskać informacje o stosowaniu filtrów do zapytań dotyczących zasobów usługi Batch, zobacz [Tworzenie zapytań w celu wydajnego wyświetlania listy zasobów usługi Batch](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

