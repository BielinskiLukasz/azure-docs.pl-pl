---
title: Przekazywanie danych zadania usługi Azure Automation do usługi Log Analytics
description: W tym artykule przedstawiono sposób przesyłania stanu zadania i elementów runbook strumieni zadań usługi Azure Log Analytics, aby dostarczyć dodatkowy wgląd i zarządzania.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/12/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6170b69d213470b1f5b7e75c9b102e5e07c09209
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682896"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics"></a>Przekazuj strumienie zadania i stan zadania z usługi Automation do usługi Log Analytics

Automatyzacja może wysyłać elementu runbook strumieni zadań i stanu zadania do obszaru roboczego usługi Log Analytics. Ten proces nie wiąże się z obszaru roboczego połączeń i jest całkowicie niezależny. Dzienniki zadań i strumieni zadań są widoczne w witrynie Azure portal lub za pomocą programu PowerShell, dla poszczególnych zadań i to umożliwia wykonywanie prostych dochodzenia. Teraz z usługą Log Analytics można:

* Uzyskaj wgląd w zadań usługi Automation.
* Wyzwalacz poczty e-mail lub alertu oparte na stan zadania elementu runbook (na przykład nie powiodło się lub zawieszone).
* Napisz zaawansowanych zapytań między strumienie zadania.
* Korelowanie zadania w kont usługi Automation.
* Wizualizowanie historii zadania, wraz z upływem czasu.

## <a name="prerequisites-and-deployment-considerations"></a>Wymagania wstępne i zagadnienia dotyczące wdrażania

Aby rozpocząć wysyłanie dzienników usługi Automation do usługi Log Analytics, potrzebne są:

* Listopad 2016 lub nowszej wersji [programu Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* Obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Log Analytics](../log-analytics/log-analytics-get-started.md). 
* Identyfikator zasobu dla konta usługi Azure Automation.

Aby znaleźć identyfikator zasobu dla konta usługi Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Aby znaleźć identyfikator zasobu obszaru roboczego usługi Log Analytics, uruchom następujące polecenie programu PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Jeśli masz więcej niż jednego konta usługi Automation, lub znaleźć obszarów roboczych w danych wyjściowych poprzedniego polecenia, *nazwa* należy skonfigurować, a następnie skopiuj wartość dla *ResourceId*.

