---
title: Tworzenie maszyn wirtualnych w programie DevTest Labs i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać Azure DevTest Labs do tworzenia maszyn wirtualnych i zarządzania nimi za pomocą interfejsu wiersza polecenia platformy Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167053"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Tworzenie maszyn wirtualnych i zarządzanie nimi za pomocą DevTest Labs przy użyciu interfejsu wiersza polecenia platformy Azure
Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia, uruchamiania, łączenia, aktualizowania i czyszczenia maszyny deweloperskiej w laboratorium. 

Przed rozpoczęciem:

* Jeśli laboratorium nie zostało utworzone, instrukcje można znaleźć [tutaj](devtest-lab-create-lab.md).

* [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Aby rozpocząć, uruchom polecenie AZ login, aby utworzyć połączenie z platformą Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Utwórz i sprawdź maszynę wirtualną 
Przed wykonaniem poleceń związanych z DevTest Labs ustaw odpowiedni kontekst platformy Azure przy użyciu polecenia `az account set`:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Polecenie do tworzenia maszyny wirtualnej: `az lab vm create`. Wymagana jest Grupa zasobów dla laboratorium, nazwy laboratorium i nazwy maszyny wirtualnej. Pozostałe argumenty zmieniają się w zależności od typu maszyny wirtualnej.

Poniższe polecenie tworzy obraz oparty na systemie Windows z miejsca na platformie Azure. Nazwa obrazu jest taka sama, jak podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Następujące polecenie tworzy maszynę wirtualną na podstawie obrazu niestandardowego dostępnego w laboratorium:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Argument **typu obrazu** został zmieniony z **galerii** na **niestandardowy**. Nazwa obrazu jest zgodna z informacjami wyświetlanymi w przypadku utworzenia maszyny wirtualnej w Azure Portal.

Następujące polecenie tworzy maszynę wirtualną na podstawie obrazu z witryny Marketplace z uwierzytelnianiem SSH:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Można również tworzyć maszyny wirtualne na podstawie formuł, ustawiając parametr **typu obrazu** na **formułę**. Jeśli musisz wybrać konkretną sieć wirtualną dla swojej maszyny wirtualnej, użyj parametrów nazwa sieci **wirtualnej** i **podsieci** . Aby uzyskać więcej informacji, zobacz [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Sprawdź, czy maszyna wirtualna jest dostępna.
Użyj `az lab vm show` polecenia, aby sprawdzić, czy maszyna wirtualna jest dostępna przed rozpoczęciem i nawiązać z nią połączenia. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Uruchamianie i nawiązywanie połączenia z maszyną wirtualną
Następujące przykładowe polecenie uruchamia maszynę wirtualną:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Połącz się z maszyną wirtualną: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) lub [pulpit zdalny](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizowanie maszyny wirtualnej
Następujące przykładowe polecenie stosuje artefakty do maszyny wirtualnej:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Lista artefaktów dostępnych w laboratorium

Aby wyświetlić listę artefaktów dostępnych na maszynie wirtualnej w laboratorium, uruchom następujące polecenia.

**Cloud Shell — PowerShell**: Zwróć uwagę na użycie znacznika sprzed (\`) przed $expand $ in (tj. "$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell-bash**: Zauważ użycie znaku ukośnika (\\) przed $ w poleceniu. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Przykładowe dane wyjściowe: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Zatrzymaj i Usuń maszynę wirtualną    
Następujące przykładowe polecenie zatrzyma maszynę wirtualną.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Usuń maszynę wirtualną.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującą zawartością: [Dokumentacja interfejsu wiersza polecenia platformy Azure dla Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
