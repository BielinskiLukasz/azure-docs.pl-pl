---
title: Pobieranie wirtualnego dysku twardego z systemem Linux z platformy Azure | Microsoft Docs
description: Pobierz dysk VHD z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 21720ead788a505f86c4d35eb5a39943ff4a2e12
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981000"
---
# <a name="download-a-linux-vhd-from-azure"></a>Pobieranie wirtualnego dysku twardego z systemem Linux z platformy Azure

W tym artykule dowiesz się, jak pobrać plik wirtualnego dysku twardego z systemem Linux z platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal. 

Jeśli jeszcze tego nie zrobiono, zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny wirtualnej. Musisz zatrzymać maszynę wirtualną, aby pobrać dysk VHD. Jeśli chcesz użyć dysku VHD jako [obrazu](tutorial-custom-images.md) do tworzenia innych maszyn wirtualnych z nowymi dyskami, musisz anulować obsługę administracyjną i uogólnić system operacyjny zawarty w pliku i zatrzymać maszynę wirtualną. Aby można było użyć wirtualnego dysku twardego dla nowego wystąpienia istniejącej maszyny wirtualnej lub dysku z danymi, należy zatrzymać i cofnąć alokację maszyny wirtualnej.

Aby użyć dysku VHD jako obrazu do tworzenia innych maszyn wirtualnych, wykonaj następujące kroki:

1. Użyj protokołu SSH, nazwy konta i publicznego adresu IP maszyny wirtualnej w celu nawiązania połączenia z serwerem i anulowania aprowizacji. Publiczny adres IP można znaleźć za pomocą [AZ Network Public-IP show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Parametr + User usuwa także ostatnio zainicjowane konto użytkownika. Jeśli masz poświadczenia konta pieczenia na maszynie wirtualnej, pozostaw ten parametr + użytkownika. Poniższy przykład usuwa ostatnio zainicjowane konto użytkownika:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Zaloguj się do konta platformy Azure przy użyciu [AZ login](https://docs.microsoft.com/cli/azure/reference-index).
3. Zatrzymaj i Cofnij przydział maszyny wirtualnej.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Uogólnij maszynę wirtualną. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Aby użyć dysku VHD jako dysku dla nowego wystąpienia istniejącej maszyny wirtualnej lub dysku danych, wykonaj następujące kroki:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Z menu po lewej stronie wybierz pozycję **Virtual Machines**.
3.  Z listy wybierz maszynę wirtualną.
4.  Na stronie maszyny wirtualnej wybierz pozycję **Zatrzymaj**.

    ![Zatrzymaj maszynę wirtualną](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generuj adres URL SAS

Aby pobrać plik VHD, należy wygenerować adres URL sygnatury [dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Po wygenerowaniu adresu URL zostanie do niego przypisany czas wygaśnięcia.

1.  W menu strony dla maszyny wirtualnej wybierz pozycję **dyski**.
2.  Wybierz dysk systemu operacyjnego dla maszyny wirtualnej, a następnie wybierz pozycję **eksport dysku**.
3.  Wybierz pozycję **Generuj adres URL**.

    ![Generuj adres URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Pobierz dysk VHD

1.  W obszarze wygenerowanego adresu URL wybierz pozycję **Pobierz plik VHD**.
**
    ![Pobierz dysk VHD](./media/download-vhd/export-download.png)

2.  Aby rozpocząć pobieranie, może być konieczne wybranie opcji **Zapisz** w przeglądarce. Domyślna nazwa pliku VHD jest *ABCD*.

    ![Wybierz pozycję Zapisz w przeglądarce](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przekazać i utworzyć maszynę wirtualną z systemem Linux z dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

