---
title: Podrzędne elementy Runbook w Azure Automation
description: Opisuje różne metody uruchamiania elementu Runbook w Azure Automation z innego elementu Runbook i udostępniania informacji między nimi.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367367"
---
# <a name="child-runbooks-in-azure-automation"></a>Podrzędne elementy Runbook w Azure Automation

Zaleca się, aby Azure Automation pisać wielokrotne, modularne elementy Runbook z dyskretną funkcją wywoływaną przez inne elementy Runbook. Nadrzędny element Runbook często wywołuje jeden lub więcej podrzędnych elementów Runbook w celu wykonania wymaganych funkcji. Istnieją dwa sposoby wywoływania podrzędnego elementu Runbook, a istnieją różne różnice, które należy zrozumieć, aby można było określić, który jest najlepszy dla Twoich scenariuszy.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Wywoływanie podrzędnego elementu Runbook przy użyciu wykonywania wbudowanego

Aby wywołać element Runbook w sposób wbudowany z innego elementu Runbook, użyj nazwy elementu Runbook i podaj wartości dla jego parametrów, podobnie jak w przypadku użycia działania lub polecenia cmdlet. Wszystkie elementy Runbook w tym samym koncie usługi Automation są dostępne dla wszystkich innych, aby można było ich używać w ten sposób. Nadrzędny element Runbook czeka na zakończenie działania podrzędnego elementu Runbook przed przejściem do następnego wiersza, a wszystkie dane wyjściowe powracają bezpośrednio do elementu nadrzędnego.

Gdy użytkownik śródwierszowo wywołać element runbook, jest ono wykonywane w tym samym zadaniu co nadrzędny element runbook. Nie ma żadnego wskazania w historii zadań podrzędnego elementu Runbook. Wszystkie wyjątki i wszystkie dane wyjściowe strumienia z podrzędnego elementu Runbook są skojarzone z elementem nadrzędnym. Takie zachowanie skutkuje mniejszą liczbą zadań i ułatwia ich śledzenie i rozwiązywanie problemów.

Po opublikowaniu elementu Runbook wszystkie podrzędne elementy Runbook, które wywołuje, muszą już być opublikowane. Przyczyną jest to, że Azure Automation kompiluje skojarzenie z dowolnymi podrzędnymi elementami Runbook podczas kompilowania elementu Runbook. Jeśli podrzędne elementy Runbook nie zostały jeszcze opublikowane, nadrzędny element Runbook pojawia się w celu poprawnego opublikowania, ale generuje wyjątek podczas jego uruchamiania. W takim przypadku można ponownie opublikować nadrzędny element Runbook, aby prawidłowo odwoływać się do podrzędnych elementów Runbook. Nie trzeba ponownie publikować nadrzędnego elementu Runbook, jeśli jakikolwiek podrzędny element Runbook zostanie zmieniony, ponieważ skojarzenie zostało już utworzone.

