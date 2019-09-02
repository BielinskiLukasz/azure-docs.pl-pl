---
title: Konfiguracja sterowników procesora GPU platformy Azure dla systemu Windows | Microsoft Docs
description: Jak skonfigurować sterowniki NVIDIA GPU dla maszyn wirtualnych serii N z systemem Windows Server lub Windows na platformie Azure
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ebd34c20e3733971593344925dcb566a1bf912
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207365"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instalowanie sterowników NVIDIA GPU na maszynach wirtualnych serii N z systemem Windows 

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych serii N z systemem Windows, należy zainstalować sterowniki NVIDIA GPU. [Rozszerzenie sterownika NVIDIA GPU](../extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika graficznego procesora NVIDIA](../extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroków wdrażania.

W przypadku wybrania opcji ręcznego instalowania sterowników procesora GPU w tym artykule przedstawiono obsługiwane systemy operacyjne, sterowniki oraz czynności instalacyjne i weryfikacyjne. Informacje o ręcznej instalacji sterownika są również dostępne dla [maszyn wirtualnych z systemem Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby zapoznać się z podstawowymi danymi, pojemnościami magazynu i szczegółami dysku, zobacz [rozmiary maszyn wirtualnych w systemie Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalacja sterownika

1. Połącz się Pulpit zdalny z każdą maszyną wirtualną serii N.

2. Pobierz, Wyodrębnij i zainstaluj obsługiwany sterownik dla systemu operacyjnego Windows.

Po instalacji sterownika siatki na maszynie wirtualnej wymagane jest ponowne uruchomienie. Po zainstalowaniu sterownika CUDA ponowne uruchomienie nie jest wymagane.

## <a name="verify-driver-installation"></a>Weryfikuj instalację sterownika

Należy pamiętać, że Panel sterowania NVIDIA jest dostępny tylko w przypadku instalacji sterownika siatki. Jeśli zainstalowano sterowniki CUDA, Panel sterowania NVIDIA nie będzie widoczny.

Instalację sterownika można sprawdzić w Menedżer urządzeń. Poniższy przykład przedstawia pomyślne Konfigurowanie karty Tesla K80 na maszynie wirtualnej Azure NC.

![Właściwości sterownika procesora GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Aby wykonać zapytanie o stan urządzenia GPU, uruchom narzędzie wiersza polecenia [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane wraz z sterownikiem.

1. Otwórz wiersz polecenia i przejdź do katalogu **C:\Program Files\NVIDIA Corporation\NVSMI** .

2. Uruchom polecenie `nvidia-smi`. Jeśli sterownik jest zainstalowany, zostaną wyświetlone dane wyjściowe podobne do poniższych. **Procesor GPU** pokazuje **0%** , chyba że obecnie działa obciążenie procesora GPU na maszynie wirtualnej. Informacje o wersji sterownika i procesora GPU mogą różnić się od podanych elementów.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Łączność sieciowa RDMA

Łączność sieciową RDMA można włączyć na maszynach wirtualnych z serii N obsługujących funkcję RDMA, takich jak NC24r wdrożone w tym samym zestawie dostępności lub w pojedynczej grupie umieszczania w zestawie skalowania maszyn wirtualnych. Aby zainstalować sterowniki urządzeń sieciowych z systemem Windows, które umożliwiają łączność RDMA, należy dodać rozszerzenie HpcVmDrivers. Aby dodać rozszerzenie maszyny wirtualnej do maszyny wirtualnej z serii N z obsługą funkcji RDMA, użyj [Azure PowerShell](/powershell/azure/overview) poleceń cmdlet dla Azure Resource Manager.

Aby zainstalować najnowszą wersję 1,1 rozszerzenia HpcVMDrivers na istniejącej maszynie wirtualnej z obsługą funkcji RDMA o nazwie myVM w regionie zachodnie stany USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Aby uzyskać więcej informacji, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Sieć RDMA obsługuje ruch MPI (Message Passing Interface) dla aplikacji uruchamianych z programem [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) lub Intel MPI 5. x. 


## <a name="next-steps"></a>Następne kroki

* Deweloperzy tworzący przyspieszające procesor GPU aplikacje dla procesorów GPU NVIDIA Tesla mogą również pobrać i zainstalować najnowszy [zestaw narzędzi cuda](https://developer.nvidia.com/cuda-downloads). Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji cuda](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


