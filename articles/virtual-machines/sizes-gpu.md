---
title: Rozmiary maszyn wirtualnych platformy Azure — procesor GPU | Microsoft Docs
description: Wyświetla różne rozmiary procesora GPU dostępne dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 9be3c14bb435aaa1cddf008a27389bb3b1fdbc3d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493542"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU są wyspecjalizowanymi maszynami wirtualnymi z jednym lub wieloma procesorami GPU firmy NVIDIA. Te rozmiary są przeznaczone do obciążeń intensywnie korzystających z operacji obliczeniowych, intensywnie korzystających z grafiki i wizualizacji. Ten artykuł zawiera informacje o liczbie i typie procesorów GPU, procesorów wirtualnych vCPU, dysków danych i kart interfejsu sieciowego. Przepustowość magazynu i przepustowość sieci są również uwzględniane dla każdego rozmiaru w ramach grupowania.

- Rozmiary serii [NC](nc-series.md), [NCv2](ncv2-series.md), [Seria NCV3](ncv3-series.md) są zoptymalizowane pod kątem intensywnych obliczeń i aplikacji intensywnie korzystających z sieci i algorytmów. Przykładami są aplikacje i symulacje oparte na CUDA i OpenCL, a następnie w programie AI i uczeniu głębokiego. Seria Seria NCV3 koncentruje się na obciążeniach obliczeniowych o wysokiej wydajności, które obejmują procesor GPU Tesla V100 firmy NVIDIA. W serii NC jest używany procesor Intel Xeon E5-2690 v3 2.60 GHz v3 (Haswell), a maszyny wirtualne serii NCv2 i seria NCV3 używają procesora Intel Xeon E5-2690 v4 (Broadwell).

- Rozmiary serii [ND](nd-series.md)i [NDv2](ndv2-series.md) są ukierunkowane na scenariusze szkolenia i wnioskowania dotyczące uczenia głębokiego. Korzystają one z procesora GPU NVIDIA Tesla P40 i procesorów Intel Xeon E5-2690 v4 (Broadwell). Seria NDv2 używa procesora Intel Xeon Platinum 8168 (Skylake).

- Rozmiary [serii NV](nv-series.md) i [NVv3](nvv3-series.md) są zoptymalizowane i zaprojektowane pod kątem zdalnego wizualizacji, przesyłania strumieniowego, gier, kodowania i infrastruktury VDI przy użyciu struktur, takich jak OpenGL i DirectX. Te maszyny wirtualne są obsługiwane przez procesor GPU NVIDIA Tesla M60.

- [Seria NVv4](nvv4-series.md) Rozmiary maszyn wirtualnych zoptymalizowane i zaprojektowane pod kątem infrastruktury VDI i zdalnej wizualizacji. W przypadku procesorów GPU z podziałem na partycje NVv4 oferuje odpowiedni rozmiar obciążeń wymagających mniejszych zasobów procesora GPU. Te maszyny wirtualne są obsługiwane przez procesor GPU z procesorem AMD Radeon Instinct MI25.

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N, należy zainstalować sterowniki graficznego procesora NVIDIA.

[Rozszerzenie sterownika NVIDIA GPU](/extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika graficznego procesora NVIDIA](/extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroków wdrażania. Aby uzyskać ogólne informacje o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](/extensions/overview.md).

W przypadku wybrania opcji ręcznego instalowania sterowników procesora GPU firmy NVIDIA należy zapoznać się z tematem Konfiguracja [sterownika procesora GPU dla systemu Windows](/windows/n-series-driver-setup.md) lub [n-Series](/linux/n-series-driver-setup) w systemie Linux dla obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacyjnych.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

- Aby uzyskać dostęp do maszyn wirtualnych z serii N, zobacz [produkty dostępne według regionów](https://azure.microsoft.com/regions/services/).

- Maszyny wirtualne z serii N można wdrożyć tylko w modelu wdrażania Menedżer zasobów.

- Maszyny wirtualne serii N różnią się w zależności od typu magazynu platformy Azure obsługiwanego dla ich dysków. Maszyny wirtualne NC i NV obsługują tylko dyski maszyn wirtualnych, które są obsługiwane przez standardową Disk Storage (dysk twardy). Maszyny wirtualne NCv2, Seria NCV3, ND, NDv2 i NVv2 obsługują tylko dyski maszyn wirtualnych, których kopie zapasowe są obsługiwane przez Disk Storage Premium (SSD).

- Jeśli chcesz wdrożyć więcej niż kilka maszyn wirtualnych serii N, weź pod uwagę subskrypcję z opcją płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

- Może być konieczne zwiększenie przydziału rdzeni (na region) w ramach subskrypcji platformy Azure i zwiększenie oddzielnego przydziału rdzeni NC, NCv2, Seria NCV3, ND, NDv2, NV lub NVv2. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](/../azure-supportability/how-to-create-azure-support-request.md) . Limity domyślne mogą się różnić w zależności od kategorii subskrypcji.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.