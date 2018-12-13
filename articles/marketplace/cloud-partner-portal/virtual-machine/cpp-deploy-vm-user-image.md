---
title: Wdróż Maszynę wirtualną platformy Azure przez użytkownika wirtualnego dysku twardego | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak wdrożyć obraz wirtualnego dysku twardego użytkownika w celu utworzenia wystąpienia maszyny Wirtualnej platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 9c163ddf7859246fcdaa28edfd4b598a24a32be2
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196945"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Wdróż Maszynę wirtualną platformy Azure przez użytkownika wirtualnego dysku twardego

W tym artykule opisano sposób wdrażania uogólnionego obrazu dysku VHD, aby utworzyć nowy zasób maszyny Wirtualnej platformy Azure przy użyciu dostarczonego szablonu usługi Azure Resource Manager i skrypt programu Azure PowerShell.


## <a name="vhd-deployment-template"></a>Szablon wdrożenia wirtualnego dysku twardego

Kopiowanie szablonu usługi Azure Resource Manager dla [wdrożenia wirtualnego dysku twardego](cpp-deploy-json-template.md) w lokalnym pliku o nazwie `VHDtoImage.json`.  Edytuj ten plik, aby podać wartości dla następujących parametrów. 

|  **Parametr**             |   **Opis**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Nazwa istniejącej grupy zasobów platformy Azure.  Zazwyczaj używa się tej samej grupie replikacji, skojarzone z Twoim magazynem kluczy  |
| TemplateFile               | Pełna nazwa ścieżki do pliku `VHDtoImage.json`                                    |
| userStorageAccountName     | Nazwa konta magazynu                                                    |
| sNameForPublicIP           | Nazwa DNS dla publicznego adresu IP. Muszą być małymi literami                                  |
| subscriptionId             | Identyfikator subskrypcji platformy Azure                                                  |
| Lokalizacja                   | Standardowa Azure lokalizacji geograficznej, grupy zasobów                       |
| vmName                     | Nazwa maszyny wirtualnej                                                    |
| VaultName                  | Nazwa magazynu kluczy                                                          |
| vaultResourceGroup         | Grupa zasobów magazynu kluczy
| certificateUrl             | Adres URL certyfikatu, w tym wersja przechowywanych w magazynie kluczy, na przykład:  https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7 |
| vhdUrl                     | Adres URL wirtualnego dysku twardego                                                   |
| vmSize                     | Rozmiar wystąpienia maszyny wirtualnej                                           |
| publicIPAddressName        | Nazwa publicznego adresu IP                                                  |
| virtualNetworkName         | Nazwa sieci wirtualnej                                                    |
| nicName                    | Nazwa karty sieciowej dla sieci wirtualnej                     |
| adminUserName              | Nazwa użytkownika konta administratora                                          |
| adminPassword              | Hasło administratora                                                          |
|  |  |


## <a name="powershell-script"></a>Skrypt programu PowerShell

Skopiuj i Edytuj następujący skrypt, aby dostarczać wartości `$storageaccount` i `$vhdUrl` zmiennych.  Wykonaj go, aby utworzyć zasób maszyny Wirtualnej platformy Azure z istniejącej uogólnionego wirtualnego dysku twardego.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Kolejne kroki

Po wdrożeniu maszyny Wirtualnej można przystąpić do [certyfikować swój obraz maszyny Wirtualnej](./cpp-certify-vm.md).
