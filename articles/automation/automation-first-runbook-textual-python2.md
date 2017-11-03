---
title: "Mój pierwszy Python runbook automatyzacji Azure | Dokumentacja firmy Microsoft"
description: "Samouczek, który przeprowadzi Cię przez tworzenie, testowanie i publikowania elementu runbook prostego języka Python."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: eslesar
ms.openlocfilehash: 4e7b3049fff76c86956e08d71b22a0f8dbf55b0e
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2017
---
# <a name="my-first-python-runbook"></a>Moje pierwszego elementu runbook języka Python

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [Program PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ten samouczek przedstawia tworzenie [Python runbook](automation-runbook-types.md#python-runbooks) automatyzacji Azure. Rozpoczniemy pracę od prostego elementu Runbook, który przetestujemy i opublikujemy, objaśniając równocześnie, jak śledzić stan zadania elementu Runbook. Następnie zmodyfikujemy element Runbook, aby faktycznie zarządzać zasobami platformy Azure (w tym przypadku uruchomić maszynę wirtualną platformy Azure). Udoskonalimy również element Runbook, dodając parametry, aby działał on bardziej niezawodnie.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Będziemy uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.

## <a name="create-a-new-runbook"></a>Utwórz nowy element runbook

Rozpoczniemy od utworzenia prostego elementu runbook służącego do wyświetlania tekstu *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.
   Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).
1. Kliknij kafelek **Elementy runbook**, aby otworzyć listę elementów runbook.
   ![RunbooksControl](media/automation-first-runbook-textual-python/runbooks-control-tiles.png)
1. Utwórz nowy element Runbook, klikając przycisk **Dodaj element Runbook**, a następnie pozycję **Utwórz nowy element Runbook**.
1. Nadaj nazwę elementu runbook *MyFirstRunbook Python*.
1. W takim przypadku zamierzamy utworzyć [Python runbook](automation-runbook-types.md#python-runbooks) tak wybierz **Python 2** dla **typ elementu Runbook**.
1. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="add-code-to-the-runbook"></a>Dodawanie kodu do elementu runbook

Teraz dodamy prostego polecenia, aby wydrukować tekst "Hello World":

```python
print("Hello World!")
```

Kliknij przycisk **zapisać** można zapisać elementu runbook.

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Przed opublikowaniem elementu Runbook w celu udostępnienia go w środowisku produkcyjnym chcemy go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego **wersji roboczej** i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.
   ![Okienko testowania](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
1. Zostanie utworzone [zadanie elementu Runbook](automation-runbook-execution.md) i pojawi się jego stan.
   Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu runbook w chmurze. Następnym stanem będzie *Uruchamianie*, gdy proces roboczy wywołuje zadanie, a następnie *Uruchomiono*, gdy element Runbook faktycznie zacznie działać.
1. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W naszym przypadku powinien być widoczny ciąg *Witaj, świecie*.
1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i uruchamiania elementu runbook

Element runbook, który utworzyliśmy jest nadal w trybie wersji roboczej. Przed uruchomieniem elementu w środowisku produkcyjnym musimy go opublikować.
Podczas publikowania elementu runbook można zastąpienie istniejącej opublikowanej wersji wersję roboczą.
W tym przypadku nie mamy opublikowanej wersji jeszcze ponieważ właśnie utworzyliśmy elementu runbook.

1. Kliknij pozycję **Opublikuj**, aby opublikować element Runbook, a następnie kliknij pozycję **Tak** po wyświetleniu monitu.
   ![Przycisk Opublikuj](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Jeśli teraz przewiniesz w lewo, aby wyświetlić element Runbook w okienku **Elementy Runbook**, element **Stan tworzenia** będzie mieć wartość **Opublikowano**.
1. Prawo do wyświetlania w okienku przewiń **MyFirstRunbook Python**.
   Opcje w górnej części umożliwiają uruchamianie elementu Runbook, wyświetlanie elementu Runbook, planowanie jego uruchomienia w przyszłości lub utworzenie [elementu webhook](automation-webhooks.md) w celu umożliwienia uruchamiania za pośrednictwem wywołania HTTP.
1. Chcemy uruchomić element Runbook, dlatego w otwartym bloku uruchamiania elementu Runbook kliknij przycisk **Uruchom**, a następnie kliknij przycisk **OK**.
1. Zostanie otwarte okienko zadania elementu Runbook, który utworzyliśmy. Możemy zamknąć to okienko, ale w tym przypadku pozostawimy je otwarte, aby obserwować postęp zadania.
1. Stan zadania jest wyświetlany w oknie **Podsumowanie zadania** i odpowiada stanom obserwowanym podczas testowania elementu Runbook.
1. Gdy stanem elementu Runbook będzie *Ukończono*, kliknij pozycję **Dane wyjściowe**. Zostanie otwarte okienko danych wyjściowych z naszym ciągiem *Witaj, świecie*.
1. Zamknij okienko danych wyjściowych.
1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. W strumieniu danych wyjściowych powinien być widoczny tylko ciąg *Witaj, świecie*, ale mogą zostać wyświetlone inne strumienie zadania elementu Runbook, takie jak Pełne informacje i Błąd, jeśli element Runbook wykonuje w nich operacje zapisywania.
1. Zamknij okienko strumieni i w okienku zadania, aby wrócić do okienka MyFirstRunbook Python.
1. Kliknij pozycję **Zadania**, aby otworzyć okienko zadań dla tego elementu Runbook. Zawiera ono listę wszystkich zadań utworzonych przez dany element Runbook. Ponieważ uruchomiliśmy zadanie tylko raz, powinniśmy zobaczyć tylko jedno zadanie.
1. Możesz kliknąć to zadanie, aby otworzyć okienko zadania wyświetlone przez nas wcześniej po uruchomieniu elementu Runbook. Dzięki temu możesz cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania do zarządzania zasobami Azure

Nasz element Runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Chcemy, aby zarządzał zasobami platformy Azure.
Do zarządzania zasobami Azure, skrypt musi przeprowadzić uwierzytelnianie przy użyciu poświadczeń z sieci [konto automatyzacji](automation-offering-get-started.md).

> [!NOTE]
> Konto automatyzacji musi być utworzony przy użyciu funkcji główną usługi dla niego, aby był certyfikat runas.
> Jeśli nie utworzono konto automatyzacji z nazwy głównej usługi, można uwierzytelniać za pomocą metody w sposób opisany w [Uwierzytelnij z bibliotekami zarządzania platformy Azure dla języka Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Otwórz edytor tekstowy, klikając **Edytuj** w okienku MyFirstRunbook Python.
1. Dodaj następujący kod, aby uwierzytelniać na platformie Azure:
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Dodaj kod, aby utworzyć obliczeniowe Python klienta i uruchomić maszyny Wirtualnej

Aby pracować z maszyn wirtualnych platformy Azure, Utwórz wystąpienie [rozwiązań usługi obliczenia Azure klienta dla języka Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.compute.computemanagementclient?view=azure-python).

Korzystając z klienta obliczeniowe można uruchomić maszyny Wirtualnej. Dodaj następujący kod do elementu runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Gdzie _MyResourceGroup_ to nazwa grupy zasobów, która zawiera maszynę Wirtualną, a _TestVM_ jest nazwa maszyny wirtualnej chcesz uruchomić. 

Testowanie i uruchomić element runbook ponownie, aby zobaczyć, że rozpoczyna się maszyny Wirtualnej.

## <a name="use-input-parameters"></a>Użyj parametrów wejściowych

Element runbook używa obecnie zakodowanych wartości dla nazwy grupy zasobów i maszyny Wirtualnej.
Teraz możemy dodać kod, który pobiera te wartości z parametrów wejściowych.

Używamy `sys.argv` zmienną, aby uzyskać wartości parametrów.
Dodaj następujący kod do elementu runbook natychmiast po drugim `import` instrukcji:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Zostaną zaimportowane `sys` modułu i tworzy dwie zmienne do przechowywania nazwy grupy zasobów i maszyny Wirtualnej.
Zwróć uwagę, że element listy argumentów `sys.argv[0]`, to nazwa skryptu i nie jest wejściem przez użytkownika.

Teraz możemy zmodyfikować ostatnie dwa wiersze z elementu runbook, aby użyć wartości parametru wejściowego zamiast zakodowanych wartości:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Po uruchomieniu elementu runbook języka Python (na **testu** bloku lub jako opublikowanego elementu runbook), można wprowadzić wartości dla parametrów w **Uruchom element Runbook** bloku w obszarze **parametry**.

![Pole wartości parametru](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

Po uruchomieniu wprowadzenie wartości w pierwszym polu, drugi wyświetlane i tak dalej, dzięki czemu w razie potrzeby można wprowadzić dowolną liczbę wartości parametrów.

Wartości są dostępne do skryptu jako `sys.argv` tablicy, tak jak właśnie dodaliśmy kod.

Wprowadź nazwę grupy zasobów jako wartość parametru pierwszy i Nazwa maszyny Wirtualnej do uruchamiania jako wartość drugiego parametru.

![Wprowadź wartości parametrów](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kliknij przycisk **OK** można uruchomić elementu runbook. Element runbook uruchamia i uruchamia wybraną maszynę Wirtualną.

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby dowiedzieć się więcej o tworzeniu aplikacji Azure z języka Python, zobacz [Azure dla deweloperów języka Python](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).
* Aby wyświetlić przykładowe elementy runbook 2 języka Python, zobacz [GitHub automatyzacji Azure](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).
