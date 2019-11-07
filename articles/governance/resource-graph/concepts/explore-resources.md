---
title: Eksplorowanie zasobów platformy Azure
description: Dowiedz się, jak używać języka zapytań grafu zasobów do eksplorowania zasobów i dowiedzieć się, jak są one połączone.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 58eb5abc9a8857b81ada65c96eb7deaaa5cc5aeb
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622661"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Badanie zasobów platformy Azure przy użyciu usługi Resources Graph

Usługa Azure Resource Graph umożliwia szybkie eksplorowanie i odnajdywanie zasobów platformy Azure na dużą skalę. Zaprojektowana w celu uzyskania szybkich odpowiedzi to świetny sposób poznania środowiska oraz właściwości, które tworzą zasoby platformy Azure.

## <a name="explore-virtual-machines"></a>Eksplorowanie maszyn wirtualnych

Typowym zasobem na platformie Azure jest maszyna wirtualna. Jako typ zasobu maszyny wirtualne mają wiele właściwości, które mogą być badane. Każda właściwość zawiera opcję filtrowania lub znajdowania dokładnie szukanego zasobu.

### <a name="virtual-machine-discovery"></a>Odnajdywanie maszyn wirtualnych

Zacznijmy od prostego zapytania, aby uzyskać pojedynczą maszynę wirtualną z naszego środowiska i przyjrzeć się zwracanym właściwościom.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Polecenie cmdlet Azure PowerShell `Search-AzGraph` domyślnie zwraca **parametr PSCustomObject** . Aby dane wyjściowe wyglądały tak samo jak zwracane przez interfejs wiersza polecenia platformy Azure, jest używane polecenie cmdlet `ConvertTo-Json`. Wartość domyślna dla **głębokości** wynosi _2_. Ustawienie go na _100_ powinno spowodować przekonwertowanie wszystkich zwracanych poziomów.

Wyniki JSON są strukturalne podobne do poniższego przykładu:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Właściwości te zawierają dodatkowe informacje dotyczące samego zasobu maszyny wirtualnej, wszystkich elementów SKU, systemów operacyjnych, dysków, tagów i grup zasobów oraz subskrypcji, do których należy.

### <a name="virtual-machines-by-location"></a>Maszyny wirtualne według lokalizacji

Biorąc pod uwagę informacje o zasobach maszyn wirtualnych, użyjmy właściwości **Location** , aby policzyć wszystkie maszyny wirtualne według lokalizacji. Aby zaktualizować zapytanie, usuniemy limit i podsumowuje liczbę wartości lokalizacji.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Wyniki JSON są strukturalne podobne do poniższego przykładu:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Teraz możemy sprawdzić liczbę maszyn wirtualnych w każdym regionie świadczenia usługi Azure.

### <a name="virtual-machines-by-sku"></a>Maszyny wirtualne według jednostki SKU

Powracając do właściwości oryginalnej maszyny wirtualnej, spróbuj znaleźć wszystkie maszyny wirtualne o rozmiarze SKU **Standard_B2s**. Po zwróconym kodzie JSON zobaczymy, że jest on przechowywany w **właściwości. — obiekt hardwareprofile. VmSize**. Zaktualizujemy zapytanie, aby znaleźć wszystkie maszyny wirtualne, które pasują do tego rozmiaru i zwracają tylko nazwę maszyny wirtualnej i regionu.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Maszyny wirtualne połączone z dyskami zarządzanymi w warstwie Premium

Jeśli chcemy uzyskać szczegóły dotyczące dysków zarządzanych w warstwie Premium, które są dołączone do tych maszyn wirtualnych **Standard_B2s** , można rozszerzyć zapytanie w celu przekazania nam identyfikatora zasobu dla tych dysków zarządzanych.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Innym sposobem uzyskania jednostki SKU byłby użycie właściwości **aliass** **Microsoft. COMPUTE/virtualMachines/SKU. Name**. Zapoznaj się z przykładami [Pokaż aliasy](../samples/starter.md#show-aliases) i [Pokaż różne wartości aliasów](../samples/starter.md#distinct-alias-values) .

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Wynikiem jest lista identyfikatorów dysków.

### <a name="managed-disk-discovery"></a>Odnajdywanie dysku zarządzanego

W przypadku pierwszego rekordu z poprzedniego zapytania będziemy eksplorować właściwości istniejące na dysku zarządzanym, który został dołączony do pierwszej maszyny wirtualnej. Zaktualizowane zapytanie używa identyfikatora dysku i zmienia typ.

Przykład danych wyjściowych z poprzedniego zapytania na przykład:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Przed uruchomieniem zapytania, w jaki sposób wiemy, że **Typ** powinien być teraz **Microsoft. COMPUTE/disks**?
Jeśli zobaczysz pełny identyfikator, zobaczysz **/providers/Microsoft.COMPUTE/disks/** jako część ciągu. Ten fragment ciągu zawiera wskazówkę dotyczącą typu wyszukiwania. Alternatywną metodą jest usunięcie limitu według typu i wyszukanie tylko według pola identyfikatora. Ponieważ identyfikator jest unikatowy, zwracany jest tylko jeden rekord i Właściwość **Type** na nim zawiera te szczegóły.

> [!NOTE]
> Aby ten przykład działał, należy zamienić pole ID na wynik z własnego środowiska.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Wyniki JSON są strukturalne podobne do poniższego przykładu:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Eksplorowanie maszyn wirtualnych w celu znalezienia publicznych adresów IP

Ten zestaw zapytań najpierw odnajduje i przechowuje wszystkie zasoby interfejsów sieciowych podłączonych do maszyn wirtualnych. Następnie zapytania używają listy kart sieciowych, aby znaleźć każdy zasób adresu IP, który jest publicznym adresem IP i przechowywać te wartości. Na koniec zapytania zawierają listę publicznych adresów IP.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Użyj pliku (interfejsu wiersza polecenia platformy Azure) lub zmiennej (Azure PowerShell) w następnym zapytaniu, aby uzyskać szczegółowe informacje o zasobach związanych z interfejsem sieciowym, w których istnieje Publiczny adres IP dołączony do karty sieciowej.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Na koniec Użyj listy zasobów publicznego adresu IP przechowywanych w pliku (interfejsu wiersza polecenia platformy Azure) lub zmiennej (Azure PowerShell), aby uzyskać rzeczywisty publiczny adres IP z pokrewnego obiektu i wyświetlić.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Aby dowiedzieć się, jak wykonać te kroki w pojedynczym zapytaniu z operatorem `join`, zapoznaj się z [listą maszyn wirtualnych z ich interfejsem sieciowym i przykładem publicznego adresu IP](../samples/advanced.md#join-vmpip) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku zapytań](query-language.md).
- Zobacz język używany w [zapytaniach początkowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](../samples/advanced.md).