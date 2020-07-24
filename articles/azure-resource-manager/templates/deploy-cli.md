---
title: Wdrażanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure i szablonu
description: Użyj Azure Resource Manager i interfejsu wiersza polecenia platformy Azure, aby wdrożyć zasoby na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: da865d3b425da6b5969e540a424b513d9a58bd9a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040810"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Wdrażanie zasobów za pomocą szablonów ARM i interfejsu wiersza polecenia platformy Azure

W tym artykule wyjaśniono, jak używać interfejsu wiersza polecenia platformy Azure z szablonami Azure Resource Manager (szablony ARM) do wdrażania zasobów na platformie Azure. Jeśli nie znasz pojęć związanych z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Omówienie wdrażania szablonów](overview.md).

Polecenia wdrożenia zmieniły się w interfejsie CLI platformy Azure w wersji 2.2.0. Przykłady w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Jeśli nie masz zainstalowanego interfejsu wiersza polecenia platformy Azure, możesz użyć [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można określić w grupie zasobów, subskrypcji, grupie zarządzania lub dzierżawie. W większości przypadków wdrożenie zostanie ukierunkowane na grupę zasobów. Aby zastosować zasady i przypisania ról w większym zakresie, należy użyć subskrypcji, grupy zarządzania lub wdrożeń dzierżawców. Podczas wdrażania w ramach subskrypcji można utworzyć grupę zasobów i wdrożyć do niej zasoby.

W zależności od zakresu wdrożenia używane są inne polecenia.

* Aby wdrożyć w **grupie zasobów**, użyj [AZ Deployment Group Create](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Aby wdrożyć w **ramach subskrypcji**, użyj polecenie [AZ Deployment sub Create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Aby uzyskać więcej informacji o wdrożeniach na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

* Aby wdrożyć w **grupie zarządzania**, użyj [AZ Deployment mg Create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Aby uzyskać więcej informacji o wdrożeniach na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

* Aby wdrożyć aplikację w **dzierżawie**, użyj polecenie [AZ Deployment dzierżawca Create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Aby uzyskać więcej informacji na temat wdrożeń na poziomie dzierżawy, zobacz [Tworzenie zasobów na poziomie dzierżawy](deploy-to-tenant.md).

W przykładach w tym artykule są używane wdrożenia grup zasobów.

## <a name="deploy-local-template"></a>Wdróż szablon lokalny

Podczas wdrażania zasobów na platformie Azure:

1. Zaloguj się do swojego konta platformy Azure
2. Utwórz grupę zasobów, która służy jako kontener dla wdrożonych zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może składać się z maksymalnie 90 znaków. Nie może kończyć się kropką.
3. Wdróż w grupie zasobów szablon definiujący zasoby do utworzenia.

Szablon może zawierać parametry, które umożliwiają dostosowanie wdrożenia. Na przykład można podać wartości, które są dostosowane do określonego środowiska (np. deweloperskiego, testowego i produkcyjnego). Przykładowy szablon definiuje parametr dla jednostki SKU konta magazynu.

Poniższy przykład tworzy grupę zasobów i wdraża szablon z komputera lokalnego:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zobaczysz komunikat, który zawiera wynik:

```output
"provisioningState": "Succeeded",
```

## <a name="deployment-name"></a>Nazwa wdrożenia

W poprzednim przykładzie nazwa wdrożenia została nadana `ExampleDeployment` . Jeśli nie podano nazwy wdrożenia, zostanie użyta nazwa pliku szablonu. Na przykład jeśli wdrażasz szablon o nazwie `azuredeploy.json` i nie określisz nazwy wdrożenia, wdrożenie ma nazwę `azuredeploy` .

Za każdym razem, gdy uruchamiasz wdrożenie, do historii wdrożenia grupy zasobów zostanie dodany wpis z nazwą wdrożenia. Jeśli zostanie uruchomione inne wdrożenie i nastąpi taka sama nazwa, wcześniejszy wpis zostanie zastąpiony bieżącym wdrożeniem. Jeśli chcesz zachować unikatowe wpisy w historii wdrożenia, nadaj każdemu wdrożeniu unikatową nazwę.

Aby utworzyć unikatową nazwę, można przypisać liczbę losową.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Lub Dodaj wartość daty.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

W przypadku uruchamiania współbieżnych wdrożeń w tej samej grupie zasobów o tej samej nazwie wdrożenia zostanie wykonane tylko ostatnie wdrożenie. Wszystkie wdrożenia o tej samej nazwie, które nie zostały zakończone, są zastępowane przez ostatnie wdrożenie. Jeśli na przykład zostanie uruchomione wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage1` , a w tym samym czasie zostanie uruchomione inne wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage2` , zostanie wdrożone tylko jedno konto magazynu. Utworzone konto magazynu ma nazwę `storage2` .

Jeśli jednak zostanie uruchomione wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage1` i natychmiast po jego zakończeniu uruchomisz inne wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage2` , masz dwa konta magazynu. Jeden z nich ma nazwę `storage1` , a drugi o nazwie `storage2` . Jednak w historii wdrożenia znajduje się tylko jeden wpis.

W przypadku określenia unikatowej nazwy dla każdego wdrożenia można uruchomić je współbieżnie bez konfliktu. W przypadku uruchomienia wdrożenia o nazwie, `newStorage1` które wdraża konto magazynu o nazwie `storage1` i w tym samym czasie, należy uruchomić inne wdrożenie o nazwie, `newStorage2` które wdraża konto magazynu o nazwie `storage2` , istnieją dwa konta magazynu i dwa wpisy w historii wdrażania.

Aby uniknąć konfliktów z jednoczesnymi wdrożeniami i zapewnić unikatowe wpisy w historii wdrożenia, należy nadać każdemu wdrożeniu unikatową nazwę.

## <a name="deploy-remote-template"></a>Wdróż zdalny szablon

Zamiast przechowywać szablony ARM na komputerze lokalnym, warto przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Możesz również przechowywać je na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji.

Aby wdrożyć szablon zewnętrzny, użyj parametru **Template-URI** . Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z usługi GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Poprzedni przykład wymaga publicznie dostępnego identyfikatora URI dla szablonu, który działa w większości scenariuszy, ponieważ szablon nie powinien zawierać poufnych danych. Jeśli musisz określić dane poufne (na przykład hasło administratora), przekaż tę wartość jako bezpieczny parametr. Jeśli jednak nie chcesz, aby szablon był dostępny publicznie, możesz go chronić, przechowując go w prywatnym kontenerze magazynu. Informacje o wdrażaniu szablonu wymagającego tokenu sygnatury dostępu współdzielonego (SAS) znajdują się w temacie [Deploy Private Template with SAS token](secure-template-with-sas-token.md).

## <a name="preview-changes"></a>Podgląd zmian

Przed wdrożeniem szablonu można wyświetlić podgląd zmian wprowadzonych przez szablon w danym środowisku. Użyj [operacji działania warunkowego](template-deploy-what-if.md) , aby sprawdzić, czy szablon wprowadza zmiany, których oczekujesz. Co-jeśli również sprawdza poprawność szablonu pod kątem błędów.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

W Cloud Shell Użyj następujących poleceń:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametry

Aby przekazać wartości parametrów, można użyć parametrów wbudowanych lub pliku parametrów.

### <a name="inline-parameters"></a>Parametry wbudowane

Aby przekazać parametry wbudowane, podaj wartości w `parameters` . Na przykład, aby przekazać ciąg i tablicę do szablonu, jest powłoką bash, użyj:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Jeśli używasz interfejsu wiersza polecenia platformy Azure z wierszem poleceń systemu Windows (CMD) lub PowerShell, Przekaż tablicę w formacie: `exampleArray="['value1','value2']"` .

Możesz również pobrać zawartość pliku i podać tę zawartość jako parametr wbudowany.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Pobieranie wartości parametru z pliku jest przydatne, gdy trzeba podać wartości konfiguracyjne. Można na przykład udostępnić [wartości z usługi Cloud-init dla maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/using-cloud-init.md).

arrayContent.jsw formacie:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywać parametry jako wartości wbudowane w skrypcie, można ułatwić korzystanie z pliku JSON, który zawiera wartości parametrów. Plik parametru musi być plikiem lokalnym. Zewnętrzne pliki parametrów nie są obsługiwane w interfejsie wiersza polecenia platformy Azure.

Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów Menedżer zasobów](parameter-files.md).

Aby przekazać lokalny plik parametrów, użyj, `@` Aby określić plik lokalny o nazwie storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Obsługa rozszerzonego formatu JSON

Aby wdrożyć szablon z wielowierszowymi ciągami lub komentarzami przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.3.0 lub starszej, należy użyć `--handle-extended-json-format` przełącznika.  Na przykład:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>Następne kroki

- Aby wrócić do pomyślnego wdrożenia, gdy wystąpi błąd, zobacz [wycofywanie po pomyślnym wdrożeniu](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Azure Resource Manager trybami wdrożenia](deployment-modes.md).
- Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
- Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](common-deployment-errors.md).
- Informacje o wdrażaniu szablonu wymagającego tokenu SAS można znaleźć w temacie [Deploy Private Template with SAS token](secure-template-with-sas-token.md).
