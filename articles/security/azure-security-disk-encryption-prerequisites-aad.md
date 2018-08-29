---
title: Usługa Azure Disk Encryption przy użyciu usługi Azure AD App wstępnie wymagane składniki (poprzedniej wersji) | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wymagania wstępne dotyczące korzystania z systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 973cbdc1-12bc-4e0e-90cd-f19b045417a2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: mstewart
ms.openlocfilehash: 62ae0bcfb30e39b3b2cfcf94b3877cce9cadf9d1
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128007"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Usługa Azure Disk Encryption wymagania wstępne dotyczące (poprzedniej wersji)

**Nowa wersja usługi Azure Disk Encryption eliminuje wymaganie zapewniające parametrem aplikacji usługi Azure AD, aby włączyć szyfrowanie dysku maszyny Wirtualnej. Za pomocą nowej wersji już nie są wymagane poświadczenia usługi Azure AD podczas wykonywania kroku Włączanie szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje, aby włączyć szyfrowanie dysku maszyny Wirtualnej przy użyciu nowej wersji, zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md). Maszyny wirtualne, które już zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, parametry są nadal obsługiwane i powinno być kontynuowane przy użyciu składni usługi AAD.**

 W tym artykule, wymagań wstępnych szyfrowania dysków Azure, opisano elementy, które muszą być spełnione, zanim użyjesz usługi Azure Disk Encryption. Wraz z ogólne wymagania wstępne, usługa Azure Disk Encryption jest zintegrowana z usługą [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) i aplikację usługi Azure AD używa do zapewnienia uwierzytelniania, aby można było zarządzać kluczami szyfrowania w magazynie kluczy. Możesz również użyć [programu Azure PowerShell](/powershell/azure/overview) lub [wiersza polecenia platformy Azure](/cli/azure/) na konfigurowanie usługi Key Vault i aplikacji usługi Azure AD.

Przed włączeniem usługi Azure Disk Encryption na maszynach wirtualnych IaaS platformy Azure w przypadku obsługiwanych scenariuszy, które zostały omówione w [Omówienie szyfrowania dysków Azure](azure-security-disk-encryption-overview.md) artykuł, pamiętaj zostały spełnione wymagania wstępne w miejscu. 

> [!NOTE]
> Zastosowanie niektórych zaleceń zamieszczonych może zwiększyć danych, sieci lub użycia zasobów obliczeniowych, wynikiem dodatkowych kosztów licencji lub subskrypcji. Musi mieć prawidłową aktywną subskrypcją platformy Azure do tworzenia zasobów na platformie Azure w obsługiwanych regionach.


## <a name="bkmk_OSs"></a> Obsługiwane systemy operacyjne
Usługa Azure Disk Encryption jest obsługiwana w następujących systemach operacyjnych:

