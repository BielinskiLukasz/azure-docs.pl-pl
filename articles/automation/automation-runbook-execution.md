---
title: Wykonanie elementu Runbook w Azure Automation
description: Opisuje szczegóły dotyczące sposobu przetwarzania elementu Runbook w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4070b004ee791a433b5aeb9e3e0cdd9662fb0429
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191150"
---
# <a name="runbook-execution-in-azure-automation"></a>Wykonanie elementu Runbook w Azure Automation

Elementy Runbook są wykonywane na podstawie logiki zdefiniowanej wewnątrz nich. Jeśli element Runbook zostanie przerwany, element Runbook zostanie ponownie uruchomiony na początku. Takie zachowanie wymaga zapisania elementów Runbook, które obsługują ponownie uruchamianie w przypadku wystąpienia problemów przejściowych.

Uruchomienie elementu Runbook w Azure Automation powoduje utworzenie zadania. Zadanie jest pojedynczym wystąpieniem wykonywania elementu Runbook. Każde zadanie ma dostęp do zasobów platformy Azure przez nawiązanie połączenia z subskrypcją platformy Azure. Zadanie ma tylko dostęp do zasobów w centrum danych, jeśli te zasoby są dostępne z chmury publicznej.

Azure Automation przypisuje proces roboczy do uruchamiania każdego zadania podczas wykonywania elementu Runbook. Chociaż procesy robocze są współużytkowane przez wiele kont platformy Azure, zadania z różnych kont usługi Automation są od siebie odizolowane. Nie masz kontroli nad tym, które usługi procesu roboczego żądają Twojego zadania.

Po wyświetleniu listy elementów Runbook w Azure Portal zostanie wyświetlony stan każdego zadania uruchomionego dla każdego elementu Runbook. Azure Automation przechowuje dzienniki zadań przez maksymalnie 30 dni. 

