---
title: Serie Dav4 i Dasv4
description: Specyfikacje dotyczące maszyn wirtualnych z serii Dav4 i Dasv4.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 5d8478d97ba2615836f3e121d9dd56d94fcd73fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678396"
---
# <a name="dav4-and-dasv4-series"></a>Serie Dav4 i Dasv4

Serie Dav4 i Dasv4 to nowe rozmiary wykorzystujące procesor<sup>AMD 7452 o</sup> rozmiarze 2.35 GHz w konfiguracji wielowątkowej z maksymalnie 256 MB pamięci podręcznej L3 z 8 MB pamięci podręcznej L3 do każdego 8 rdzeni, które zwiększają możliwości uruchamiania ich obciążeń ogólnego przeznaczenia. Serie Dav4 i Dasv4 mają takie same konfiguracje pamięci i dysków jak seria D & Dsv3.

## <a name="dav4-series"></a>Seria Dav4

ACU: 230-260

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

Rozmiary serii Dav4 są oparte na procesorze 2.35 GHz AMD EPYC<sup>TM</sup> 7452, który umożliwia zwiększenie maksymalnej częstotliwości 3.35 GHz. Rozmiary serii Dav4 oferują kombinację vCPU, pamięci i magazynu tymczasowego dla większości obciążeń produkcyjnych. Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysku SSD Premium, użyj rozmiarów Dasv4. Liczniki cen i rozliczeń dla rozmiarów Dasv4 są takie same jak dla serii Dav4.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16 000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000/1000/500 | 8 / 24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000/1000/500 | 8 / 30000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000/1000/500 | 8 / 30000 |

## <a name="dasv4-series"></a>Seria Dasv4

ACU: 230-260

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

Rozmiary serii Dasv4 są oparte na procesorze 2.35 GHz AMD EPYC<sup>TM</sup> 7452, który może osiągnąć podwyższaną maksymalną częstotliwość 3.35 GHz i korzystać z dysku SSD Premium. Rozmiary serii Dasv4 oferują kombinację vCPU, pamięci i magazynu tymczasowego dla większości obciążeń produkcyjnych.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 / 16 000 |
| Standard_D48as_v4|48|192|384|32|96000/1020 (1200)|76800/1148|8 / 24000 |
| Standard_D64as_v4|64|256|512|32|128000/1020 (1600)|80000/1200|8 / 30000 | 
| Standard_D96as_v4|96|384|768|32|192000/1020 (2400)|80000/1200|8 / 30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
