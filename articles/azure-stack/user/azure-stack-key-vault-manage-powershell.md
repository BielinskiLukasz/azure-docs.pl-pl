---
title: Zarządzanie usługi Key Vault w usłudze Azure Stack przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać usługi Key Vault w usłudze Azure Stack przy użyciu programu PowerShell
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: db68d3ac626d80361e456a251b93d847a73afb8c
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192260"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Zarządzanie usługi Key Vault w usłudze Azure Stack przy użyciu programu PowerShell

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Możesz zarządzać usługi Key Vault w usłudze Azure Stack przy użyciu programu PowerShell. W tym artykule opisano sposób użycia poleceń cmdlet programu PowerShell dla Key Vault:

* Tworzenie magazynu kluczy.
* Store i zarządzaniu wpisami tajnymi i kluczami kryptograficznymi.
* Zezwolić użytkownikom i aplikacjom wywoływanie operacji w magazynie.

>[!NOTE]
>Opisane poleceń cmdlet programu PowerShell dla Key Vault, w tym artykule znajdują się w zestawie SDK programu PowerShell platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Należy subskrybować ofertę, która obejmuje usługę Azure Key Vault.
* [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).
* [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Włączenie subskrypcji dzierżawy dla operacje usługi Key Vault

Zanim będzie można wykonywać dowolne operacje w magazynie kluczy, upewnij się, że subskrypcji dzierżawcy jest włączony dla operacji magazynu. Aby sprawdzić, czy operacje magazynu są włączone, uruchom następujące polecenie:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Dane wyjściowe**

Jeśli Twoja subskrypcja została włączona dla operacji magazynu, dane wyjściowe pokazują **RegistrationState** jest **zarejestrowanej** dla wszystkich typów zasobów magazynu kluczy.

![Stan rejestracji usługi Key vault](media/azure-stack-key-vault-manage-powershell/image1.png)

Jeśli operacje magazynu nie są włączone, uruchom następujące polecenie, aby zarejestrować w usłudze Key Vault w ramach subskrypcji:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Dane wyjściowe**

Jeśli rejestracja zakończy się pomyślnie, zwracany jest następujące dane wyjściowe:

![Zarejestruj subskrypcję](media/azure-stack-key-vault-manage-powershell/image2.png)

Po wywołaniu poleceń usługi Key Vault może otrzymasz komunikat o błędzie, takie jak "subskrypcja nie jest zarejestrowana do korzystania z przestrzeni nazw"Microsoft.KeyVault"." Jeśli wystąpi błąd, upewnij się, że masz [włączone dostawcy zasobów usługi Key Vault](#enable-your-tenant-subscription-for-vault-operations) zgodnie z instrukcjami, które zostały określone wcześniej.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Przed przystąpieniem do tworzenia magazynu kluczy, Utwórz grupę zasobów, dzięki czemu wszystkie zasoby związane z usługą key vault istnieje w grupie zasobów. Aby utworzyć nową grupę zasobów, użyj następującego polecenia:

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

**Dane wyjściowe**

![Nowa grupa zasobów](media/azure-stack-key-vault-manage-powershell/image3.png)

Teraz użyj **New-AzureRMKeyVault** polecenia cmdlet, aby utworzyć magazyn kluczy w grupie zasobów, który został utworzony wcześniej. To polecenie odczytuje trzy obowiązkowe parametry: Nazwa grupy zasobów, nazwę magazynu kluczy i lokalizacji geograficznej.

Uruchom następujące polecenie, aby utworzyć magazyn kluczy:

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**Dane wyjściowe**

![Nowy magazyn kluczy](media/azure-stack-key-vault-manage-powershell/image4.png)

Dane wyjściowe tego polecenia są wyświetlane właściwości magazynu kluczy, który został utworzony. Gdy aplikacja uzyskuje dostęp do tego magazynu, należy użyć `Vault URI` właściwość, która jest `https://vault01.vault.local.azurestack.external` w tym przykładzie.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Wdrażanie usługi Active Directory Federation Services (AD FS)

We wdrożeniu usług AD FS możesz otrzymać ostrzeżenie to: "Nie ustawiono zasad dostępu. Nie użytkownika lub aplikacji ma uprawnienia dostępu, aby używać tego magazynu." Aby rozwiązać ten problem, należy ustawić zasady dostępu dla magazynu przy użyciu [Set-AzureRmKeyVaultAccessPolicy](azure-stack-key-vault-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) polecenia cmdlet:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Zarządzanie kluczami i wpisami tajnymi

Po utworzeniu magazynu kluczy, należy użyć następujących kroków do tworzenia i obsługi kluczy i wpisów tajnych w magazynie.

### <a name="create-a-key"></a>Utwórz klucz

Użyj **Add-AzureKeyVaultKey** polecenia cmdlet, tworzenie lub Importowanie klucza chronionego przez oprogramowanie, w magazynie kluczy.

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

`Destination` Parametr jest używany do wskazania, że klucz jest chroniony oprogramowania. Po pomyślnym utworzeniu klucza, polecenie wyświetla szczegółowe informacje o nowo utworzony klucz.

**Dane wyjściowe**

![Nowy klucz](media/azure-stack-key-vault-manage-powershell/image5.png)

Nowo utworzony klucz może teraz odwoływać za pomocą jego identyfikatora URI. Jeśli tworzysz lub importowania klucza, który ma takiej samej nazwie jak istniejący klucz oryginalny klucz jest aktualizowana wartości określone w nowy klucz. Dostęp z poprzedniej wersji, korzystając z identyfikatora URI specyficzny dla wersji klucza. Na przykład:

* Użyj `https://vault10.vault.local.azurestack.external:443/keys/key01` można zawsze uzyskać bieżącą wersję.
* Użyj `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` uzyskać tę konkretną wersję.

### <a name="get-a-key"></a>Pobieranie klucza

Użyj **Get-AzureKeyVaultKey** polecenia cmdlet można odczytać klucza i jego szczegóły.

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Utwórz klucz tajny

Użyj **Set-AzureKeyVaultSecret** polecenia cmdlet, aby utworzyć lub zaktualizować wpisu tajnego w magazynie. Klucz tajny jest tworzony, jeśli już nie istnieje. Nowa wersja klucza tajnego jest tworzony, jeśli już istnieje.

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**Dane wyjściowe**

![Utwórz klucz tajny](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Pobierz klucz tajny

Użyj **Get-AzureKeyVaultSecret** polecenia cmdlet w celu odczytu wpisu tajnego w magazynie kluczy. To polecenie może zwrócić wszystkich lub określonych wersji wpisu tajnego.

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Po utworzeniu kluczy i wpisów tajnych można autoryzować zewnętrznych aplikacji, aby umożliwić ich używanie.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autoryzuj aplikację do korzystania z klucza lub wpisu tajnego

Użyj **Set-AzureRmKeyVaultAccessPolicy** polecenia cmdlet, aby autoryzować aplikację, aby uzyskać dostęp do klucza lub klucza tajnego w magazynie kluczy. W poniższym przykładzie nazwa magazynu jest `ContosoKeyVault`, i identyfikator klienta aplikacji, którą chcesz autoryzować `8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed`. Aby autoryzować aplikację, uruchom następujące polecenie. Opcjonalnie można określić `PermissionsToKeys` parametru, aby ustawić uprawnienia dla użytkownika, aplikacji lub grupy zabezpieczeń.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Jeśli chcesz zezwolić tej samej aplikacji na odczytywanie wpisów tajnych w magazynie, uruchom następujące polecenie cmdlet:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie maszyny Wirtualnej przy użyciu hasła przechowywane w usłudze Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Wdrażanie maszyny Wirtualnej przy użyciu certyfikatu przechowywanego w usłudze Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
