---
title: Uruchamianie elementów Runbook na Azure Automation hybrydowego procesu roboczego elementu Runbook
description: Ten artykuł zawiera informacje na temat uruchamiania elementów Runbook na maszynach w lokalnym centrum danych lub dostawcy chmury z rolą hybrydowego procesu roboczego elementu Runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: c67fff32770446cac3adef8af50c9e5733077bc7
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226292"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook

Elementy Runbook przeznaczone dla hybrydowego procesu roboczego elementu Runbook zwykle zarządzają zasobami na komputerze lokalnym lub z zasobami w środowisku lokalnym, w którym jest wdrażany proces roboczy. Elementy Runbook w Azure Automation zazwyczaj zarządzają zasobami w chmurze platformy Azure. Mimo że są one używane inaczej, elementy Runbook działające w Azure Automation i elementy Runbook, które działają w hybrydowym procesie roboczym elementu Runbook, są identyczne w strukturze.

Podczas tworzenia elementu Runbook do uruchamiania w hybrydowym procesie roboczym elementu Runbook należy edytować i testować element Runbook na komputerze hostującym proces roboczy. Komputer hosta ma wszystkie moduły programu PowerShell i dostęp do sieci wymagany do zarządzania zasobami lokalnymi i uzyskiwania do nich dostępu. Po przetestowaniu elementu Runbook na komputerze hybrydowego procesu roboczego elementu Runbook można przekazać go do środowiska Azure Automation, w którym można go uruchomić w procesie roboczym. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Uprawnienia elementu Runbook dla hybrydowego procesu roboczego elementu Runbook

