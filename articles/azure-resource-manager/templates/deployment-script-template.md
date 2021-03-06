---
title: Używanie skryptów wdrażania w szablonach | Microsoft Docs
description: Użyj skryptów wdrażania w szablonach Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: jgao
ms.openlocfilehash: a04377289b78c23a83fc696ebebb9b5808e904c9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321648"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Używanie skryptów wdrażania w szablonach (wersja zapoznawcza)

Dowiedz się, jak używać skryptów wdrażania w szablonach zasobów platformy Azure. Po wywołaniu nowego typu zasobu `Microsoft.Resources/deploymentScripts` Użytkownicy mogą wykonywać skrypty wdrażania w ramach wdrożeń szablonów i przeglądać wyniki wykonania. Skrypty te mogą służyć do wykonywania czynności niestandardowych, takich jak:

- Dodawanie użytkowników do katalogu
- Wykonaj operacje płaszczyzny danych, na przykład skopiuj obiekty blob lub bazę danych inicjatora
- Wyszukiwanie i sprawdzanie poprawności klucza licencji
- Tworzenie certyfikatu z podpisem własnym
- Tworzenie obiektu w usłudze Azure AD
- Wyszukiwanie bloków adresów IP z niestandardowego systemu

Zalety skryptu wdrażania:

- Łatwe do kodu, używania i debugowania. Skrypty wdrażania można opracowywać w ulubionych środowiskach deweloperskich. Skrypty mogą być osadzone w szablonach lub w zewnętrznych plikach skryptów.
- Możesz określić język skryptu i platformę. Obecnie obsługiwane są Azure PowerShell i skrypty wdrażania interfejsu wiersza polecenia platformy Azure w środowisku systemu Linux.
- Zezwalaj na Określanie tożsamości, które są używane do wykonywania skryptów. Obecnie obsługiwana jest tylko [tożsamość zarządzana przypisana przez użytkownika platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- Zezwalaj na przekazywanie do skryptu argumentów wiersza polecenia.
- Można określić dane wyjściowe skryptu i przekazać je z powrotem do wdrożenia.

Zasób skryptu wdrożenia jest dostępny tylko w regionach, w których usługa Azure Container instance jest dostępna.  Zobacz [dostępność zasobów dla Azure Container Instances w regionach świadczenia usługi Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Konto magazynu i wystąpienie kontenera są niezbędne do wykonania skryptu i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. w przeciwnym razie konto magazynu oraz wystąpienie kontenera są tworzone automatycznie przez usługę skryptów. Dwa automatycznie tworzone zasoby są zwykle usuwane przez usługę skryptów, gdy wykonywanie skryptu wdrożenia zostanie oddzielone w stanie terminalu. Opłaty są naliczane za zasoby do momentu usunięcia zasobów. Aby dowiedzieć się więcej, zobacz [Zasoby skryptu wdrożenia oczyszczanie](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Wymagania wstępne

- **Tożsamość zarządzana przypisana przez użytkownika z rolą współautor do docelowej grupy zasobów**. Ta tożsamość jest używana do wykonywania skryptów wdrażania. Aby wykonać operacje poza grupą zasobów, należy udzielić dodatkowych uprawnień. Na przykład Przypisz tożsamość do poziomu subskrypcji, jeśli chcesz utworzyć nową grupę zasobów.

  > [!NOTE]
  > Usługa skryptów tworzy konto magazynu (chyba że użytkownik określi istniejące konto magazynu) i wystąpienie kontenera w tle.  Tożsamość zarządzana przypisana przez użytkownika z rolą współautor na poziomie subskrypcji jest wymagana, jeśli subskrypcja nie zarejestrowała dostawców zasobów konta usługi Azure Storage (Microsoft. Storage) i Azure Container Instance (Microsoft. ContainerInstance).

  Aby utworzyć tożsamość, zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)lub przy [użyciu interfejsu wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)lub przy [użyciu Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Identyfikator tożsamości jest wymagany podczas wdrażania szablonu. Format tożsamości:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Użyj poniższego skryptu interfejsu wiersza polecenia lub programu PowerShell, aby uzyskać identyfikator, podając nazwę grupy zasobów i nazwę tożsamości.

  # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** lub **interfejs wiersza polecenia platformy Azure**. Zapoznaj się z listą [obsługiwanych wersji Azure PowerShell](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Zapoznaj się z listą [obsługiwanych wersji interfejsu wiersza polecenia platformy Azure](https://mcr.microsoft.com/v2/azure-cli/tags/list).

    >[!IMPORTANT]
    > Skrypt wdrażania używa dostępnych obrazów interfejsu wiersza polecenia firmy Microsoft Container Registry (MCR). Zaświadczanie obrazu interfejsu wiersza polecenia dla skryptu wdrożenia trwa około miesiąca. Nie używaj wersji interfejsu wiersza polecenia, które zostały wydane w ciągu 30 dni. Aby znaleźć daty wydania dla obrazów, zobacz informacje o [wersji interfejsu wiersza polecenia platformy Azure](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true). Jeśli używana jest nieobsługiwana wersja, komunikat o błędzie zawiera listę obsługiwanych wersji.

    Te wersje nie są potrzebne do wdrażania szablonów. Jednak te wersje są zbędne do lokalnego testowania skryptów wdrażania. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Można użyć wstępnie skonfigurowanego obrazu platformy Docker.  Zobacz [Konfigurowanie środowiska deweloperskiego](#configure-development-environment).

## <a name="sample-templates"></a>Przykładowe szablony

Poniższy kod JSON jest przykładem.  Najnowszy schemat szablonu można znaleźć [tutaj](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Przykład służy do celów demonstracyjnych.  **scriptContent** i **primaryScriptUri** nie mogą współistnieć w szablonie.

Szczegóły wartości właściwości:

- **Tożsamość** : usługa skryptu wdrażania używa zarządzanej tożsamości przypisanej przez użytkownika do wykonywania skryptów. Obecnie obsługiwana jest tylko tożsamość zarządzana przypisana przez użytkownika.
- **rodzaj** : Określ typ skryptu. Obecnie obsługiwane są Azure PowerShell i skrypty interfejsu wiersza polecenia platformy Azure. Wartości to **AzurePowerShell** i **AzureCLI**.
- **forceUpdateTag** : zmiana tej wartości między wdrożeniami szablonów Wymusza ponowne wykonanie skryptu wdrażania. Użyj funkcji newGuid () lub utcNow (), która musi być ustawiona jako wartość domyślna parametru. Aby dowiedzieć się więcej, zobacz [Uruchamianie skryptu więcej niż raz](#run-script-more-than-once).
- **containerSettings** : Określ ustawienia umożliwiające dostosowanie wystąpienia kontenera platformy Azure.  **containerGroupName** służy do określania nazwy grupy kontenerów.  Jeśli nie zostanie określony, nazwa grupy jest generowana automatycznie.
- **storageAccountSettings** : Określ ustawienia do użycia istniejącego konta magazynu. Jeśli nie zostanie określony, konto magazynu zostanie utworzone automatycznie. Zobacz [Korzystanie z istniejącego konta magazynu](#use-existing-storage-account).
- **azPowerShellVersion** / **azCliVersion** : Określ wersję modułu, która ma zostać użyta. Aby zapoznać się z listą obsługiwanych wersji programu PowerShell i interfejsu wiersza polecenia, zobacz [wymagania wstępne](#prerequisites).
- **argumenty** : Określ wartości parametrów. Wartości są rozdzielone spacjami.

    Skrypty wdrażania dzielą argumenty na tablicę ciągów przez wywoływanie wywołania systemowego [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) . Jest to konieczne, ponieważ argumenty są przekazane jako [Właściwość polecenia](/rest/api/container-instances/containergroups/createorupdate#containerexec) do wystąpienia kontenera platformy Azure, a właściwość polecenia jest tablicą ciągów.

    Jeśli argumenty zawierają znaki ucieczki, użyj [JsonEscaper](https://www.jsonescaper.com/) do podwójnego ucieczki znaków. Wklej oryginalny ciąg ucieczki do narzędzia, a następnie wybierz pozycję **ucieczki**.  Narzędzie wyprowadza podwójnie zmieniony ciąg. Na przykład w poprzednim przykładowym szablonie argument ma wartość **-name \\ "Jan dole \\ "**.  Ciąg ucieczki to **-name \\ \\ \\ "Jan dole \\ \\ \\ "**.

    Aby przekazać parametr szablonu ARM typu Object jako argument, przekonwertuj obiekt na ciąg za pomocą funkcji [String ()](./template-functions-string.md#string) , a następnie użyj funkcji [replace ()](./template-functions-string.md#replace) w celu zastąpienia dowolnego elementu **\\ "** INTO **\\ \\ \\ "**. Przykład:

    ```json
    replace(string(parameters('tables')), '\"', '\\\"')
    ```

    Aby wyświetlić przykładowy szablon, wybierz [tutaj](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- **environmentVariables** : Określ zmienne środowiskowe, które mają zostać przekazane do skryptu. Aby uzyskać więcej informacji, zobacz temat [programowanie skryptów wdrażania](#develop-deployment-scripts).
- **scriptContent** : Określ zawartość skryptu. Aby uruchomić zewnętrzny skrypt, należy `primaryScriptUri` zamiast tego użyć. Aby zapoznać się z przykładami, zobacz [używanie skryptu wbudowanego](#use-inline-scripts) i [używanie skryptu zewnętrznego](#use-external-scripts).
- **primaryScriptUri** : Określ publicznie dostępny adres URL dla podstawowego skryptu wdrażania z obsługiwanymi rozszerzeniami plików.
- **supportingScriptUris** : Określ tablicę dostępnych publicznie adresów URL do obsługi plików, które są wywoływane w `ScriptContent` lub `PrimaryScriptUri` .
- **limit czasu** : Określ maksymalny dozwolony czas wykonywania skryptu określony w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartość domyślna to **P1D**.
- **cleanupPreference**. Określ preferencję oczyszczania zasobów wdrożenia, gdy wykonywanie skryptu jest odbierane w stanie terminalu. Ustawieniem domyślnym jest **zawsze** , co oznacza usunięcie zasobów pomimo stanu terminalu (zakończone powodzeniem, zakończone niepowodzeniem, anulowane). Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).
- **retentionInterval** : Określ interwał, dla którego usługa zachowuje zasoby skryptu wdrożenia po osiągnięciu przez wykonanie skryptu wdrożenia stanu terminalu. Zasoby skryptu wdrażania zostaną usunięte po upływie tego czasu trwania. Czas trwania zależy od [wzorca ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Interwał przechowywania jest z przedziału od 1 do 26 godzin (PT26H). Ta właściwość jest używana, gdy cleanupPreference jest ustawiony na *onwygaśnięcia*. Właściwość *onwygaśnięcia* nie jest obecnie włączona. Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Dodatkowe przykłady

- [Przykład 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): Tworzenie magazynu kluczy i używanie skryptu wdrażania do przypisywania certyfikatu do magazynu kluczy.
- [Przykład 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): Utwórz grupę zasobów na poziomie subskrypcji, Utwórz magazyn kluczy w grupie zasobów, a następnie Użyj skryptu wdrażania, aby przypisać certyfikat do magazynu kluczy.
- [Przykład 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): Tworzenie tożsamości zarządzanej przypisanej przez użytkownika, przypisywanie roli współautor do tożsamości na poziomie grupy zasobów, tworzenie magazynu kluczy, a następnie używanie skryptu wdrażania do przypisywania certyfikatu do magazynu kluczy.

> [!NOTE]
> Zaleca się utworzenie tożsamości przypisanej do użytkownika i przyznanie uprawnień z wyprzedzeniem. Po utworzeniu tożsamości i udzieleniu uprawnień w tym samym szablonie, w którym uruchamiane są skrypty wdrażania, może zostać wyświetlony błąd związany z logowaniem i uprawnieniami. Trwa to trochę czasu, zanim uprawnienia staną się skuteczne.

## <a name="use-inline-scripts"></a>Użyj skryptów wbudowanych

Następujący szablon ma zdefiniowany jeden zasób z `Microsoft.Resources/deploymentScripts` typem. Wyróżniona część jest wbudowanym skryptem.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Ponieważ wbudowane skrypty wdrażania są ujęte w podwójne cudzysłowy, ciągi wewnątrz skryptów wdrażania muszą być wyprowadzane przy użyciu **&#92;** lub ujęte w apostrofy. Można również rozważyć użycie podstawienia ciągów, tak jak pokazano w poprzednim przykładzie JSON.

Skrypt przyjmuje jeden parametr i wyprowadza wartość parametru. **DeploymentScriptOutputs** jest używany do przechowywania danych wyjściowych.  W sekcji dane wyjściowe wiersz **wartości** pokazuje, jak uzyskać dostęp do przechowywanych wartości. `Write-Output` jest używany do celów debugowania. Aby dowiedzieć się, jak uzyskać dostęp do pliku wyjściowego, zobacz [monitorowanie i rozwiązywanie problemów ze skryptami wdrażania](#monitor-and-troubleshoot-deployment-scripts).  Aby zapoznać się z opisami właściwości, zobacz [przykładowe szablony](#sample-templates).

Aby uruchomić skrypt, wybierz opcję **Wypróbuj** , aby otworzyć Cloud Shell, a następnie wklej poniższy kod do okienka powłoki.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Dane wyjściowe wyglądają następująco:

![Skrypt wdrożenia szablonu Menedżer zasobów Witaj świecie Output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Korzystanie ze skryptów zewnętrznych

Oprócz skryptów wbudowanych można również używać zewnętrznych plików skryptów. Obsługiwane są tylko podstawowe skrypty programu PowerShell z rozszerzeniem pliku **ps1** . W przypadku skryptów interfejsu wiersza polecenia skrypty podstawowe mogą mieć dowolne rozszerzenia (lub bez rozszerzenia), o ile skrypty są prawidłowymi skryptami bash. Aby użyć zewnętrznych plików skryptu, Zamień `scriptContent` na `primaryScriptUri` . Przykład:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Aby zobaczyć przykład, wybierz [tutaj](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Pliki skryptów zewnętrznych muszą być dostępne.  Aby zabezpieczyć pliki skryptów, które są przechowywane na kontach usługi Azure Storage, zobacz [wdrażanie prywatnego szablonu usługi ARM z tokenem SAS](./secure-template-with-sas-token.md).

Użytkownik jest odpowiedzialny za zapewnienie integralności skryptów, do których odwołuje się skrypt wdrożenia, to **PrimaryScriptUri** lub **SupportingScriptUris**.  Odwołuj się tylko do skryptów zaufanych.

## <a name="use-supporting-scripts"></a>Używanie skryptów pomocniczych

Skomplikowane logiki można rozdzielić na jeden lub więcej plików skryptów pomocniczych. `supportingScriptUris`Właściwość pozwala udostępnić tablicę identyfikatorów URI w plikach skryptów pomocniczych w razie potrzeby:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Pliki skryptów pomocniczych mogą być wywoływane zarówno ze skryptów wbudowanych, jak i podstawowych plików skryptów. Pliki skryptów pomocniczych nie mają żadnych ograniczeń dotyczących rozszerzenia pliku.

Pliki pomocnicze są kopiowane do azscripts/azscriptinput w czasie wykonywania. Użyj ścieżki względnej, aby odwoływać się do plików pomocniczych ze skryptów wbudowanych i podstawowych plików skryptów.

## <a name="work-with-outputs-from-powershell-script"></a>Pracuj z wynikami z poziomu skryptu programu PowerShell

Poniższy szablon pokazuje, jak przekazać wartości między dwoma zasobami deploymentScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

W pierwszym zasobie należy zdefiniować zmienną o nazwie **$DeploymentScriptOutputs** i użyć jej do przechowywania wartości wyjściowych. Aby uzyskać dostęp do wartości wyjściowej z innego zasobu w ramach szablonu, użyj:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Pracuj z wynikami z poziomu skryptu interfejsu wiersza polecenia

Inaczej niż w przypadku skryptu wdrażania programu PowerShell obsługa interfejsu wiersza polecenia/bash nie uwidacznia wspólnej zmiennej do przechowywania danych wyjściowych skryptu, a zamiast tego występuje zmienna środowiskowa o nazwie **AZ_SCRIPTS_OUTPUT_PATH** , która przechowuje lokalizację, w której znajduje się plik danych wyjściowych skryptu. Jeśli skrypt wdrożenia jest uruchamiany z szablonu Menedżer zasobów, ta zmienna środowiskowa jest ustawiana automatycznie przez powłokę bash.

Dane wyjściowe skryptu wdrożenia muszą być zapisane w lokalizacji AZ_SCRIPTS_OUTPUT_PATH, a wyjście musi być prawidłowym obiektem ciągu JSON. Zawartość pliku musi być zapisana jako para klucz-wartość. Na przykład tablica ciągów jest przechowywana jako {"result": ["foo", "bar"]}.  Przechowywanie tylko wyników tablicowych, na przykład ["foo", "bar"], jest nieprawidłowe.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) jest używany w poprzednim przykładzie. Zawiera obrazy kontenerów. Zobacz [Konfigurowanie środowiska deweloperskiego](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Użyj istniejącego konta magazynu

Konto magazynu i wystąpienie kontenera są niezbędne do wykonania skryptu i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. w przeciwnym razie konto magazynu oraz wystąpienie kontenera są tworzone automatycznie przez usługę skryptów. Wymagania dotyczące korzystania z istniejącego konta magazynu:

- Obsługiwane rodzaje kont magazynu:

    | Jednostka SKU             | Obsługiwany rodzaj     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Magazyn, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Magazyn, StorageV2 |
    | Standard_RAGRS  | Magazyn, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Te kombinacje obsługują udział plików.  Aby uzyskać więcej informacji, zobacz [Tworzenie udziału plików platformy Azure](../../storage/files/storage-how-to-create-file-share.md) i [typów kont magazynu](../../storage/common/storage-account-overview.md).
- Reguły zapory konta magazynu nie są jeszcze obsługiwane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../../storage/common/storage-network-security.md).
- Tożsamość zarządzana przypisana przez użytkownika skryptu wdrożenia musi mieć uprawnienia do zarządzania kontem magazynu, które obejmuje odczyt, tworzenie i usuwanie udziałów plików.

Aby określić istniejące konto magazynu, Dodaj następujący kod JSON do elementu właściwości `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName** : Określ nazwę konta magazynu.
- **storageAccountKey "** : Określ jeden z kluczy konta magazynu. Możesz użyć funkcji, [`listKeys()`](./template-functions-resource.md#listkeys) Aby pobrać klucz. Przykład:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Zobacz [przykładowe szablony](#sample-templates) , aby zapoznać się z kompletnym `Microsoft.Resources/deploymentScripts` przykładem definicji.

W przypadku korzystania z istniejącego konta magazynu usługa skryptów tworzy udział plików z unikatową nazwą. Zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources) , aby usługa skryptów czyści udział plików.

## <a name="develop-deployment-scripts"></a>Programowanie skryptów wdrażania

### <a name="handle-non-terminating-errors"></a>Obsługuj błędy niepowodujące zakończenia

Można kontrolować, jak program PowerShell reaguje na błędy niepowodujące zakończenia przy użyciu zmiennej **$ErrorActionPreference** w skrypcie wdrożenia. Jeśli zmienna nie jest ustawiona w skrypcie wdrażania, usługa **skryptów będzie używać wartości domyślnej**.

Usługa skryptów ustawia stan aprowizacji zasobów na **Niepowodzenie** , gdy skrypt napotka błąd pomimo ustawienia $ErrorActionPreference.

### <a name="pass-secured-strings-to-deployment-script"></a>Przekaż zabezpieczone ciągi do skryptu wdrażania

Ustawienie zmiennych środowiskowych (zmiennych środowiskowych) w wystąpieniach kontenera pozwala na zapewnienie dynamicznej konfiguracji aplikacji lub skryptu uruchamianego przez kontener. Skrypt wdrażania obsługuje niezabezpieczone i zabezpieczone zmienne środowiskowe w taki sam sposób, jak wystąpienie kontenera platformy Azure. Aby uzyskać więcej informacji, zobacz [Ustawianie zmiennych środowiskowych w wystąpieniach kontenerów](../../container-instances/container-instances-environment-variables.md#secure-values).

Maksymalny dozwolony rozmiar zmiennych środowiskowych to 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Monitorowanie skryptów wdrażania i rozwiązywanie problemów

Usługa skryptów tworzy [konto magazynu](../../storage/common/storage-account-overview.md) (o ile nie zostanie określone istniejące konto magazynu) i [wystąpienie kontenera](../../container-instances/container-instances-overview.md) na potrzeby wykonywania skryptu. Jeśli te zasoby są tworzone automatycznie przez usługę skryptów, oba zasoby mają sufiks **azscripts** w nazwach zasobów.

![Nazwy zasobów skryptu wdrożenia szablonu Menedżer zasobów](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Skrypt użytkownika, wyniki wykonywania oraz plik stdout są przechowywane w udziałach plików konta magazynu. Istnieje folder o nazwie **azscripts**. W folderze istnieją dwa więcej folderów dla danych wejściowych i plików wyjściowych: **azscriptinput** i **azscriptoutput**.

Folder wyjściowy zawiera **executionresult.js** i plik wyjściowy skryptu. Komunikat o błędzie wykonywania skryptu można zobaczyć w **executionresult.js**. Plik wyjściowy jest tworzony tylko po pomyślnym wykonaniu skryptu. Folder wejściowy zawiera systemowy plik skryptu programu PowerShell i pliki skryptów wdrażania użytkownika. Można zastąpić plik skryptu wdrożenia użytkownika zmienionym i ponownie uruchomić skrypt wdrażania z wystąpienia kontenera platformy Azure.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po wdrożeniu zasobu skryptu wdrożenia zasób zostanie wyświetlony w obszarze Grupa zasobów w Azure Portal. Poniższy zrzut ekranu przedstawia stronę przegląd zasobu skryptu wdrożenia:

![Omówienie portalu skryptów wdrażania Menedżer zasobów Template](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Na stronie Przegląd są wyświetlane istotne informacje dotyczące zasobu, takie jak **stan aprowizacji** , **konto magazynu** , **wystąpienie kontenera** i **dzienniki**.

Z menu po lewej stronie można wyświetlić zawartość skryptu wdrożenia, argumenty przekazane do skryptu i dane wyjściowe.  Możesz również wyeksportować szablon skryptu wdrażania, w tym skrypt wdrożenia.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Za pomocą Azure PowerShell można zarządzać skryptami wdrażania w zakresie subskrypcji lub grupy zasobów:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): Pobiera lub wyświetla skrypty wdrażania.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): Pobiera dziennik wykonywania skryptu wdrożenia.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): usuwa skrypt wdrożenia i powiązane z nim zasoby.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): zapisuje dziennik wykonania skryptu wdrożenia na dysku.

Dane wyjściowe Get-AzDeploymentScript są podobne do:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można zarządzać skryptami wdrażania w zakresie subskrypcji lub grupy zasobów:

- [AZ Deployment-scripts Delete](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-delete): usuwa skrypt wdrożenia.
- [AZ Deployment-Script list](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-list): wyświetla wszystkie skrypty wdrażania.
- [AZ Deployment-scripts show](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-show): pobieranie skryptu wdrożenia.
- [AZ Deployment-scripts show-log](/cli/azure/deployment-scripts?view=azure-cli-lates&preserve-view=truet#az-deployment-scripts-show-log): wyświetla dzienniki skryptu wdrażania.

Dane wyjściowe polecenia list są podobne do:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Informacje o wdrożeniu zasobów skryptu wdrażania można uzyskać na poziomie grupy zasobów i na poziomie subskrypcji przy użyciu interfejsu API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

W poniższym przykładzie zastosowano [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Dane wyjściowe są podobne do następujących:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

Następujący interfejs API REST zwraca dziennik:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Działa tylko przed usunięciem zasobów skryptu wdrażania.

Aby wyświetlić zasób deploymentScripts w portalu, wybierz pozycję **Pokaż ukryte typy** :

![Skrypt wdrażania szablonu Menedżer zasobów, wyświetlanie ukrytych typów, Portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Czyszczenie zasobów skryptu wdrożenia

Konto magazynu i wystąpienie kontenera są niezbędne do wykonania skryptu i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. w przeciwnym razie konto magazynu oraz wystąpienie kontenera są tworzone automatycznie przez usługę skryptów. Dwa automatycznie utworzone zasoby są usuwane przez usługę skryptów, gdy wykonywanie skryptu wdrożenia zostanie oddzielone w stanie terminalu. Opłaty są naliczane za zasoby do momentu usunięcia zasobów. Aby uzyskać informacje o cenach, zobacz [Container Instances Cennik](https://azure.microsoft.com/pricing/details/container-instances/) i [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Cykl życia tych zasobów jest kontrolowany przez następujące właściwości w szablonie:

- **cleanupPreference** : Wyczyść preferencję, gdy wykonywanie skryptu zostanie odszukane w stanie terminalu. Obsługiwane są następujące wartości:

  - **Zawsze** : Usuń automatycznie utworzone zasoby, gdy wykonywanie skryptu zostanie rozpoczęte w stanie terminalu. Jeśli używane jest istniejące konto magazynu, usługa skryptów usuwa udział plików utworzony na koncie magazynu. Ponieważ zasób deploymentScripts może nadal występować po oczyszczeniu zasobów, usługa skryptów zachowuje wyniki wykonywania skryptu, na przykład stdout, Output, Value Return itp., zanim zasoby zostaną usunięte.
  - **OnSuccess** : usuwanie automatycznie utworzonych zasobów tylko wtedy, gdy wykonywanie skryptu zakończyło się pomyślnie. Jeśli używane jest istniejące konto magazynu, usługa skryptów usuwa udział plików tylko po pomyślnym wykonaniu skryptu. Nadal możesz uzyskać dostęp do zasobów, aby znaleźć informacje debugowania.
  - **Onwygaśnięcia** : usuwanie automatycznie utworzonych zasobów tylko wtedy, gdy ustawienie **retentionInterval** wygasło. Jeśli używane jest istniejące konto magazynu, usługa skryptów usuwa udział plików, ale zachowa konto magazynu.

- **retentionInterval** : Określ interwał czasu, przez który zasób skryptu zostanie zachowany, a następnie zostanie usunięty.

> [!NOTE]
> Nie zaleca się używania konta magazynu i wystąpienia kontenera, które są generowane przez usługę skryptów do innych celów. Te dwa zasoby mogą zostać usunięte w zależności od cyklu życia skryptu.

## <a name="run-script-more-than-once"></a>Uruchom skrypt więcej niż raz

Wykonanie skryptu wdrożenia jest operacją idempotentne. Jeśli żadna z właściwości zasobów deploymentScripts (łącznie z skryptem wbudowanym) nie zostanie zmieniona, skrypt nie zostanie wykonany po ponownym wdrożeniu szablonu. Usługa skryptu wdrażania porównuje nazwy zasobów w szablonie z istniejącymi zasobami w tej samej grupie zasobów. Istnieją dwie opcje, jeśli chcesz wykonać ten sam skrypt wdrażania wielokrotnie:

- Zmień nazwę zasobu deploymentScripts. Na przykład użyj funkcji szablonu [UtcNow](./template-functions-date.md#utcnow) jako nazwy zasobu lub jako części nazwy zasobu. Zmiana nazwy zasobu powoduje utworzenie nowego zasobu deploymentScripts. Jest to dobre dla zachowania historii wykonywania skryptu.

    > [!NOTE]
    > Funkcja utcNow może być używana tylko w wartości domyślnej dla parametru.

- Określ inną wartość we `forceUpdateTag` Właściwości szablonu.  Na przykład użyj utcNow jako wartości.

> [!NOTE]
> Napisz skrypty wdrażania, które są idempotentne. Pozwala to zagwarantować, że jeśli zostaną uruchomione ponownie przypadkowo, nie spowoduje to zmian w systemie. Jeśli na przykład skrypt wdrożenia jest używany do tworzenia zasobów platformy Azure, przed utworzeniem zasobu należy sprawdzić, czy zasób nie istnieje, więc skrypt powiedzie się lub nie utworzysz ponownie zasobu.

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

Wstępnie skonfigurowanego obrazu kontenera można użyć jako środowiska programistycznego skryptu wdrożenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego pod kątem skryptów wdrażania w szablonach](./deployment-script-template-configure-dev.md).

Po pomyślnym przetestowaniu skryptu można go użyć jako skryptu wdrożenia w szablonach.

## <a name="deployment-script-error-codes"></a>Kody błędów skryptów wdrażania

| Kod błędu | Opis |
|------------|-------------|
| DeploymentScriptInvalidOperation | Definicja zasobu skryptu wdrażania w szablonie zawiera nieprawidłowe nazwy właściwości. |
| DeploymentScriptResourceConflict | Nie można usunąć zasobu skryptu wdrożenia, który jest w stanie innym niż Terminal, a wykonywanie nie przekroczyło 1 godziny. Lub nie można uruchomić tego samego skryptu wdrożenia z tym samym identyfikatorem zasobu (ta sama subskrypcja, nazwa grupy zasobów i nazwa zasobu), ale inna zawartość treści skryptu w tym samym czasie. |
| DeploymentScriptOperationFailed | Nie można wewnętrznie wykonać operacji skryptu wdrażania. Skontaktuj się z pomocą techniczną firmy Microsoft. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | Nie określono klucza dostępu dla istniejącego konta magazynu.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Grupa kontenerów utworzona przez usługę skryptu wdrożenia została zmodyfikowana zewnętrznie i dodano nieprawidłowe kontenery. |
| DeploymentScriptContainerGroupInNonterminalState | Co najmniej dwa zasoby skryptu wdrożenia używają tej samej nazwy wystąpienia kontenera platformy Azure w tej samej grupie zasobów, a jeden z nich nie zakończył jeszcze jego wykonywania. |
| DeploymentScriptStorageAccountInvalidKind | Istniejące konto magazynu typu BlobBlobStorage lub BlobStorage nie obsługuje udziałów plików i nie można go używać. |
| DeploymentScriptStorageAccountInvalidKindAndSku | Istniejące konto magazynu nie obsługuje udziałów plików. Aby uzyskać listę obsługiwanych rodzajów kont magazynu, zobacz [Korzystanie z istniejącego konta magazynu](#use-existing-storage-account). |
| DeploymentScriptStorageAccountNotFound | Konto magazynu nie istnieje lub zostało usunięte przez proces zewnętrzny lub narzędzie. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Określone konto magazynu ma punkt końcowy usługi. Konto magazynu z punktem końcowym usługi nie jest obsługiwane. |
| DeploymentScriptStorageAccountInvalidAccessKey | Określono nieprawidłowy klucz dostępu dla istniejącego konta magazynu. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Nieprawidłowy format klucza konta magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | Czas wykonania skryptu wdrożenia przekroczył wartość limitu czasu określoną w definicji zasobu skryptu wdrożenia. |
| DeploymentScriptInvalidOutputs | Dane wyjściowe skryptu wdrożenia nie są prawidłowym obiektem JSON. |
| DeploymentScriptContainerInstancesServiceLoginFailure | Tożsamość zarządzana przypisana przez użytkownika nie mogła zalogować się po 10 próbach z 1-minutowym interwałem. |
| DeploymentScriptContainerGroupNotFound | Grupa kontenerów utworzona przez usługę skryptu wdrażania została usunięta przez zewnętrzne narzędzie lub proces. |
| DeploymentScriptDownloadFailure | Nie można pobrać skryptu pomocniczego. Zobacz [używanie skryptu pomocniczego](#use-supporting-scripts).|
| DeploymentScriptError | Skrypt użytkownika zgłosił błąd. |
| DeploymentScriptBootstrapScriptExecutionFailed | Skrypt ładowania początkowego zgłosił błąd. Skrypt Bootstrap to skrypt systemowy, który organizuje wykonywanie skryptu wdrożenia. |
| DeploymentScriptExecutionFailed | Nieznany błąd podczas wykonywania skryptu wdrożenia. |
| DeploymentScriptContainerInstancesServiceUnavailable | Podczas tworzenia wystąpienia kontenera platformy Azure (ACI) usługa ACI zgłosiła błąd niedostępności usługi. |
| DeploymentScriptContainerGroupInNonterminalState | Podczas tworzenia wystąpienia kontenera platformy Azure (ACI) inny skrypt wdrożenia używa tej samej nazwy ACI w tym samym zakresie (tej samej subskrypcji, nazwie grupy zasobów i nazwy zasobu). |
| DeploymentScriptContainerGroupNameInvalid | Określona nazwa wystąpienia kontenera platformy Azure (ACI) nie spełnia wymagań ACI. Zobacz [Rozwiązywanie typowych problemów w Azure Container Instances](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania ze skryptów wdrażania. Aby zapoznać się z samouczkiem dotyczącym skryptu wdrożenia:

> [!div class="nextstepaction"]
> [Samouczek: używanie skryptów wdrażania w szablonach Azure Resource Manager](./template-tutorial-deployment-script.md)
