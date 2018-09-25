---
title: Szyfrowanie dysków dla zestawów skalowania platformy Azure przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do szyfrowania wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyn wirtualnych systemu Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: fecf7e121b1c0404f1ec67d46cc88008250b9d68
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981063"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-preview"></a>Szyfrowanie systemu operacyjnego i dołączonych dysków z danymi w maszynie wirtualnej zestawu skalowania przy użyciu wiersza polecenia platformy Azure (wersja zapoznawcza)

Aby chronić pliki i chronić dane magazynowane przy użyciu technologii szyfrowania standardowych w branży, zestawy skalowania maszyn wirtualnych obsługują szyfrowania dysków Azure (ADE). Szyfrowanie można włączyć dla maszyny wirtualnej systemu Linux i Windows zestawów skalowania. Aby uzyskać więcej informacji, zobacz [usługi Azure Disk Encryption dla systemu Linux i Windows](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Usługa Azure disk encryption dla zestawów skalowania maszyn wirtualnych jest obecnie w publicznej wersji zapoznawczej dostępne we wszystkich publicznych regionach platformy Azure.

Usługa Azure disk encryption jest obsługiwane:
- Skalowanie zestawów utworzonych za pomocą dysków zarządzanych i nie jest obsługiwane dla zestawów skalowania dysku natywnego (lub niezarządzanego).
- w przypadku woluminów systemu operacyjnego i danych w zestawach skalowania Windows. Wyłącz szyfrowanie jest obsługiwane w przypadku woluminów systemu operacyjnego i danych dla zestawów skalowania Windows.
- woluminy danych w zestawach skalowania systemu Linux. Szyfrowanie dysku systemu operacyjnego nie jest obsługiwane w bieżącej wersji zapoznawczej dla zestawów skalowania systemu Linux.

Zestaw maszyn wirtualnych odtworzenia z obrazu i uaktualniania, operacje skalowania nie są obsługiwane w bieżącej wersji zapoznawczej. Usługa Azure disk encryption dla wersji zapoznawczej zestawów skalowania maszyn wirtualnych jest zalecane tylko w środowiskach testowych. W wersji zapoznawczej nie włączaj szyfrowania dysków w środowiskach produkcyjnych, w których konieczne może być uaktualnienia obrazu systemu operacyjnego w zestawie skalowania zaszyfrowane.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten samouczek wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="register-for-disk-encryption-preview"></a>Rejestrowanie na potrzeby szyfrowania dysku w wersji zapoznawczej

Usługa Azure disk encryption dla zestawów skalowania maszyn wirtualnych w wersji zapoznawczej, musisz zarejestrować się samodzielnie subskrypcji za pomocą [az feature register](/cli/azure/feature#az_feature_register). Wystarczy użycie funkcji w wersji zapoznawczej szyfrowania dysku po raz pierwszy należy wykonać następujące czynności:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Może upłynąć do 10 minut dla żądania rejestrowania do propagowania. Można sprawdzić stanu rejestracji za pomocą [az feature show](/cli/azure/feature#az_feature_show). Gdy `State` raporty *zarejestrowanej*, ponownie zarejestrować *Mirosoft.Compute* dostawcy o [az provider register](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss#az_vmss_create). Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet*, który jest skonfigurowany do automatycznej aktualizacji w miarę stosowania zmian, a następnie generuje klucze SSH, jeśli nie istnieją, w lokalizacji *~/.ssh/id_rsa*. 32Gb dysku danych jest dołączony do każdego wystąpienia maszyny Wirtualnej i Azure [rozszerzenia niestandardowego skryptu](../virtual-machines/linux/extensions-customscript.md) służy do przygotowywania dysków z danymi za pomocą [az vmss rozszerzenia zestawu](/cli/azure/vmss/extension#az_vmss_extension_set):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie usługi Azure key vault umożliwia szyfrowania dysków

Usługa Azure Key Vault można przechowywać klucze, wpisy tajne lub haseł, które umożliwiają bezpieczne wdrożenie ich w aplikacji i usług. Klucze szyfrowania są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane zgodnych ze standardami FIPS 140-2 poziom 2 standardy. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania dyski wirtualne dołączone do maszyny Wirtualnej. Zachowanie kontroli nad te klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie.

Zdefiniuj własny unikatowy *keyvault_name*. Następnie należy utworzyć magazyn kluczy przy użyciu [tworzenie az keyvault](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) w tej samej subskrypcji i regionu skali ustawić i *— włączone for szyfrowania dysków* zasady dostępu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Użyj istniejącego magazynu kluczy

Ten krok jest tylko wymagane, jeśli masz istniejące usługi Key Vault, którą chcesz za pomocą szyfrowania dysków. Pomiń ten krok, jeśli magazyn kluczy został utworzony w poprzedniej sekcji.

Zdefiniuj własny unikatowy *keyvault_name*. Następnie zaktualizowane Twojego magazynu kluczy przy użyciu [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) i ustaw *— włączone for szyfrowania dysków* zasady dostępu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Włącz szyfrowanie

Aby zaszyfrować wystąpień maszyn wirtualnych w zestawie skalowania, należy najpierw uzyskać pewne informacje o identyfikator zasobu usługi Key Vault przy użyciu [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Te zmienne są używane do następnie rozpocznij proces szyfrowania za pomocą [Włącz az vmss szyfrowania](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Może zająć minutę lub dwie do rozpoczęcia procesu szyfrowania.

Ponieważ zestaw skalowania jest zasad uaktualniania na zestawu skalowania utworzonego w poprzednim kroku jest ustawiona na *automatyczne*, wystąpień maszyn wirtualnych automatycznie rozpocząć proces szyfrowania. W zestawach skalowania, gdzie jest zasad uaktualniania na ręczny, należy uruchomić zasady szyfrowania na wystąpieniach maszyn wirtualnych za pomocą [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

## <a name="check-encryption-progress"></a>Sprawdzanie postępu szyfrowania

Aby sprawdzić stan szyfrowania dysku, należy użyć [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

W przypadku wystąpień maszyn wirtualnych są szyfrowane, kod stanu raporty *EncryptionState/zaszyfrowane*, jak pokazano w następujących przykładowych danych wyjściowych:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Wyłącz szyfrowanie

Jeśli nie chcesz już korzystać z zaszyfrowanych dysków wystąpień maszyny Wirtualnej, można wyłączyć szyfrowania przy użyciu [az vmss szyfrowanie wyłączyć](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) w następujący sposób:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wiersza polecenia platformy Azure jest używany do szyfrowania zestawu skalowania maszyn wirtualnych. Można również użyć [programu Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) lub szablonów dla [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) lub [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

Przykład pliku wsadowego end-to-end do szyfrowania dysku danych zestaw skalowania systemu Linux można znaleźć [tutaj](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). W tym przykładzie tworzy grupę zasobów, zestaw skalowania systemu Linux, instaluje dysk 5 GB danych oraz szyfruje zestawu skalowania maszyn wirtualnych.