Na poniższym diagramie przedstawiono cykl życia zadania elementu Runbook dla [elementów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks), [graficznych elementów Runbook](automation-runbook-types.md#graphical-runbooks)i [elementów Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stany zadań — przepływ pracy programu PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Gdzie można uruchamiać elementy Runbook

Elementy Runbook w Azure Automation mogą być uruchamiane w piaskownicy platformy Azure lub [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md). Większość elementów Runbook można łatwo uruchomić w piaskownicy platformy Azure, współdzielonym środowisku, które może być używane przez wiele zadań. Zadania korzystające z tej samej piaskownicy są powiązane z ograniczeniami zasobów piaskownicy.

Hybrydowego procesu roboczego elementu Runbook można używać do uruchamiania elementów Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów lokalnych w środowisku. Azure Automation przechowuje elementy Runbook i zarządza nimi, a następnie dostarcza je do co najmniej jednego przypisanego komputera.

W poniższej tabeli wymieniono niektóre zadania wykonywania elementów Runbook z zalecanym środowiskiem wykonywania wymienionym dla każdego z nich.

|Zadanie|Najlepszy wybór|Uwagi|
|---|---|---|
|Integracja z zasobami platformy Azure|Azure Sandbox|Uwierzytelnianie jest obsługiwane na platformie Azure. Jeśli używasz hybrydowego procesu roboczego elementu Runbook na maszynie wirtualnej platformy Azure, możesz używać [tożsamości zarządzanych dla zasobów platformy Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Uzyskaj optymalną wydajność zarządzania zasobami platformy Azure|Azure Sandbox|Skrypt jest uruchamiany w tym samym środowisku, w którym jest mniej opóźnienia.|
|Minimalizuj koszty operacyjne|Azure Sandbox|Nie ma obciążeń obliczeniowych i nie ma potrzeby dla maszyny wirtualnej.|
|Wykonywanie długotrwałego skryptu|Hybrydowy proces roboczy elementu Runbook|Piaskownice platformy Azure mają [ograniczenia dotyczące zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Korzystanie z usług lokalnych|Hybrydowy proces roboczy elementu Runbook|Może mieć dostęp bezpośrednio do komputera hosta.|
|Wymagaj oprogramowania i plików wykonywalnych innych firm|Hybrydowy proces roboczy elementu Runbook|Zarządzanie systemem operacyjnym i instalowanie oprogramowania może być możliwe.|
|Monitorowanie pliku lub folderu za pomocą elementu Runbook|Hybrydowy proces roboczy elementu Runbook|Użyj [zadania obserwatora](automation-watchers-tutorial.md) w hybrydowym procesie roboczym elementu Runbook.|
|Uruchamianie skryptu intensywnie korzystającego z zasobów|Hybrydowy proces roboczy elementu Runbook| Piaskownice platformy Azure mają [ograniczenia dotyczące zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Używanie modułów z określonymi wymaganiami| Hybrydowy proces roboczy elementu Runbook|Przykłady to:</br> WinSCP — zależność w WinSCP. exe </br> IISAdministration — zależność od włączenia usług IIS.|
|Instalowanie modułu przy użyciu Instalatora|Hybrydowy proces roboczy elementu Runbook|Moduły dla piaskownicy muszą obsługiwać kopiowanie.|
|Użyj elementów Runbook lub modułów, które wymagają .NET Framework wersji innej niż 4.7.2|Hybrydowy proces roboczy elementu Runbook|Piaskownice automatyzacji mają .NET Framework 4.7.2 i nie ma sposobu na ich uaktualnienie.|
|Uruchamianie skryptów wymagających podniesienia uprawnień|Hybrydowy proces roboczy elementu Runbook|Piaskownice nie umożliwiają podniesienia uprawnień. Przy użyciu hybrydowego procesu roboczego elementu Runbook można wyłączyć funkcję Kontrola konta użytkownika i użyć **polecenia Invoke-Command** przy uruchamianiu polecenia, które wymaga podniesienia uprawnień.|
|Uruchamianie skryptów, które wymagają dostępu do usługi WMI|Hybrydowy proces roboczy elementu Runbook|Zadania uruchomione w piaskownicach w chmurze nie mają dostępu do usługi WMI. |

## <a name="runbook-behavior"></a>Zachowanie elementu Runbook

### <a name="creating-resources"></a>Tworzenie zasobów

Jeśli element Runbook tworzy zasób, skrypt powinien sprawdzić, czy zasób już istnieje, przed podjęciem próby jego utworzenia. Oto przykład podstawowy.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Obsługa skryptów zależnych od czasu

Elementy Runbook muszą być niezawodne i mogą obsługiwać błędy przejściowe, które mogą spowodować ponowne uruchomienie lub niepowodzenie. Jeśli element Runbook ulegnie awarii, Azure Automation ponowienia próby.

Jeśli element Runbook jest zwykle uruchamiany w ramach ograniczenia czasu, należy wdrożyć logikę implementacji skryptu, aby sprawdzić czas wykonywania. To sprawdzenie gwarantuje uruchomienie operacji, takich jak uruchamianie, zamykanie lub skalowanie w poziomie tylko w określonych godzinach.

> [!NOTE]
> Czas lokalny w procesie piaskownicy platformy Azure jest ustawiony na czas UTC. Obliczenia dla daty i godziny w elementach Runbook muszą uwzględniać ten fakt.

### <a name="tracking-progress"></a>Śledzenie postępu

Dobrym sposobem jest utworzenie elementów Runbook w celu ich modularnej natury, utworzenie struktury logiki elementu Runbook, aby można ją było ponownie wykorzystać i uruchomić ponownie. Śledzenie postępów w elemencie Runbook to dobry sposób, aby zapewnić, że logika elementu Runbook jest wykonywana prawidłowo w przypadku wystąpienia problemów. Istnieje możliwość śledzenia postępu elementu Runbook przy użyciu zewnętrznego źródła, takiego jak konto magazynu, baza danych lub udostępnione pliki. W elemencie Runbook można utworzyć logikę, aby najpierw sprawdzić stan ostatniej podjętej akcji. Następnie w oparciu o wynik sprawdzenia logika może pominąć lub kontynuować określone zadania w elemencie Runbook.

### <a name="preventing-concurrent-jobs"></a>Uniemożliwianie współbieżnych zadań

Niektóre elementy Runbook działają dziwnie, jeśli działają w wielu zadaniach w tym samym czasie. W takim przypadku ważne jest, aby element Runbook zaimplementował logikę w celu ustalenia, czy istnieje już uruchomione zadanie. Oto przykład podstawowy.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Praca z wieloma subskrypcjami

Aby zająć się wieloma subskrypcjami, element Runbook musi użyć polecenia cmdlet [disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) , aby upewnić się, że kontekst uwierzytelniania nie zostanie pobrany z innego elementu Runbook działającego w tej samej piaskownicy. Element Runbook używa również parametru *AzContext* w poleceniach cmdlet AZ module i przekazuje go w odpowiednim kontekście.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Obsługa wyjątków

W tej sekcji opisano niektóre sposoby obsługi wyjątków lub sporadycznych problemów w elementach Runbook.

#### <a name="erroractionpreference"></a>$ErrorActionPreference

Zmienna [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) określa, jak program PowerShell reaguje na błąd niepowodujący zakończenia. Przerywanie błędów zawsze kończy się niezależnie od *$ErrorActionPreference*.

Gdy element Runbook używa *$ErrorActionPreference*, zazwyczaj błąd niepowodujący zakończenia, taki jak **PathNotFound** z polecenia cmdlet **GET-ChildItem** , uniemożliwia ukończenie elementu Runbook. W poniższym przykładzie pokazano sposób użycia *$ErrorActionPreference*. Końcowe polecenie **Write-Output** nigdy nie jest wykonywane, gdy skrypt zostaje zatrzymany.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Wypróbuj teraz

Aby obsłużyć błędy powodujące przerwanie, w skryptach programu PowerShell należy użyć instrukcji [catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) . Skrypt może używać tego mechanizmu do przechwytywania określonych wyjątków lub ogólnych wyjątków. Instrukcja **catch** powinna służyć do śledzenia lub próby obsługi błędów. Poniższy przykład próbuje pobrać plik, który nie istnieje. Przechwytuje wyjątek System .NET. WebException i zwraca ostatnią wartość dla każdego innego wyjątku.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Generować

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) można użyć do wygenerowania błędu kończącego. Ten mechanizm może być przydatny podczas definiowania własnej logiki w elemencie Runbook. Jeśli skrypt spełnia kryterium, które powinno zostać zatrzymane, może użyć instrukcji **throw** , aby zatrzymać. Poniższy przykład używa tej instrukcji, aby wyświetlić wymagany parametr funkcji.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Używanie plików wykonywalnych lub procesów wywołujących

Elementy Runbook, które działają w piaskownicach platformy Azure, nie obsługują wywoływania procesów, takich jak pliki wykonywalne ( **. exe** ) lub podprocesy.  Przyczyną tego jest to, że piaskownica systemu Azure jest uruchomionym procesem w kontenerze, który może nie mieć dostępu do wszystkich podstawowych interfejsów API. W przypadku scenariuszy wymagających oprogramowania innych firm lub wywołań podprocesów zaleca się wykonanie elementu Runbook w [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Uzyskiwanie dostępu do charakterystyki urządzenia i aplikacji

Zadania elementu Runbook, które są uruchamiane w piaskownicach platformy Azure, nie mają dostępu do żadnych właściwości urządzeń i aplikacji. Najpopularniejszym interfejsem API używanym do wykonywania zapytań o metryki wydajności w systemie Windows jest usługa WMI z niektórymi typowymi metrykami używanymi do wykorzystania pamięci i procesora CPU. Nie ma znaczenia, który interfejs API jest używany, ponieważ zadania w chmurze nie mają dostępu do wdrożenia firmy Microsoft opartego na sieci Web (WBEM). Ta platforma jest oparta na model wspólnych informacji (CIM), co zapewnia standardy branżowe do definiowania cech urządzeń i aplikacji.

## <a name="handling-errors"></a>Obsługa błędów

Elementy Runbook muszą być w stanie obsłużyć błędy. Program PowerShell zawiera dwa typy błędów, kończące się i niepowodujące zakończenia. Przerywanie błędów Zatrzymaj wykonywanie elementu Runbook, gdy wystąpią. Element Runbook zostaje zatrzymany ze stanem zadania **zakończonego niepowodzeniem**.

Błędy niepowodujące zakończenia umożliwiają wykonywanie skryptu nawet po wystąpieniu. Przykładem błędu niekończącego jest taki, który występuje, gdy element Runbook używa polecenia cmdlet **GET-ChildItem** ze ścieżką, która nie istnieje. Program PowerShell widzi, że ścieżka nie istnieje, zgłasza błąd i kontynuuje do następnego folderu. W tym przypadku błąd nie ustawi stanu zadania elementu Runbook na **Niepowodzenie**, a zadanie może być nawet ukończone. Aby wymusić zatrzymanie elementu Runbook na nieprzerwanym błędzie, można użyć `-ErrorAction Stop` w poleceniu cmdlet.

## <a name="handling-jobs"></a>Obsługa zadań

Środowisko wykonawcze można ponownie wykorzystać do zadań z tego samego konta usługi Automation. Pojedynczy element Runbook może mieć wiele zadań uruchomionych jednocześnie. Im więcej zadań jest wykonywanych w tym samym czasie, tym częściej można je wysłać do tej samej piaskownicy.

Zadania uruchomione w tym samym procesie piaskownicy mogą mieć wpływ na siebie nawzajem. Przykładem jest uruchomienie polecenia cmdlet **Disconnect-AzAccount** . Wykonanie tego polecenia cmdlet rozłącza każde zadanie elementu Runbook w udostępnionym procesie piaskownicy.

Zadania programu PowerShell uruchomione z elementu Runbook, który działa w piaskownicy platformy Azure, mogą nie działać w trybie pełnego języka. Aby dowiedzieć się więcej na temat trybów języka programu PowerShell, zobacz [tryby języka programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Aby uzyskać dodatkowe informacje dotyczące współpracy z zadaniami w Azure Automation, zobacz [pobieranie stanu zadania przy użyciu programu PowerShell](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Stany zadań

W poniższej tabeli opisano Stany, które są możliwe dla danego zadania.

| Stan | Opis |
|:--- |:--- |
| Zakończone |Zadanie zostało ukończone pomyślnie. |
| Niepowodzenie |Nie można skompilować elementu Runbook graficznego lub przepływu pracy programu PowerShell. Nie można uruchomić elementu Runbook skryptu programu PowerShell lub zadanie miało wyjątek. Zobacz [Azure Automation typów elementów Runbook](automation-runbook-types.md).|
| Niepowodzenie, oczekiwanie na zasoby |Zadanie nie powiodło się, ponieważ osiągnął limit [godziwego udziału](#fair-share) trzy razy i został uruchomiony z tego samego punktu kontrolnego lub od początku elementu Runbook za każdym razem. |
| W kolejce |Zadanie oczekuje na udostępnienie zasobów w procesie roboczym usługi Automation, aby można było je uruchomić. |
| Uruchamianie |Zadanie zostało przypisane do procesu roboczego, a system go uruchamia. |
| Wznawianie |System wznawia zadanie po jego wstrzymaniu. |
| Działanie |Zadanie jest uruchomione. |
| Uruchamianie, oczekiwanie na zasoby |Zadanie zostało zwolnione, ponieważ osiągnęło odpowiedni limit udostępniania. Wkrótce zostanie wznowione od ostatniego punktu kontrolnego. |
| Zatrzymano |Zadanie zostało zatrzymane przez użytkownika przed jego ukończeniem. |
| Zatrzymywanie |System zatrzymuje zadanie. |
| Suspended |Dotyczy tylko [elementów Runbook środowiska graficznego i programu PowerShell](automation-runbook-types.md) . Zadanie zostało zawieszone przez użytkownika, przez system lub za pomocą polecenia w elemencie Runbook. Jeśli element Runbook nie ma punktu kontrolnego, rozpocznie się od początku. Jeśli ma punkt kontrolny, można go uruchomić ponownie i wznowić od ostatniego punktu kontrolnego. System zawiesza element Runbook tylko wtedy, gdy wystąpi wyjątek. Domyślnie zmienna *ErrorActionPreference* jest ustawiona na **Kontynuuj**, co oznacza, że zadanie działa w przypadku błędu. Jeśli zmienna preferencji jest ustawiona na **Zatrzymaj**, zadanie zawiesza się po błędzie.  |
| Zawieszanie |Dotyczy tylko [elementów Runbook środowiska graficznego i programu PowerShell](automation-runbook-types.md) . System próbuje zawiesić zadanie na żądanie użytkownika. Element Runbook musi dotrzeć do swojego następnego punktu kontrolnego, zanim będzie można go wstrzymać. Jeśli przeszedł już ostatni punkt kontrolny, kończy się przed jego wstrzymaniem. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Wyświetlanie stanu zadania z Azure Portal

Można wyświetlić podsumowanie stanu wszystkich zadań elementu Runbook lub przejść do szczegółów dotyczących określonego zadania elementu Runbook w Azure Portal. Możesz również skonfigurować integrację z obszarem roboczym Log Analytics, aby przesyłać strumieniowo stan zadań elementu Runbook i zadania. Więcej informacji na temat integrowania z dziennikami Azure Monitor można znaleźć [w temacie przekazywanie stanu zadań i strumieni zadań z automatyzacji do Azure monitor dzienników](automation-manage-send-joblogs-log-analytics.md).

Po prawej stronie wybranego konta usługi Automation można zobaczyć podsumowanie wszystkich zadań elementu Runbook na kafelku **Statystyka zadania** .

![Kafelek statystyk zadania](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ten kafelek przedstawia liczbę i graficzną reprezentację stanu zadania dla każdego wykonanego zadania.

Kliknięcie kafelka przedstawia stronę **zadania** , która zawiera skróconą listę wszystkich wykonanych zadań. Na tej stronie jest wyświetlany stan, nazwa elementu Runbook, czas rozpoczęcia i czas zakończenia każdego zadania.

![Strona zadań konta usługi Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Listę zadań można filtrować, wybierając pozycję **Filtruj zadania**. Odfiltruj określony element Runbook, stan zadania lub wybór z listy rozwijanej i podaj zakres czasu dla wyszukiwania.

![Stan zadania filtrowania](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Możesz też wyświetlić szczegóły podsumowania zadania dla określonego elementu Runbook, wybierając ten element Runbook ze strony **elementów Runbook** na koncie usługi Automation, a następnie wybierając kafelek **zadania** . Ta akcja przedstawia stronę **zadania** . W tym miejscu możesz kliknąć rekord zadania, aby wyświetlić jego szczegóły i dane wyjściowe.

![Strona zadań konta usługi Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Wyświetlanie podsumowania zadania

Opisane powyżej podsumowanie zadań pozwala wyświetlić listę wszystkich zadań, które zostały utworzone dla określonego elementu Runbook, oraz ich najnowszy stan. Aby wyświetlić szczegółowe informacje i dane wyjściowe dla zadania, kliknij jego nazwę na liście. Widok szczegółowy zadania zawiera wartości parametrów elementu Runbook, które zostały udostępnione dla tego zadania.

Następujące kroki służą do wyświetlania zadań elementu Runbook.

1. W Azure Portal wybierz pozycję **Automatyzacja** , a następnie wybierz nazwę konta usługi Automation.
2. Z poziomu centrum wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
3. Na stronie **elementy Runbook** wybierz element Runbook z listy.
3. Na stronie wybranego elementu Runbook kliknij kafelek **zadania** .
4. Kliknij jedno z zadań na liście i Wyświetl jego szczegóły i dane wyjściowe na stronie szczegółów zadania elementu Runbook.

### <a name="retrieving-job-status-using-powershell"></a>Pobieranie stanu zadania przy użyciu programu PowerShell

Użyj polecenia cmdlet **Get-AzAutomationJob** , aby pobrać zadania utworzone dla elementu Runbook i szczegółowe informacje o konkretnym zadaniu. Po uruchomieniu elementu Runbook za pomocą programu PowerShell przy użyciu polecenia **Start-AzAutomationRunbook**funkcja zwróci wynikowe zadanie. Użyj polecenia [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) , aby pobrać dane wyjściowe zadania.

Poniższy przykład pobiera ostatnie zadanie dla przykładowego elementu Runbook i wyświetla jego stan, wartości podane dla parametrów elementu Runbook oraz dane wyjściowe zadania.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Poniższy przykład pobiera dane wyjściowe dla określonego zadania i zwraca każdy rekord. Jeśli istnieje wyjątek dla jednego z rekordów, skrypt zapisuje wyjątek zamiast wartości. To zachowanie jest przydatne, ponieważ wyjątki mogą zawierać dodatkowe informacje, które mogą nie być rejestrowane normalnie podczas danych wyjściowych.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="getting-details-from-the-activity-log"></a>Pobieranie szczegółów z dziennika aktywności

Możesz pobrać szczegóły elementu Runbook, takie jak osoba lub konto, które uruchomiło element Runbook, z dziennika aktywności dla konta usługi Automation. Poniższy przykład programu PowerShell zawiera ostatniego użytkownika do uruchomienia określonego elementu Runbook.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Udostępnianie zasobów między elementami Runbook

Aby udostępnić zasoby między wszystkimi elementami Runbook w chmurze, Azure Automation tymczasowo zwalnia lub przerywa każde zadanie, które zostało uruchomione przez ponad trzy godziny. Zadania dla [elementów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [elementów Runbook języka Python](automation-runbook-types.md#python-runbooks) są zatrzymane i nie są ponownie uruchamiane, a stan zadania zostanie **zatrzymany**.

W przypadku długotrwałych zadań zaleca się użycie hybrydowego procesu roboczego elementu Runbook. Hybrydowe procesy robocze elementów Runbook nie są ograniczone przez sprawiedliwy udział i nie mają ograniczenia czasu wykonywania elementu Runbook. Pozostałe [limity](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) zadań dotyczą zarówno piaskownic systemu Azure, jak i hybrydowych procesów roboczych elementów Runbook. Chociaż hybrydowe procesy robocze elementów Runbook nie są ograniczone przez 3-godzinny limit udziałów, należy opracować elementy Runbook do działania w ramach procesów roboczych, które obsługują ponowne uruchomienia z nieoczekiwanych problemów z infrastrukturą lokalną.

Kolejną opcją jest optymalizacja elementu Runbook przy użyciu podrzędnych elementów Runbook. Na przykład element Runbook może przechodzić przez tę samą funkcję na kilku zasobach, takich jak operacja bazy danych na kilku bazach danych. Tę funkcję można przenieść do [podrzędnego elementu Runbook](automation-child-runbooks.md) i mieć do niej wywołanie elementu Runbook za pomocą polecenia **Start-AzAutomationRunbook**. Podrzędne elementy Runbook są wykonywane równolegle w oddzielnych procesach.

Użycie podrzędnych elementów Runbook zmniejsza łączny czas pracy nadrzędnego elementu Runbook. Element Runbook może użyć polecenia cmdlet **Get-AzAutomationJob** , aby sprawdzić stan zadania podrzędnego elementu Runbook, jeśli nadal ma operacje do wykonania po zakończeniu działania podrzędnego.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat metod, których można użyć do uruchomienia elementu Runbook w Azure Automation, zobacz [Uruchamianie elementu Runbook w programie Azure Automation](automation-starting-a-runbook.md).
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
