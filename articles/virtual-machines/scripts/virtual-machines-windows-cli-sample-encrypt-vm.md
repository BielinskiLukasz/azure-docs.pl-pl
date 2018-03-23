---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — szyfrowanie maszyny wirtualnej z systemem Windows | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — szyfrowanie maszyny wirtualnej z systemem Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 9d7b2b0c731c5fc820fe649394a13ee586e47606
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
---
# <a name="encrypt-a-windows-virtual-machine-in-azure"></a>Szyfrowanie maszyny wirtualnej z systemem Windows na platformie Azure

Ten skrypt tworzy bezpieczny magazyn Azure Key Vault, klucze szyfrowania, jednostkę usługi Azure Active Directory i maszynę wirtualną z systemem Windows. Maszyna wirtualna jest następnie szyfrowana przy użyciu klucza szyfrowania z magazynu Key Vault i poświadczeń jednostki usługi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_windows_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, magazynu Azure Key Vault, jednostki usługi, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Tworzy magazyn Azure Key Vault do przechowywania zabezpieczonych danych, takich jak klucze szyfrowania. |
| [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key#az_keyvault_key_create) | Tworzy klucz szyfrowania w magazynie Key Vault. |
| [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp#az_ad_sp_create_for_rbac) | Tworzy jednostkę usługi Azure Active Directory używaną do bezpiecznego uwierzytelniania i sterowania dostępem do kluczy szyfrowania. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Ustawia uprawnienia w magazynie Key Vault w celu udzielenia jednostce usługi dostępu do kluczy szyfrowania. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az vm encryption enable](https://docs.microsoft.com/cli/azure/vm/encryption#az_vm_encryption_enable) | Włącza szyfrowanie na maszynie wirtualnej przy użyciu poświadczeń jednostki usługi i klucza szyfrowania. |
| [az vm encryption show](https://docs.microsoft.com/cli/azure/vm/encryption#az_vm_encryption_show) | Wyświetla stan procesu szyfrowania maszyny wirtualnej. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%windows%2ftoc.json).
