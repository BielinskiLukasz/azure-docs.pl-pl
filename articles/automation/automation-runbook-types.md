---
title: Azure Automation typy elementów Runbook
description: W tym artykule opisano typy elementów Runbook, których można użyć w Azure Automation i zagadnienia dotyczące określania, który typ ma być używany.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 24d0123eecc56b56573e94d831283d8d360cd16e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185929"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation typy elementów Runbook

Funkcja automatyzacji procesów Azure Automation obsługuje kilka typów elementów Runbook, zgodnie z definicją w poniższej tabeli. Aby dowiedzieć się więcej o środowisku automatyzacji procesów, zobacz [wykonywanie elementów Runbook w Azure Automation](automation-runbook-execution.md).

| Type | Opis |
|:--- |:--- |
| [Element graficzny](#graphical-runbooks)|Graficzny element Runbook oparty na programie Windows PowerShell i utworzony i edytowany całkowicie w edytorze graficznym w Azure Portal. |
| [Graficzny przepływ pracy programu PowerShell](#graphical-runbooks)|Graficzny element Runbook oparty na przepływie pracy programu Windows PowerShell i utworzony i edytowany całkowicie w edytorze graficznym w Azure Portal. |
| [Program PowerShell](#powershell-runbooks) |Tekstowy element Runbook oparty na skryptach programu Windows PowerShell. |
| [Przepływ pracy programu PowerShell](#powershell-workflow-runbooks)|Tekstowy element Runbook oparty na skryptach przepływu pracy programu Windows PowerShell. |
| [Python](#python-runbooks) |Tekstowy element Runbook oparty na skryptach języka Python. |

Należy wziąć pod uwagę poniższe zagadnienia podczas określania, który typ ma być używany dla określonego elementu Runbook.

* Nie można konwertować elementów Runbook z typu graficznego na tekst lub w inny sposób.
* Istnieją ograniczenia w przypadku używania elementów Runbook różnych typów jako podrzędnych elementów Runbook. Aby uzyskać więcej informacji, zobacz [podrzędne elementy Runbook w Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Graficzne elementy Runbook

Możesz tworzyć i edytować graficzne i graficzne elementy Runbook przepływu pracy programu PowerShell przy użyciu edytora graficznego w Azure Portal. Nie można jednak tworzyć ani edytować tego typu elementu Runbook za pomocą innego narzędzia. Główne funkcje graficznego elementu Runbook:

* Mogą zostać wyeksportowane do plików na koncie usługi Automation, a następnie zaimportowane na inne konto usługi Automation. 
* Generuj kod programu PowerShell. 
* Mogą być konwertowane na lub z graficznych elementów Runbook przepływu pracy programu PowerShell podczas importowania. 

### <a name="advantages"></a>Zalety

* Użyj elementu Visual INSERT-link-Configure Authoring model.
* Skup się na przepływie danych przez proces.
* Wizualizacja reprezentuje procesy zarządzania.
* Dołącz inne elementy Runbook jako podrzędne elementy Runbook, aby utworzyć przepływy pracy wysokiego poziomu.
* Zachęcaj do programowania modularnego.

### <a name="limitations"></a>Ograniczenia

* Nie można utworzyć ani edytować poza Azure Portal.
* Może wymagać działania kodu zawierającego kod programu PowerShell do wykonywania złożonej logiki.
* Nie można przekonwertować na jeden z [formatów tekstowych](automation-runbook-types.md)ani konwertować elementu Runbook Text na format graficzny. 
* Nie można wyświetlić lub bezpośrednio edytować kodu programu PowerShell tworzonego przez graficzny przepływ pracy. Możesz wyświetlić kod utworzony w ramach jakichkolwiek działań kodu.
* Nie można uruchamiać elementów Runbook w hybrydowym programie Runbook Worker systemu Linux. Zobacz [Automatyzowanie zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Elementy Runbook programu PowerShell

Elementy Runbook programu PowerShell są oparte na programie Windows PowerShell. Możesz bezpośrednio edytować kod elementu Runbook przy użyciu edytora tekstu w Azure Portal.  Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportować element Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj wszystkie złożone logiki za pomocą kodu programu PowerShell bez dodatkowych złożoności przepływu pracy programu PowerShell.
* Zacznij szybciej niż elementy Runbook przepływu pracy programu PowerShell, ponieważ nie trzeba ich kompilować przed uruchomieniem.
* Działa na platformie Azure i w hybrydowych procesach roboczych elementu Runbook dla systemów Windows i Linux.

### <a name="limitations"></a>Ograniczenia

* Musisz znać skrypty programu PowerShell.
* Elementy Runbook nie mogą używać [przetwarzania równoległego](automation-powershell-workflow.md#use-parallel-processing) w celu równoległego wykonywania wielu akcji.
* Jeśli wystąpi błąd, elementy Runbook nie mogą użyć [punktów kontrolnych](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) w celu wznowienia działania elementu Runbook.
* Można uwzględnić tylko elementy Runbook przepływu pracy programu PowerShell i graficzne elementy Runbook jako podrzędne elementy Runbook za pomocą polecenia cmdlet [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) , które tworzy nowe zadanie.

### <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono bieżące znane problemy dotyczące elementów Runbook programu PowerShell:

* Elementy Runbook programu PowerShell nie mogą pobrać nieszyfrowanego [zasobu zmiennej](./shared-resources/variables.md) z wartością null.
* Elementy Runbook programu PowerShell nie mogą pobrać zasobu zmiennej z `*~*` nazwą.
* Operacja [Get-Process](/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) w pętli w elemencie Runbook programu PowerShell może ulec awarii po około 80 iteracjach.
* Element Runbook programu PowerShell może zakończyć się niepowodzeniem, jeśli próbuje napisać dużą ilość danych do strumienia wyjściowego jednocześnie. Na ogół można obejść ten problem, ponieważ element Runbook wyprowadza tylko informacje potrzebne do pracy z dużymi obiektami. Na przykład zamiast używać bez `Get-Process` ograniczeń, można użyć polecenia cmdlet, które są wymagane tylko w programie `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>Elementy Runbook przepływu pracy programu PowerShell

Elementy Runbook przepływu pracy programu PowerShell są tekstowymi elementami Runbook opartymi na [przepływie pracy programu Windows PowerShell](automation-powershell-workflow.md). Możesz bezpośrednio edytować kod elementu Runbook przy użyciu edytora tekstu w Azure Portal. Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportować element Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj wszystkie złożone logiki za pomocą kodu przepływu pracy programu PowerShell.
* Aby wznowić działanie w przypadku wystąpienia błędu, należy użyć [punktów kontrolnych](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) .
* Równoległe [Przetwarzanie](automation-powershell-workflow.md#use-parallel-processing) umożliwia równoległe wykonywanie wielu akcji.
* Może zawierać inne graficzne elementy Runbook i elementy Runbook przepływu pracy programu PowerShell jako podrzędne elementy Runbook do tworzenia przepływów pracy wysokiego poziomu.

### <a name="limitations"></a>Ograniczenia

* Należy zapoznać się z przepływem pracy programu PowerShell.
* Elementy Runbook muszą dotyczyć dodatkowej złożoności przepływu pracy programu PowerShell, takich jak [obiekty deserializowane](automation-powershell-workflow.md#deserialized-objects).
* Uruchamianie elementów Runbook trwa dłużej niż elementy Runbook programu PowerShell, ponieważ muszą one zostać skompilowane przed uruchomieniem.
* Elementy Runbook programu PowerShell można dołączać tylko jako podrzędne elementy Runbook za pomocą `Start-AzAutomationRunbook` polecenia cmdlet programu.
* Nie można uruchomić elementów Runbook w hybrydowym procesie roboczym systemu Linux.

## <a name="python-runbooks"></a>Elementy Runbook języka Python

Kompilacja elementów Runbook języka Python w języku Python 2. Kod elementu Runbook można edytować bezpośrednio przy użyciu edytora tekstu w Azure Portal. Możesz również użyć edytora tekstu w trybie offline i [zaimportować element Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Zalety

* Używaj niezawodnych bibliotek języka Python.
* Można uruchamiać na platformie Azure lub na hybrydowych procesach roboczych elementów Runbook w systemie Linux. Hybrydowe procesy robocze elementów Runbook systemu Windows są obsługiwane z zainstalowanym środowiskiem [Python 2.7](https://www.python.org/downloads/release/latest/python2) .

### <a name="limitations"></a>Ograniczenia

* Musisz znać skrypty języka Python.
* Obecnie jest obsługiwany tylko język Python 2. Wszystkie funkcje specyficzne dla języka Python 3 kończą się niepowodzeniem.
* Aby korzystać z bibliotek innych firm, należy [zaimportować pakiety](python-packages.md) do konta usługi Automation.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o elementach Runbook programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook programu PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Aby dowiedzieć się więcej o elementach Runbook przepływu pracy programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook przepływu pracy programu PowerShell](learn/automation-tutorial-runbook-textual.md).
* Aby dowiedzieć się więcej na temat graficznych elementów Runbook, zobacz [Samouczek: Tworzenie graficznego elementu Runbook](learn/automation-tutorial-runbook-graphical.md).
* Aby dowiedzieć się więcej o elementach Runbook języka Python, zobacz [Samouczek: Tworzenie elementu Runbook w języku Python](learn/automation-tutorial-runbook-textual-python2.md).
