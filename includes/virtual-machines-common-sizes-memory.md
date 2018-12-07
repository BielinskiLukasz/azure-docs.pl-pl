---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: dc78c8794923a180264058fc984a58aa29171c58
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978855"
---
Zoptymalizowane pod kątem pamięci oferty rozmiarów maszyny Wirtualnej wysokiego współczynnika pamięci do Procesora, który jest najlepszy w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także przepustowość przepływności i sieć magazynu dla każdego rozmiaru w tej metodzie grupowania. 

* Seria M oferuje największą liczbę procesorów vCPU (maksymalnie 128 procesorów wirtualnych) i największą ilość pamięci (aż do 3,8 TiB) dowolnej maszyny Wirtualnej w chmurze.  Jest to idealne rozwiązanie dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby procesorów wirtualnych vCPU i dużych ilości pamięci.

* Seria Dv2 serii G i ich odpowiedniki DSv2/GS są idealne dla aplikacji wymagających szybszych procesorów wirtualnych vcpu, lepszej wydajności magazynu tymczasowego lub większych ilości pamięci.  Oferują one kombinację opcji o dużych możliwościach dla wielu aplikacji klasy korporacyjnej.


* Seria Dv2, kontynuacja oryginalnej serii D, jest wyposażona w procesor CPU o większych możliwościach. Procesor CPU serii Dv2 jest o około 35% szybszy niż procesor CPU serii D. Jest on oparty na procesorach najnowszej generacji 2,4 GHz Intel Xeon® E5-2673 v3 2,4 GHz (Haswell) lub E5-2673 v4 2,3 GHz (broadwell z zegarem) procesorów i przy użyciu technologii Intel Turbo Boost Technology 2.0, można przejść do 3,1 GHz. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

* Seria Ev3 funkcji E5-2673 v4 2,3 GHz (broadwell z zegarem) procesora w konfiguracji funkcji hyper Threading, zapewniając lepsze korzyści, w przypadku większości obciążeń cel i zapewniania Ev3 wyrównanie innych chmur przy użyciu maszyn wirtualnych ogólnego przeznaczenia.  Pamięci została rozwinięta (z 7 GiB/procesora wirtualnego vCPU do 8 GiB/vCPU), gdy zostały dostosowane limity dysku i sieci na podstawie za rdzeń w celu zapewnienia zgodności z przejścia na wielowątkowość.  Ev3 to uzupełnianie rozmiarów maszyn wirtualnych dużą ilość pamięci rodzin D/Dv2.

