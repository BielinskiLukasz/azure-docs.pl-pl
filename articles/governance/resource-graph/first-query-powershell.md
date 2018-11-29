---
title: Uruchamianie pierwszego zapytania usługi Resource Graph przy użyciu programu Azure PowerShell
description: W tym artykule przedstawiono kroki umożliwiające włączenie modułu usługi Resource Graph dla programu Azure PowerShell i uruchomienie pierwszego zapytania.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 6bd7ceaeaf0b064ebbd8f069a4741489349bb980
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427509"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Uruchamianie pierwszego zapytania usługi Resource Graph przy użyciu programu Azure PowerShell

Pierwszym krokiem do korzystania z usługi Azure Resource Graph jest zainstalowanie modułu dla programu Azure PowerShell. Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania modułu do instalacji programu Azure PowerShell.

Po zakończeniu tego procesu będziesz mieć moduł dodany do wybranej instalacji programu Azure PowerShell i uruchomisz swoje pierwsze zapytanie usługi Resource Graph.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="add-the-resource-graph-module"></a>Dodaj moduł usługi Resource Graph

Aby włączyć program Azure PowerShell do wykonywania zapytań do usługi Azure Resource Graph, musisz dodać moduł. Ten moduł może być używany z lokalnie zainstalowanym środowiskiem Windows PowerShell i programem PowerShell Core lub z [obrazem platformy Docker programu Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Wymagania podstawowe

Moduł usługi Azure Resource Graph wymaga następującego oprogramowania:

- Programu Azure PowerShell w wersji 6.3.0 lub nowszej. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/azure/install-azurerm-ps).

  - W przypadku programu PowerShell Core użyj wersji **Az** modułu Azure PowerShell.

  - W przypadku środowiska Windows PowerShell użyj wersji **AzureRm** modułu Azure PowerShell.

- Modułu PowerShellGet w wersji 2.0.1 lub nowszej. Jeśli jeszcze nie został on zainstalowany lub zaktualizowany, postępuj zgodnie z [tymi instrukcjami](/powershell/gallery/installing-psget).

### <a name="cloud-shell"></a>Cloud Shell

Aby dodać moduł usługi Azure Resource Graph w programie CloudShell, postępuj zgodnie z poniższymi instrukcjami dotyczącymi programu PowerShell Core.

### <a name="powershell-core"></a>Program PowerShell Core

Moduł usługi Resource Graph dla programu PowerShell Core to **Az.ResourceGraph**.

1. Za pomocą **administracyjnego** monitu programu PowerShell Core uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Zweryfikuj, czy moduł został zaimportowany i ma poprawną wersję (0.3.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Włącz wsteczne aliasy dla wersji **Az** do wersji **AzureRm** za pomocą następującego polecenia:

   ```azurepowershell-interactive
   # Enable backwards alias compatibility
   Enable-AzureRmAlias
   ```

### <a name="windows-powershell"></a>Windows PowerShell

Moduł usługi Resource Graph dla programu Windows PowerShell to **AzureRm.ResourceGraph**.

1. Za pomocą **administracyjnego** monitu środowiska Windows PowerShell uruchom następujące polecenie:

   ```powershell
   # Install the Resource Graph (prerelease) module from PowerShell Gallery
   Install-Module -Name AzureRm.ResourceGraph -AllowPrerelease
   ```

1. Zweryfikuj, czy moduł został zaimportowany i ma poprawną wersję (0.1.1-preview):

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Teraz, gdy moduł programu Azure PowerShell został dodany do Twojego wybranego środowiska, nadszedł czas na wypróbowanie prostego zapytania usługi Resource Graph. Zapytanie zwróci pierwsze pięć zasobów platformy Azure przy użyciu właściwości **Name** i **Resource Type** każdego zasobu.

1. Uruchom swoje pierwsze zapytanie usługi Azure Resource Graph za pomocą polecenia cmdlet `Search-AzureRmGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj zapytanie, dodając modyfikator `order by` do właściwości **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Spowoduje to najpierw ograniczenie wyników zapytania, a następnie ich uporządkowanie.

1. Zaktualizuj zapytanie, aby najpierw wykonywało polecenie `order by` w celu sortowania według właściwości **Name**, a następnie polecenie `limit` w celu ograniczenia do pięciu pierwszych wyników:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Gdy końcowe zapytanie zostanie uruchomione wielokrotnie, zakładając, że nic się nie zmieniło w Twoim środowisku, zwrócone wyniki będą spójne i zgodne z oczekiwaniami — uporządkowane według właściwości **Name**, ale nadal ograniczone do pięciu pierwszych wyników.

## <a name="cleanup"></a>Czyszczenie

Jeśli chcesz usunąć moduł usługi Resource Graph ze środowiska programu Azure PowerShell, możesz to zrobić za pomocą następującego polecenia:

```powershell
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> Nie powoduje ono usunięcia wcześniej pobranego pliku modułu. Powoduje tylko usunięcie go z uruchomionej sesji programu PowerShell.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat [języka zapytań](./concepts/query-language.md)
- Dowiedz się, jak [eksplorować zasoby](./concepts/explore-resources.md)
- Uruchamianie pierwszego zapytania za pomocą [interfejsu wiersza polecenia platformy Azure](first-query-azurecli.md)
- Zobacz przykłady [zapytań dla początkujących](./samples/starter.md)
- Zobacz przykłady [zapytań zaawansowanych](./samples/advanced.md)
- Podziel się opinią na platformie [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)