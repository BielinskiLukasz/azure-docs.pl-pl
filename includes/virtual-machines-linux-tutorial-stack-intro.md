---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: b922b5ea225c61948240e40903ac43f56fde3fb5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227280"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). 

Następujący przykład umożliwia utworzenie maszyny wirtualnej o nazwie *myVM* i kluczy SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji kluczy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. To polecenie ustawia również nazwę *azureuser* jako nazwę użytkownika administratora. Użyjesz tej nazwy później, aby nawiązać połączenia z maszyną wirtualną. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po utworzeniu maszyny wirtualnej w interfejsie wiersza polecenia platformy Azure zostanie wyświetlona informacja podobna do następującej. Zwróć uwagę na element `publicIpAddress`. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w późniejszych krokach.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web 

Domyślnie dozwolone są tylko połączenia SSH z maszynami wirtualnymi z systemem Linux wdrożonymi na platformie Azure. Ponieważ ta maszyna wirtualna ma być serwerem sieci Web, port 80 należy otworzyć z Internetu. Otwórz odpowiedni port za pomocą polecenia [az vm open-port](/cli/azure/vm#az_vm_open_port).  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH


Jeśli nie znasz jeszcze publicznego adresu IP maszyny wirtualnej, uruchom polecenie [az network public-ip list](/cli/azure/network/public-ip#list). Ten adres IP będzie potrzebny do wykonania kilku późniejszych kroków.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Pamiętaj o wstawieniu prawidłowego publicznego adresu IP swojej maszyny wirtualnej. W tym przykładzie adres IP to *40.68.254.142*. Element *azureuser* to nazwa użytkownika administratora określona podczas tworzenia maszyny wirtualnej.

```bash
ssh azureuser@40.68.254.142
```

