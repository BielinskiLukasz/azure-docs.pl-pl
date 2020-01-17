---
title: Używanie skryptów wdrażania w szablonach | Microsoft Docs
description: Użyj skryptów wdrażania w szablonach Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jgao
ms.openlocfilehash: 5475f49acfdcd74a792a9a0f2f16cf1ea3272232
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121900"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Używanie skryptów wdrażania w szablonach (wersja zapoznawcza)

Dowiedz się, jak używać skryptów wdrażania w szablonach zasobów platformy Azure. Przy użyciu nowego typu zasobu o nazwie `Microsoft.Resources/deploymentScripts`użytkownicy mogą wykonywać skrypty wdrażania w ramach wdrożeń szablonów i przeglądać wyniki wykonania. Skrypty te mogą służyć do wykonywania czynności niestandardowych, takich jak:

- Dodawanie użytkowników do katalogu
- Tworzenie rejestracji aplikacji
- Wykonaj operacje płaszczyzny danych, na przykład skopiuj obiekty blob lub bazę danych inicjatora
- Wyszukiwanie i sprawdzanie poprawności klucza licencji
- Tworzenie certyfikatu z podpisem własnym
- Tworzenie obiektu w usłudze Azure AD
- Wyszukiwanie bloków adresów IP z niestandardowego systemu

Zalety skryptu wdrażania:

- Łatwe do kodu, używania i debugowania. Skrypty wdrażania można opracowywać w ulubionych środowiskach deweloperskich. Skrypty mogą być osadzone w szablonach lub w zewnętrznych plikach skryptów.
- Możesz określić język skryptu i platformę. Obecnie obsługiwane są tylko Azure PowerShell skrypty wdrażania w środowisku systemu Linux.
- Zezwalaj na Określanie tożsamości, które są używane do wykonywania skryptów. Obecnie obsługiwana jest tylko [tożsamość zarządzana przypisana przez użytkownika platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) .
- Zezwalaj na przekazywanie do skryptu argumentów wiersza polecenia.
- Można określić dane wyjściowe skryptu i przekazać je z powrotem do wdrożenia.

