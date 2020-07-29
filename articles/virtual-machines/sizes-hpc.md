---
title: Rozmiary maszyn wirtualnych platformy Azure — HPC | Microsoft Docs
description: Wyświetla listę różnych rozmiarów dostępnych w przypadku maszyn wirtualnych o wysokiej wydajności obliczeniowych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: c347f637083d8dfdf39cbd032df97bc52973465f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372573"
---
# <a name="high-performance-computing-vm-sizes"></a>Rozmiary maszyn wirtualnych o wysokiej wydajności obliczeniowej

Maszyny wirtualne z serii H platformy Azure zaprojektowano w celu zapewnienia wydajności klasy lidera, skalowalności MPI i efektywności kosztów dla różnych rzeczywistych obciążeń platformy HPC.

[Seria HBv2](hbv2-series.md) Funkcja maszyny wirtualne 200 GB/s — karta Mellanox HDR InfiniBand, podczas gdy maszyna wirtualna z 100 serii GB/s Mellanox EDR InfiniBand. Każdy z tych typów maszyn wirtualnych jest podłączony do nieblokującego drzewa FAT w celu zoptymalizowanej i spójnej wydajności RDMA. Maszyny wirtualne HBv2 obsługują Routing adaptacyjny oraz transport podłączony dynamicznie (DCT) w dodatkowych standardach do standardowych wersji RC i UD Transports. Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zdecydowanie zalecane.

