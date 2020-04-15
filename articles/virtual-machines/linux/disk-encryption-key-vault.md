---
title: Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption
description: Ten artykuł zawiera kroki dotyczące tworzenia i konfigurowania magazynu kluczy do użycia z szyfrowaniem dysku azure
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5d9acb525f35da756a986826574082f1ecafedf5
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314116"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption

Usługa Azure Disk Encryption używa usługi Azure Key Vault do kontrolowania kluczy i wpisów tajnych szyfrowania dysków i zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Wprowadzenie do usługi Azure Key Vault](../../key-vault/key-vault-get-started.md) i [zabezpieczanie magazynu kluczy](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Jeśli wcześniej używano szyfrowania dysków platformy Azure z usługą Azure AD do szyfrowania maszyny wirtualnej, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja),](disk-encryption-key-vault-aad.md) aby uzyskać szczegółowe informacje.

Tworzenie i konfigurowanie magazynu kluczy do użycia za pomocą szyfrowania dysków platformy Azure obejmuje trzy kroki:

1. W razie potrzeby tworzenie grupy zasobów.
2. Tworzenie magazynu kluczy. 
3. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.

Te kroki są zilustrowane w następujących przewodnikach Szybki start:

- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu programu Azure PowerShell](disk-encryption-cli-quickstart.md)

Możesz również, jeśli chcesz, wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

> [!Note]
> Kroki opisane w tym artykule są zautomatyzowane w [skrypcie interfejsu wiersza polecenia szyfrowania dysków platformy Azure](https://github.com/ejarvi/ade-cli-getting-started) i [skrypcie wstępnym szyfrowania dysków platformy Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i łączenie się z platformą Azure

Kroki opisane w tym artykule można wykonać za pomocą [interfejsu wiersza polecenia platformy Azure,](/cli/azure/) [modułu Azure PowerShell Az](/powershell/azure/overview)lub [portalu Azure.](https://portal.azure.com) 

Gdy portal jest dostępny za pośrednictwem przeglądarki, interfejs wiersza polecenia platformy Azure i usługi Azure PowerShell wymagają instalacji lokalnej; Zobacz [Azure Disk Encryption for Linux: Zainstaluj narzędzia, aby](disk-encryption-linux.md#install-tools-and-connect-to-azure) uzyskać szczegółowe informacje.

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Przed użyciem interfejsu wiersza polecenia platformy Azure lub usługi Azure PowerShell należy najpierw połączyć się z subskrypcją platformy Azure. Można to zrobić, [logując się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Logując się za pomocą usługi Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)lub podajesz poświadczenia do witryny Azure portal po wyświetleniu monitu.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 ```
 
## Next steps

- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Learn [Azure Disk Encryption scenarios on Linux VMs](disk-encryption-linux.md)
- Learn how to [troubleshoot Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Read the [Azure Disk Encryption sample scripts](disk-encryption-sample-scripts.md)
