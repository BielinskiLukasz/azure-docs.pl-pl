---
title: Samouczek — Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager
description: W tym artykule opisano kroki tworzenia pierwszego szablonu Azure Resource Manager. Dowiesz się więcej na temat składni pliku szablonu i sposobu wdrażania konta magazynu.
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7996f564aaa78313304bf3bc11f549d24fce82a4
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963856"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Samouczek: Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager

W tym samouczku przedstawiono Menedżer zasobów szablonów. Pokazuje, jak utworzyć początkowy szablon i wdrożyć go na platformie Azure. Poznasz strukturę szablonu i narzędzia potrzebne do pracy z szablonami. Ukończenie tego samouczka zajmuje około **12 minut** , ale rzeczywisty czas będzie różny w zależności od liczby narzędzi, które należy zainstalować.

Ten samouczek jest pierwszą częścią serii. W miarę postępów przez serię należy zmodyfikować szablon startowy krok po kroku do momentu zbadania wszystkich podstawowych części szablonu Menedżer zasobów. Elementy te są blokami konstrukcyjnymi dla znacznie bardziej złożonych szablonów. Mamy nadzieję, że na końcu serii masz pewność, że tworzysz własne szablony i chcesz zautomatyzować wdrożenia przy użyciu szablonów.

Jeśli chcesz dowiedzieć się więcej na temat korzyści z używania szablonów i dlaczego należy zautomatyzować wdrażanie za pomocą szablonów, zobacz [Azure Resource Manager templates](template-deployment-overview.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="get-tools"></a>Pobierz narzędzia

Zacznijmy od zagwarantowania, że masz narzędzia potrzebne do tworzenia i wdrażania szablonów.

### <a name="editor"></a>Edytor

Szablony są plikami JSON. Do tworzenia szablonów potrzebny jest dobry Edytor JSON. Zalecamy Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów Tools. Jeśli zachodzi potrzeba zainstalowania tych narzędzi, zobacz [tworzenie Azure Resource Manager szablonów przy użyciu Visual Studio Code](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Wdrożenie wiersza polecenia

Do wdrożenia szablonu wymagane są również Azure PowerShell lub interfejs wiersza polecenia platformy Azure. Instrukcje instalacji znajdują się w temacie:

- [Zainstaluj Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Linux](/cli/azure/install-azure-cli-linux)

Po zainstalowaniu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure upewnij się, że logujesz się po raz pierwszy. Aby uzyskać pomoc, zobacz artykuł [Logowanie — PowerShell](/powershell/azure/install-az-ps#sign-in) lub [Logowanie się do interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli#sign-in).

Teraz możesz zacząć uczenie się o szablonach.

## <a name="create-your-first-template"></a>Utwórz swój pierwszy szablon

1. Otwórz Visual Studio Code z zainstalowanym rozszerzeniem narzędzi Menedżer zasobów.
1. Z menu **plik** wybierz pozycję **nowy plik** , aby utworzyć nowy plik.
1. Z menu **plik** wybierz polecenie **Zapisz jako**.
1. Nazwij plik **azuredeploy** i wybierz rozszerzenie pliku **JSON** . Pełna nazwa pliku **azuredeploy. JSON**.
1. Zapisz plik na stacji roboczej. Wybierz ścieżkę, która jest łatwa do zapamiętania, ponieważ będzie ona dostarczana później podczas wdrażania szablonu.
1. Skopiuj i wklej następujący kod JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Oto jak wygląda środowisko VS Code:

    ![Szablon Menedżer zasobów szablonu Visual Studio Code First](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Ten szablon nie wdraża żadnych zasobów. Zaczynamy od pustego szablonu, dzięki czemu możesz zapoznać się z krokami wdrażania szablonu, minimalizując ryzyko wystąpienia problemu.

    Plik JSON zawiera następujące elementy:

    - **$Schema**: określa lokalizację pliku schematu JSON. Plik schematu opisuje właściwości, które są dostępne w ramach szablonu. Na przykład schemat definiuje **zasoby** jako jedną z prawidłowych właściwości szablonu. Nie martw się, że data schematu to 2015-01-01. Ta wersja schematu jest aktualna i zawiera wszystkie najnowsze funkcje. Data schematu nie została zmieniona, ponieważ od wprowadzenia nie wprowadzono żadnych zmian.
    - **contentversion —** : określa wersję szablonu (na przykład 1.0.0.0). Możesz podać dowolną wartość dla tego elementu. Użyj tej wartości, aby udokumentować znaczące zmiany w szablonie. W przypadku wdrażania zasobów przy użyciu szablonu Ta wartość może być używana do upewnienia się, że odpowiedni szablon jest używany.
    - **zasoby**: zawiera zasoby, które mają zostać wdrożone lub zaktualizowane. Obecnie jest ona pusta, ale później dodasz zasoby.

1. Zapisz plik.

Gratulacje, utworzono pierwszy szablon.

## <a name="sign-in-to-azure"></a>Zaloguj się do platformy Azure

Aby rozpocząć pracę z interfejsem wiersza polecenia Azure PowerShell/Azure, zaloguj się przy użyciu poświadczeń platformy Azure.

# <a name="powershelltabazure-powershell"></a>[Narzędzia](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Utwórz grupę zasobów

Podczas wdrażania szablonu należy określić grupę zasobów, która będzie zawierać zasoby. Przed uruchomieniem polecenia wdrożenia Utwórz grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. Wybierz karty w poniższej sekcji kodu, aby wybrać między Azure PowerShell i interfejsem wiersza polecenia platformy Azure.

# <a name="powershelltabazure-powershell"></a>[Narzędzia](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Wdróż szablon

Aby wdrożyć szablon, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. Użyj utworzonej grupy zasobów. Nadaj nazwę wdrożenia, aby można je było łatwo zidentyfikować w historii wdrażania. Dla wygody należy również utworzyć zmienną, która przechowuje ścieżkę do pliku szablonu. Ta zmienna ułatwia uruchamianie poleceń wdrażania, ponieważ nie trzeba ponownie wpisywać ścieżki przy każdym wdrożeniu.

# <a name="powershelltabazure-powershell"></a>[Narzędzia](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Polecenie wdrożenia zwraca wyniki. Poszukaj `ProvisioningState`, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie.

# <a name="powershelltabazure-powershell"></a>[Narzędzia](#tab/azure-powershell)

![Stan aprowizacji wdrożenia programu PowerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

![Stan aprowizacji wdrożenia interfejsu wiersza polecenia platformy Azure](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Weryfikuj wdrożenie

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.

1. Wybierz wdrożenie grupy zasobów w ostatniej procedurze. Nazwa domyślna to moja **resourceName**. W grupie zasobów nie ma żadnego wdrożonego zasobu.

1. W prawym górnym rogu przeglądu zostanie wyświetlony stan wdrożenia. Wybieranie **1 zakończyło się pomyślnie**.

   ![Wyświetl stan wdrożenia](./media/template-tutorial-create-first-template/deployment-status.png)

1. Zobaczysz historię wdrożenia grupy zasobów. Wybierz pozycję **blanktemplate**.

   ![Wybieranie wdrożenia](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Zostanie wyświetlone podsumowanie wdrożenia. W tym przypadku nie istnieje dużo do sprawdzenia, ponieważ nie wdrożono żadnych zasobów. W dalszej części tej serii pomocne może okazać się zapoznanie się z podsumowaniem w historii wdrażania. Zwróć uwagę na to, że po lewej stronie można wyświetlić dane wejściowe i wyjściowe oraz szablon użyty podczas wdrażania.

   ![Wyświetl podsumowanie wdrożenia](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz teraz, możesz chcieć usunąć grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy** .
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Utworzono prosty szablon do wdrożenia na platformie Azure. W następnym samouczku dodasz do szablonu konto magazynu i wdrożono je w grupie zasobów.

> [!div class="nextstepaction"]
> [Dodaj zasób](template-tutorial-add-resource.md)