[HB — seria](hb-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na przepustowości pamięci, takich jak dynamika płynów, jawna, skończona analiza elementów i modelowanie pogody. HB VM funkcja 60 AMD EPYC 7551 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Platforma AMD EPYC zapewnia ponad 260 GB/s przepustowości pamięci.

[Seria HC](hc-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na gęstym obliczaniu, takich jak niejawne ograniczone analizy elementów, biocząsteczkowa i obliczeniowa. Funkcja maszyn wirtualnych HC 44 rdzeni procesora Intel Xeon Platinum 8168, 8 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Platforma Intel Xeon Platinum obsługuje bogate ekosystemy narzędzi programistycznych firmy Intel, takich jak biblioteka jądra matematycznych firmy Intel.

[Seria H](h-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji z wysoką częstotliwością procesora CPU lub dużą ilością pamięci na podstawowe wymagania. Maszyny wirtualne serii H funkcja 8 lub 16 rdzeni procesora Intel Xeon E5 2667 v3, 7 lub 14 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Funkcja serii H 56 GB/s karta Mellanox FDR InfiniBand w nieblokującej konfiguracji drzewa FAT dla spójnej wydajności RDMA. Maszyny wirtualne z serii H obsługują technologię Intel MPI 5. x i MS-MPI.

> [!NOTE]
> Maszyny wirtualne A8 – A11 są planowane do wycofania w dniu 3/2021. Aby uzyskać więcej informacji, zobacz [Przewodnik migracji HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Wystąpienia z obsługą technologii RDMA

Większość rozmiarów maszyn wirtualnych HPC (HBv2, HB, HC, H16r, H16mr, A8 i A9) ma interfejs sieciowy do łączności z funkcją zdalnego bezpośredniego dostępu do pamięci (RDMA). Wybrane rozmiary [serii N](./nc-series.md) oznaczone przy użyciu funkcji "r" (ND40rs_v2, ND24rs, NC24rs_v3, NC24rs_v2 i NC24r) również obsługują funkcję RDMA. Ten interfejs jest poza standardowym interfejsem sieciowym platformy Azure dostępnym w innych rozmiarach maszyn wirtualnych.

Ten interfejs umożliwia wystąpieniom z obsługą funkcji RDMA komunikowanie się za pośrednictwem sieci InfiniBand (IB), co pozwala na korzystanie z stawek za HBv2, EDR stawek za HB, HC, NDv2, FDR dla H16r, H16mr i innych maszyn wirtualnych z serii N z obsługą funkcji RDMA, a następnie częstotliwości QDR dla maszyn wirtualnych A8 i A9. Te możliwości RDMA umożliwiają zwiększenie skalowalności i wydajności niektórych aplikacji MPI (Message Passing Interface). Aby uzyskać więcej informacji na temat szybkości, zobacz szczegóły w tabelach na tej stronie.

> [!NOTE]
> W systemie Azure HPC istnieją dwie klasy maszyn wirtualnych w zależności od tego, czy są włączone Wirtualizacja SR-IOV dla InfiniBand. Obecnie maszyna wirtualna SR-IOV dla maszyn wirtualnych z włączoną funkcją InfiniBand to: HBv2, HB, HC, Seria NCV3 i NDv2. Pozostałe maszyny wirtualne z włączoną funkcją InfiniBand nie mają obecnie włączonej funkcji SR-IOV.
> Funkcja RDMA przez IB jest obsługiwana dla wszystkich maszyn wirtualnych z obsługą funkcji RDMA.
> Protokół IP over IB jest obsługiwany tylko na maszynach wirtualnych z włączoną funkcją SR-IOV.

- **System operacyjny** — Linux jest bardzo dobrze obsługiwany dla maszyn wirtualnych HPC; dystrybucje, takie jak CentOS, RHEL, Ubuntu, SUSE są powszechnie używane. W przypadku pomocy technicznej systemu Windows system Windows Server 2016 i nowsze wersje są obsługiwane we wszystkich maszynach wirtualnych z serii HPC. System Windows Server 2012 R2, Windows Server 2012 są również obsługiwane na maszynach wirtualnych z obsługą wirtualizacji SR-IOV (H16r, H16mr, A8 i A9). Należy pamiętać, że [system Windows Server 2012 R2 nie jest obsługiwany na HBv2 i innych maszynach wirtualnych z ponad 64 (wirtualnym lub fizycznym) rdzeniami](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

- **Sterowniki InfiniBand i RDMA** — na maszynach wirtualnych z włączoną funkcją InfiniBand wymagane są odpowiednie sterowniki, aby włączyć funkcję RDMA. W systemie Linux obrazy maszyn wirtualnych CentOS-HPC w portalu Marketplace są wstępnie skonfigurowane przy użyciu odpowiednich sterowników. Obrazy maszyn wirtualnych Ubuntu można skonfigurować za pomocą odpowiednich sterowników, korzystając z [instrukcji](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)przedstawionych tutaj. Na maszynach wirtualnych z serii H i N z obsługą wirtualizacji SR-IOV [rozszerzenia maszyny wirtualnej InfiniBandDriverLinux](./extensions/hpc-compute-infiniband-linux.md) można użyć do zainstalowania sterowników Mellanox OFED i włączenia funkcji InfiniBand. Dowiedz się więcej na temat włączania funkcji InfiniBand w przypadku maszyn wirtualnych obsługujących funkcję [RDMA.](./workloads/hpc/overview.md)

W systemie Windows [rozszerzenie maszyny wirtualnej InfiniBandDriverWindows](./extensions/hpc-compute-infiniband-windows.md) instaluje sterowniki bezpośrednie sieci systemu Windows (na maszynach wirtualnych innych niż SR-IOV) lub sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) na potrzeby łączności RDMA. W niektórych wdrożeniach wystąpień A8 i A9 rozszerzenie HpcVmDrivers jest dodawane automatycznie. Należy pamiętać, że rozszerzenie maszyny wirtualnej HpcVmDrivers jest przestarzałe; nie zostanie ona zaktualizowana.

Aby dodać rozszerzenie maszyny wirtualnej do maszyny wirtualnej, można użyć poleceń cmdlet [Azure PowerShell](/powershell/azure/) . Aby uzyskać więcej informacji, zobacz [rozszerzenia i funkcje maszyny wirtualnej](./extensions/overview.md). Możesz również korzystać z rozszerzeń dla maszyn wirtualnych wdrożonych w [klasycznym modelu wdrażania](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **MPI** — rozmiary maszyn wirtualnych z obsługą wirtualizacji SR-IOV na platformie Azure (HBV2, HB, HC, Seria NCV3, NDv2) pozwalają na używanie niemal wszelkich wersji MPI do użycia z interfejsem Mellanox OFED.
W przypadku maszyn wirtualnych z obsługą funkcji SR-IOV obsługiwane implementacje MPI używają interfejsu Microsoft Network Direct (ND) do komunikacji między maszynami wirtualnymi. W związku z tym obsługiwane są tylko programy Microsoft MPI (MS-MPI) 2012 R2 lub nowsze i Intel MPI 5. x. Nowsze wersje (2017, 2018) biblioteki środowiska uruchomieniowego Intel MPI mogą być niezgodne ze sterownikami usługi Azure RDMA.

- **Przestrzeń adresów sieciowych RDMA** — sieć RDMA na platformie Azure rezerwuje przestrzeń adresową 172.16.0.0/16. Aby uruchamiać aplikacje MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, że przestrzeń adresowa sieci wirtualnej nie nakłada się na sieć RDMA.

## <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

Platforma Azure oferuje kilka opcji tworzenia klastrów maszyn wirtualnych z systemem Windows HPC, które mogą komunikować się za pomocą sieci RDMA, w tym: 

- **Maszyny wirtualne** — Wdróż maszyny wirtualne z obsługą funkcji RDMA w tym samym zestawie skalowania lub zestawie dostępności (w przypadku korzystania z Azure Resource Manager modelu wdrażania). W przypadku korzystania z klasycznego modelu wdrażania należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze.

- **Zestawy skalowania maszyn wirtualnych** — w zestawie skalowania maszyn wirtualnych upewnij się, że wdrożenie jest ograniczone do pojedynczej grupy umieszczania na potrzeby komunikacji InfiniBand w ramach zestawu skalowania. Na przykład w szablonie Menedżer zasobów Ustaw `singlePlacementGroup` Właściwość na `true` . Należy zauważyć, że maksymalny rozmiar zestawu skalowania, który może być przypadający z `singlePlacementGroup` właściwością do `true` jest domyślnie ustawiony na 100 maszyn wirtualnych. Jeśli Twoje wymagania dotyczące skalowania zadań HPC są większe niż 100 maszyn wirtualnych w ramach jednej dzierżawy, możesz poprosić o zwiększenie, a bezpłatnie [otworzyć żądanie pomocy technicznej online](../azure-portal/supportability/how-to-create-azure-support-request.md) . Limit liczby maszyn wirtualnych w pojedynczym zestawie skalowania można zwiększyć do 300. Należy pamiętać, że podczas wdrażania maszyn wirtualnych przy użyciu zestawów dostępności maksymalny limit wynosi 200 maszyn wirtualnych na zestaw dostępności.

- **MPI między maszynami wirtualnymi** — Jeśli na maszynach wirtualnych jest wymagana funkcja RDMA (np. Używanie komunikacji MPI), upewnij się, że maszyny wirtualne znajdują się w tym samym zestawie skalowania maszyn wirtualnych lub zestawie dostępności.

- **Azure CycleCloud** — Tworzenie klastra HPC w [usłudze Azure CycleCloud](/azure/cyclecloud/) w celu uruchamiania zadań MPI.

- **Azure Batch** — utwórz pulę [Azure Batch](../batch/index.yml) do uruchamiania obciążeń MPI. Aby korzystać z wystąpień intensywnie korzystających z obliczeń podczas uruchamiania aplikacji MPI przy użyciu Azure Batch, zobacz temat [Używanie zadań z wielu wystąpień w celu uruchamiania aplikacji interfejsu przekazywania komunikatów (MPI) w programie Azure Batch](../batch/batch-mpi.md).

- **Pakiet Microsoft HPC Pack**  -  [Pakiet HPC Pack](/powershell/high-performance-computing/overview) zawiera środowisko uruchomieniowe dla MS-MPI, które używa sieci usługi Azure RDMA w przypadku wdrożenia na maszynach wirtualnych z systemem Linux obsługujących funkcję RDMA. Na przykład wdrożenia, zobacz [Konfigurowanie klastra RDMA systemu Linux przy użyciu pakietu HPC Pack w celu uruchamiania aplikacji MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

- **Subskrypcja platformy Azure** — aby wdrożyć więcej niż kilka wystąpień intensywnie korzystających z obliczeń, weź pod uwagę subskrypcję z płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

- **Cennik i dostępność** — te rozmiary maszyn wirtualnych są oferowane tylko w warstwie cenowej standardowa. Sprawdź dostępność [produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/) w regionach świadczenia usługi Azure.

- **Przydział rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni w ramach subskrypcji platformy Azure z wartości domyślnej. Twoja subskrypcja może również ograniczyć liczbę rdzeni, które można wdrożyć w niektórych rodzinach rozmiarów maszyn wirtualnych, w tym dla serii H. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../azure-portal/supportability/how-to-create-azure-support-request.md) . (Domyślne limity mogą się różnić w zależności od kategorii subskrypcji).

  > [!NOTE]
  > Skontaktuj się z pomocą techniczną platformy Azure, jeśli potrzebujesz dużej pojemności. Przydziały platformy Azure to limity kredytowe, a nie gwarancje wydajności. Niezależnie od limitu przydziału opłata jest naliczana tylko za używane rdzenie.
  
- **Sieć wirtualna** — [Sieć wirtualna](https://azure.microsoft.com/documentation/services/virtual-network/) platformy Azure nie jest wymagana do korzystania z wystąpień intensywnie korzystających z obliczeń. Jednak w przypadku wielu wdrożeń potrzebna jest co najmniej oparta na chmurze usługa Azure Virtual Network lub połączenie lokacja-lokacja, jeśli trzeba uzyskać dostęp do zasobów lokalnych. W razie potrzeby utwórz nową sieć wirtualną, aby wdrożyć wystąpienia. Dodawanie maszyn wirtualnych intensywnie korzystających z obliczeń do sieci wirtualnej w grupie koligacji nie jest obsługiwane.

- Zmiana **rozmiaru** — ze względu na wyspecjalizowany sprzęt można zmienić tylko wystąpienia intensywnie korzystające z mocy obliczeniowej w tej samej rodzinie wielkości (seria H lub seria N). Na przykład można zmienić rozmiar maszyny wirtualnej serii H tylko z jednego rozmiaru serii H na inny. Dodatkowe zagadnienia dotyczące obsługi sterowników InfiniBand i dysków interfejsu NVMe mogą być brane pod uwagę w przypadku niektórych maszyn wirtualnych.


## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat optymalizowania aplikacji HPC dla platformy Azure i niektórych przykładów w [obciążeniach HPC](./workloads/hpc/overview.md) 

- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
