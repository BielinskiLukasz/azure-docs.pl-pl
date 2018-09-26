---
title: Mój pierwszy element runbook przepływu pracy programu PowerShell w usłudze Azure Automation
description: Samouczek, który przeprowadzi Cię przez procesy tworzenia, testowania i publikowania prostego tekstowego elementu Runbook przy użyciu przepływu programu PowerShell.
keywords: przepływ pracy programu powershell, przykłady przepływu pracy programu powershell, program powershell przepływu pracy
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 90d2dcb81a6b54d1a8437f0d3ae01aa96cdac3db
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165403"
---
# <a name="my-first-powershell-workflow-runbook"></a>Mój pierwszy element Runbook przepływu pracy programu PowerShell

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ten samouczek przeprowadzi Cię przez proces tworzenia [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) w usłudze Azure Automation. Możesz rozpocząć od prostego elementu runbook, który możesz i opublikujemy, objaśniając równocześnie, jak śledzić stan zadania elementu runbook. Następnie zmodyfikujesz element runbook, aby faktycznie zarządzał zasobami platformy Azure — w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Na koniec należy elementu runbook bardziej niezawodne, dodając parametry elementu runbook.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Zatrzymaj i uruchom tę maszynę, aby nie może być produkcyjna maszyna wirtualna.

## <a name="step-1---create-new-runbook"></a>Krok 1. Tworzenie nowego elementu Runbook

Rozpocznij od utworzenia prostego elementu runbook służącego do wyświetlania tekstu *Witaj, świecie*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).

1. Kliknij przycisk **elementów Runbook** w obszarze **automatyzacji procesów** aby otworzyć listę elementów runbook.
1. Utwórz nowy element runbook, klikając **+ Dodaj element runbook** przycisku i następnie **Utwórz nowy element runbook**.
1. Nadaj elementowi Runbook nazwę *MyFirstRunbook-Workflow*.
1. W takim przypadku możesz zacząć tworzyć [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) dlatego wybierz **przepływu pracy programu Powershell** dla **typ elementu Runbook**.
1. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="step-2---add-code-to-the-runbook"></a>Krok 2. Dodawanie kodu do elementu Runbook

Możesz wpisać kod bezpośrednio w elemencie Runbook lub wybrać polecenia cmdlet, elementy Runbook i elementy zawartości, używając kontrolki Biblioteka, a następnie dodać je do elementu Runbook z powiązanymi parametrami. W ramach tego przewodnika, możesz wpisać bezpośrednio w elemencie runbook.

1. Element runbook jest obecnie pusta, tylko wymagane *przepływu pracy* — słowo kluczowe, nazwa elementu runbook i nawiasy klamrowe, który encases całego przepływu pracy.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Wpisz ciąg *Write-Output „Witaj, świecie”* między nawiasami klamrowymi.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
1. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.

   Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu Runbook w chmurze. Przenosi do *od* gdy proces roboczy pobierze zadanie, a następnie *systemem* , gdy element runbook faktycznie zacznie działać.  