- Wersje systemu Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016.
    - Dla systemu Windows Server 2008 R2 musisz mieć zainstalowany przed włączeniem szyfrowania na platformie Azure program .NET Framework 4.5. Zainstaluj go Windows Update z opcjonalną aktualizację programu Microsoft .NET Framework 4.5.2 systemów Windows Server 2008 R2 x64 64 ([KB2901983](https://support.microsoft.com/kb/2901983)).    
- Wersje klienta Windows: Windows 10 i klienta systemu Windows 8.
- Usługa Azure Disk Encryption jest tylko obsługiwana w określonych w galerii platformy Azure są dystrybucje systemu Linux serwera i wersje. Aby uzyskać listę aktualnie obsługiwanych wersji, zobacz [często zadawane pytania dotyczące usługi Azure dysku szyfrowanie](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
- Usługa Azure Disk Encryption wymaga, że maszyny wirtualne i usługi key vault, na których znajdują się w tym samym regionie platformy Azure i subskrypcji. Konfigurowanie zasobów w oddzielnych regionach powoduje awarię w włączenie funkcji usługi Azure Disk Encryption.

## <a name="bkmk_LinuxPrereq"></a> Dodatkowe wymagania wstępne dotyczące maszyn wirtualnych Iaas z systemem Linux 

- Usługa Azure Disk Encryption dla systemu Linux wymaga 7 GB pamięci RAM na maszynę Wirtualną, aby włączyć szyfrowanie dysku systemu operacyjnego na [obsługiwane obrazy](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Po zakończeniu procesu szyfrowania dysku systemu operacyjnego maszyny Wirtualnej można skonfigurować do uruchamiania przy użyciu mniejszej ilości pamięci.
- Przed włączeniem szyfrowania, dysków danych do zaszyfrowania muszą właściwie się na liście/etc/fstab. Użyj nazwy urządzenia trwałego blok dla tego wpisu jako nazwy w formacie "/ dev/sdX" nie można polegać ma zostać skojarzony z tym samym dysku między ponownymi uruchomieniami, szczególnie w przypadku, po zastosowaniu szyfrowania urządzenia. Aby uzyskać więcej szczegółowych informacji dotyczących tego zachowania, zobacz: [zmiany nazwy urządzenia Rozwiązywanie problemów z maszyny Wirtualnej systemu Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Upewnij się, że poprawnie skonfigurowano ustawienia/etc/fstab potrzeby instalowania. Aby skonfigurować te ustawienia, polecenie instalacji lub ponowne uruchomienie maszyny Wirtualnej i wyzwolić ponowne zainstalowanie w ten sposób. Sprawdź dane wyjściowe polecenia lsblk, aby sprawdzić, czy żądany dysk jest wciąż zainstalowany, po zakończeniu tej operacji. 
    - Jeśli plik/etc/fstab nie zainstalować dysk prawidłowo przed włączeniem szyfrowania, usługa Azure Disk Encryption nie będzie mogła poprawnie go zainstalować.
    - Proces szyfrowania dysków Azure zostanie przesunięty informacji dotyczących instalacji poza/etc/fstab i w jej własnym pliku konfiguracji w ramach procesu szyfrowania. Zostać zignorowany, aby zobaczyć, że kończy zapis brakuje/etc/fstab po szyfrowaniem dysków danych.
    -  Po ponownym uruchomieniu potrwa czas procesu szyfrowania dysków Azure na instalowanie nowo zaszyfrowanych dysków. Natychmiast będą dostępne po ponownym uruchomieniu. Proces wymaga czasu do uruchomienia, odblokowywania i następnie zainstalować zaszyfrowanych dysków przed ich jest dostępna na potrzeby dostępu do innych procesów. Ten proces może potrwać ponad minutę po ponownym uruchomieniu, w zależności od charakterystyki systemu.

Przykład polecenia, które mogą służyć do zamontowania dysków z danymi oraz tworzenie niezbędne/etc/fstab wpisów można znaleźć w [linii 197-205 tego pliku skryptu](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L197-L205). 


## <a name="bkmk_GPO"></a> Sieci i zasad grupy

**Aby włączyć usługi Azure Disk Encryption funkcji maszyn wirtualnych IaaS musi spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:**
  - Aby uzyskać tokenu, połączyć się z magazynem kluczy, maszyn wirtualnych IaaS musi być w stanie połączyć się z punktem końcowym usługi Azure Active Directory, \[login.microsoftonline.com\].
  - Można zapisać klucze szyfrowania do magazynu kluczy, maszyn wirtualnych IaaS musi być możliwe nawiązanie połączenia z punktu końcowego magazynu kluczy.
  - Maszyn wirtualnych IaaS musi być możliwe nawiązanie połączenia z punktu końcowego usługi Azure storage, który hostuje repozytorium rozszerzenie platformy Azure i konto magazynu platformy Azure, w którym przechowywane są pliki wirtualnego dysku twardego.
  -  Zasady zabezpieczeń ogranicza dostęp do Internetu z maszyn wirtualnych platformy Azure, przypadku rozwiązać poprzedni identyfikator URI i skonfigurowania określonych Reguła zezwalająca na łączności wychodzącej do adresów IP. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault za zaporą](../key-vault/key-vault-access-behind-firewall.md).    


**Zasady grupy:**
 - Rozwiązanie Azure Disk Encryption dla maszyn wirtualnych IaaS Windows korzysta z zewnętrznego ochrony klucza funkcji BitLocker. Dla maszyn wirtualnych przyłączonych do domeny, nie Wypchnij żadnych zasad grupy, które wymuszają modułu TPM funkcje ochrony kluczy. Aby uzyskać informacje o zasadach grupy "Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM", zobacz [dokumentacja zasad grupy funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup).

-  Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny za pomocą zasad grupy niestandardowe musi zawierać następujące ustawienie: [Konfigurowanie użytkownika magazynu informacji odzyskiwania funkcji bitlocker -> Zezwalaj na 256-bitowego klucza odzyskiwania](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Usługa Azure Disk Encryption zakończy się niepowodzeniem, jeśli ustawienia zasad grupy niestandardowe do używania funkcji Bitlocker są niezgodne. Na komputerach, które nie mają poprawne ustawienie, należy zastosować nowe zasady, wymusić nowe zasady w celu aktualizacji (/ Force gpupdate.exe) i ponowne uruchomienie, może być wymagane.  


## <a name="bkmk_PSH"></a> Program Azure PowerShell
[Program Azure PowerShell](/powershell/azure/overview) udostępnia zestaw poleceń cmdlet, który używa [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modelu do zarządzania zasobami platformy Azure. Używasz go w przeglądarce za pośrednictwem [usługi Azure Cloud Shell](../cloud-shell/overview.md), lub można go zainstalować na komputerze lokalnym wykonując poniższe instrukcje z niej korzystać w dowolnej sesji programu PowerShell. Jeśli masz już zainstalowany lokalnie, upewnij się, że używasz najnowszej wersji programu Azure PowerShell SDK w wersji do konfigurowania usługi Azure Disk Encryption. Pobierz najnowszą wersję [wersji programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Instalowanie programu Azure PowerShell do użytku na komputerze lokalnym (opcjonalnie): 
1. Postępuj zgodnie z instrukcjami w linkach w systemie operacyjnym, następnie Kontynuuj mimo że pozostałe kroki.      
    - [Instalowanie i konfigurowanie programu PowerShell platformy Azure dla Windows](/powershell/azure/install-azurerm-ps). 
        - Zainstaluj moduł PowerShellGet, programu Azure PowerShell i Załaduj moduł AzureRM. 
    - [Instalowanie i konfigurowanie programu Azure Powershell w systemach macOS i Linux](/powershell/azure/install-azurermps-maclinux).
        -  Zainstaluj program PowerShell Core, programu Azure PowerShell dla platformy .NET Core i ładowanie modułu AzureRM.Netcore.
2. Zainstaluj [modułu programu PowerShell usługi Azure Active Directory](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Sprawdź zainstalowane wersje modułów.
      ```powershell
      Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Zaloguj się do platformy Azure za pomocą [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia cmdlet.
     
     ```powershell
     Connect-AzureRmAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzureRmSubscription and 
     specify it with Set-AzureRmContext.  #>
     Get-AzureRmSubscription
     Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Łączenie z usługą Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. Przegląd [wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps) i [AzureAD](/powershell/module/azuread), jeśli to konieczne.

## <a name="bkmk_CLI"></a> Wiersza polecenia platformy Azure

[Interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) jest narzędziem wiersza polecenia do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia służy do elastyczne wykonywanie zapytań o dane, obsługi długotrwałych operacji jak nieblokujące procesy i ułatwienia tworzenia skryptów. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](/cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell.

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) do użytku na komputerze lokalnym (opcjonalnie):

2. Sprawdź zainstalowaną wersję.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Zaloguj się do platformy Azure za pomocą [az login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Przegląd [Rozpoczynanie pracy z usługą Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) w razie potrzeby. 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Wymagań wstępnych przepływ pracy dla usługi Key Vault i aplikację usługi Azure AD

Jeśli już znasz z usługi Key Vault i Azure AD wymagania wstępne dotyczące usługi Azure Disk Encryption, możesz użyć [skrypt programu PowerShell wymagania wstępne dotyczące usługi Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Aby uzyskać więcej informacji na temat używania skryptu wymagań wstępnych, zobacz [szyfrowania VM Quickstart](quick-encrypt-vm-powershell.md) i [dodatku szyfrowania dysków Azure](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Tworzenie magazynu kluczy. 
2. Konfigurowanie aplikacji usługi Azure AD i jednostkę usługi.
3. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD.
4. Zaawansowane zasady dostępu magazynu kluczy zestawu.
 
## <a name="bkmk_KeyVault"></a> Tworzenie magazynu kluczy 
Usługa Azure Disk Encryption jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzanie kluczami szyfrowania dysku i wpisami tajnymi w ramach subskrypcji usługi key vault. Można utworzyć magazyn kluczy lub użyć istniejącego dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji dotyczących magazynów kluczy, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](../key-vault/key-vault-get-started.md) i [zabezpieczanie własnego magazynu kluczy](../key-vault/key-vault-secure-your-key-vault.md). Szablon usługi Resource Manager, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure umożliwia tworzenie magazynu kluczy. 


>[!WARNING]
>Upewnij się, że klucze tajne szyfrowania nie przekracza granic regionalnych, usługa Azure Disk Encryption musi znajdować się w tym samym regionie usługi Key Vault i maszyn wirtualnych. Tworzenie i używanie usługi Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 


### <a name="bkmk_KVPSH"></a> Tworzenie magazynu kluczy przy użyciu programu PowerShell

Można utworzyć magazynu kluczy przy użyciu programu Azure PowerShell [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault) polecenia cmdlet. Aby uzyskać dodatkowe polecenia cmdlet usługi Key Vault, zobacz [AzureRM.KeyVault](/powershell/module/azurerm.keyvault/). 

1. Jeśli to konieczne, [nawiązać połączenie z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Utwórz nową grupę zasobów, jeśli to konieczne, za pomocą [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup).  Aby wyświetlić lokalizacje centrów danych, użyj [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocationn). 
     
     ```azurepowershell-interactive
     # Get-AzureRmLocation 
     New-AzureRmResourceGroup –Name 'MySecureRG' –Location 'East US'
     ```

3. Tworzenie nowego magazynu kluczy przy użyciu [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault)
    
      ```azurepowershell-interactive
     New-AzureRmKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -Location 'East US'
     ```

4. Uwaga **nazwa magazynu**, **Nazwa grupy zasobów**, **identyfikator zasobu**, **identyfikator URI magazynu**i **obiekt o identyfikatorze** które są zwracane do późniejszego użycia podczas szyfrowania dysków. 


### <a name="bkmk_KVCLI"></a> Tworzenie magazynu kluczy przy użyciu wiersza polecenia platformy Azure
Można zarządzać magazynem kluczy przy użyciu wiersza polecenia platformy Azure [az keyvault](/cli/azure/keyvault#commands) poleceń. Aby utworzyć magazyn kluczy, użyj [tworzenie az keyvault](/cli/azure/keyvault#az-keyvault-create).

1. Jeśli to konieczne, [nawiązać połączenie z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Utwórz nową grupę zasobów, jeśli to konieczne, za pomocą [Tworzenie grupy az](/cli/azure/groupt#az-group-create). Aby wyświetlić lokalizacje, użyj [konta az list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MySecureRG" -l "East US"
     ```

3. Tworzenie nowego magazynu kluczy przy użyciu [tworzenie az keyvault](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MySecureRG" --location "East US"
     ```

4. Uwaga **nazwa magazynu** (nazwa), **nazwy grupy zasobów**, **identyfikator zasobu** (ID), **identyfikator URI magazynu**i **obiektu o identyfikatorze** później, które są zwracane do użycia. 

### <a name="bkmk_KVRM"></a> Tworzenie magazynu kluczy przy użyciu szablonu usługi Resource Manager

Można utworzyć magazynu kluczy przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Szablon szybkiego startu platformy Azure, wybierz polecenie **Wdróż na platformie Azure**.
2. Wybierz subskrypcję, grupy zasobów, lokalizację grupy zasobów, usługi Key Vault nazwa, identyfikator obiektu, postanowienia prawne i umowy, a następnie kliknij przycisk **zakupu**. 


## <a name="bkmk_ADapp"></a> Konfigurowanie aplikacji usługi Azure AD i usługę podmiotu zabezpieczeń 
Jeśli potrzebujesz szyfrowania była włączona na uruchomionej maszyny Wirtualnej na platformie Azure, usługi Azure Disk Encryption generuje i zapisuje klucze szyfrowania w magazynie kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. Utwórz aplikację usługi Azure AD, w tym celu. Na potrzeby uwierzytelniania, można użyć albo uwierzytelnianie oparte na klucz tajny klienta lub [uwierzytelnianie klienta oparte na certyfikatach usługi Azure AD](../active-directory/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Konfigurowanie aplikacji usługi Azure AD i usługi głównej przy użyciu programu Azure PowerShell 
Aby wykonać następujące polecenia i korzystaj [modułu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. Jeśli to konieczne, [nawiązać połączenie z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Użyj [New AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication) polecenia cmdlet programu PowerShell, aby utworzyć aplikację usługi Azure AD. MyApplicationHomePage i MyApplicationUri może być żadnych wartości, którą chcesz.

     ```azurepowershell-interactive
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzureRmADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication.ApplicationId jest ClientID usługi AD platformy Azure i $aadClientSecret jest klucz tajny klienta, który będzie później używane do włączenia usługi Azure Disk Encryption. Chroń klucz tajny klienta usługi Azure AD, odpowiednio. Uruchamianie `$azureAdApplication.ApplicationId` pokaże identyfikator aplikacji.


### <a name="bkmk_ADappCLI"></a> Konfigurowanie aplikacji usługi Azure AD i usługi głównej przy użyciu wiersza polecenia platformy Azure

Można zarządzać z jednostki usługi przy użyciu wiersza polecenia platformy Azure [az ad sp](/cli/azure/ad/sp) poleceń. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure ](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Jeśli to konieczne, [nawiązać połączenie z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Utwórz nową jednostkę usługi.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Identyfikator aplikacji zwracany jest identyfikatora klienta usługi Azure AD używane w innych poleceniach. Jest to również nazwę SPN, użyjesz az keyvault set-policy. Hasło jest klucz tajny klienta, którego należy później włączyć usługi Azure Disk Encryption. Chroń klucz tajny klienta usługi Azure AD, odpowiednio.
 
### <a name="bkmk_ADappRM"></a> Konfigurowanie aplikacji usługi Azure AD i usługi głównej do witryny Azure portal
Skorzystaj z procedury [w obsłudze portalu do tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) artykuł, aby utworzyć aplikację usługi Azure AD. Poszczególne kroki wymienione poniżej spowoduje przejście bezpośrednio do sekcji artykułu, aby zakończyć. 

1. [Sprawdź wymagane uprawnienia](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)
2. [Tworzenie aplikacji usługi Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Można użyć dowolnej nazwy i chcesz przy tworzeniu aplikacji adres URL logowania.
3. [Pobierz identyfikator aplikacji i klucza uwierzytelniania](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key). 
     - Klucz uwierzytelniania jest klucz tajny klienta i jest używana jako AadClientSecret polecenia Set-azurermvmdiskencryptionextension. 
        - Klucz uwierzytelniania jest używany przez aplikację jako poświadczeń do logowania do usługi Azure AD. W witrynie Azure portal ten klucz tajny jest nazywany kluczy, ale nie zawiera żadnych relacji do magazynów kluczy. Zabezpiecz ten wpis tajny odpowiednio. 
     - Identyfikator aplikacji posłuży później jako AadClientId polecenia Set-azurermvmdiskencryptionextension i ServicePrincipalName dla Set-AzureRmKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD
Do zapisu kluczy tajnych szyfrowania określonej usługi Key Vault, usługa Azure Disk Encryption wymaga Identyfikatora klienta i klucz tajny klienta aplikacji usługi Azure Active Directory, która ma uprawnienia do zapisu kluczy tajnych w usłudze Key Vault. 

> [!NOTE]
> Usługa Azure Disk Encryption, musisz skonfigurować następujące zasady dostępu do aplikacji klienta usługi Azure AD: _WrapKey_ i _ustaw_ uprawnienia.

### <a name="bkmk_KVAPPSH"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu programu Azure PowerShell
Aplikacja usługi Azure AD wymaga praw dostępu do kluczy lub wpisów tajnych w magazynie. Użyj [AzureKeyVaultAccessPolicy zestaw](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) polecenia cmdlet, aby udzielić uprawnień do aplikacji przy użyciu Identyfikatora klienta, (który został wygenerowany, gdy aplikacja została zarejestrowana) jako _— ServicePrincipalName_ wartość parametru. Aby dowiedzieć się więcej, zobacz wpis w blogu [usługi Azure Key Vault — krok po kroku](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Jeśli to konieczne, [nawiązać połączenie z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi AD za pomocą programu PowerShell.

     ```azurepowershell-interactive
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $rgname = 'MySecureRG'
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
     ```

### <a name="bkmk_KVAPCLI"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu wiersza polecenia platformy Azure
Użyj [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault.md#az-keyvault-set-policy) ustawić zasady dostępu. Aby uzyskać więcej informacji, zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia 2.0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. Jeśli to konieczne, [nawiązać połączenie z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Zapewniają utworzonej jednostki usługi, za pośrednictwem dostępu wiersza polecenia platformy Azure, aby uzyskać klucze tajne i zawijania klucze za pomocą następującego polecenia:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD za pomocą portalu

1. Otwórz grupę zasobów za pomocą usługi key vault.
2. Wybierz magazyn kluczy, przejdź do **zasady dostępu**, następnie kliknij przycisk **Dodaj nowe**.
3. W obszarze **Wybierz podmiot zabezpieczeń**, wyszukaj utworzoną aplikację usługi Azure AD i wybierz ją. 
4. Aby uzyskać **uprawnienia klucza**, sprawdź **Opakuj klucz** w obszarze **operacje kryptograficzne**.
5. Dla **uprawnienia klucza tajnego**, sprawdź **ustaw** w obszarze **operacji zarządzania wpisu tajnego**.
6. Kliknij przycisk **OK** Aby zapisać zasady dostępu. 

![Usługa Azure Key Vault operacje kryptograficzne - Opakuj klucz](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Ustaw uprawnienia klucza wpisu tajnego z magazynu platformy Azure — ](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Zestaw magazynu kluczy, zaawansowane zasady dostępu
Platforma Azure wymaga dostępu do kluczy szyfrowania i wpisy tajne w magazynie kluczy, aby udostępnić je do maszyny Wirtualnej do rozruchu i odszyfrowywania woluminów. Włącz szyfrowanie dysków w magazynie kluczy lub wdrożenia zakończy się niepowodzeniem.  

### <a name="bkmk_KVperPSH"></a> Zestaw magazynu kluczy, zaawansowane zasady dostępu przy użyciu programu Azure PowerShell
 Użyj polecenia cmdlet programu PowerShell usługi key vault [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) umożliwia szyfrowanie dysków dla magazynu kluczy.

  - **Włączanie usługi Key Vault do szyfrowania dysku:** EnabledForDiskEncryption jest wymagany dla usługi Azure Disk encryption.
      
     ```azurepowershell-interactive 
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDiskEncryption
     ```

  - **Włączenie usługi Key Vault w przypadku wdrożenia w razie potrzeby:** umożliwia dostawcy zasobów Microsoft.Compute można pobrać Wpisy tajne z tego magazynu kluczy, podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej odwołuje się do tego magazynu kluczy.

     ```azurepowershell-interactive
      Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDeployment
     ```

  - **Włączenie usługi Key Vault dla wdrożenia szablonu, w razie potrzeby:** umożliwia usługi Azure Resource Manager można pobrać Wpisy tajne z tego magazynu kluczy, podczas wdrażania szablonu odwołuje się ten magazyn kluczy.

     ```azurepowershell-interactive             
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForTemplateDeployment`
     ```

### <a name="bkmk_KVperCLI"></a> Zestaw magazynu kluczy, zaawansowane zasady dostępu przy użyciu wiersza polecenia platformy Azure
Użyj [az keyvault update](/cli/azure/keyvault#az-keyvault-update) umożliwia szyfrowanie dysków dla magazynu kluczy. 

 - **Włączanie usługi Key Vault do szyfrowania dysku:** włączone dla dysku szyfrowanie jest wymagane. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-disk-encryption "true"
     ```  

 - **Włączenie usługi Key Vault w przypadku wdrożenia w razie potrzeby:** Zezwalaj maszyn wirtualnych, aby pobrać certyfikaty zapisane jako wpisy tajne z magazynu.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-deployment "true"
     ``` 

 - **Włączenie usługi Key Vault dla wdrożenia szablonu, w razie potrzeby:** Zezwalaj na Menedżera zasobów można pobrać Wpisy tajne z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Zestaw magazynu kluczy, zaawansowane zasady dostępu w witrynie Azure portal

1. Wybierz z magazynu kluczy, przejdź do **zasady dostępu**, i **kliknij, aby wyświetlić zaawansowane zasady dostępu**.
2. Zaznacz pole opcji **zapewnianie dostępu do usługi Azure Disk Encryption dla szyfrowania woluminu**.
3. Wybierz **włączyć dostęp do usługi Azure Virtual Machines na potrzeby wdrożenia** i/lub **Włącz dostęp do usługi Azure Resource Manager dla wdrożenia szablonu**, jeśli to konieczne. 
4. Kliknij pozycję **Zapisz**.

![Zaawansowane zasady dostępu magazynu kluczy Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Konfigurowanie klucza szyfrowania (opcjonalnie)
Jeśli chcesz użyć klucz szyfrowania klucza (KEK) Aby uzyskać dodatkową warstwę zabezpieczeń dla kluczy szyfrowania, należy dodać KEK do magazynu kluczy. Użyj [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) polecenia cmdlet, aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Można także zaimportować KEK, z usługi zarządzania kluczami w środowisku lokalnym przez moduł HSM. Aby uzyskać więcej informacji, zobacz [Key Vault dokumentacji](../key-vault/key-vault-hsm-protected-keys.md). Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. 

* KEK adresy URL i wpisu tajnego usługi key vault, na których musi być poddany kontroli wersji. Azure wymusza to ograniczenie wersji. Nieprawidłowy klucz tajny i adresy URL KEK zobacz następujące przykłady:

  * Przykład prawidłowy adres URL wpisu tajnego:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowego adresu URL klucza KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Usługa Azure Disk Encryption nie obsługuje określania numerów portów jako część wpisy tajne usługi key vault i KEK adresów URL. Aby zapoznać się z przykładami adresów URL obsługiwane i nieobsługiwane key vault zobacz następujące przykłady:

  * Adres URL magazynu kluczy można zaakceptować  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Adres URL dopuszczalne magazynu kluczy:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> Skonfiguruj klucz szyfrowania klucza przy użyciu programu Azure PowerShell 
Przed użyciem skryptu programu PowerShell, należy zapoznać się z wstępnie wymagane składniki usługi Azure Disk Encryption, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmiany w danym środowisku. Ten skrypt tworzy wszystkie wymagania wstępne usługi Azure Disk Encryption i szyfruje istniejącej maszyny Wirtualnej IaaS, zawijanie klucz szyfrowania dysku przy użyciu klucza szyfrowania. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MySecureRG', and 'MySecureVault' with your values.
     # Use Get-AzureRmLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzureRmResourceGroup
     
     $Loc = 'MyLocation';
     $rgname = 'MySecureRG';
     $KeyVaultName = 'MySecureVault'; 
     New-AzureRmResourceGroup –Name $rgname –Location $Loc;
     New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc;
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $KeyVaultResourceId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption;
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname;
     
 #Step 4: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' with your value. 
     
     $VMName = 'MySecureVM';
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> Uwierzytelnianie oparte na certyfikatach (opcjonalnie)
Jeśli chcesz użyć uwierzytelniania certyfikatu, możesz przekazać jeden do magazynu kluczy i wdrożyć ją do klienta. Przed użyciem skryptu programu PowerShell, należy zapoznać się z wstępnie wymagane składniki usługi Azure Disk Encryption, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmiany w danym środowisku.

     
 ```powershell

 # Fill in "MySecureRG", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> Uwierzytelnianie oparte na certyfikatach oraz KEK (opcjonalnie)

Jeśli chcesz użyć uwierzytelniania certyfikatu, a następnie opakuj klucz szyfrowania za pomocą klucza KEK, możesz użyć poniższego skryptu jako przykład. Przed użyciem skryptu programu PowerShell, należy zapoznać się z wszystkie poprzednie wymagania wstępne usługi Azure Disk Encryption, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmiany w danym środowisku.

> [!IMPORTANT]
> Uwierzytelnianie usługi Azure AD na podstawie certyfikatu nie jest obecnie obsługiwane na maszynach wirtualnych z systemem Linux.



     
 ```powershell
# Fill in 'MySecureRG', 'MySecureVault', and 'MyLocation' (if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption for Windows](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux-aad.md)
