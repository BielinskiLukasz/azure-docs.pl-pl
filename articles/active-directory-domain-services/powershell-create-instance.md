---
title: Włączanie usług Azure DS Domain Services przy użyciu programu PowerShell | Microsoft Docs
description: Dowiedz się, jak skonfigurować i włączyć Azure Active Directory Domain Services przy użyciu programu Azure AD PowerShell i Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 2d291af3cc6175b371f71fb63402ecb45afcba34
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223469"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Włączanie Azure Active Directory Domain Services przy użyciu programu PowerShell

Azure Active Directory Domain Services (AD DS platformy Azure) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP, uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z systemem Windows Server Active Directory. Te usługi domenowe są używane bez konieczności samodzielnego wdrażania kontrolerów domeny i zarządzania nimi. Platforma Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta Integracja umożliwia użytkownikom logowanie się przy użyciu poświadczeń firmowych, a także umożliwia korzystanie z istniejących grup i kont użytkowników w celu zabezpieczenia dostępu do zasobów.

W tym artykule przedstawiono sposób włączania usługi Azure AD DS przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten artykuł, potrzebne są następujące zasoby:

* Zainstaluj i skonfiguruj program Azure PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](/powershell/azure/install-az-ps).
    * Upewnij się, że logujesz się do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Zainstaluj i skonfiguruj program Azure AD PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure AD PowerShell i nawiązać połączenie z usługą Azure AD](/powershell/azure/active-directory/install-adv2).
    * Upewnij się, że logujesz się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD][Connect-AzureAD] .
* Aby włączyć usługę Azure AD DS, musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usługi Azure AD DS, musisz mieć uprawnienia *współautora* w ramach subskrypcji platformy Azure.

## <a name="create-required-azure-ad-resources"></a>Tworzenie wymaganych zasobów usługi Azure AD

Usługa Azure AD DS wymaga nazwy głównej usługi i grupy usługi Azure AD. Te zasoby umożliwiają synchronizowanie danych przez domenę zarządzaną przez usługę Azure AD DS i definiowanie użytkowników, którzy mają uprawnienia administracyjne w domenie zarządzanej.

Najpierw utwórz jednostkę usługi Azure AD dla AD DS Azure, aby komunikować się i uwierzytelniać samodzielnie. Określony identyfikator aplikacji jest używany o nazwie *usługi kontrolera domeny* o identyfikatorze *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. Nie zmieniaj tego identyfikatora aplikacji.

Utwórz nazwę główną usługi Azure AD przy użyciu polecenia cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] :

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Teraz należy utworzyć grupę usługi Azure AD o nazwie *administratorzy DC w usłudze AAD*. Użytkownicy dodani do tej grupy uzyskują uprawnienia do wykonywania zadań administracyjnych w domenie zarządzanej.

Utwórz grupę *administratorów kontrolera domeny usługi AAD* za pomocą polecenia cmdlet [New-AzureADGroup][New-AzureADGroup] :

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Po utworzeniu grupy *administratorów kontrolera domeny usługi AAD* Dodaj użytkownika do grupy przy użyciu polecenia cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember] . Najpierw uzyskasz identyfikator obiektu grupy *administratorów kontrolera domeny usługi AAD* za pomocą polecenia cmdlet [Get-AzureADGroup][Get-AzureADGroup] , a następnie identyfikator obiektu żądanego użytkownika przy użyciu polecenia cmdlet [Get-AzureADUser][Get-AzureADUser] .

W poniższym przykładzie identyfikator obiektu użytkownika dla konta z nazwą UPN `admin@contoso.onmicrosoft.com` . Zastąp to konto użytkownika nazwą UPN użytkownika, który chcesz dodać do grupy *administratorów kontrolera domeny usługi AAD* :

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Tworzenie pomocniczych zasobów platformy Azure

