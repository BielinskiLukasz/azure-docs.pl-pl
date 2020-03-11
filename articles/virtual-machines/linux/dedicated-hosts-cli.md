---
title: Wdrażanie maszyn wirtualnych z systemem Linux na dedykowanych hostach przy użyciu interfejsu wiersza polecenia
description: Wdróż maszyny wirtualne na dedykowanych hostach przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: f712d1be76a9bb3dc4856b9f1fa7c7b805296dea
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970768"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach przy użyciu interfejsu wiersza polecenia platformy Azure
 

W tym artykule opisano sposób tworzenia [dedykowanego hosta](dedicated-hosts.md) platformy Azure do hostowania maszyn wirtualnych. 

Upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.70 lub nowszej oraz że zalogowano się na koncie platformy Azure przy użyciu `az login`. 


## <a name="limitations"></a>Ograniczenia

- Zestawy skalowania maszyn wirtualnych nie są obecnie obsługiwane na dedykowanych hostach.
- Początkowa wersja obsługuje następujące serie maszyn wirtualnych: DSv3, ESv3, FSv2, LSv2 i MSv2. 
 

## <a name="create-resource-group"></a>Tworzenie grupy zasobów 
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą AZ Group Create. Poniższy przykład tworzy grupę zasobów o nazwie *myDHResourceGroup* w lokalizacji *Wschodnie stany USA* .

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Tworzenie grupy hostów 

**Grupa hostów** jest zasobem, który reprezentuje kolekcję dedykowanych hostów. Można utworzyć grupę hostów w regionie i strefie dostępności, a następnie dodać do niej hosty. W przypadku planowania wysokiej dostępności dostępne są dodatkowe opcje. W przypadku dedykowanych hostów można użyć jednej lub obu następujących opcji: 
- Obejmuje wiele stref dostępności. W takim przypadku konieczne jest posiadanie grupy hostów w każdej ze stref, które mają być używane.
- Obejmuje wiele domen błędów, które są mapowane na stojaki fizyczne. 
 
W obu przypadkach należy podać liczbę domen błędów dla grupy hostów. Jeśli nie chcesz obejmować domen błędów w grupie, użyj liczby domen błędów wynoszącej 1. 

Możesz również zdecydować się na korzystanie ze stref dostępności i domen błędów. 

W tym przykładzie użyjemy polecenia [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) , aby utworzyć grupę hostów przy użyciu stref dostępności i domen błędów. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Inne przykłady

Można również użyć [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) , aby utworzyć grupę hostów w strefie dostępności 1 (i bez domen błędów).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Poniższe zastosowania: [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) , aby utworzyć grupę hostów przy użyciu tylko domen błędów (do użycia w regionach, w których strefy dostępności nie są obsługiwane). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Tworzenie hosta 

Teraz Utwórzmy dedykowanego hosta w grupie hostów. Oprócz nazwy hosta, należy podać jednostkę SKU dla hosta. Jednostka SKU hosta przechwytuje obsługiwaną serię maszyn wirtualnych oraz generowanie sprzętu dla dedykowanego hosta.  Obsługiwane są następujące wartości jednostki SKU: DSv3_Type1 i ESv3_Type1.

Aby uzyskać więcej informacji o jednostkach SKU i cenach hosta, zobacz [Cennik dedykowanego hosta platformy Azure](https://aka.ms/ADHPricing).

Użyj [AZ VM Host Create](/cli/azure/vm/host#az-vm-host-create) , aby utworzyć hosta. Jeśli ustawisz liczbę domen błędów dla grupy hostów, zostanie wyświetlony monit o określenie domeny błędów dla hosta.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej 
Utwórz maszynę wirtualną na dedykowanym hoście za pomocą polecenia [AZ VM Create](/cli/azure/vm#az-vm-create). Jeśli podczas tworzenia grupy hostów została określona strefa dostępności, należy użyć tej samej strefy podczas tworzenia maszyny wirtualnej.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Jeśli utworzono maszynę wirtualną na hoście, który nie ma wystarczającej ilości zasobów, maszyna wirtualna zostanie utworzona w stanie niepowodzenia. 


## <a name="check-the-status-of-the-host"></a>Sprawdź stan hosta

Można sprawdzić stan kondycji hosta oraz liczbę maszyn wirtualnych, które można wdrożyć na hoście za pomocą polecenia [AZ VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Dane wyjściowe będą wyglądać podobnie do tego:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Eksportowanie jako szablon 
Możesz wyeksportować szablon, jeśli chcesz teraz utworzyć dodatkowe środowisko programistyczne z tymi samymi parametrami lub środowiskiem produkcyjnym, które je dopasowuje. Menedżer zasobów używa szablonów JSON, które definiują wszystkie parametry środowiska. Wszystkie środowiska można tworzyć, odwołując się do tego szablonu JSON. Szablony JSON można kompilować ręcznie lub wyeksportować istniejące środowisko, aby utworzyć szablon JSON. Użyj [AZ Group Export](/cli/azure/group#az-group-export) , aby wyeksportować grupę zasobów.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

To polecenie tworzy plik `myDHResourceGroup.json` w bieżącym katalogu roboczym. Po utworzeniu środowiska na podstawie tego szablonu zostanie wyświetlony monit o podanie wszystkich nazw zasobów. Można wypełnić te nazwy w pliku szablonu, dodając parametr `--include-parameter-default-value` do polecenia `az group export`. Edytuj szablon JSON, aby określić nazwy zasobów, lub Utwórz plik Parameters. JSON, który określa nazwy zasobów.
 
Aby utworzyć środowisko na podstawie szablonu, użyj [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Czyszczenie 

Opłaty są naliczane za dedykowane hosty nawet wtedy, gdy nie wdrożono żadnych maszyn wirtualnych. Należy usunąć wszystkie hosty, których obecnie nie używasz, aby zaoszczędzić koszty.  

Hosta można usunąć tylko wtedy, gdy nie ma dłużej używanych maszyn wirtualnych. Usuń maszyny wirtualne za pomocą polecenia [AZ VM Delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Po usunięciu maszyn wirtualnych można usunąć hosta za pomocą polecenia [AZ VM Host Delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Po usunięciu wszystkich hostów można usunąć grupę hostów za pomocą polecenia [AZ VM Host Group Delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Możesz również usunąć całą grupę zasobów w pojedynczym poleceniu. Spowoduje to usunięcie wszystkich zasobów utworzonych w grupie, w tym wszystkich maszyn wirtualnych, hostów i grup hostów.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz Omówienie [dedykowanych hostów](dedicated-hosts.md) .

- Możesz również utworzyć dedykowane hosty przy użyciu [Azure Portal](dedicated-hosts-portal.md).

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.