1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W Twoim przypadku powinien zostać wyświetlony *Witaj, świecie*.

   ![Witaj, świecie](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Nowo utworzony element runbook nadal działa w trybie roboczym. należy ją opublikować, zanim będzie można uruchomić w środowisku produkcyjnym. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W Twoim przypadku możesz nie jeszcze wersji opublikowanej został właśnie utworzony element runbook.

1. Kliknij pozycję **Opublikuj**, aby opublikować element Runbook, a następnie kliknij pozycję **Tak** po wyświetleniu monitu.
1. Jeśli przewiniesz w lewo, aby wyświetlić element runbook w programie **elementów Runbook** okienko teraz pokazuje **stan pisania przyp** z **opublikowano**.
1. Przewiń w prawo, aby wyświetlić okienko elementu **MyFirstRunbook-Workflow**.  
   Opcje w górnej części umożliwiają nam uruchamianie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.
1. po prostu chcesz uruchomić element runbook kliknij przycisk **Start** i następnie **tak** po wyświetleniu monitu.

   ![Uruchamianie elementu Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Zostanie otwarte okienko zadania zadanie elementu runbook, który został utworzony. Możesz zamknąć to okienko, ale w takim przypadku użytkownik pozostanie on otwarty, aby było obserwować postęp zadania.
1. Stan zadania jest wyświetlany w **Podsumowanie zadania** i odpowiada stanom widoczny podczas testowania elementu runbook.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Gdy stanem elementu Runbook będzie *Ukończono*, kliknij pozycję **Dane wyjściowe**. Zostanie otwarte okienko danych wyjściowych i można zobaczyć swoje *Witaj, świecie*.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  

1. Zamknij okienko danych wyjściowych.
1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być widoczny tylko *Witaj, świecie* w danych wyjściowych usługi stream, ale mogą zostać wyświetlone inne strumienie zadania elementu runbook, takie jak pełne informacje i błąd jeśli element runbook zapisuje do nich.

   ![Podsumowanie zadania](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Zamknij okienko strumieni i okienko zadania, aby wrócić do okienka MyFirstRunbook.
1. Kliknij pozycję **Zadania**, aby otworzyć okienko zadań dla tego elementu Runbook. Zawiera ono listę wszystkich zadań utworzonych przez dany element Runbook. Powinien być widoczny tylko jedno zadanie, ponieważ możesz uruchomiono zadanie tylko raz.

   ![Zadania](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Możesz kliknąć to zadanie, aby otworzyć okienko zadania wyświetlone wcześniej po uruchomieniu elementu runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Został przetestowany i opublikowany element runbook, ale do tej pory nie robi nic, które są przydatne. Chcesz zarządzał zasobami platformy Azure. Nie będzie możliwe, to jednak, chyba że uwierzytelniania przy użyciu poświadczeń, które są określone w [wymagania wstępne](#prerequisites). Można to zrobić za pomocą **Connect-AzureRmAccount** polecenia cmdlet.

1. Otwórz edytor tekstów, klikając pozycję **Edytuj** w okienku MyFirstRunbook-Workflow.
2. Nie ma potrzeby **Write-Output** już wiersza, więc Przejdź dalej i usuń go.
3. Umieść kursor w pustym wierszu między nawiasami klamrowymi.
4. Wpisz lub skopiuj i wklej poniższy kod, który będzie obsługiwać uwierzytelnianie za pomocą konta Uruchom jako w usłudze Automation:

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** i **Login-AzureRmAccount** są teraz aliasów **Connect-AzureRMAccount**. Jeśli **Connect-AzureRMAccount** polecenie cmdlet nie istnieje, możesz użyć **Add-AzureRmAccount** lub **Login-AzureRmAccount**, możesz też [Aktualizuj moduły ](automation-update-azure-modules.md) na koncie usługi Automation do najnowszej wersji.

> [!NOTE]
> Konieczne może być [Aktualizuj moduły](automation-update-azure-modules.md) mimo, że nowe konto usługi automation zostało właśnie utworzone.

1. Kliknij przycisk **okienko testowania** tak, aby przetestować element runbook.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu powinny pojawić się dane wyjściowe podobne do poniższych, zawierające podstawowe informacje powiązane z kontem. Będzie to potwierdzenie, że poświadczenie jest prawidłowe.

   ![Uwierzytelnianie](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Krok 6. Dodawanie kodu w celu uruchomienia maszyny wirtualnej

Teraz, gdy element runbook jest uwierzytelniany w subskrypcji platformy Azure, można zarządzać zasobami. Możesz dodać polecenie do uruchomienia maszyny wirtualnej. Można wybrać dowolną maszynę wirtualną w subskrypcji platformy Azure, a teraz jest zakodowana nazwa elementu runbook. W przypadku zarządzania zasobami w wielu subskrypcjach należy użyć **- AzureRmContext** parametru wraz z [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

1. Po *Connect-AzureRmAccount*, typ *Start-AzureRmVM-Name 'Nazwa_maszyny_wirtualnej' - ResourceGroupName 'Nazwa_grupy_zasobów'* podanie nazwy i nazwy grupy zasobów maszyny wirtualnej, aby rozpocząć.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzureRmContext $AzureContext
   }
   ```

1. Zapisz element runbook, a następnie kliknij przycisk **okienko testowania** tak, aby można ją przetestować.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Po zakończeniu sprawdź, czy maszyna wirtualna została uruchomiona.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Krok 7. Dodawanie parametru wejściowego do elementu Runbook

Element runbook aktualnie umożliwia uruchomienie maszyny wirtualnej tego zostanie zapisane na stałe w elemencie runbook, ale będzie bardziej użyteczny, jeśli maszyny wirtualnej można określić po uruchomieniu elementu runbook. Dodanie parametrów wejściowych elementu runbook w celu zapewnienia tej funkcji.

1. Dodaj parametry *VMName* i *ResourceGroupName* do elementu Runbook i użyj tych zmiennych z poleceniem cmdlet **Start AzureRmVM**, tak jak w przykładzie poniżej.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które znajdują się w teście.
3. Zamknij okienko testowania.
4. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
5. Zatrzymaj maszynę wirtualną uruchomioną w poprzednim kroku.
6. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. Wpisz wartości parametrów **VMName** i **ResourceGroupName** dla maszyny wirtualnej do uruchomienia.

   ![Uruchamianie elementu Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

7. Po zakończeniu działania elementu Runbook sprawdź, czy maszyna wirtualna została uruchomiona.  

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz artykuł [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz artykuł [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).