Parametry podrzędnego elementu Runbook o nazwie inline mogą być dowolnego typu danych, łącznie z obiektami złożonymi. Nie ma [serializacji JSON](start-runbooks.md#runbook-parameters), ponieważ jest uruchamiany element Runbook przy użyciu Azure Portal lub polecenia cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) .

### <a name="runbook-types"></a>Typy elementów Runbook

Które typy elementów Runbook mogą być wzajemnie wywoływane?

* [Element Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [graficzny element Runbook](automation-runbook-types.md#graphical-runbooks) mogą wywoływać siebie nawzajem, ponieważ obie są oparte na programie PowerShell.
* [Element Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) i graficzny element Runbook przepływu pracy programu PowerShell mogą wywołać siebie nawzajem, ponieważ oba są oparte na przepływach pracy programu PowerShell.
* Typy programu PowerShell i typy przepływów pracy programu PowerShell nie mogą wywoływać siebie nawzajem i muszą używać `Start-AzAutomationRunbook`.

Kiedy publikuje się zamówienie?

Kolejność publikowania elementów Runbook dotyczy tylko przepływu pracy programu PowerShell i graficznych elementów Runbook przepływu pracy programu PowerShell.

Gdy element Runbook wywoła graficzny lub podrzędny element Runbook przepływu pracy programu PowerShell przy użyciu wykonywania wbudowanego, używa nazwy elementu Runbook. Nazwa musi zaczynać się od `.\\`, aby określić, że skrypt znajduje się w katalogu lokalnym.

### <a name="example"></a>Przykład

W poniższym przykładzie jest uruchamiany testowy podrzędny element Runbook, który akceptuje obiekt złożony, wartość całkowitą i wartość logiczną. Dane wyjściowe podrzędnego elementu runbook jest przypisany do zmiennej. W takim przypadku podrzędny element Runbook jest elementem Runbook przepływu pracy programu PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

W tym samym przykładzie użyto elementu Runbook programu PowerShell jako elementu podrzędnego.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Uruchamianie podrzędnego elementu Runbook za pomocą polecenia cmdlet

> [!IMPORTANT]
> Jeśli element Runbook wywoła podrzędny element Runbook za pomocą polecenia cmdlet `Start-AzAutomationRunbook` z parametrem `Wait` i podrzędny element Runbook generuje wynik obiektu, może wystąpić błąd. Aby obejść ten błąd, zobacz [podrzędne elementy Runbook z danymi wyjściowymi obiektów](troubleshoot/runbooks.md#child-runbook-object) , aby dowiedzieć się, jak wdrożyć logikę w celu sondowania wyników przy użyciu polecenia cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

Za pomocą `Start-AzAutomationRunbook` można uruchomić element Runbook, zgodnie z opisem w artykule [Aby uruchomić element Runbook za pomocą programu Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Istnieją dwa tryby użycia tego polecenia cmdlet. W jednym trybie polecenie cmdlet zwraca identyfikator zadania, gdy zadanie jest tworzone dla podrzędnego elementu Runbook. W innym trybie, który skrypt włącza, określając parametr *wait* , polecenie cmdlet czeka do momentu zakończenia zadania podrzędnego i zwraca dane wyjściowe z podrzędnego elementu Runbook.

Zadanie z podrzędnego elementu Runbook uruchomionego za pomocą polecenia cmdlet jest uruchamiane niezależnie od zadania nadrzędnego elementu Runbook. To zachowanie skutkuje większymi zadaniami niż Uruchamianie elementu Runbook w tekście i sprawia, że zadania są trudniejsze do śledzenia. Element nadrzędny może uruchomić więcej niż jeden podrzędny element Runbook, asynchronicznie bez oczekiwania na ukończenie każdego z nich. W przypadku tego wykonywania równoległego wywoływanie podrzędnych elementów Runbook, nadrzędny element Runbook musi używać [słowa kluczowego Parallel](automation-powershell-workflow.md#parallel-processing).

Podrzędne dane wyjściowe elementu Runbook nie są w sposób wiarygodny zwracane do nadrzędnego elementu Runbook z powodu chronometrażu. Ponadto zmienne, takie jak `$VerbosePreference`, `$WarningPreference`i inne, mogą nie być propagowane do podrzędnych elementów Runbook. Aby uniknąć tych problemów, można uruchomić podrzędne elementy Runbook jako oddzielne zadania automatyzacji przy użyciu `Start-AzAutomationRunbook` z parametrem `Wait`. Ta technika blokuje nadrzędny element Runbook do momentu ukończenia podrzędnego elementu Runbook.

Jeśli nie chcesz, aby nadrzędny element Runbook był blokowany podczas oczekiwania, możesz uruchomić podrzędny element Runbook przy użyciu `Start-AzAutomationRunbook` bez parametru `Wait`. W takim przypadku element Runbook musi używać [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) , aby oczekiwać na ukończenie zadania. Do pobrania wyników należy również użyć [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) i [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

Parametry podrzędnego elementu Runbook uruchomionego za pomocą polecenia cmdlet są podane jako tablica skrótów, zgodnie z opisem w [parametrach elementu Runbook](start-runbooks.md#runbook-parameters). Można używać tylko prostych typów danych. Jeśli element runbook ma parametr o złożonym typie danych, następnie go musi być wywoływany śródwierszowo.

Kontekst subskrypcji może zostać utracony podczas uruchamiania podrzędnych elementów Runbook jako oddzielnych zadań. Aby podrzędny element Runbook wykonywał polecenia AZ module dla określonej subskrypcji platformy Azure, element podrzędny musi uwierzytelniać się w tej subskrypcji niezależnie od nadrzędnego elementu Runbook.

Jeśli zadania w ramach tego samego konta usługi Automation działają z więcej niż jedną subskrypcją, wybranie subskrypcji w jednym zadaniu może zmienić bieżący kontekst subskrypcji dla innych zadań. Aby uniknąć tej sytuacji, użyj `Disable-AzContextAutosave –Scope Process` na początku każdego elementu Runbook. Ta akcja powoduje zapisanie tylko kontekstu dla tego wykonywania elementu Runbook.

### <a name="example"></a>Przykład

Poniższy przykład uruchamia podrzędny element Runbook z parametrami, a następnie czeka na jego ukończenie przy użyciu polecenia cmdlet `Start-AzAutomationRunbook` z parametrem `Wait`. Po zakończeniu przykład zbiera dane wyjściowe poleceń cmdlet z podrzędnego elementu Runbook. Aby użyć `Start-AzAutomationRunbook`, skrypt musi być uwierzytelniany w ramach subskrypcji platformy Azure.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porównanie metod wywoływania podrzędnego elementu Runbook

Poniższa tabela zawiera podsumowanie różnic między dwoma sposobami wywoływania elementu Runbook z innego elementu Runbook.

|  | wbudowane | Polecenie cmdlet |
|:--- |:--- |:--- |
| Zadanie |Podrzędne elementy runbook uruchomione w tym samym zadaniu co element nadrzędny. |Tworzone jest osobne zadanie podrzędnego elementu runbook. |
| Wykonanie |Nadrzędny element runbook czeka, aż podrzędnego elementu runbook, które należy wykonać przed kontynuowaniem. |Nadrzędny element Runbook jest kontynuowany natychmiast po uruchomieniu podrzędnego elementu Runbook *lub* nadrzędny element Runbook czeka na zakończenie zadania podrzędnego. |
| Dane wyjściowe |Nadrzędny element runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu runbook. |Nadrzędny element Runbook musi pobrać dane wyjściowe z podrzędnego zadania elementu Runbook *lub* nadrzędnego elementu Runbook można bezpośrednio pobrać dane wyjściowe z podrzędnego elementu Runbook. |
| Parametry |Wartości parametrów podrzędnego elementu runbook są określane oddzielnie i mogą mieć dowolny typ danych. |Wartości parametrów podrzędnego elementu Runbook muszą być połączone w jedną tablicę skrótów. Ta tablica skrótów może zawierać tylko typy danych Simple, Array i Object, które używają serializacji JSON. |
| Konto usługi Automation |Nadrzędny element Runbook może używać tylko podrzędnego elementu Runbook na tym samym koncie usługi Automation. |Nadrzędne elementy Runbook mogą używać podrzędnego elementu Runbook z dowolnego konta usługi Automation, z tej samej subskrypcji platformy Azure, a nawet z innej subskrypcji, z którą masz połączenie. |
| Publikowanie |Podrzędny element runbook należy opublikować przed opublikowaniem nadrzędnego elementu runbook. |Podrzędny element Runbook jest publikowany w dowolnym momencie przed uruchomieniem nadrzędnego elementu Runbook. |

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie elementu Runbook w Azure Automation](start-runbooks.md)
* [Dane wyjściowe i komunikaty elementu Runbook w Azure Automation](automation-runbook-output-and-messages.md)