> [!NOTE]
> Skrypt wdrażania jest obecnie w wersji zapoznawczej. Aby go użyć, musisz [utworzyć konto w wersji zapoznawczej](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Dwa zasoby skryptu wdrożenia, konto magazynu i wystąpienie kontenera są tworzone w tej samej grupie zasobów na potrzeby wykonywania skryptu i rozwiązywania problemów. Te zasoby są zwykle usuwane przez usługę skryptów, gdy wykonywanie skryptu wdrożenia jest w stanie terminalu. Opłaty są naliczane za zasoby do momentu usunięcia zasobów. Aby dowiedzieć się więcej, zobacz [Zasoby skryptu wdrożenia oczyszczanie](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Wymagania wstępne

- **Tożsamość zarządzana przypisana przez użytkownika z rolą współautor na poziomie subskrypcji**. Ta tożsamość jest używana do wykonywania skryptów wdrażania. Aby go utworzyć, zobacz [tożsamość zarządzana przypisana przez użytkownika](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). Identyfikator tożsamości jest wymagany podczas wdrażania szablonu. Format tożsamości:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Użyj poniższego skryptu programu PowerShell, aby uzyskać identyfikator, podając nazwę grupy zasobów i nazwę tożsamości.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

- **Azure PowerShell w wersji 2.7.0, 2.8.0 lub 3.0.0**. Te wersje nie są potrzebne do wdrażania szablonów. Jednak te wersje są zbędne do lokalnego testowania skryptów wdrażania. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Można użyć wstępnie skonfigurowanego obrazu platformy Docker.  Zobacz [Konfigurowanie środowiska deweloperskiego](#configure-development-environment).

## <a name="resource-schema"></a>Schemat zasobów

Poniższy kod JSON jest przykładem.  Najnowszy schemat szablonu można znaleźć [tutaj](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "myDeploymentScript",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "2.8",
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Przykład służy do celów demonstracyjnych.  **scriptContent** i **primaryScriptUris** nie mogą współistnieć w szablonie.

Szczegóły wartości właściwości:

- **Tożsamość**: usługa skryptu wdrażania używa zarządzanej tożsamości przypisanej przez użytkownika do wykonywania skryptów. Obecnie obsługiwana jest tylko tożsamość zarządzana przypisana przez użytkownika.
- **rodzaj**: Określ typ skryptu. Obecnie obsługiwane są tylko skrypty Azure PowerShell. Wartość to **AzurePowerShell**.
- **forceUpdateTag**: zmiana tej wartości między wdrożeniami szablonów Wymusza ponowne wykonanie skryptu wdrażania. Użyj funkcji newGuid () lub utcNow (), która musi być ustawiona jako wartość domyślna parametru. Aby dowiedzieć się więcej, zobacz [Uruchamianie skryptu więcej niż raz](#run-script-more-than-once).
- **azPowerShellVersion**: Określ wersję modułu Azure PowerShell, która ma zostać użyta. Skrypt wdrażania obsługuje obecnie wersje 2.7.0, 2.8.0 i 3.0.0.
- **argumenty**: Określ wartości parametrów. Wartości są rozdzielone spacjami.
- **scriptContent**: Określ zawartość skryptu. Aby uruchomić zewnętrzny skrypt, należy zamiast tego użyć `primaryScriptUri`. Aby zapoznać się z przykładami, zobacz [używanie skryptu wbudowanego](#use-inline-scripts) i [używanie skryptu zewnętrznego](#use-external-scripts).
- **primaryScriptUri**: Określ publicznie dostępny adres URL dla podstawowego skryptu programu PowerShell z obsługiwanym rozszerzeniem pliku programu PowerShell.
- **supportingScriptUris**: Określ tablicę dostępnych publicznie adresów URL do obsługi plików programu PowerShell, które będą wywoływane w `ScriptContent` lub `PrimaryScriptUri`.
- **limit czasu**: Określ maksymalny dozwolony czas wykonywania skryptu określony w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartość domyślna to **P1D**.
- **cleanupPreference**. Określ preferencję oczyszczania zasobów wdrożenia, gdy wykonywanie skryptu jest odbierane w stanie terminalu. Ustawieniem domyślnym jest **zawsze**, co oznacza usunięcie zasobów pomimo stanu terminalu (zakończone powodzeniem, zakończone niepowodzeniem, anulowane). Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).
- **retentionInterval**: Określ interwał, dla którego usługa zachowuje zasoby skryptu wdrożenia po osiągnięciu przez wykonanie skryptu wdrożenia stanu terminalu. Zasoby skryptu wdrażania zostaną usunięte po upływie tego czasu trwania. Czas trwania zależy od [wzorca ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartość domyślna to **P1D**, co oznacza siedem dni. Ta właściwość jest używana, gdy cleanupPreference jest ustawiony na *onwygaśnięcia*. Właściwość *onwygaśnięcia* nie jest obecnie włączona. Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).

## <a name="use-inline-scripts"></a>Użyj skryptów wbudowanych

Następujący szablon ma zdefiniowany jeden zasób z typem `Microsoft.Resources/deploymentScripts`.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Ponieważ wbudowane skrypty wdrażania są ujęte w podwójne cudzysłowy, ciągi wewnątrz skryptów wdrażania muszą być ujęte w apostrofy. Znak ucieczki dla programu PowerShell **&#92;** to. Można również rozważyć użycie podstawienia ciągów, tak jak pokazano w poprzednim przykładzie JSON. Zobacz wartość domyślną parametru name.

Skrypt przyjmuje jeden parametr i wyprowadza wartość parametru. **DeploymentScriptOutputs** jest używany do przechowywania danych wyjściowych.  W sekcji dane wyjściowe wiersz **wartości** pokazuje, jak uzyskać dostęp do przechowywanych wartości. `Write-Output` jest używany do celów debugowania. Aby dowiedzieć się, jak uzyskać dostęp do pliku wyjściowego, zobacz [debugowanie skryptów wdrażania](#debug-deployment-scripts).  Aby zapoznać się z opisami właściwości, zobacz [schemat zasobów](#resource-schema).

Aby uruchomić skrypt, wybierz opcję **Wypróbuj** , aby otworzyć usługę Cloud Shell, a następnie wklej następujący kod do okienka powłoki.

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

Oprócz skryptów wbudowanych można również używać zewnętrznych plików skryptów. Obecnie obsługiwane są tylko skrypty programu PowerShell z rozszerzeniem pliku **ps1** . Aby użyć zewnętrznych plików skryptów, Zastąp `scriptContent` z `primaryScriptUri`. Przykład:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Aby zobaczyć przykład, wybierz [tutaj](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Pliki skryptów zewnętrznych muszą być dostępne.  Aby zabezpieczyć pliki skryptów, które są przechowywane na kontach usługi Azure Storage, zobacz [Samouczek: bezpieczne artefakty w ramach wdrożeń szablonów Azure Resource Manager](./template-tutorial-secure-artifacts.md).

## <a name="use-supporting-scripts"></a>Używanie skryptów pomocniczych

Skomplikowane logiki można rozdzielić na jeden lub więcej plików skryptów pomocniczych. Właściwość `supportingScriptURI` pozwala udostępnić tablicę identyfikatorów URI w plikach skryptów pomocniczych w razie potrzeby:

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

Pliki skryptów pomocniczych mogą być wywoływane zarówno ze skryptów wbudowanych, jak i podstawowych plików skryptów.

Pliki pomocnicze są kopiowane do azscripts/azscriptinput w czasie wykonywania. Użyj ścieżki względnej, aby odwoływać się do plików pomocniczych ze skryptów wbudowanych i podstawowych plików skryptów.

## <a name="work-with-outputs-from-deployment-scripts"></a>Pracuj z wynikami ze skryptów wdrażania

Poniższy szablon pokazuje, jak przekazać wartości między dwoma zasobami deploymentScripts:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

W pierwszym zasobie należy zdefiniować zmienną o nazwie **$DeploymentScriptOutputs**i użyć jej do przechowywania wartości wyjściowych. Aby uzyskać dostęp do wartości wyjściowej z innego zasobu w ramach szablonu, użyj:

```json
reference('<ResourceName>').output.text
```

## <a name="debug-deployment-scripts"></a>Debuguj skrypty wdrażania

Usługa skryptów tworzy [konto magazynu](../../storage/common/storage-account-overview.md) i [wystąpienie kontenera](../../container-instances/container-instances-overview.md) na potrzeby wykonywania skryptu. Oba zasoby mają sufiks **azscripts** w nazwach zasobów.

![Nazwy zasobów skryptu wdrożenia szablonu Menedżer zasobów](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Skrypt użytkownika, wyniki wykonywania oraz plik stdout są przechowywane w udziałach plików konta magazynu. Istnieje folder o nazwie **azscripts**. W folderze istnieją dwa więcej folderów dla danych wejściowych i plików wyjściowych: **azscriptinput** i **azscriptoutput**.

Folder wyjściowy zawiera plik **ExecutionResult. JSON** i skrypt. Komunikat o błędzie wykonywania skryptu można zobaczyć w pliku **ExecutionResult. JSON**. Plik wyjściowy jest tworzony tylko po pomyślnym wykonaniu skryptu. Folder wejściowy zawiera systemowy plik skryptu programu PowerShell i pliki skryptów wdrażania użytkownika. Można zastąpić plik skryptu wdrożenia użytkownika zmienionym i ponownie uruchomić skrypt wdrażania z wystąpienia kontenera platformy Azure.

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

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

Dane wyjściowe pokazują stan wdrożenia i identyfikatory zasobów skryptu wdrażania.

Następujący interfejs API REST zwraca dziennik:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Działa tylko przed usunięciem zasobów skryptu wdrażania.

Aby wyświetlić zasób deploymentScripts w portalu, wybierz pozycję **Pokaż ukryte typy**:

![Skrypt wdrażania szablonu Menedżer zasobów, wyświetlanie ukrytych typów, Portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Czyszczenie zasobów skryptu wdrożenia

Skrypt wdrażania tworzy konto magazynu i wystąpienie kontenera, które są używane do wykonywania skryptów wdrażania i przechowywania informacji debugowania. Te dwa zasoby są tworzone w tej samej grupie zasobów co zasoby aprowizacji i zostaną usunięte przez usługę skryptów, gdy skrypt wygaśnie. Możesz kontrolować cykl życia tych zasobów.  Dopóki nie zostaną usunięte, opłaty są naliczane za oba zasoby. Aby uzyskać informacje o cenach, zobacz [Container Instances Cennik](https://azure.microsoft.com/pricing/details/container-instances/) i [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Cykl życia tych zasobów jest kontrolowany przez następujące właściwości w szablonie:

- **cleanupPreference**: Wyczyść preferencję, gdy wykonywanie skryptu zostanie odszukane w stanie terminalu.  Obsługiwane są następujące wartości:

  - **Zawsze**: Usuń zasoby, gdy wykonywanie skryptu zostanie rozpoczęte w stanie terminalu. Ponieważ zasób deploymentScripts może nadal występować po oczyszczeniu zasobów, skrypt systemu skopiuje wyniki wykonywania skryptu, na przykład stdout, Output, Value Return itp. do bazy danych przed usunięciem zasobów.
  - **OnSuccess**: usuwanie zasobów tylko wtedy, gdy wykonywanie skryptu zakończyło się pomyślnie. Nadal możesz uzyskać dostęp do zasobów, aby znaleźć informacje debugowania.
  - **Onwygaśnięcia**: Usuń zasoby tylko wtedy, gdy ustawienie **retentionInterval** wygasło. Ta właściwość jest obecnie wyłączona.

- **retentionInterval**: Określ interwał czasu, przez który zasób skryptu zostanie zachowany, a następnie zostanie usunięty.

> [!NOTE]
> Nie zaleca się używania zasobów skryptu wdrażania do innych celów.

## <a name="run-script-more-than-once"></a>Uruchom skrypt więcej niż raz

Wykonanie skryptu wdrożenia jest operacją idempotentne. Jeśli żadna z właściwości zasobów deploymentScripts (łącznie z skryptem wbudowanym) nie zostanie zmieniona, skrypt nie zostanie wykonany po ponownym wdrożeniu szablonu. Usługa skryptu wdrażania porównuje nazwy zasobów w szablonie z istniejącymi zasobami w tej samej grupie zasobów. Istnieją dwie opcje, jeśli chcesz wykonać ten sam skrypt wdrażania wielokrotnie:

- Zmień nazwę zasobu deploymentScripts. Na przykład użyj funkcji szablonu [UtcNow](./template-functions-string.md#utcnow) jako nazwy zasobu lub jako części nazwy zasobu. Zmiana nazwy zasobu powoduje utworzenie nowego zasobu deploymentScripts. Jest to dobre dla zachowania historii wykonywania skryptu.

    > [!NOTE]
    > Funkcja utcNow może być używana tylko w wartości domyślnej dla parametru.

- Określ inną wartość we właściwości szablonu `forceUpdateTag`.  Na przykład użyj utcNow jako wartości.

> [!NOTE]
> Napisz skrypty wdrażania, które są idempotentne. Pozwala to zagwarantować, że jeśli zostaną uruchomione ponownie przypadkowo, nie spowoduje to zmian w systemie. Jeśli na przykład skrypt wdrożenia jest używany do tworzenia zasobów platformy Azure, przed utworzeniem zasobu należy sprawdzić, czy zasób nie istnieje, więc skrypt powiedzie się lub nie utworzysz ponownie zasobu.

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

Obecnie skrypt wdrażania obsługuje Azure PowerShell w wersji 2.7.0, 2.8.0 i 3.0.0.  Jeśli masz komputer z systemem Windows, możesz zainstalować jedną z obsługiwanych wersji Azure PowerShell i rozpocząć tworzenie i testowanie skryptów wdrażania.  Jeśli nie masz komputera z systemem Windows lub nie masz zainstalowanej żadnej z tych wersji Azure PowerShell, możesz użyć wstępnie skonfigurowanego obrazu kontenera Docker. Poniższa procedura przedstawia sposób konfigurowania obrazu platformy Docker w systemie Windows. W przypadku systemów Linux i Mac można znaleźć informacje z Internetu.

1. Zainstaluj program [Docker Desktop](https://www.docker.com/products/docker-desktop) na komputerze deweloperskim.
1. Otwórz pulpit Docker.
1. Wybierz ikonę platformy Docker na pasku zadań, a następnie wybierz pozycję **Ustawienia**.
1. Wybierz pozycję **dyski udostępnione**, wybierz dysk lokalny, który ma być dostępny dla kontenerów, a następnie wybierz pozycję **Zastosuj** .

    ![Dysk platformy Docker wdrażania szablonu Menedżer zasobów](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Wprowadź swoje poświadczenia systemu Windows w wierszu polecenia.
1. Otwórz okno terminalu, w wierszu polecenia lub w programie Windows PowerShell (nie używaj programu PowerShell ISE).
1. Pobierz obraz kontenera skryptu wdrożenia na komputer lokalny:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    W przykładzie zastosowano wersję 2.7.0.

1. Uruchom lokalnie obraz platformy Docker.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Zastąp **&lt;literę sterownika hosta >** i **&lt;nazwę katalogu hosta >** z istniejącym folderem na dysku udostępnionym.  Mapuje folder do folderu **/Data** w kontenerze. Aby uzyskać przykłady mapowania D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-oznacza to** utrzymywanie obrazu kontenera.

1. Po wyświetleniu monitu wybierz pozycję **Udostępnij** .
1. Uruchom skrypt programu PowerShell, jak pokazano na poniższym zrzucie ekranu (w którym znajduje się plik HelloWorld. ps1 w folderze d:\docker).

    ![Skrypt wdrażania szablonu Menedżer zasobów — polecenie Docker](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Po pomyślnym przetestowaniu skryptu programu PowerShell można go użyć jako skryptu wdrażania.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania ze skryptów wdrażania. Aby zapoznać się z samouczkiem dotyczącym skryptu wdrożenia:

> [!div class="nextstepaction"]
> [Samouczek: używanie skryptów wdrażania w szablonach Azure Resource Manager](./template-tutorial-deployment-script.md)