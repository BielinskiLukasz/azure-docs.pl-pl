---
title: Użyj pakietu cloud-init do uruchomienia skryptu powłoki systemowej na maszynie wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak używać pakietu cloud-init do uruchomienia skryptu powłoki systemowej na maszynie Wirtualnej z systemem Linux podczas tworzenia przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 8e8950cbd7927cb6b0543866ab976b550c9ec043
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959550"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Użyj pakietu cloud-init do uruchomienia skryptu powłoki systemowej na maszynie Wirtualnej z systemem Linux na platformie Azure
W tym artykule dowiesz się, jak używać [pakietu cloud-init](https://cloudinit.readthedocs.io) uruchamianie istniejącego skryptu powłoki systemowej na maszynie wirtualnej systemu Linux (VM) lub zestawów skalowania maszyn wirtualnych (zestawu skalowania maszyn wirtualnych) na inicjowanie obsługi administracyjnej czas na platformie Azure. Skrypty te pakietu cloud-init są uruchamiane podczas pierwszego rozruchu po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji o tym, jak pakietu cloud-init działa natywnie na platformie Azure i obsługiwane dystrybucje systemu Linux, zobacz [Omówienie pakietu cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Uruchom skrypt powłoki bash przy użyciu pakietu cloud-init
Za pomocą pakietu cloud-init jest konieczne konwertować istniejące skrypty konfiguracji chmury pakietu cloud-init akceptuje wiele typów danych wejściowych, z których jedna jest skrypt powłoki bash.

Jeśli korzystasz z rozszerzenia niestandardowego skryptu platformy Azure Linux można uruchamiać skrypty, można migrować je do korzystania z pakietu cloud-init. Jednak rozszerzenia Azure zintegrowano zgłoszenie alertu, błędy skryptu, wdrożenia obrazu pakietu cloud-init zakończy się niepowodzeniem, jeśli skrypt zakończy się niepowodzeniem.

Aby wyświetlić tę funkcję w działaniu, utworzyć skrypt powłoki bash proste do testowania. Pakiet cloud-init, takich jak `#cloud-config` plik, ten skrypt musi być lokalny, na którym zostanie uruchomiona polecenia codziennych, aby aprowizować maszynę wirtualną.  W tym przykładzie należy utworzyć plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor simple_bash.sh`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego pakietu cloud-init szczególnie pierwszy wiersz.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Przed wdrożeniem tego obrazu, należy utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę Wirtualną z [tworzenie az vm](/cli/azure/vm#az_vm_create) i określ plik skryptu powłoki bash, za pomocą `--custom-data simple_bash.sh` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Sprawdź skrypt powłoki bash zostało uruchomione.
Protokół SSH z publicznego adresu IP maszyny Wirtualnej w danych wyjściowych z poprzedniego polecenia. Wprowadź własne **publicznego adresu IP** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Zmień **tmp** katalogu i sprawdź plik myScript.txt istnieje i ma odpowiedni tekst wewnątrz niej.  Jeśli nie, możesz sprawdzić **/var/log/cloud-init.log** Aby uzyskać więcej informacji.  Wyszukaj następujący wpis:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe pakietu cloud-init zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety podczas pierwszego rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizować pliki konfiguracji i wstawić kluczy](tutorial-automate-vm-deployment.md)