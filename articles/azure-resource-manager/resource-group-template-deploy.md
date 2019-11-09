---
title: Wdrażanie zasobów przy użyciu programu PowerShell i szablonu | Microsoft Docs
description: Użyj Azure Resource Manager i Azure PowerShell do wdrożenia zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: 0634b069c79495ad6de536b27ebd9981eeb36128
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837091"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)

Dowiedz się, jak używać Azure PowerShell z szablonami Menedżer zasobów do wdrażania zasobów na platformie Azure. Aby uzyskać więcej informacji na temat pojęć związanych z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Azure Resource Manager omówienie](resource-group-overview.md).

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można określić w ramach subskrypcji platformy Azure lub grupy zasobów w ramach subskrypcji. W większości przypadków wdrożenie zostanie ukierunkowane na grupę zasobów. Wdrożenia subskrypcji umożliwiają stosowanie zasad i przypisań ról w ramach subskrypcji. W celu utworzenia grupy zasobów i wdrożenia do niej zasobów należy również użyć wdrożeń subskrypcji. W zależności od zakresu wdrożenia używane są inne polecenia.

Aby wdrożyć w **grupie zasobów**, użyj polecenie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Aby wdrożyć w **ramach subskrypcji**, użyj polecenie [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Aby uzyskać więcej informacji o wdrożeniach na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

Obecnie wdrożenia grup zarządzania są obsługiwane tylko za pomocą interfejsu API REST. Aby uzyskać więcej informacji o wdrożeniach na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

W przykładach w tym artykule są używane wdrożenia grup zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć szablon do wdrożenia. Jeśli jeszcze tego nie zrobiono, Pobierz i Zapisz [przykładowy szablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z repozytorium szablonów szybkiego startu platformy Azure. Nazwa pliku lokalnego użyta w tym artykule to **c:\MyTemplates\azuredeploy.JSON**.

Jeśli nie używasz usługi Azure Cloud Shell do wdrażania szablonów, musisz zainstalować Azure PowerShell i nawiązać połączenie z platformą Azure:

- **Zainstaluj Azure PowerShell polecenia cmdlet na komputerze lokalnym.** Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps).
- **Połącz się z platformą Azure za pomocą polecenia [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Jeśli masz wiele subskrypcji platformy Azure, może być również konieczne uruchomienie [polecenie Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Aby uzyskać więcej informacji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Wdróż szablon lokalny

Poniższy przykład tworzy grupę zasobów i wdraża szablon z komputera lokalnego. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może składać się z maksymalnie 90 znaków. Nie może kończyć się kropką.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Wdrożenie może potrwać kilka minut.

## <a name="deploy-remote-template"></a>Wdróż zdalny szablon

Zamiast przechowywania szablonów Menedżer zasobów na komputerze lokalnym, warto przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Możesz również przechowywać je na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji.

Aby wdrożyć szablon zewnętrzny, użyj parametru **TemplateUri** . Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z usługi GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Poprzedni przykład wymaga publicznie dostępnego identyfikatora URI dla szablonu, który działa w większości scenariuszy, ponieważ szablon nie powinien zawierać poufnych danych. Jeśli musisz określić dane poufne (na przykład hasło administratora), przekaż tę wartość jako bezpieczny parametr. Jeśli jednak nie chcesz, aby szablon był dostępny publicznie, możesz go chronić, przechowując go w prywatnym kontenerze magazynu. Informacje o wdrażaniu szablonu wymagającego tokenu sygnatury dostępu współdzielonego (SAS) znajdują się w temacie [Deploy Private Template with SAS token](resource-manager-powershell-sas-token.md). Aby przejść przez samouczek, zobacz [Samouczek: integracja Azure Key Vault w Menedżer zasobów Template Deployment](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Wdrażanie z poziomu usługi Azure Cloud Shell

Za pomocą [Azure Cloud Shell](https://shell.azure.com) można wdrożyć szablon. Aby wdrożyć szablon zewnętrzny, podaj identyfikator URI szablonu. Aby wdrożyć szablon lokalny, należy najpierw załadować szablon do konta magazynu dla Cloud Shell. Aby przekazać pliki do powłoki, wybierz ikonę menu **przekazywanie/pobieranie plików** z okna powłoki.

Aby otworzyć usługę Cloud Shell, przejdź do [https://shell.azure.com](https://shell.azure.com)lub wybierz opcję **Wypróbuj** z następującej sekcji kodu:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Aby wkleić kod do powłoki, kliknij prawym przyciskiem myszy wewnątrz powłoki, a następnie wybierz **Wklej**.

## <a name="pass-parameter-values"></a>Przekaż wartości parametrów

Aby przekazać wartości parametrów, można użyć parametrów wbudowanych lub pliku parametrów.

### <a name="inline-parameters"></a>Parametry wbudowane

Aby przekazać parametry wbudowane, Podaj nazwy parametru za pomocą polecenia `New-AzResourceGroupDeployment`. Na przykład aby przekazać ciąg i tablicę do szablonu, użyj:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Możesz również pobrać zawartość pliku i podać tę zawartość jako parametr wbudowany.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Pobieranie wartości parametru z pliku jest przydatne, gdy trzeba podać wartości konfiguracyjne. Można na przykład udostępnić [wartości z usługi Cloud-init dla maszyny wirtualnej z systemem Linux](../virtual-machines/linux/using-cloud-init.md).

Jeśli musisz przekazać tablicę obiektów, Utwórz tabele skrótów w programie PowerShell i Dodaj je do tablicy. Przekaż tę tablicę jako parametr podczas wdrażania.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywać parametry jako wartości wbudowane w skrypcie, można ułatwić korzystanie z pliku JSON, który zawiera wartości parametrów. Plik parametru może być plikiem lokalnym lub zewnętrznym z dostępnym identyfikatorem URI.

Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów Menedżer zasobów](resource-manager-parameter-files.md).

Aby przekazać lokalny plik parametrów, użyj parametru **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Aby przekazać zewnętrzny plik parametrów, użyj parametru **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Wdrożenia szablonów testowych

Aby przetestować wartości szablonu i parametrów bez faktycznego wdrażania zasobów, użyj polecenie [test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Jeśli nie wykryto żadnych błędów, polecenie kończy się bez odpowiedzi. Jeśli zostanie wykryty błąd, polecenie zwróci komunikat o błędzie. Na przykład przekazanie nieprawidłowej wartości dla jednostki SKU konta magazynu zwróci następujący błąd:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Jeśli szablon zawiera błąd składniowy, polecenie zwróci błąd wskazujący, że nie można przeanalizować szablonu. Komunikat wskazuje numer wiersza i położenie błędu analizy.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Następne kroki

- Aby wrócić do pomyślnego wdrożenia, gdy wystąpi błąd, zobacz [wycofywanie po pomyślnym wdrożeniu](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Azure Resource Manager trybami wdrożenia](deployment-modes.md).
- Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów Azure Resource Manager](resource-group-authoring-templates.md).
- Informacje o wdrażaniu szablonu wymagającego tokenu SAS można znaleźć w temacie [Deploy Private Template with SAS token](resource-manager-powershell-sas-token.md).
