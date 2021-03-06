---
title: Korzystanie ze skryptów wdrażania szablonów | Microsoft Docs
description: Dowiedz się, jak używać skryptów wdrażania w szablonach Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e1094befcc6b3a6e9d56ba3b603dc45fcb91ba13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88825498"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Samouczek: Tworzenie certyfikatu z podpisem własnym (wersja zapoznawcza) za pomocą skryptów wdrażania

Dowiedz się, jak używać skryptów wdrażania w szablonach zarządzania zasobami platformy Azure (ARM). Skrypty wdrażania mogą służyć do wykonywania czynności niestandardowych, które nie mogą być wykonywane przez szablony ARM. Na przykład utworzenie certyfikatu z podpisem własnym.  W tym samouczku utworzysz szablon służący do wdrażania magazynu kluczy platformy Azure, a następnie `Microsoft.Resources/deploymentScripts` do utworzenia certyfikatu zostanie użyty zasób z tego samego szablonu, a następnie zostanie dodany certyfikat do magazynu kluczy. Aby dowiedzieć się więcej na temat skryptu wdrażania, zobacz [Korzystanie ze skryptów wdrażania w szablonach ARM](./deployment-script-template.md).

> [!IMPORTANT]
> Dwa zasoby skryptu wdrożenia, konto magazynu i wystąpienie kontenera są tworzone w tej samej grupie zasobów na potrzeby wykonywania skryptu i rozwiązywania problemów. Te zasoby są zwykle usuwane przez usługę skryptów, gdy wykonywanie skryptu jest pobierane w stanie terminalu. Opłaty są naliczane za zasoby do momentu usunięcia zasobów. Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania](./deployment-script-template.md#clean-up-deployment-script-resources).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Edytowanie szablonu
> * Wdrażanie szablonu
> * Debuguj uszkodzony skrypt
> * Czyszczenie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* ** [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem narzędzi Menedżer zasobów Tools**. Zobacz [Szybki Start: tworzenie Azure Resource Manager szablonów z Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).

* **Tożsamość zarządzana przypisana przez użytkownika z rolą współautor na poziomie subskrypcji**. Ta tożsamość jest używana do wykonywania skryptów wdrażania. Aby go utworzyć, zobacz [tożsamość zarządzana przypisana przez użytkownika](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Identyfikator tożsamości jest wymagany podczas wdrażania szablonu. Format tożsamości:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Użyj następującego skryptu interfejsu wiersza polecenia, aby uzyskać identyfikator, podając nazwę grupy zasobów i nazwę tożsamości.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Zamiast tworzyć szablon od podstaw, otwórz szablon z obszaru [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure)](https://azure.microsoft.com/resources/templates/). Szablony szybkiego startu platformy Azure to repozytorium szablonów usługi ARM.

Szablon używany w tym przewodniku szybki start ma nazwę [Create a Azure Key Vault i wpis tajny](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Szablon tworzy magazyn kluczy, a następnie dodaje wpis tajny do magazynu kluczy.

1. W obszarze Visual Studio Code wybierz pozycję **plik** > **Otwórz plik**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **plik** > **Zapisz jako,** aby zapisać plik jako **azuredeploy.jsna** komputerze lokalnym.

## <a name="edit-the-template"></a>Edytowanie szablonu

Wprowadź następujące zmiany w szablonie:

### <a name="clean-up-the-template-optional"></a>Wyczyść szablon (opcjonalnie)

Oryginalny szablon dodaje wpis tajny do magazynu kluczy.  Aby uprościć samouczek, usuń następujący zasób:

* **Magazyn/magazyny/wpisy tajne firmy Microsoft.**

Usuń następujące dwa definicje parametrów:

* **secretName**
* **secretValue**

W przypadku wybrania opcji nieusuwania tych definicji należy określić wartości parametrów podczas wdrażania.

### <a name="configure-the-key-vault-access-policies"></a>Konfigurowanie zasad dostępu magazynu kluczy

Skrypt wdrażania dodaje certyfikat do magazynu kluczy. Skonfiguruj zasady dostępu magazynu kluczy w celu przyznania uprawnienia zarządzanej tożsamości:

1. Dodaj parametr w celu uzyskania identyfikatora tożsamości zarządzanej:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Rozszerzenie Menedżer zasobów szablonu Visual Studio Code nie może jeszcze sformatować skryptów wdrażania. Nie należy używać klawiszy [SHIFT] + [ALT] + F do formatowania zasobów deploymentScripts, takich jak poniższy.

1. Dodaj parametr służący do konfigurowania zasad dostępu magazynu kluczy, dzięki czemu zarządzana tożsamość może dodawać certyfikaty do magazynu kluczy.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Zaktualizuj istniejące zasady dostępu magazynu kluczy do:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Istnieją dwie zasady zdefiniowane, jedną dla zalogowanego użytkownika, a druga — dla tożsamości zarządzanej.  Zalogowany użytkownik potrzebuje tylko uprawnienia *listy* do zweryfikowania wdrożenia.  Aby uprościć samouczek, ten sam certyfikat jest przypisany do zarządzanej tożsamości i zalogowanych użytkowników.

### <a name="add-the-deployment-script"></a>Dodawanie skryptu wdrożenia

1. Dodaj trzy parametry, które są używane przez skrypt wdrażania.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Dodaj zasób deploymentScripts:

    > [!NOTE]
    > Ponieważ wbudowane skrypty wdrażania są ujęte w podwójne cudzysłowy, ciągi wewnątrz skryptów wdrażania muszą być ujęte w apostrofy. Znak ucieczki dla programu PowerShell jest **&#92;**.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    `deploymentScripts`Zasób jest zależny od zasobu magazynu kluczy i zasobu przypisania roli.  Ma następujące właściwości:

    * **tożsamość**: skrypt wdrażania używa tożsamości zarządzanej przypisanej przez użytkownika do wykonywania skryptów.
    * **rodzaj**: Określ typ skryptu. Obecnie obsługiwane są tylko skrypty programu PowerShell.
    * **forceUpdateTag**: Określ, czy skrypt wdrożenia ma być wykonywany, nawet jeśli źródło skryptu nie zostało zmienione. Może to być bieżąca sygnatura czasowa lub identyfikator GUID. Aby dowiedzieć się więcej, zobacz [Uruchamianie skryptu więcej niż raz](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: określa wersję modułu Azure PowerShell, która ma zostać użyta. Obecnie skrypt wdrażania obsługuje wersje 2.7.0, 2.8.0 i 3.0.0.
    * **limit czasu**: Określ maksymalny dozwolony czas wykonywania skryptu określony w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartość domyślna to **P1D**.
    * **argumenty**: Określ wartości parametrów. Wartości są rozdzielone spacjami.
    * **scriptContent**: Określ zawartość skryptu. Aby uruchomić zewnętrzny skrypt, zamiast tego należy użyć **primaryScriptURI** . Aby uzyskać więcej informacji, zobacz [Korzystanie z zewnętrznego skryptu](./deployment-script-template.md#use-external-scripts).
        Deklarowanie **$DeploymentScriptOutputs** jest wymagane tylko podczas testowania skryptu na komputerze lokalnym. Deklarowanie zmiennej pozwala na uruchomienie skryptu na maszynie lokalnej i w zasobie deploymentScript bez konieczności wprowadzania zmian. Wartość przypisana do $DeploymentScriptOutputs jest dostępna jako dane wyjściowe we wdrożeniach. Aby uzyskać więcej informacji, zobacz artykuł [Pracuj z wynikami ze skryptów wdrażania programu PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) lub [Pracuj z wynikami ze skryptów wdrażania interfejsu wiersza polecenia](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * **cleanupPreference**: Określ preferencję po usunięciu zasobów skryptu wdrażania.  Wartość domyślna to **zawsze**, co oznacza, że zasoby skryptu wdrożenia są usuwane pomimo stanu terminalu (powodzenie, zakończone niepowodzeniem, anulowane). W tym samouczku zostanie użyta wartość **onSuccess** , aby uzyskać szansę na wyświetlenie wyników wykonywania skryptu.
    * **retentionInterval**: Określ interwał, dla którego usługa zachowuje zasoby skryptu po osiągnięciu stanu terminalu. Zasoby zostaną usunięte po upływie tego czasu trwania. Czas trwania jest oparty na wzorcu ISO 8601. W tym samouczku jest używany P1D, co oznacza jeden dzień.  Ta właściwość jest używana, gdy **cleanupPreference** jest ustawiony na **onwygaśnięcia**. Ta właściwość nie jest obecnie włączona.

    Skrypt wdrożenia przyjmuje trzy parametry: nazwę magazynu kluczy, nazwę certyfikatu i nazwę podmiotu.  Tworzy certyfikat, a następnie dodaje certyfikat do magazynu kluczy.

    **$DeploymentScriptOutputs** jest używany do przechowywania wartości wyjściowej.  Aby dowiedzieć się więcej, zobacz artykuł [Pracuj z wynikami ze skryptów wdrażania programu PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) lub [Pracuj z wynikami ze skryptów wdrażania interfejsu wiersza polecenia](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Ukończony szablon można znaleźć [tutaj](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Aby wyświetlić proces debugowania, należy umieścić błąd w kodzie, dodając następujący wiersz do skryptu wdrożenia:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Poprawne polecenie to **Write-Output** zamiast **Write-Output1**.

1. Wybierz pozycję **plik** > **Zapisz** , aby zapisać plik.

## <a name="deploy-the-template"></a>Wdrażanie szablonu

1. Zaloguj się do [Azure Cloud Shell](https://shell.azure.com)

1. Wybierz preferowane środowisko, wybierając opcję **PowerShell** lub **bash** (dla interfejsu wiersza polecenia) w lewym górnym rogu.  Po przełączeniu wymagane jest ponowne uruchomienie powłoki.

    ![Azure Portal Cloud Shell przekazywania pliku](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Wybierz pozycję **Przekaż/pobierz pliki**, a następnie wybierz pozycję **Przekaż**. Zobacz poprzedni zrzut ekranu.  Wybierz plik, który został zapisany w poprzedniej sekcji. Po przekazaniu pliku można użyć polecenia **ls** i **Cat** polecenia, aby sprawdzić, czy plik został pomyślnie przekazany.

1. Uruchom następujący skrypt programu PowerShell, aby wdrożyć szablon.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
    $identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

    $adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
    $resourceGroupName = "${projectName}rg"
    $keyVaultName = "${projectName}kv"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

    Write-Host "Press [ENTER] to continue ..."
    ```

    Usługa skryptu wdrażania musi utworzyć dodatkowe zasoby skryptu wdrażania na potrzeby wykonywania skryptu. Przygotowanie i proces oczyszczania może potrwać do jednej minuty, a nie tylko rzeczywisty czas wykonywania skryptu.

    Wdrożenie nie powiodło się ze względu na nieprawidłowe polecenie **Write-Output1** w skrypcie. Zostanie wyświetlony komunikat o błędzie:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
    ```

    Wynik wykonania skryptu wdrożenia jest przechowywany w zasobach skryptu wdrożenia w celu rozwiązywania problemów.

## <a name="debug-the-failed-script"></a>Debuguj uszkodzony skrypt

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów. Jest to nazwa projektu z dołączoną **RG** . W grupie zasobów będą widoczne dwa dodatkowe zasoby. Te zasoby są określane jako *Zasoby skryptu wdrażania*.

    ![Zasoby skryptu wdrażania Menedżer zasobów szablonu](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Oba pliki mają sufiks **azscripts** . Jednym z nich jest konto magazynu, a drugie to wystąpienie kontenera.

    Wybierz pozycję **Pokaż ukryte typy** , aby wyświetlić listę zasobów deploymentScripts.

1. Wybierz konto magazynu z sufiksem **azscripts** .
1. Wybierz kafelek **udziały plików** . Zobaczysz folder **azscripts** .  Folder zawiera pliki wykonywania skryptu wdrożenia.
1. Wybierz pozycję **azscripts**. Zobaczysz dwa foldery **azscriptinput** i **azscriptoutput**.  Folder wejściowy zawiera systemowy plik skryptu programu PowerShell i pliki skryptów wdrażania użytkownika. Folder wyjściowy zawiera **executionresult.js** i plik wyjściowy skryptu. Komunikat o błędzie można zobaczyć w **executionresult.js**. Plik wyjściowy nie istnieje, ponieważ wykonywanie nie powiodło się.

Usuń wiersz **Write-Output1 i ponownie** Wdróż szablon.

Po pomyślnym uruchomieniu drugiego wdrożenia zasoby skryptu wdrożenia są usuwane przez usługę skryptów, ponieważ właściwość **cleanupPreference** ma wartość **onSuccess**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia skryptu wdrażania w szablonach ARM. Aby dowiedzieć się, jak wdrażać zasoby platformy Azure na podstawie warunków, zobacz:

> [!div class="nextstepaction"]
> [Używanie warunków](./template-tutorial-use-conditions.md)
