---
title: Rozwiązywanie typowych błędów
description: Dowiedz się, jak rozwiązywać problemy z badaniem zasobów platformy Azure za pomocą usługi Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/21/2019
ms.topic: troubleshooting
ms.service: resource-graph
ms.openlocfilehash: abf6d22f2010db9bff97c7a93354c1cf8e1e1644
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976602"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Rozwiązywanie problemów z błędami przy użyciu grafu zasobów platformy Azure

Podczas wykonywania zapytania dotyczącego zasobów platformy Azure za pomocą usługi Azure Resource Graph mogą wystąpić błędy. W tym artykule opisano różne błędy, które mogą wystąpić i sposoby ich rozwiązywania.

## <a name="finding-error-details"></a>Znajdowanie szczegółów błędu

Większość błędów jest wynikiem problemu podczas wykonywania zapytania przy użyciu grafu zasobów platformy Azure. Gdy zapytanie nie powiedzie się, zestaw SDK zawiera szczegółowe informacje o zapytaniu zakończonym niepowodzeniem. Te informacje wskazują, że problem może zostać naprawiony, a późniejsza kwerenda powiedzie się.

## <a name="general-errors"></a>Błędy ogólne

### <a name="toomanysubscription"></a>Scenariusz: zbyt wiele subskrypcji

#### <a name="issue"></a>Problem

Klienci mający dostęp do ponad 1000 subskrypcji, w tym subskrypcji między dzierżawcami i [usługi Azure Lighthouse](../../../lighthouse/overview.md), nie mogą pobierać danych ze wszystkich subskrypcji w ramach pojedynczego wywołania do grafu zasobów platformy Azure.

#### <a name="cause"></a>Przyczyna

Interfejs wiersza polecenia platformy Azure i program PowerShell przesyłają dalej tylko pierwsze subskrypcje 1000 do grafu zasobów platformy Azure. Interfejs API REST dla usługi Azure Resource Graph akceptuje maksymalną liczbę subskrypcji, na które należy wykonać zapytanie.

#### <a name="resolution"></a>Rozwiązanie

Żądania usługi Batch dotyczące zapytania z podzbiorem subskrypcji w ramach limitu subskrypcji 1000. Rozwiązanie korzysta z parametru **subskrypcji** w programie PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="rest-contenttype"></a>Scenariusz: nieobsługiwany nagłówek REST typu Content-Type

#### <a name="issue"></a>Problem

Klienci wysyłający zapytanie do interfejsu API REST usługi Azure Resource Graph otrzymują odpowiedź _500_ (błąd wewnętrzny serwera).

#### <a name="cause"></a>Przyczyna

Interfejs API REST usługi Azure Resource Graph obsługuje tylko `Content-Type` **aplikacji/JSON**. Niektóre narzędzia lub agenci REST są domyślnie **tekstem/zwykłym**, co nie jest obsługiwane przez interfejs API REST.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy narzędzie lub Agent używany do wysyłania zapytań do grafu zasobów platformy Azure ma nagłówek interfejsu API REST `Content-Type` skonfigurowany dla **aplikacji/JSON**.
### <a name="rest-403"></a>Scenariusz: Brak uprawnień do odczytu do wszystkich subskrypcji na liście

#### <a name="issue"></a>Problem

Klienci, którzy jawnie przekazują listę subskrypcji za pomocą zapytania grafu zasobów platformy Azure, uzyskują odpowiedź _403_ (dostęp zabroniony).

#### <a name="cause"></a>Przyczyna

Jeśli klient nie ma uprawnienia do odczytu wszystkich podanych subskrypcji, żądanie zostanie odrzucone z powodu braku odpowiednich praw zabezpieczeń.

#### <a name="resolution"></a>Rozwiązanie

Uwzględnij co najmniej jedną subskrypcję na liście subskrypcji, do której klient uruchamia kwerendę ma co najmniej dostęp do odczytu. Aby uzyskać więcej informacji, zobacz [uprawnienia na wykresie zasobów platformy Azure](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
- Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure w celu poprawy obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.