Najpierw Zarejestruj dostawcę zasobów Azure AD Domain Services za pomocą polecenia cmdlet [register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Następnie utwórz grupę zasobów za pomocą polecenia cmdlet [New-AzResourceGroup][New-AzResourceGroup] . W poniższym przykładzie grupa zasobów ma nazwę moja *zasobów* i jest tworzona w regionie *zachodnim* . Użyj własnej nazwy i żądanego regionu:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Utwórz sieć wirtualną i podsieci dla Azure AD Domain Services. Dwie podsieci są tworzone — jeden dla *DomainServices*i jeden dla *obciążeń*. Usługa Azure AD DS jest wdrażana w dedykowanej podsieci *DomainServices* . Nie Wdrażaj innych aplikacji ani obciążeń w tej podsieci. Użyj oddzielnych *obciążeń* lub innych podsieci dla pozostałych maszyn wirtualnych.

Utwórz podsieci przy użyciu polecenia cmdlet [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] , a następnie Utwórz sieć wirtualną za pomocą polecenia cmdlet [New-AzVirtualNetwork][New-AzVirtualNetwork] .

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-a-managed-domain"></a>Tworzenie domeny zarządzanej

Teraz Utwórzmy domenę zarządzaną. Ustaw identyfikator subskrypcji platformy Azure, a następnie podaj nazwę domeny zarządzanej, na przykład *aaddscontoso.com*. Identyfikator subskrypcji można uzyskać za pomocą polecenia cmdlet [Get-AzSubscription][Get-AzSubscription] .

W przypadku wybrania regionu, który obsługuje Strefy dostępności, zasoby AD DS platformy Azure są dystrybuowane między strefami w celu zapewnienia dodatkowej nadmiarowości.

Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach.

Nie ma niczego do skonfigurowania na potrzeby dystrybuowania AD DS platformy Azure między strefami. Platforma Azure automatycznie obsługuje dystrybucję zasobów. Aby uzyskać więcej informacji i sprawdzić dostępność regionów, zobacz [co to są strefy dostępności na platformie Azure?][availability-zones]

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Utworzenie zasobu i zwrócenie kontroli do wiersza polecenia programu PowerShell trwa kilka minut. Domena zarządzana jest w dalszym ciągu obsługiwana w tle, a ukończenie wdrożenia może potrwać do godziny. W Azure Portal strona **Przegląd** dla domeny zarządzanej pokazuje bieżący stan w tym etapie wdrażania.

Gdy Azure Portal pokazuje, że domena zarządzana zakończyła Inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzaną do przyłączania do domeny lub uwierzytelniania.
    * Aby skonfigurować serwer DNS, wybierz domenę zarządzaną w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* Jeśli utworzono domenę zarządzaną w regionie, który obsługuje Strefy dostępności, Utwórz sieciową grupę zabezpieczeń, aby ograniczyć ruch w sieci wirtualnej dla domeny zarządzanej. Tworzony jest standardowy moduł równoważenia obciążenia platformy Azure, który wymaga wprowadzenia tych reguł. Ta sieciowa Grupa zabezpieczeń zabezpiecza AD DS platformy Azure i jest wymagana do poprawnego działania domeny zarządzanej.
    * Aby utworzyć grupę zabezpieczeń sieci i wymagane reguły, wybierz domenę zarządzaną w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne utworzenie i skonfigurowanie sieciowej grupy zabezpieczeń.
* [Włącz synchronizację haseł w Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , aby użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.

## <a name="complete-powershell-script"></a>Ukończ skrypt programu PowerShell

Poniższy kompletny skrypt programu PowerShell łączy wszystkie zadania przedstawione w tym artykule. Skopiuj skrypt i Zapisz go w pliku z `.ps1` rozszerzeniem. Uruchom skrypt w lokalnej konsoli programu PowerShell lub [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Aby włączyć usługę Azure AD DS, musisz być administratorem globalnym dla dzierżawy usługi Azure AD. Musisz również mieć co najmniej uprawnienia *współautora* w ramach subskrypcji platformy Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Utworzenie zasobu i zwrócenie kontroli do wiersza polecenia programu PowerShell trwa kilka minut. Domena zarządzana jest w dalszym ciągu obsługiwana w tle, a ukończenie wdrożenia może potrwać do godziny. W Azure Portal strona **Przegląd** dla domeny zarządzanej pokazuje bieżący stan w tym etapie wdrażania.

Gdy Azure Portal pokazuje, że domena zarządzana zakończyła Inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzaną do przyłączania do domeny lub uwierzytelniania.
    * Aby skonfigurować serwer DNS, wybierz domenę zarządzaną w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* Jeśli utworzono domenę zarządzaną w regionie, który obsługuje Strefy dostępności, Utwórz sieciową grupę zabezpieczeń, aby ograniczyć ruch w sieci wirtualnej dla domeny zarządzanej. Tworzony jest standardowy moduł równoważenia obciążenia platformy Azure, który wymaga wprowadzenia tych reguł. Ta sieciowa Grupa zabezpieczeń zabezpiecza AD DS platformy Azure i jest wymagana do poprawnego działania domeny zarządzanej.
    * Aby utworzyć grupę zabezpieczeń sieci i wymagane reguły, wybierz domenę zarządzaną w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne utworzenie i skonfigurowanie sieciowej grupy zabezpieczeń.
* [Włącz synchronizację haseł w Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , aby użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić domenę zarządzaną w działaniu, można [przyłączyć do domeny maszynę wirtualną z systemem Windows][windows-join], [skonfigurować bezpieczny protokół LDAP][tutorial-ldaps]i [skonfigurować synchronizację skrótów haseł][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
