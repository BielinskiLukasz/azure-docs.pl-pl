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
ms.openlocfilehash: eac6c6d76bcc3b3d9cfeda7d8ca4e52e28ba9d8f
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44369370"
---
Rozmiary maszyn wirtualnych ogólnego przeznaczenia udostępniają o zrównoważonym obciążeniu współczynnik procesora CPU w stosunku do pamięci. Idealne na potrzeby testowania i programowania, małych i średnich baz danych oraz serwerów sieci Web o niewielkim i średnim ruchu. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także przepływności rozmiary w tej metodzie grupowania. 

- Maszyny wirtualne z serii Av2 można wdrożyć na różnych typach sprzętu i procesorach. Maszyny wirtualne serii A zapewniają wydajność procesora CPU i konfiguracje pamięci, które są najlepsze w przypadku obciążeń podstawowych, takich jak tworzenie i testowanie aplikacji. Rozmiar jest ograniczany w zależności od sprzętu, aby zapewnić spójną wydajność procesora dla uruchomionego wystąpienia niezależnie od sprzętu, na którym jest ono wdrożone. Aby określić sprzęt fizyczny, na którym jest wdrażany dany rozmiar, utwórz zapytanie o sprzęt wirtualny z poziomu maszyny wirtualnej.

  Przykładowe przypadki użycia obejmują tworzenie i testowanie serwery, serwery sieci web o niewielkim ruchu, małych do średnich baz danych, weryfikacja koncepcji i repozytoriów kodu.

- Seria Dv2, kontynuacja oryginalnej serii D, funkcjami bardziej wydajne procesora CPU i optymalną konfigurację procesora CPU w stosunku do pamięci, dzięki czemu jest odpowiedni dla większości obciążeń produkcyjnych. Procesor CPU serii Dv2 jest o około 35% szybszy niż procesor CPU serii D. Jego są oparte na najnowszej generacji Intel Xeon® E5-2673 v3 2,4 GHz (Haswell) lub E5-2673 v4 2,3 GHz (broadwell z zegarem) procesorów, a przy użyciu technologii Intel Turbo Boost Technology 2.0, można przejść do 3,1 GHz. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

- Seria Dv3 funkcji procesorze 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesora lub najnowsza wersja 4 2,3 GHz Intel XEON® E5-2673 procesora (Broadwell) w funkcji hyper Threading konfiguracji, zapewniając lepsze korzyści, w przypadku większości obciążeń przeznaczenia.  Pamięci została rozwinięta (z ~3.5 GiB/procesora wirtualnego vCPU do 4 GiB/vCPU), gdy zostały dostosowane limity dysku i sieci na podstawie za rdzeń w celu zapewnienia zgodności z przejścia na wielowątkowość.  Dv3 nie ma już duża ilość pamięci rozmiarów maszyny Wirtualnej z rodzin D/Dv2, te zostały przeniesione do nowej rodziny Ev3.

  Przykładowe przypadki użycia serii D obejmują aplikacji przeznaczonych dla przedsiębiorstw, relacyjne bazy danych, buforowanie w pamięci i analizy. 

## <a name="b-series"></a>Seria B

Usługa Premium Storage: obsługiwane

Buforowanie Premium Storage: Nieobsługiwane

Seria B z możliwością zwiększania wydajności maszyny wirtualne są idealne dla obciążeń, które nie potrzebujesz pełnej wydajności procesora CPU w sposób ciągły, takich jak serwery sieci web, małych baz danych i rozwoju i środowisk testowych. Te obciążenia mają zwykle wymagań dotyczących wydajności z możliwością zwiększania wydajności. Seria B zapewnia Ci klienci umożliwiają zakup rozmiar maszyny Wirtualnej za pomocą ceny świadome linii bazowej wydajności, umożliwiająca wystąpienia maszyny Wirtualnej do zbudowania środków w przypadku maszyny Wirtualnej jest mniejsza niż jej wydajność bazy. Gdy maszyna wirtualna zgromadzonych środków, maszyna wirtualna może serii powyżej linii bazowej maszyny Wirtualnej przy użyciu maksymalnie 100% Procesora, gdy aplikacja wymaga wyższej wydajności procesorów CPU.

Przykładowe przypadki użycia obejmują tworzenia i testowania, serwery sieci web o małym natężeniu ruchu, małych baz danych, mikrousługi, serwerów na potrzeby weryfikacji koncepcji, serwery kompilacji.


