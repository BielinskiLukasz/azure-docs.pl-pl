---
title: Azure Windows rozmiarów maszyn wirtualnych — procesor GPU | Dokumentacja firmy Microsoft
description: Rozmiarów dostępnymi dla maszyn wirtualnych Windows na platformie Azure listy różnych procesorów GPU zoptymalizowanych pod kątem. Wyświetla informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także magazynu przepustowości przepływność i sieci dla rozmiarów w tej serii.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jonbeck
ms.openlocfilehash: 222f131e92a6018b4f70c65476cc2c47fb1d1dd5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038566"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU platformy Azure serii N maszyn wirtualnych z systemem Windows, muszą być zainstalowane sterowniki procesora GPU firmy NVIDIA. [Rozszerzenia sterowników procesora GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub siatki na maszynie Wirtualnej serii N. Zainstaluj lub zarządzać rozszerzenia za pomocą witryny Azure portal lub narzędzi, takich jak szablony programu Azure PowerShell lub usługi Azure Resource Manager. Zobacz [dokumentację dotyczącą rozszerzeń sterowników procesora GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroki związane z wdrażaniem. Aby uzyskać ogólne informacje dotyczące rozszerzeń maszyn wirtualnych, zobacz [rozszerzeń maszyny wirtualnej platformy Azure i funkcji](../extensions/overview.md).

Jeśli zdecydujesz się zainstalować sterowniki procesora GPU NVIDIA ręcznie, zobacz [GPU N-series driver Setup for Windows Konfiguracja](n-series-driver-setup.md) dla obsługiwanych systemów operacyjnych, sterowników i kroki instalacji i weryfikacji.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>O innych rozmiarach
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o tym, jak [usługi Azure compute jednostki (ACU)](acu.md) pozwalają porównać wydajności obliczeń w jednostkach SKU platformy Azure.