* Usługa Azure Compute oferuje różne rozmiary maszyn wirtualnych, które są izolowane pod kątem konkretnego rodzaju sprzętu i przeznaczone dla jednego klienta.  Te rozmiary maszyn wirtualnych są dostosowane do obciążeń wymagających wysokiego stopnia izolacji od innych klientów, a zwłaszcza obciążeń związanych z takimi elementami jak zgodność z przepisami i wymogi prawne.  Klienci mogą również wybrać w celu dalszego podziału zasobów izolowanych maszyn wirtualnych przy użyciu [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Zobacz tabele rodziny maszyn wirtualnych poniżej dla izolowanego opcji maszyny Wirtualnej.

## <a name="esv3-series"></a>Serii Esv3 

ACU: 160-190 <sup>1</sup>

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

Wystąpienia serii ESv3 są oparte na procesorach Intel XEON® E5-2673 v4 (Broadwell) z zegarem 2,3 GHz, które dzięki technologii Intel Turbo Boost 2.0 mogą osiągnąć częstotliwość 3,5 GHz i korzystają z magazynu Premium Storage. Wystąpienia serii Ev3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.


| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standardowa_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16 000 / 128 (200)                                                    | 12 800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32 000 / 256 (400)                                                    | 25 600 / 384                              | 8 / 8,000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40 000 / 320 (400)                                                    | 32 000 / 480                              | 8 / 10 000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64 000 / 512 (800)                                                    | 51 200 / 768                              | 8 / 16,000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128 000/1024 (1600)                                                   | 80 000 / 1200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128 000/1024 (1600)                                                   | 80 000 / 1200                             | 8 / 30,000                             |


<sup>1</sup> maszyny Wirtualne serii Esv3 są wyposażone w technologię Intel® Hyper-Threading.

<sup>2</sup> ograniczonego dostępnych rozmiarów core.

<sup>3</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.


## <a name="ev3-series"></a>Seria Ev3 

ACU: 160-190 <sup>1</sup>

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

Wystąpienia serii Ev3 są oparte na procesorach Intel XEON® E5-2673 v4 (Broadwell) 2,3 GHz i technologią Intel Turbo Boost 2.0, dzięki której mogą osiągnąć częstotliwość 3,5 GHz. Wystąpienia serii Ev3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów ESv3. Liczniki cen i rozliczeń dla rozmiarów ESv3 są takie same jak dla serii Ev3. 


| Rozmiar            | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba kart sieciowych / przepustowość sieci |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standardowa_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1,000                 |
| Standardowa_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2,000                 |
| Standardowa_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4,000                     |
| Standardowa_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8,000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10 000                     |
| Standardowa_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16,000                 |
| Standardowa_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |
| Warstwie standardowa_e64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |

<sup>1</sup> maszyny Wirtualne serii Ev3 są wyposażone w technologię Intel® Hyper-Threading.

<sup>2</sup> ograniczonego dostępnych rozmiarów core.

<sup>3</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.


## <a name="m-series"></a>Seria M 

ACU: 160-180 <sup>1</sup>

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

Akcelerator zapisu: [obsługiwane](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Rozmiar            | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Warstwie standardowa_m8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10 000 operacji / 100 (793)  | 5000 / 125 | 4 / 2000 |
| Warstwie standardowa_m16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20 000 / 200 (1,587) | 10 000 / 250 | 8 / 4000 |
| Warstwie standardowa_m32ts | 32 | 192    | 1,024 | 32 | 40 000 / 400 (3,174) | 20 000 / 500 | 8 / 8,000 |
| Warstwie standardowa_m32ls | 32 | 256    | 1,024 | 32 | 40 000 / 400 (3,174) | 20 000 / 500 | 8 / 8,000 |
| Warstwie standardowa_m32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1,024 | 32 | 40 000 / 400 (3,174) | 20 000 / 500 | 8 / 8,000 |
| Maszyna wirtualna Standard_M64s  | 64 | 1,024   | 2048 | 64 | 80 000 operacji / 800 (6,348)| 40 000 / 1000 | 8 / 16,000          |
| Warstwie standardowa_m64ls  | 64 | 512    | 2048 | 64 | 80 000 operacji / 800 (6,348) | 40 000 / 1000 | 8 / 16,000 |
| Maszyna wirtualna Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1,792 | 2048 | 64 | 80 000 operacji / 800 (6,348)| 40 000 / 1000 | 8 / 16,000          |
| Maszyna wirtualna Standard_M128s&nbsp;<sup>2,&nbsp;3</sup> | 128  | 2048        | 4096  | 64 | 160 000 / 1 600 (12 696) | 80 000 / 2000                            | 8 / 30,000          |
| Maszyna wirtualna Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3,892  | 4096 | 64 | 160 000 / 1 600 (12 696) | 80 000 / 2000                            | 8 / 30,000          |
| Standard_M64   | 64  | 1,024 | 7,168  | 64 | 80 000 operacji / 800 (1,228) | 40 000 / 1000 | 8 / 16,000 |
| Standard_M64m  | 64  | 1,792 | 7,168  | 64 | 80 000 operacji / 800 (1,228) | 40 000 / 1000 | 8 / 16,000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14,336 | 64 | 250 000 / 1 600 (2,456) | 80 000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3,892 | 14,336 | 64 | 250 000 / 1 600 (2,456) | 80 000 / 2000 | 8 / 32000 |



<sup>1</sup> funkcji maszyn wirtualnych serii M firmy Intel® technologii hiperwątkowości

<sup>2</sup> więcej niż 64 procesory wirtualne wymagają jednego z tych systemów operacyjnych gościa: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 z dodatkiem SP2 i Red Hat Enterprise Linux, CentOS 7.3 lub Oracle Linux 7.3 z usługami LIS 4.2.1.

<sup>3</sup> ograniczonego dostępnych rozmiarów core.

<sup>4</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.
<br>

## <a name="gs-series"></a>Seria GS 

ACU: 180 – 240 <sup>1</sup>

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_GS1 |2 |28 |56 |8 |10 000 / 100 (264) |5000 / 125 |2 / 2000 |
| Standardowa_GS2 |4 |56 |112 |16 |20 000 / 200 (528) |10 000 / 250 |2 / 4000 |
| Standardowa_GS3 |8 |112 |224 |32 |40 000 / 400 (1056) |20 000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80 000 / 800 (2112) |40 000 / 1000 |8 / 16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160 000 / 1600 (4224) |80 000 / 2000 |8 / 20 000 |

<sup>1</sup> maksymalna przepływność dysków (na SEKUNDĘ lub MB/s) możliwa dla maszyny Wirtualnej serii GS może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków. Aby uzyskać szczegółowe informacje, zobacz [Premium Storage: High-performance storage for Azure virtual machine workloads](../articles/virtual-machines/windows/premium-storage.md) (Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure). 

<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

<sup>3</sup> ograniczonego dostępnych rozmiarów core.

<br>

## <a name="g-series"></a>Seria G

ACU: 180–240

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

| Rozmiar         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2000                     |
| Standardowa_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standardowa_G3  | 8         | 112         | 1536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standardowa_G4  | 16        | 224         | 3072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16 000          |
| Maszyna wirtualna Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20 000           |

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.
<br>


## <a name="dsv2-series-11-15"></a>Seria DSv2 11-15

ACU: 210-250 <sup>1</sup>

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 192 |4 / 3000 |
| Standardowa_ds13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 768 |8 / 12000 |
| Standardowa_ds15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80 000 / 640 (720) |64 000 / 960 |8 / 25000&nbsp;<sup>4</sup>


<sup>1</sup> maksymalna przepływność dysków (na SEKUNDĘ lub MB/s) możliwa dla maszyny Wirtualnej serii DSv2 może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [Premium Storage: High-performance storage for Azure virtual machine workloads](../articles/virtual-machines/windows/premium-storage.md) (Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure).

<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

<sup>3</sup> ograniczonego dostępnych rozmiarów core.

<sup>4</sup> 25000 MB/s z przyspieszoną siecią.

<br>

## <a name="dv2-series-11-15"></a>Zalecamy używanie serii Dv2 11-15

ACU: 210–250

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

| Rozmiar              | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standardowa_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standardowa_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standardowa_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Maszyna wirtualna Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi. 

<sup>2</sup> 25000 MB/s z przyspieszoną siecią.



<br>



