---
title: Konfigurowanie usługi Key Vault
description: Jak skonfigurować Key Vault do użycia z maszyną wirtualną.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: e4bff4d1826d9586495207095eccf8f6c66164a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81870008"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie Key Vault dla maszyn wirtualnych w programie Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

W Azure Resource Manager stosy klucze tajne/certyfikaty są modelowane jako zasoby dostarczone przez dostawcę zasobów Key Vault. Aby dowiedzieć się więcej na temat Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Aby Key Vault były używane z maszynami wirtualnymi Azure Resource Manager, właściwość **EnabledForDeployment** w Key Vault musi mieć wartość true. Można to zrobić na różnych klientach.
> 2. Key Vault należy utworzyć w tej samej subskrypcji i lokalizacji co maszyna wirtualna.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Konfigurowanie Key Vault przy użyciu programu PowerShell
Aby utworzyć magazyn kluczy przy użyciu programu PowerShell, zobacz [Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu programu PowerShell](../../key-vault/secrets/quick-create-powershell.md).

W przypadku nowych magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

W przypadku istniejących magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Użyj interfejsu wiersza polecenia, aby skonfigurować Key Vault
Aby utworzyć magazyn kluczy za pomocą interfejsu wiersza polecenia (CLI), zobacz [Manage Key Vault using CLI](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

W przypadku interfejsu wiersza polecenia należy utworzyć magazyn kluczy przed przypisaniem zasad wdrażania. Możesz to zrobić za pomocą następującego polecenia:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Następnie aby włączyć Key Vault do użycia z wdrożeniem szablonów, uruchom następujące polecenie:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Użyj szablonów, aby skonfigurować Key Vault
Gdy używasz szablonu, musisz ustawić `enabledForDeployment` Właściwość na `true` dla zasobu Key Vault.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Aby poznać inne opcje, które można skonfigurować podczas tworzenia magazynu kluczy za pomocą szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