W miarę uzyskiwania dostępu do zasobów spoza platformy Azure elementy Runbook działające w hybrydowym procesie roboczym elementu Runbook nie mogą używać mechanizmu uwierzytelniania zwykle używanego przez elementy Runbook uwierzytelniane w zasobach platformy Azure. Element Runbook udostępnia własne uwierzytelnianie do zasobów lokalnych lub konfiguruje uwierzytelnianie przy użyciu [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Możesz również określić konto Uruchom jako, aby zapewnić kontekst użytkownika dla wszystkich elementów Runbook.

### <a name="runbook-authentication"></a>Uwierzytelnianie elementu Runbook

Domyślnie elementy Runbook są uruchamiane na komputerze lokalnym. W przypadku systemu Windows są one uruchamiane w kontekście konta systemu lokalnego. W systemie Linux są one uruchamiane w kontekście specjalnego konta użytkownika **nxautomation**. W każdym scenariuszu elementy Runbook muszą zapewnić własne uwierzytelnianie dla zasobów, do których uzyskują dostęp.

W elemencie Runbook można używać zasobów [poświadczeń](automation-credentials.md) i [certyfikatów](automation-certificates.md) z poleceniami cmdlet, które umożliwiają określenie poświadczeń, aby element Runbook mógł być uwierzytelniany w różnych zasobach. Poniższy przykład przedstawia część elementu Runbook, który uruchamia ponownie komputer. Pobiera poświadczenia z zasobu poświadczeń i nazwy komputera z zasobu zmiennej, a następnie używa tych wartości za pomocą polecenia cmdlet **Restart-Computer** .

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Można również użyć działania [InlineScript](automation-powershell-workflow.md#inlinescript) . InlineScript umożliwia uruchamianie bloków kodu na innym komputerze z poświadczeniami określonymi przez [wspólny parametr PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Konto Uruchom jako

Zamiast używać elementu Runbook do udostępniania zasobów lokalnych, można określić konto Uruchom jako dla grupy hybrydowych procesów roboczych elementu Runbook. W tym celu należy zdefiniować [zasób poświadczeń](automation-credentials.md) , który ma dostęp do zasobów lokalnych. Te zasoby obejmują magazyny certyfikatów i wszystkie elementy Runbook są uruchamiane w ramach tych poświadczeń w hybrydowym procesie roboczym elementu Runbook w grupie.

Nazwa użytkownika dla poświadczenia musi mieć jeden z następujących formatów:

* użytkownika
* username@domain
* Nazwa użytkownika (dla kont lokalnych dla komputera lokalnego)

Aby określić konto Uruchom jako dla grupy hybrydowych procesów roboczych elementu Runbook, należy wykonać czynności opisane w poniższej procedurze.

1. Utwórz [zasób poświadczeń](automation-credentials.md) z dostępem do zasobów lokalnych.
2. Otwórz konto usługi Automation w Azure Portal.
3. Wybierz kafelek **grupy hybrydowych procesów roboczych** , a następnie wybierz grupę.
4. Wybierz **wszystkie ustawienia**, a następnie **Ustawienia grupy hybrydowych procesów roboczych**.
5. Zmień wartość parametru **Uruchom jako** **domyślną** na **niestandardowy**.
6. Wybierz poświadczenie, a następnie kliknij przycisk **Zapisz**.

### <a name="managed-identities-for-azure-resources"></a>Zarządzane tożsamości dla zasobów platformy Azure

Hybrydowe procesy robocze elementów Runbook w usłudze Azure Virtual Machines mogą używać tożsamości zarządzanych dla zasobów platformy Azure do uwierzytelniania w zasobach platformy Azure. Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure zamiast kont Uruchom jako zapewnia korzyści, ponieważ nie ma potrzeby:

* Wyeksportuj certyfikat Uruchom jako, a następnie zaimportuj go do hybrydowego procesu roboczego elementu Runbook
* Odnów certyfikat używany przez konto Uruchom jako
* Obsługuj obiekt połączenia Uruchom jako w kodzie elementu Runbook

Wykonaj kolejne kroki, aby użyć zarządzanej tożsamości dla zasobów platformy Azure w hybrydowym procesie roboczym elementu Runbook.

1. Utwórz maszynę wirtualną platformy Azure.
2. Skonfiguruj zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Nadaj MASZYNom wirtualnym dostęp do grupy zasobów w Menedżer zasobów. Aby [uzyskać dostęp do Menedżer zasobów, Użyj tożsamości zarządzanej przypisanej przez system Windows VM](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Zainstaluj hybrydowy proces roboczy elementu Runbook na maszynie wirtualnej. Zobacz [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](automation-windows-hrw-install.md).
5. Zaktualizuj element Runbook, aby użyć polecenia cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) z parametrem *Identity* do uwierzytelniania w zasobach platformy Azure. Ta konfiguracja zmniejsza konieczność użycia konta Uruchom jako i umożliwia zarządzanie kontami skojarzonymi.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` działa dla hybrydowego procesu roboczego elementu Runbook przy użyciu tożsamości przypisanej do systemu i pojedynczej tożsamości przypisanej przez użytkownika. Jeśli używasz wielu tożsamości przypisanych przez użytkownika w hybrydowym procesie roboczym elementu Runbook, element Runbook musi określić parametr *AccountId* dla opcji **Connect-AzAccount** , aby wybrać określoną tożsamość przypisaną przez użytkownika.

### <a name="runas-script"></a>Konto Uruchom jako usługi Automation

W ramach zautomatyzowanego procesu kompilacji na potrzeby wdrażania zasobów na platformie Azure może być konieczne uzyskanie dostępu do systemów lokalnych w celu obsługi zadania lub zestawu kroków w sekwencji wdrożenia. Aby zapewnić uwierzytelnianie na platformie Azure przy użyciu konta Uruchom jako, należy zainstalować certyfikat konta Uruchom jako.

Następujący element Runbook programu PowerShell o nazwie **Export-RunAsCertificateToHybridWorker**eksportuje certyfikat Uruchom jako z konta Azure Automation. Element Runbook pobiera i importuje certyfikat do magazynu certyfikatów na komputerze lokalnym w hybrydowym procesie roboczym elementu Runbook, który jest podłączony do tego samego konta. Po zakończeniu tego kroku element Runbook sprawdzi, czy proces roboczy może pomyślnie uwierzytelnić się na platformie Azure przy użyciu konta Uruchom jako.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>W przypadku elementów Runbook programu PowerShell polecenia **Add-AzAccount** i **Add-AzureRMAccount** są aliasami dla polecenia **Connect-AzAccount**. Podczas wyszukiwania elementów biblioteki, jeśli nie widzisz pozycji **Connect-AzAccount**, możesz użyć polecenie **Add-AzAccount**lub zaktualizować moduły na koncie usługi Automation.

Aby zakończyć przygotowywanie konta Uruchom jako:

1. Zapisz element Runbook **Export-RunAsCertificateToHybridWorker** na komputerze z rozszerzeniem **. ps1** .
2. Zaimportuj ją na konto usługi Automation.
3. Edytuj element Runbook, zmieniając wartość zmiennej *Password* o własnym haśle. 
4. Opublikuj element Runbook.
5. Uruchom element Runbook, który jest przeznaczony dla grupy hybrydowych procesów roboczych elementu Runbook, która uruchamia i uwierzytelnia elementy Runbook przy użyciu konta Uruchom jako. 
6. Sprawdź strumień zadań, aby zobaczyć, że raport podjął próbę zaimportowania certyfikatu do lokalnego magazynu komputerowego i postępuj zgodnie z wieloma wierszami. To zachowanie zależy od liczby kont usługi Automation zdefiniowanych w ramach subskrypcji i stopnia sukcesu uwierzytelniania.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Zachowanie zadania dla hybrydowych procesów roboczych elementów Runbook

Azure Automation obsługuje zadania dla hybrydowych procesów roboczych elementu Runbook nieco inaczej niż zadania uruchamiane w piaskownicach platformy Azure. Jedną z kluczowych różnic polega na tym, że nie ma żadnego limitu czasu trwania zadania dla procesów roboczych elementów Runbook. Elementy Runbook działające w piaskownicach platformy Azure są ograniczone do trzech godzin z powodu [sprawiedliwego udziału](automation-runbook-execution.md#fair-share).

W przypadku długotrwałego elementu Runbook trzeba upewnić się, że jest to odporne na możliwe ponowne uruchomienie, na przykład jeśli komputer obsługujący ponowne uruchomienie procesu roboczego. Jeśli maszyna hosta hybrydowego procesu roboczego elementu Runbook zostanie ponownie uruchomiona, wszystkie uruchomione zadania elementu Runbook są uruchamiane od początku lub z ostatniego punktu kontrolnego dla elementów Runbook przepływu pracy programu PowerShell. Po ponownym uruchomieniu zadania elementu Runbook więcej niż trzy razy zostanie ono zawieszone.

Należy pamiętać, że zadania dla hybrydowych procesów roboczych elementów Runbook działają w ramach lokalnego konta systemowego w systemie Windows lub konta **nxautomation** w systemie Linux. W przypadku systemu Linux należy upewnić się, że konto **nxautomation** ma dostęp do lokalizacji, w której są przechowywane moduły Runbook. W przypadku korzystania z polecenia cmdlet [Install-module](/powershell/module/powershellget/install-module) należy określić **ALLUSERS** dla parametru *SCOPE* , aby upewnić się, że konto **nxautomation** ma dostęp. Aby uzyskać więcej informacji na temat programu PowerShell w systemie Linux, zobacz [znane problemy dotyczące programu PowerShell na platformach innych niż Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Uruchamianie elementu Runbook w hybrydowym procesie roboczym elementu Runbook

[Uruchamianie elementu Runbook w Azure Automation](automation-starting-a-runbook.md) opisuje różne metody uruchamiania elementu Runbook. Uruchomienie elementu Runbook w hybrydowym procesie roboczym elementu Runbook powoduje użycie opcji **Uruchom na** , która umożliwia określenie nazwy grupy hybrydowych procesów roboczych elementu Runbook. Po określeniu grupy jeden z procesów roboczych w tej grupie pobiera i uruchamia element Runbook. Jeśli element Runbook nie określa tej opcji, Azure Automation uruchamia element Runbook w zwykły sposób.

Po uruchomieniu elementu Runbook w Azure Portal zostanie wyświetlona opcja **Uruchom na** , dla której można wybrać **platformę Azure** lub **hybrydowy proces roboczy**. W przypadku wybrania opcji **hybrydowy proces roboczy**można wybrać grupę hybrydowych procesów roboczych elementu Runbook z listy rozwijanej.

Użyj parametru *Uruchom* z poleceniem cmdlet **Start-AzureAutomationRunbook** . Poniższy przykład używa środowiska Windows PowerShell, aby uruchomić element Runbook o nazwie **test-Runbook** w grupie hybrydowych procesów roboczych elementu Runbook o nazwie Moja hybrydowa.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Parametr *Uruchom* został dodany do **menu Start-AzureAutomationRunbook** w wersji od 0.9.1 elementu Microsoft Azure PowerShell. Należy [pobrać najnowszą wersję,](https://azure.microsoft.com/downloads/) jeśli jest zainstalowana wcześniejsza wersja. Tę wersję należy zainstalować tylko na stacji roboczej, w której jest uruchamiany element Runbook z programu PowerShell. Nie trzeba go instalować na komputerze hybrydowego procesu roboczego elementu Runbook, chyba że zamierzasz uruchomić elementy Runbook z tego komputera.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Praca z podpisanymi elementami Runbook w hybrydowym procesie roboczym elementu Runbook systemu Windows

Można skonfigurować hybrydowy proces roboczy elementu Runbook systemu Windows do uruchamiania tylko podpisanych elementów Runbook.

> [!IMPORTANT]
> Po skonfigurowaniu hybrydowego procesu roboczego elementu Runbook do uruchamiania tylko podpisanych elementów Runbook, elementy Runbook, które nie zostały podpisane, nie będą wykonywane w procesie roboczym.

### <a name="create-signing-certificate"></a>Utwórz certyfikat podpisywania

Poniższy przykład tworzy certyfikat z podpisem własnym, który może służyć do podpisywania elementów Runbook. Ten kod tworzy certyfikat i eksportuje go tak, aby hybrydowy proces roboczy elementu Runbook mógł zaimportować go później. Odcisk palca jest również zwracany do późniejszego użycia w odniesieniu do certyfikatu.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importowanie certyfikatu i konfigurowanie procesów roboczych do weryfikacji podpisu

Skopiuj certyfikat utworzony przez użytkownika do każdego hybrydowego procesu roboczego elementu Runbook w grupie. Uruchom następujący skrypt, aby zaimportować certyfikat i skonfigurować procesy robocze do korzystania z walidacji podpisu w elementach Runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Podpisywanie elementów Runbook przy użyciu certyfikatu

Hybrydowe procesy robocze elementu Runbook skonfigurowane do używania tylko podpisanych elementów Runbook muszą podpisywać elementy Runbook, które mają być używane w hybrydowym procesie roboczym elementu Runbook. Aby podpisać te elementy Runbook, użyj następującego przykładowego kodu programu PowerShell.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Po podpisaniu elementu Runbook należy go zaimportować do konta usługi Automation i opublikować przy użyciu bloku podpisu. Aby dowiedzieć się, jak importować elementy Runbook, zobacz [Importowanie elementu Runbook z pliku do Azure Automation](manage-runbooks.md#import-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Praca z podpisanymi elementami Runbook w hybrydowym procesie roboczym elementu Runbook systemu Linux

Aby można było korzystać z podpisanych elementów Runbook, hybrydowy proces roboczy elementu Runbook systemu Linux musi mieć plik wykonywalny [GPG](https://gnupg.org/index.html) na komputerze lokalnym.

> [!IMPORTANT]
> Po skonfigurowaniu hybrydowego procesu roboczego elementu Runbook do uruchamiania tylko podpisanych elementów Runbook, elementy Runbook, które nie zostały podpisane, nie będą wykonywane w procesie roboczym.

### <a name="create-a-gpg-keyring-and-keypair"></a>Tworzenie GPG i pary kluczy

Aby utworzyć GPG pęku kluczy i pary kluczy, użyj konta hybrydowego procesu roboczego elementu Runbook **nxautomation** .

1. Użyj aplikacji sudo, aby zalogować się jako konto **nxautomation** .

    ```bash
    sudo su – nxautomation
    ```

2. Gdy korzystasz z programu **nxautomation**, wygeneruj parę kluczy GPG. GPG przeprowadzi Cię przez kroki. Musisz podać nazwę, adres e-mail, czas wygaśnięcia i hasło. Następnie poczekaj, aż na komputerze jest wystarczająca Entropia dla klucza do wygenerowania.

    ```bash
    sudo gpg --generate-key
    ```

3. Ponieważ katalog GPG został wygenerowany z sudo, należy zmienić jego właściciela na **nxautomation** przy użyciu następującego polecenia.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Udostępnienie dzwonka dla hybrydowego procesu roboczego elementu Runbook

Po utworzeniu dzwonka kluczy Udostępnij go dla hybrydowego procesu roboczego elementu Runbook. Zmodyfikuj `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` pliku ustawień, aby uwzględnić Poniższy przykładowy kod w sekcji pliku **[Worker-Optional]** .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Sprawdź, czy sprawdzanie poprawności podpisu jest włączone

Jeśli walidacja podpisu została wyłączona na komputerze, musisz ją włączyć, uruchamiając następujące polecenie sudo. Zastąp `<LogAnalyticsworkspaceId>` IDENTYFIKATORem obszaru roboczego.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Podpisywanie elementu Runbook

Po skonfigurowaniu weryfikacji podpisu użyj następującego polecenia GPG, aby podpisać element Runbook.

```bash
gpg –-clear-sign <runbook name>
```

Podpisany element Runbook jest nazywany `<runbook name>.asc`.

Możesz teraz przekazać podpisany element Runbook do Azure Automation i wykonać go jak zwykłego elementu Runbook.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o metodach uruchamiania elementu Runbook, zobacz [Uruchamianie elementu Runbook w Azure Automation](automation-starting-a-runbook.md).
* Aby dowiedzieć się, jak używać edytora tekstu do pracy z elementami Runbook programu PowerShell w Azure Automation, zobacz [Edytowanie elementu Runbook w programie Azure Automation](automation-edit-textual-runbook.md).
* Jeśli elementy Runbook nie zakończą się pomyślnie, zapoznaj się z przewodnikiem rozwiązywania problemów z [błędami wykonywania elementu Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
