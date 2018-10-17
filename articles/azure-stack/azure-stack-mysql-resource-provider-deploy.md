---
title: Przy użyciu bazy danych MySQL w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wdrożyć bazy danych MySQL jako usługi w usłudze Azure Stack i Szybkie kroki wdrażania karty dostawcy zasobów serwera MySQL.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 1c5a2e4102b251490bf3a1fa9b82e9dbce075242
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364425"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Wdrażanie dostawcy zasobów MySQL w usłudze Azure Stack

Dostawcy zasobów MySQL Server umożliwia udostępnianie baz danych MySQL jako usługi Azure Stack. Dostawcy zasobów MySQL działa jako usługa na maszynie wirtualnej (VM) systemu Windows Server 2016 Server Core.

> [!IMPORTANT]
> Tworzenie elementów na serwerach tego hosta SQL lub MySQL jest obsługiwane tylko dostawcy zasobów. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może prowadzić do niezgodnego stanu.

## <a name="prerequisites"></a>Wymagania wstępne

Istnieje kilka wymagań wstępnych, które muszą być spełnione przed wdrożeniem dostawcy zasobów usługi Azure Stack bazy danych MySQL. Aby spełnić te wymagania, wykonaj kroki opisane w tym artykule, na komputerze, na których mogą uzyskiwać dostęp uprzywilejowany punktu końcowego maszyny Wirtualnej.

* Jeśli użytkownik jeszcze tego nie zrobiono, [rejestrowania usługi Azure Stack](.\azure-stack-registration.md) z platformą Azure, dzięki czemu można pobrać elementów portalu Azure marketplace.
* Moduły platformy Azure i usługi Azure Stack PowerShell należy zainstalować w systemie, w którym będzie uruchamiany tej instalacji. Ten system musi być obrazem systemu Windows 10 lub Windows Server 2016 z najnowszą wersją środowiska uruchomieniowego .NET. Zobacz [zainstalować program PowerShell dla usługi Azure Stack](.\azure-stack-powershell-install.md).
* Dodaj wymagane core systemu Windows Server maszyny Wirtualnej w portalu Marketplace usługi Azure Stack, pobierając **systemu Windows Server 2016 Datacenter — instalacja Server Core** obrazu.

* Pobierz binarne dostawcy zasobów bazy danych MySQL, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego.

  >[!NOTE]
  >Aby wdrożyć dostawcy bazy danych MySQL w systemie, który nie ma dostępu do Internetu, skopiuj [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) pliku na ścieżkę lokalną. Dostarcza przy użyciu nazwy ścieżki **DependencyFilesLocalPath** parametru.

