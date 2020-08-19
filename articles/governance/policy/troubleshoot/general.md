---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z tworzeniem definicji zasad, różnymi zestawami SDK i dodatkiem dla Kubernetes.
ms.date: 08/17/2020
ms.topic: troubleshooting
ms.openlocfilehash: d4ede1703df922196c89a4c1ca4f37cbc95a6297
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545543"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Rozwiązywanie problemów z błędami przy użyciu Azure Policy

Podczas tworzenia definicji zasad, pracy z zestawem SDK lub konfigurowania [Azure Policy dla dodatku Kubernetes](../concepts/policy-for-kubernetes.md) można napotkać błędy. W tym artykule opisano różne błędy, które mogą wystąpić i sposoby ich rozwiązywania.

## <a name="finding-error-details"></a>Znajdowanie szczegółów błędu

Lokalizacja szczegółów błędu zależy od akcji, która powoduje błąd.

- Podczas pracy z zasadami niestandardowymi Wypróbuj ją w Azure Portal, aby uzyskać Zaznaczanie błędów opinię na temat schematu lub przejrzeć wynikowe [dane zgodności](../how-to/get-compliance-data.md) , aby zobaczyć, jak są oceniane zasoby.
- Podczas pracy z różnymi zestawami SDK zestaw SDK zawiera szczegółowe informacje o tym, dlaczego działanie funkcji nie powiodło się.
- Podczas pracy z dodatkiem dla Kubernetes Rozpocznij od [rejestrowania](../concepts/policy-for-kubernetes.md#logging) w klastrze.

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-alias-not-found"></a>Scenariusz: nie znaleziono aliasu

#### <a name="issue"></a>Problem

Azure Policy używa [aliasów](../concepts/definition-structure.md#aliases) do mapowania na Azure Resource Manager właściwości.

#### <a name="cause"></a>Przyczyna

W definicji zasad użyto nieprawidłowego lub nieistniejącego aliasu.

#### <a name="resolution"></a>Rozwiązanie

Najpierw sprawdź, czy właściwość Menedżer zasobów ma alias. Użyj [rozszerzenia Azure Policy dla Visual Studio Code](../how-to/extension-for-vscode.md), [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)lub SDK, aby wyszukać dostępne aliasy. Jeśli alias dla właściwości Menedżer zasobów nie istnieje, Utwórz bilet pomocy technicznej.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scenariusz: Szczegóły oceny nieaktualne

#### <a name="issue"></a>Problem

Zasób jest w stanie "nie uruchomiono" lub szczegóły zgodności nie są aktualne.

#### <a name="cause"></a>Przyczyna

Zastosowanie nowych zasad lub przydziału inicjatywy trwa około 30 minut. Nowe lub zaktualizowane zasoby w ramach istniejącego przypisania stają się dostępne około 15 minut później. Standardowe skanowanie pod kątem zgodności odbywa się co 24 godziny. Aby uzyskać więcej informacji, zobacz [wyzwalacze oceny](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Rozwiązanie

Najpierw poczekaj na ukończenie odpowiedniej ilości czasu, aby Ocena została ukończona, a wyniki zgodności staną się dostępne w Azure Portal lub SDK. Aby rozpocząć nowe skanowanie w celu oceny przy użyciu Azure PowerShell lub interfejsu API REST, zobacz [skanowanie na żądanie](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-evaluation-not-as-expected"></a>Scenariusz: Obliczanie nie jest zgodne z oczekiwaniami

#### <a name="issue"></a>Problem

Zasób nie jest w stanie oceny, jest _zgodny_ lub _niezgodny_, oczekiwany dla tego zasobu.

#### <a name="cause"></a>Przyczyna

Zasób nie jest w poprawnym zakresie przypisania zasad lub definicja zasad nie działa zgodnie z oczekiwaniami.

#### <a name="resolution"></a>Rozwiązanie

- W przypadku niezgodnego zasobu, który powinien być zgodny, Zacznij od [określenia przyczyn braku zgodności](../how-to/determine-non-compliance.md). Porównanie definicji z obliczoną wartością właściwości wskazuje, dlaczego zasób nie jest zgodny.
- W przypadku zgodnego zasobu, który powinien być niezgodny, przeczytaj warunek definicji zasad według warunku i Oceń właściwości zasobów. Upewnij się, że operatory logiczne grupują odpowiednie warunki razem i że Twoje warunki nie są odwrócone.

Jeśli zgodność z przypisaniem zasad zawiera `0/0` zasoby, nie zostały określone żadne zasoby, które mają zastosowanie w zakresie przypisania. Sprawdź zarówno definicję zasad, jak i zakres przypisania.

### <a name="scenario-enforcement-not-as-expected"></a>Scenariusz: wymuszanie nie jest zgodne z oczekiwaniami

#### <a name="issue"></a>Problem

Zasób, na który oczekuje się, Azure Policy nie jest i nie ma wpisu w [dzienniku aktywności platformy Azure](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Przyczyna

Przypisanie zasad zostało skonfigurowane do [wymuszania](../concepts/assignment-structure.md#enforcement-mode) _wyłączenia_. Gdy tryb wymuszania jest wyłączony, efekt zasad nie jest wymuszany, a w dzienniku aktywności nie ma wpisu.

#### <a name="resolution"></a>Rozwiązanie

**Zaktualizujmode** do _włączenia_. Ta zmiana umożliwia Azure Policy działanie na zasobach w tym przypisaniu zasad i wysyłanie wpisów do dziennika aktywności. Jeśli **wymuszanie** jest już włączone, zobacz [ocenę nie zgodnie z oczekiwaniami](#scenario-evaluation-not-as-expected) w przypadku kursów akcji.

### <a name="scenario-denied-by-azure-policy"></a>Scenariusz: odmowa przez Azure Policy

#### <a name="issue"></a>Problem

Odmowa utworzenia lub zaktualizowania zasobu.

#### <a name="cause"></a>Przyczyna

Przypisanie zasad do zakresu, w którym znajduje się nowy lub zaktualizowany zasób, spełnia kryteria definicji zasad z efektem [odmowy](../concepts/effects.md#deny) . Brak tworzenia lub aktualizowania zasobów spotkań z tymi definicjami.

#### <a name="resolution"></a>Rozwiązanie

Komunikat o błędzie z przypisania zasad Odmów obejmuje definicję zasad i identyfikatory przypisań zasad. Jeśli informacje o błędzie w wiadomości nie zostaną pominięte, są również dostępne w [dzienniku aktywności](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Korzystając z tych informacji, można uzyskać więcej szczegółowych informacji, aby zrozumieć ograniczenia zasobów i dostosować właściwości zasobów w żądaniu w celu dopasowania do wartości dozwolonych.

## <a name="template-errors"></a>Błędy szablonów

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenariusz: obsługiwane przez zasady funkcje przetwarzane przez szablon

#### <a name="issue"></a>Problem

Azure Policy obsługuje kilka funkcji i funkcji szablonu Azure Resource Manager (szablon ARM), które są dostępne tylko w definicji zasad. Menedżer zasobów przetwarza te funkcje jako część wdrożenia, a nie jako część definicji zasad.

#### <a name="cause"></a>Przyczyna

Używanie obsługiwanych funkcji, takich jak `parameter()` lub `resourceGroup()` , skutkuje przetworzonym wynikiem funkcji w czasie wdrażania zamiast opuszczania funkcji dla definicji zasad i aparatu Azure Policy do przetworzenia.

#### <a name="resolution"></a>Rozwiązanie

Aby przekazać funkcję w ramach definicji zasad, należy wypróbować cały ciąg, `[` tak aby Właściwość wyglądała tak, jak wygląda `[[resourceGroup().tags.myTag]` . Znak ucieczki powoduje, Menedżer zasobów traktować wartość jako ciąg podczas przetwarzania szablonu. Azure Policy następnie umieści funkcję w definicji zasad, umożliwiając jej dynamiczne działanie zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [składnia i wyrażenia w szablonach Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-installation-errors"></a>Błędy instalacji dodatku

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scenariusz: Instalacja za pomocą wykresu Helm kończy się niepowodzeniem przy haśle

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Polecenie kończy się niepowodzeniem z jedną z następujących komunikatów:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Przyczyna

Wygenerowane hasło zawiera przecinek ( `,` ), na którym jest podzielenie wykresu Helm.

#### <a name="resolution"></a>Rozwiązanie

`,`W przypadku korzystania `helm install azure-policy-addon` z ukośnika odwrotnego () w polu wartość hasła należy wprowadzić znak ucieczki () `\` .

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scenariusz: Instalacja za pomocą wykresu Helm kończy się niepowodzeniem, ponieważ nazwa już istnieje

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Polecenie kończy się niepowodzeniem z następującym komunikatem:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Przyczyna

Wykres Helm o nazwie `azure-policy-addon` został już zainstalowany lub częściowo zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z instrukcjami, aby [usunąć Azure Policy dodatku Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on), a następnie ponownie uruchom `helm install azure-policy-addon` polecenie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów za pośrednictwem usługi [Microsoft Q&A](/answers/topics/azure-policy.html).
- Connect with [@AzureSupport](https://twitter.com/azuresupport) — oficjalne konto Microsoft Azure, aby usprawnić obsługę klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
