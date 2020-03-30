---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74279212"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definicje tabel rozmiaru

- Pojemność magazynu jest podawana w jednostkach GiB (1024^3 bajtów). Porównując dyski mierzone w GB (1000^3 bajtów) z dyskami mierzonymi w GiB (1024^3), należy pamiętać, że numery pojemności podane w GiB mogą wydawać się mniejsze. Na przykład 1023 GiB = 1098.4 GB.
- Przepływność dysku mierzona jest jako liczba operacji wejścia/wyjścia na sekundę i MB/s, gdzie 1 MB/s = 10^6 bajtów/s.
- Dyski danych mogą działać w trybie buforowanym lub niebuforowanym. Dla pracy dysku danych w trybie buforowanym tryb pamięci podręcznej hosta jest ustawiony na wartość **ReadOnly** lub **ReadWrite**.  Dla pracy dysku danych bez buforowania tryb pamięci podręcznej hosta jest ustawiony na wartość **None**.
- Jeśli chcesz uzyskać najlepszą wydajność dla maszyn wirtualnych, należy ograniczyć liczbę dysków z danymi do dwóch dysków na procesor wirtualny.
- **Oczekiwana przepustowość sieci** to maksymalna zagregowana przepustowość przydzielona na typ maszyny Wirtualnej dla wszystkich kart sieciowych dla wszystkich miejsc docelowych. Aby uzyskać więcej informacji, zobacz [Przepustowość sieci maszyn wirtualnych](../articles/virtual-network/virtual-machine-network-throughput.md).

  Górne limity nie są gwarantowane. Limity oferują wskazówki dotyczące wybierania odpowiedniego typu maszyny Wirtualnej dla zamierzonej aplikacji. Rzeczywista wydajność sieci zależy od kilku czynników, takich jak przeciążenie sieci, obciążenia aplikacji i ustawienia sieci. Aby uzyskać informacje na temat optymalizacji przepływności sieci, zobacz [Optymalizowanie przepływności sieci dla maszyn wirtualnych platformy Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Aby osiągnąć oczekiwaną wydajność sieci w systemie Linux lub Windows, może być konieczne wybranie określonej wersji lub optymalizacja maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Testowanie przepustowości/przepływności (NTTTCP).](../articles/virtual-network/virtual-network-bandwidth-testing.md)