| Rozmiar             | Procesor wirtualny  | Pamięć: GiB | Lokalne dyski SSD: GiB | Podstawowej wydajności rdzenia | Środki na korzystanie z wpłaty / godzinę | Maksymalna liczba wpłaty środki na korzystanie z | Maks. liczba dysków danych | Maksymalna liczba dysków lokalnych danych o wydajności: operacje We/Wy / MB/s | Maksymalna liczba niebuforowanych dysków danych o wydajności: operacje We/Wy / MB/s | Maksymalna liczba kart sieciowych |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 6                  | 144            | 2                                      | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 12                 | 288            | 2                                      | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |


## <a name="dsv3-series-sup1sup"></a>Seria Dsv3 <sup>1</sup>

ACU: 160–190

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

Rozmiary serii Dsv3 są oparte na procesorze 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesora lub najnowsza wersja 4 2,3 GHz Intel XEON® E5-2673 procesora (Broadwell), który może osiągnąć częstotliwość 3,5 GHz dzięki technologii Intel Turbo Boost Technology 2.0 i użyj usługi premium storage. Rozmiary serii Dsv3 oferują kombinację procesora wirtualnego vCPU, pamięci i magazynu tymczasowego spełniającą potrzeby większości obciążeń produkcyjnych.


| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standardowa_D2s_v3  | 2      | 8           | 16             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1,000                                   |
| Standardowa_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2,000                                   |
| Standardowa_D8s_v3  | 8      | 32          | 64             | 16             | 16 000 / 128 (200)                                                    | 12 800 / 192                              | 4 / 4,000                                      |
| Standardowa_D16s_v3 | 16     | 64          | 128            | 32             | 32 000 / 256 (400)                                                    | 25 600 / 384                              | 8 / 8,000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64 000 / 512 (800)                                                    | 51 200 / 768                              | 8 / 16,000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128,000 / 1024 (1600)                                                    | 80 000 / 1200                              | 8 / 30,000                                               |

<sup>1</sup> maszyny Wirtualne serii Dsv3 są wyposażone w technologię Intel® Hyper-Threading

## <a name="dv3-series-sup1sup"></a>Seria Dv3 <sup>1</sup>

ACU: 160–190

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

Rozmiary serii Dv3 są oparte na procesorze 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesora lub 2,3 GHz Intel XEON® E5-2673 v4 (broadwell z zegarem) procesora, który może osiągnąć szybkość 3,5 GHz dzięki technologii Intel Turbo Boost Technology 2.0. Rozmiary serii Dv3 oferują kombinację procesora wirtualnego vCPU, pamięci i magazynu tymczasowego spełniającą potrzeby większości obciążeń produkcyjnych.

Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów Dsv3. Liczniki cen i rozliczeń dla rozmiarów Dsv3 są takie same jak dla serii Dv3. 


| Rozmiar            | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba kart sieciowych / przepustowość sieci |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standardowa_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1,000                    |
| Standardowa_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2,000                    |
| Standardowa_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4,000                    |
| Standardowa_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8,000                    |
| Standard_D32_v3 | 32        | 128          | 800            | 32             | 48000/750/375                                            | 8 / 16,000                             |
| Standard_D64_v3 | 64        | 256          | 1600            | 32             | 96000/1000/500                                            | 8 / 30,000                             |

<sup>1</sup> maszyny Wirtualne serii Dv3 są wyposażone w technologię Intel® Hyper-Threading


## <a name="dsv2-series"></a>Seria DSv2

ACU: 210–250

Usługa Premium Storage: obsługiwane

Buforowanie magazynu Premium: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS1_v2 |1 |3,5 |7 |4 |4000 / 32 (43) |3200 / 48 |2 / 750 |
| Standardowa_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 96 |2 / 1500 |
| Standardowa_DS3_v2 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 192 |4 / 3000 |
| Standardowa_DS4_v2 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 384 |8 / 6000 |
| Standardowa_DS5_v2 |16 |56 |112 |64 |64 000 / 512 (688) |51 200 / 768 |8 / 12000 |



## <a name="dv2-series"></a>Seria Dv2

ACU: 210–250

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

| Rozmiar           | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maks. liczba dysków danych | Przepływność: operacje We/Wy | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standardowa_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4 x 500            | 2 / 750                                      |
| Standardowa_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8 x 500            | 2 / 1500                                     |
| Standardowa_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16 x 500           | 4 / 3000                                       |
| Standardowa_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32 x 500           | 8 / 6000                                       |
| Standardowa_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |


## <a name="av2-series"></a>Seria Av2

ACU: 100

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

| Rozmiar            | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standardowa_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standardowa_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |
| Standardowa_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2000                     |
| Standardowa_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standardowa_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |
| Standardowa_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2000                     |

<br>