* Dostawca zasobów ma minimalne odpowiednie usługi Azure Stack kompilacji.

    | Minimalna wersja usługi Azure Stack | Wersja MySQL RP|
    | --- | --- |
    | W wersji 1804 (1.0.180513.1)|[MySQL RP wersji 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    |     |     |

* Upewnij się, że zostały spełnione wymagania wstępne integrację centrum danych:

    |Wymagania wstępne|Informacje ogólne|
    |-----|-----|
    |Przesyłanie warunkowe DNS jest prawidłowo.|[Usługa Azure Stack Integracja z centrum danych — DNS](azure-stack-integrate-dns.md)|
    |Porty dla ruchu przychodzącego dla dostawców zasobów są otwarte.|[Azure Stack — Integracja z centrum danych — publikowanie punktów końcowych](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Podmiot certyfikatu PKI i SAN są prawidłowo ustawione.|[Usługa Azure Stack obowiązkowe infrastruktury kluczy publicznych wymagania wstępne dotyczące wdrażania](azure-stack-pki-certs.md#mandatory-certificates)[wymagania wstępne dotyczące usługi Azure Stack wdrożenia PaaS certyfikatu](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certyfikaty

_Zintegrowane systemy tylko dla instalacji_. Musisz podać certyfikat SQL PaaS PKI opisane w sekcji opcjonalnej certyfikaty PaaS z [wymagania dotyczące infrastruktury kluczy publicznych wdrażania usługi Azure Stack](.\azure-stack-pki-certs.md#optional-paas-certificates). Umieść plik pfx w lokalizacji określonej przez **DependencyFilesLocalPath** parametru. Nie są oferowane certyfikatu dla systemów ASDK.

## <a name="deploy-the-resource-provider"></a>Wdrażanie dostawcy zasobów

Jeśli masz wszystkie wstępnie wymagane składniki zainstalowane, uruchomić **DeployMySqlProvider.ps1** skrypt w celu wdrożenia dostawcy zasobów bazy danych MYSQL. Skrypt DeployMySqlProvider.ps1 jest wyodrębniany jako część plik binarny w dostawcy zasobów MySQL, który został pobrany dla używanej wersji programu Azure Stack.

Aby wdrożyć dostawcy zasobów bazy danych MySQL, Otwórz okno programu PowerShell (nie PowerShell ISE) nowe z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym zostały wyodrębnione pliki binarne dostawcy zasobów MySQL. Zalecamy używanie nowe okno programu PowerShell, aby uniknąć potencjalnych problemów, spowodowane przez moduły programu PowerShell, które zostały już załadowane.

Uruchom **DeployMySqlProvider.ps1** skryptu, który wykonuje następujące zadania:

* Służy do przekazywania certyfikatów i innych artefaktów z kontem magazynu w usłudze Azure Stack.
* Publikuje pakiety galerii, dzięki czemu można wdrożyć przy użyciu galerii baz danych MySQL.
* Publikuje pakietu galerii do wdrażania serwerów hostingu.
* Wdraża maszynę Wirtualną przy użyciu obrazu systemu Windows Server 2016 core pobrany, a następnie instaluje dostawcy zasobów bazy danych MySQL.
* Rejestruje lokalne rekord DNS, który jest mapowany do Twojego dostawcę zasobów maszyny Wirtualnej.
* Rejestruje dostawcę zasobów za pomocą lokalnej usługi Azure Resource Manager dla konta operatora.

> [!NOTE]
> Po rozpoczęciu wdrażania dostawcy zasobów MySQL, **system.local.mysqladapter** zostanie utworzona grupa zasobów. Może upłynąć do 75 minut na zakończenie wdrożenia wymagane do tej grupy zasobów.

### <a name="deploymysqlproviderps1-parameters"></a>Parametry DeployMySqlProvider.ps1

Można określić te parametry, z poziomu wiersza polecenia. Jeśli nie istnieje lub dowolnym Walidacja parametru nie powiedzie się, zostanie wyświetlony monit zapewnić wymagane parametry.

| Nazwa parametru | Opis | Komentarz lub wartość domyślną |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury, niezbędne do uzyskania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure Stack. Użyj tych samych poświadczeń, które były używane do wdrażania usługi Azure Stack. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów MySQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **AzureEnvironment** | Środowiska platformy Azure z konta administratora usługi, które używanych do wdrażania usługi Azure Stack. Wymagane tylko w przypadku wdrożeń usługi Azure AD. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, lub jeśli za pomocą (Chiny) usługi Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Tylko zintegrowane systemy plik PFX certyfikatu musi być umieszczane w tym katalogu. Dla odłączonych enviroments pobrać [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) do tego katalogu. Opcjonalnie można skopiować jeden pakiet Windows Update MSU tutaj. | _Opcjonalnie_ (_obowiązkowe_ zintegrowanych systemów lub w środowiskach rozłączonych) |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Liczba przypadków, o których chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcę zasobów i wszystkie powiązane zasoby (zobacz poniższe informacje o). | Nie |
| **DebugMode** | Zapobiega automatycznego czyszczenia po awarii. | Nie |
| **AcceptLicense** | Pomija monit o zaakceptowanie licencji GPL.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Wdrażanie dostawcy zasobów MySQL przy użyciu niestandardowego skryptu

Aby wyeliminować żadnej ręcznej konfiguracji podczas wdrażania dostawcy zasobów, można dostosować poniższy skrypt. Zmień domyślne informacje konta i hasła dla danego wdrożenia usługi Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.4.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Po zakończeniu działania skryptu instalacji dostawcy zasobów, Odśwież przeglądarkę, aby upewnić się, że można sprawdzić najnowsze aktualizacje.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Weryfikacja wdrożenia za pomocą portalu usługi Azure Stack

1. Zaloguj się do portalu administracyjnego jako administratora usługi.
2. Wybierz **grup zasobów**
3. Wybierz **systemu.\< Lokalizacja\>.mysqladapter** grupy zasobów.
4. Na stronie Podsumowanie dla grupy zasobów — omówienie powinno istnieć żadne wdrożenia nie powiodło się.
5. Na koniec wybierz pozycję **maszyn wirtualnych** w portalu administracyjnym, aby sprawdzić dostawcy zasobów MySQL maszyna wirtualna została pomyślnie utworzone i jest uruchomiona.

## <a name="next-steps"></a>Kolejne kroki

[Dodawanie serwerów hostingu](azure-stack-mysql-resource-provider-hosting-servers.md)
