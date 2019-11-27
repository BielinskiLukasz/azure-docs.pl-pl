---
title: Zarządzanie elementami członkowskimi usługi Azure łańcucha bloków Service Consortium — PowerShell
description: Dowiedz się, jak zarządzać członkami usługi Azure łańcucha bloków Service Consortium przy użyciu Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: 4eb35838394d3324f460b5e83cfbcb47b8114221
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455542"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Zarządzanie członkami konsorcjum w usłudze Azure łańcucha bloków Service przy użyciu programu PowerShell

Za pomocą programu PowerShell można zarządzać członkami łańcucha bloków konsorcjum usługi Azure łańcucha bloków. Członkowie z uprawnieniami administratora mogą zapraszać, dodawać, usuwać i zmieniać role dla wszystkich uczestników w programie łańcucha bloków Consortium. Członkowie, którzy mają uprawnienia użytkownika, mogą wyświetlać wszystkich uczestników konsorcjum łańcucha bloków i zmieniać jego nazwę wyświetlaną.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz element członkowski łańcucha bloków przy użyciu [Azure Portal](create-member.md).
* Aby uzyskać więcej informacji na temat konsorcjów, członków i węzłów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Azure Cloud Shell to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do [Shell.Azure.com/PowerShell](https://shell.azure.com/powershell). Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz **klawisz ENTER** , aby go uruchomić.

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

Zainstaluj pakiet Microsoft.AzureBlockchainService.ConsortiumManagement.PS z Galeria programu PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Ustawianie preferencji informacji

Aby uzyskać więcej informacji podczas wykonywania poleceń cmdlet, należy ustawić zmienną preferencji informacji. Domyślnie *$InformationPreference* jest ustawiona na *SilentlyContinue*.

Aby uzyskać pełne informacje z poleceń cmdlet, ustaw preferencję w programie PowerShell w następujący sposób:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Ustanów połączenie Web3

Aby zarządzać członkami konsorcjum, Ustanów połączenie Web3 z punktem końcowym elementu członkowskiego usługi łańcucha bloków. Za pomocą tego skryptu można ustawić zmienne globalne do wywoływania poleceń cmdlet zarządzania konsorcjum.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Zastąp *\<hasło konta członka\>* z hasłem konta elementu członkowskiego, które zostało użyte podczas tworzenia elementu członkowskiego.

Znajdź pozostałe wartości w Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do domyślnej strony **przeglądu** członków usługi łańcucha bloków.

    ![Przegląd składowej](./media/manage-consortium-powershell/member-overview.png)

    Zastąp *\<konto elementu członkowskiego\>* i *\<adres RootContract\>* wartościami z portalu.

1. W polu adres punktu końcowego wybierz pozycję **węzły transakcji**, a następnie wybierz **węzeł domyślna transakcja**. Węzeł domyślny ma taką samą nazwę jak element członkowski łańcucha bloków.
1. Wybierz **Parametry połączenia**.

    ![Parametry połączeń](./media/manage-consortium-powershell/connection-strings.png)

    Zastąp *\<adres punktu końcowego\>* wartością z **protokołu HTTPS (dostęp do klucza 1)** lub **https (dostęp do klucza 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Zarządzanie siecią i inteligentnymi kontraktami

Za pomocą poleceń cmdlet sieci i inteligentnej kontraktu można nawiązać połączenie z inteligentnymi kontraktami punktu końcowego łańcucha bloków, które są odpowiedzialne za zarządzanie konsorcjum.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Użyj tego polecenia cmdlet, aby nawiązać połączenie z inteligentnymi kontraktami zarządzania konsorcjum. Te kontrakty są używane do zarządzania i wymuszania członków w ramach konsorcjum.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| RootContractAddress | Adres kontraktu głównego dla inteligentnych kontraktów zarządzania konsorcjum | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New-Web3Connection | Yes |

#### <a name="example"></a>Przykład

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

To polecenie cmdlet służy do tworzenia obiektu do przechowywania informacji dla konta zarządzania węzła zdalnego.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adres konta elementu członkowskiego łańcucha bloków | Yes |
| ManagedAccountPassword | Hasło adresu konta | Yes |

#### <a name="example"></a>Przykład

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Użyj tego polecenia cmdlet, aby nawiązać połączenie z punktem końcowym RPC węzła transakcji.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adres punktu końcowego elementu członkowskiego łańcucha bloków | Yes |

#### <a name="example"></a>Przykład

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Zarządzanie członkami konsorcjum

Za pomocą poleceń cmdlet zarządzania składową konsorcjum można zarządzać członkami w ramach konsorcjum. Dostępne akcje zależą od roli konsorcjum.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Użyj tego polecenia cmdlet, aby uzyskać szczegóły elementu członkowskiego lub listę członków konsorcjum.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| Nazwa | Nazwa elementu członkowskiego usługi łańcucha bloków, dla którego chcesz pobrać szczegóły. Po wprowadzeniu nazwy zwraca szczegóły elementu członkowskiego. Po pominięciu nazwy zwraca listę wszystkich członków konsorcjum. | Nie |
| Members | Obiekt składowych uzyskany z importu-ConsortiumManagementContracts | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New-Web3Connection | Yes |

#### <a name="example"></a>Przykład

[Ustanów połączenie Web3](#establish-a-web3-connection) , aby ustawić zmienną $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Przykładowe dane wyjściowe

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Użyj tego polecenia cmdlet, aby usunąć element członkowski łańcucha bloków.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| Nazwa | Nazwa elementu członkowskiego do usunięcia | Yes |
| Members | Obiekt składowych uzyskany z importu-ConsortiumManagementContracts | Yes |
| Web3Account | Obiekt Web3Account uzyskany z importu Web3Account | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New-Web3Connection | Yes |

#### <a name="example"></a>Przykład

[Ustanów połączenie Web3](#establish-a-web3-connection) , aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Użyj tego polecenia cmdlet, aby ustawić atrybuty elementu członkowskiego łańcucha bloków, w tym nazwę wyświetlaną i rolę konsorcjum.

Administratorzy konsorcjum mogą ustawiać wartości **DisplayName** i **role** dla wszystkich członków. Członek konsorcjum z rolą użytkownika może zmienić tylko nazwę wyświetlaną własnego elementu członkowskiego.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| Nazwa | Nazwa elementu członkowskiego łańcucha bloków | Yes |
| DisplayName | Nowa nazwa wyświetlana | Nie |
| AccountAddress | Adres konta | Nie |
| Members | Obiekt składowych uzyskany z importu-ConsortiumManagementContracts | Yes |
| Web3Account | Obiekt Web3Account uzyskany z importu Web3Account | Yes |
| Web3Client |  Obiekt Web3Client uzyskany z New-Web3Connection| Yes |

#### <a name="example"></a>Przykład

[Ustanów połączenie Web3](#establish-a-web3-connection) , aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Zarządzanie zaproszeniami członków konsorcjum

Za pomocą poleceń cmdlet zarządzania zaproszeniami członków konsorcjum można zarządzać zaproszeniami uczestników konsorcjum. Dostępne akcje zależą od roli konsorcjum.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Użyj tego polecenia cmdlet, aby zaprosić nowych członków do konsorcjum.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure dla elementu członkowskiego do zaproszenia | Yes |
| Rola | Rola konsorcjum. Wartości mogą być administratora lub użytkownika. Administrator jest rolą administratora konsorcjum. Użytkownik jest rolą członka konsorcjum. | Yes |
| Members | Obiekt składowych uzyskany z importu-ConsortiumManagementContracts | Yes |
| Web3Account | Obiekt Web3Account uzyskany z importu Web3Account | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New-Web3Connection | Yes |

#### <a name="example"></a>Przykład

[Ustanów połączenie Web3](#establish-a-web3-connection) , aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

To polecenie cmdlet umożliwia pobranie lub wyświetlenie stanu zaproszenia członka konsorcjum.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure dla elementu członkowskiego, który ma zostać zaproszony. W przypadku podanego identyfikatora subskrypcji funkcja zwraca szczegóły dotyczące zaproszenia identyfikatora subskrypcji. Jeśli identyfikator subskrypcji zostanie pominięty, zwraca listę wszystkich zaproszeń członków. | Nie |
| Members | Obiekt składowych uzyskany z importu-ConsortiumManagementContracts | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New-Web3Connection | Yes |

#### <a name="example"></a>Przykład

[Ustanów połączenie Web3](#establish-a-web3-connection) , aby ustawić zmienną $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Przykładowe dane wyjściowe

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Użyj tego polecenia cmdlet, aby odwołać zaproszenie członka konsorcjum.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure do odwołania | Yes |
| Members | Obiekt składowych uzyskany z importu-ConsortiumManagementContracts | Yes |
| Web3Account | Obiekt Web3Account uzyskany z importu Web3Account | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New-Web3Connection | Yes |

#### <a name="example"></a>Przykład

[Ustanów połączenie Web3](#establish-a-web3-connection) , aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Użyj tego polecenia cmdlet, aby ustawić **rolę** dla istniejącego zaproszenia. Tylko Administratorzy konsorcjum mogą zmieniać zaproszenia.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure dla elementu członkowskiego do zaproszenia | Yes |
| Rola | Nowa rola konsorcjum dla zaproszenia. Wartością może być **użytkownik** lub **administrator**. | Yes |
| Members |  Obiekt składowych uzyskany z importu-ConsortiumManagementContracts | Yes |
| Web3Account | Obiekt Web3Account uzyskany z importu Web3Account | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New-Web3Connection | Yes |

#### <a name="example"></a>Przykład

[Ustanów połączenie Web3](#establish-a-web3-connection) , aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konsorcjów, członków i węzłów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md)