Jeśli chcesz znaleźć *nazwa* konta usługi Automation w witrynie Azure portal wybierz konto usługi Automation z **konta usługi Automation** bloku, a następnie wybierz pozycję **wszystkie ustawienia** . W bloku **Wszystkie ustawienia** w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.  W bloku **Właściwości** możesz zauważyć poniższe wartości.<br> ![Właściwości konta usługi Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Konfigurowanie integracji z usługą Log Analytics

1. Na komputerze, należy uruchomić **programu Windows PowerShell** z **Start** ekranu.
2. Uruchom następujące polecenie programu PowerShell, a następnie edytować wartość `[your resource id]` i `[resource id of the log analytics workspace]` z wartościami w poprzednim kroku.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

Po uruchomieniu tego skryptu, zostanie wyświetlony rekordów w usłudze Log Analytics w ciągu 10 minut nowe JobLogs lub JobStreams zapisywana.

Aby wyświetlić dzienniki, uruchom następujące zapytanie w przeszukiwania dzienników usługi Log Analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Zweryfikuj konfigurację
Aby upewnić się, że konto usługi Automation wysyła dzienniki do obszaru roboczego usługi Log Analytics, sprawdź, czy diagnostyki są poprawnie skonfigurowane na koncie usługi Automation przy użyciu następujące polecenie programu PowerShell:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

W danych wyjściowych upewnij się, że:
+ W obszarze *dzienniki*, wartość *włączone* jest *True*.
+ Wartość *WorkspaceId* jest ustawiona na identyfikator zasobu obszaru roboczego usługi Log Analytics.

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Diagnostyka usługi Azure Automation tworzy dwa typy rekordów w usłudze Log Analytics i są oznaczone jako **AzureDiagnostics**. Następujące zapytania używać języka kwerend uaktualnione do usługi Log Analytics. Informacji na temat typowych zapytań między starsze zapytanie języka i nowego języka zapytań usługi Azure Log Analytics [starszych do nowego języka zapytań usługi Azure Log Analytics — ściągawka](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Dzienniki zadań
| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy dla obiektu wywołującego. |
| JobId_g |Identyfikator GUID, który jest identyfikatorem zadania elementu Runbook. |
| Typ resultType |Stan zadania elementu Runbook. Możliwe wartości:<br>-Nowych<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>-Ukończone |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs. |
| OperationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie. |
| Zasób | Nazwa konta usługi Automation |
| SourceSystem | Jak usługa Log Analytics zbierane dane. Zawsze *Azure* dla diagnostyki platformy Azure. |
| ResultDescription |Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji zadania elementu Runbook. |
| ResourceId |Określa identyfikator zasobu konta usługi Azure Automation runbook. |
| SubscriptionId | Subskrypcja platformy Azure identyfikator (GUID) dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | FIRMY MICROSOFT. USŁUGI AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Strumienie zadania
| Właściwość | Opis |
| --- | --- |
| TimeGenerated |Data i godzina dla wykonania zadania elementu Runbook. |
| RunbookName_s |Nazwa elementu Runbook. |
| Caller_s |Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań. |
| StreamType_s |Typ strumienia zadania. Możliwe wartości:<br>— Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne |
| Tenant_g | Identyfikator GUID, który identyfikuje dzierżawy dla obiektu wywołującego. |
| JobId_g |Identyfikator GUID, który jest identyfikatorem zadania elementu Runbook. |
| Typ resultType |Stan zadania elementu Runbook. Możliwe wartości:<br>— W toku |
| Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams. |
| OperationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie. |
| Zasób | Nazwa konta usługi Automation |
| SourceSystem | Jak usługa Log Analytics zbierane dane. Zawsze *Azure* dla diagnostyki platformy Azure. |
| ResultDescription |Obejmuje strumień wyjściowy z elementu Runbook. |
| CorrelationId |Identyfikator GUID, który jest identyfikatorem korelacji zadania elementu Runbook. |
| ResourceId |Określa identyfikator zasobu konta usługi Azure Automation runbook. |
| SubscriptionId | Subskrypcja platformy Azure identyfikator (GUID) dla konta usługi Automation. |
| ResourceGroup | Nazwa grupy zasobów dla konta usługi Automation. |
| ResourceProvider | FIRMY MICROSOFT. USŁUGI AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Wyświetlanie automatyzacji dzienników w usłudze Log Analytics
Teraz, gdy rozpoczęto wysyłanie dzienników zadań usługi Automation do usługi Log Analytics, zobaczmy, co można zrobić za pomocą tych dzienników w usłudze Log Analytics.

Aby wyświetlić dzienniki, uruchom następujące zapytanie: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Wyślij wiadomość e-mail, gdy zadanie elementu runbook nie powiedzie się lub wstrzymuje
Jedną z najważniejszych klientów pyta, jest możliwość wysyłania wiadomości e-mail lub SMS, gdy coś pójdzie nie tak z zadania elementu runbook.   

Aby utworzyć regułę alertu, należy rozpocząć od tworzenia przeszukiwania dzienników dla rekordów zadania elementu runbook, które powinny wywoływać alert. Kliknij przycisk **Alert** przycisk, aby utworzyć i skonfigurować regułę alertu.

1. Na stronie Przegląd usługi Log Analytics kliknij **wyszukiwanie w dzienniku**.
2. Utwórz zapytanie wyszukiwania w dzienniku alertu, wpisując następujące wyszukiwanie w polu zapytania: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` można także grupować według RunbookName przy użyciu: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Jeśli skonfigurowano dzienników z więcej niż jednego konta usługi Automation lub subskrypcji do swojego obszaru roboczego, można grupować alerty według subskrypcji i konto usługi Automation. Nazwa konta usługi Automation można znaleźć w polu zasobu w wyszukiwaniu JobLogs.
1. Aby otworzyć **Utwórz regułę** ekranu, kliknij przycisk **+ Nowa reguła alertu** w górnej części strony. Aby uzyskać więcej informacji na temat opcji, aby skonfigurować alert, zobacz [alerty dzienników w usłudze Azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Znajdź wszystkie zadania, które zostały wykonane z błędami
Oprócz alertów na błędy, można znaleźć, gdy zadanie elementu runbook ma błąd niepowodujący. W takich przypadkach programu PowerShell tworzy strumień błędów, ale błędy niepowodujące nie powodują zadania wstrzymać lub zakończyć się niepowodzeniem.    

1. W obszarze roboczym usługi Log Analytics, kliknij przycisk **wyszukiwanie w dzienniku**.
2. W polu zapytania wpisz `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` a następnie kliknij przycisk **wyszukiwania** przycisku.

### <a name="view-job-streams-for-a-job"></a>Widok zadania strumieni dla zadania
Podczas debugowania zadania, można również możliwość przejrzenia strumieni zadań. Następujące zapytanie Wyświetla wszystkie strumienie w jednym zadaniu za pomocą identyfikatora GUID 2ebd22ea-e05e-4eb9 - 9d 76-d73cbd4356e0:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Wyświetlanie stanu zadania historyczne
Na koniec można wizualizować historię zadania, wraz z upływem czasu. To zapytanie służy do wyszukiwania stan zadań wraz z upływem czasu.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Wykres stanu historycznych zadanie analizy dzienników](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Usuwanie ustawień diagnostycznych

Aby usunąć ustawienia diagnostyczne konta usługi Automation, uruchom następujące polecenia:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Podsumowanie

Wysyłając automatyzacji zadań stan strumienia danych i do usługi Log Analytics, można uzyskać lepszy wgląd w stan zadań usługi Automation przez:
+ Konfigurowanie alertów, aby otrzymywać powiadomienia, gdy występuje problem.
+ Przy użyciu niestandardowych widoków i zapytania wyszukiwania, aby zwizualizować wyniki elementu runbook, stan zadania elementu runbook i inne powiązane, kluczowe wskaźniki lub metryk.  

Usługa log Analytics zapewnia większą widoczność operacyjną do automatyzacji zadań i mogą pomóc rozwiązuj szybciej.  

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat do tworzenia różnych zapytań wyszukiwania i przeglądania dzienników zadań usługi Automation z usługą Log Analytics, zobacz [przeszukiwanie dzienników w usłudze Log Analytics](../log-analytics/log-analytics-log-searches.md).
* Aby dowiedzieć się, jak utworzyć i pobierać dane wyjściowe i komunikaty o błędach z elementów runbook, zobacz [elementu Runbook dane wyjściowe i komunikaty](automation-runbook-output-and-messages.md).
* Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).
* Aby dowiedzieć się więcej na temat usługi Log Analytics i źródłach zbierania danych, zobacz [Azure zbieranie danych magazynu w usłudze Log Analytics — omówienie](../azure-monitor/platform/collect-azure-metrics-logs.md).
