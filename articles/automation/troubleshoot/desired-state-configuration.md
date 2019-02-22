---
title: Rozwiązywanie problemów przy użyciu usługi Azure Automation Desired State Configuration (DSC)
description: Ten artykuł zawiera informacje na temat rozwiązywania problemów Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a34dea7e1eb53531db55dc62df8fbad8541f7a35
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586804"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Rozwiązywanie problemów z Desired State Configuration (DSC)

Ten artykuł zawiera informacje na temat rozwiązywania problemów z Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Typowe błędy podczas pracy z Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Scenariusz: Nie można usunąć konfiguracji przy użyciu znaków specjalnych w portalu

#### <a name="issue"></a>Problem

Podczas próby usunięcia konfiguracji DSC w portalu, zostanie wyświetlony następujący błąd:

```
An error occured while deleteing the DSC configuration '<name>'.  Error-details: The arguement configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Przyczyna

Jest to problem tymczasowy, planowane jest rozpoznawana.

#### <a name="resolution"></a>Rozwiązanie

* Użyj polecenia cmdlet "Remove-AzAutomationDscConfiguration" Az, aby usunąć konfigurację.
* Dokumentacja dla tego polecenia cmdlet nie został jeszcze zaktualizowany.  W międzyczasie można znaleźć w dokumentacji modułu AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](https://docs.microsoft.com/en-us/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration?view=azurermps-6.13.0)

### <a name="failed-not-found"></a>Scenariusz: Węzeł jest w stanie nie powiodło się z powodu błędu "Nie znaleziono"

#### <a name="issue"></a>Problem

Węzeł ma raport o stanu, zawiera kod **błędu**:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj gdy węzeł zostanie przypisany do nazwy konfiguracji (na przykład ABC) zamiast Nazwa konfiguracji węzła (np. ABC. Serwer sieci Web).

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, przypisywanej węzeł z "Nazwa konfiguracji węzła", a nie "konfiguracji nazwę".
* Można przypisać konfiguracji węzła do węzła przy użyciu witryny Azure portal lub za pomocą polecenia cmdlet programu PowerShell.

  * Aby można było przypisać konfiguracji węzła do węzła przy użyciu witryny Azure portal, otwórz **węzłów DSC** strona, a następnie wybierz węzeł i kliknij **przypisywanie konfiguracji węzła** przycisku.  
  * Aby można było przypisać konfiguracji węzła do węzła przy użyciu polecenia cmdlet programu PowerShell, użyj **AzureRmAutomationDscNode zestaw** polecenia cmdlet

### <a name="no-mof-files"></a>Scenariusz: Brak konfiguracji węzła (pliki MOF), zostały utworzone podczas kompilowania konfiguracji

#### <a name="issue"></a>Problem

Zadania kompilacji DSC zawiesza się z powodu błędu:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Przyczyna

Podczas następujące wyrażenie **węzła** — słowo kluczowe w konfiguracji DSC, które daje w wyniku `$null`, a następnie są tworzone nie konfiguracje węzłów.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań rozwiązać ten problem:

* Upewnij się, że wyrażenie obok **węzła** — słowo kluczowe w definicji konfiguracji nie ocenia do $null.
* Jeśli przekazujesz ConfigurationData podczas kompilowania konfiguracji upewnij się, czy przekazujesz oczekiwanych wartości, których konfiguracja wymaga od [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC staje się została zablokowana w stanie "w toku"

#### <a name="issue"></a>Problem

Dane wyjściowe agenta DSC:

```
No instance found with given property values
```

#### <a name="cause"></a>Przyczyna

Uaktualniono używanej wersji programu WMF i spowodować uszkodzenie usługi WMI.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać problem, postępuj zgodnie z instrukcjami w [DSC — znane problemy i ograniczenia](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artykułu.

### <a name="issue-using-credential"></a>Scenariusz: Nie można użyć poświadczeń w konfiguracji DSC

#### <a name="issue"></a>Problem

Zadania kompilacji DSC zostało wstrzymane z powodu błędu:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Przyczyna

Użyto poświadczeń w konfiguracji, ale nie zapewniają odpowiedniego **ConfigurationData** można ustawić **PSDscAllowPlainTextPassword** na wartość true dla każdej konfiguracji węzła.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się przekazać właściwego **ConfigurationData** można ustawić **PSDscAllowPlainTextPassword** na wartość true dla każdej konfiguracji węzła wymienione w konfiguracji. Aby uzyskać więcej informacji, zobacz [zasobów w usłudze Azure Automation DSC](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie została uwzględniona w rozwiązaniu problemu, lub nie można rozwiązać problem, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
