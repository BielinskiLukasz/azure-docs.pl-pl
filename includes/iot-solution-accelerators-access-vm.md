---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: db1af4f046bd8849fddee299e949d6edbdaae86a
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609093"
---
## <a name="access-the-virtual-machine"></a>Dostęp do maszyny wirtualnej

Następujące kroki użycia `az` polecenia w usłudze Azure Cloud Shell. Jeśli wolisz, możesz to zrobić [instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) na rozwój swojej maszyny, a następnie uruchom polecenia lokalnie.

Poniższe kroki pokazują, jak skonfigurować maszyny wirtualnej platformy Azure, aby umożliwić **SSH** dostępu. Pokazano założono w nim wybrana dla akceleratora rozwiązań jest nazwa **symulacji contoso** — Zastąp tę wartość o nazwie wdrożenia:

1. Wyświetl zawartość grupy zasobów, która zawiera zasoby akceleratora rozwiązań:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Zanotuj nazwę maszyny wirtualnej, publiczny adres IP i sieciową grupą zabezpieczeń — te wartości będą potrzebne później.

1. Zaktualizuj sieciową grupę zabezpieczeń, aby zezwolić na dostęp SSH. Poniższego polecenia założono, Nazwa sieciowa grupa zabezpieczeń jest **contoso symulacji nsg** — Zastąp tę wartość na nazwę sieciową grupę zabezpieczeń:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Dostęp SSH należy włączyć tylko podczas projektowania i testowania. Po włączeniu protokołu SSH, [należy wyłączyć je ponownie tak szybko jak to możliwe](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Zaktualizuj hasło **azureuser** konta na maszynie wirtualnej, aby hasła znasz. Wybierz swoje własne hasło po uruchomieniu następującego polecenia:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Znajdź publiczny adres IP swojej maszyny wirtualnej. Poniższego polecenia założono, Nazwa maszyny wirtualnej jest **vikxv maszyny wirtualnej** — Zastąp tę wartość o nazwie maszyny wirtualnej, należy zanotować wcześniej:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Zanotuj publiczny adres IP swojej maszyny wirtualnej.
