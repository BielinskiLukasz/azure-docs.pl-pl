---
title: Microsoft Azure Data Box Disk — omówienie | Microsoft Docs — dane
description: Ten artykuł dotyczy usługi Azure Data Box Disk, rozwiązania w chmurze umożliwiającego przenoszenie dużych ilości danych na platformę Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/04/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: b1beb0e9a5a0435bdf298eddbefc230b2f95ed0a
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698506"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Co to jest usługa Azure Data Box Disk? (Wersja zapoznawcza)

Rozwiązanie Microsoft Azure Data Box Disk umożliwia wysyłanie terabajtów danych lokalnych na platformę Azure w szybki, niedrogi i niezawodny sposób. Bezpieczny transfer danych jest przyspieszany przez wysłanie od 1 do 5 dysków półprzewodnikowych (SSD). Te szyfrowane dyski o pojemności 8 TB są wysyłane do centrum danych za pośrednictwem przewoźnika regionalnego. 

Za pomocą usługi Data Box w witrynie Azure Portal możesz szybko konfigurować, łączyć i odblokowywać dyski. Skopiuj dane na dyski i odeślij dyski z powrotem do platformy Azure. W centrum danych platformy Azure dane są automatycznie przekazywane z dysków do chmury przez szybki, prywatny link przekazywania do sieci.


> [!IMPORTANT]
> - Usługa Data Box Disk jest dostępna w wersji zapoznawczej. Przed wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - Musisz utworzyć konto dla tej usługi. Aby utworzyć konto, przejdź do [portalu w wersji zapoznawczej](http://aka.ms/azuredataboxfromdiskdocs).
> - W okresie obowiązywania wersji zapoznawczej usługa Data Box Disk może być wysyłana do klientów w Stanach Zjednoczonych i Unii Europejskiej. Aby uzyskać więcej informacji, przejdź do tematu [Dostępność regionalna](#region-availability).

## <a name="use-cases"></a>Przypadki zastosowań

Usługa Data Box Disk umożliwia przenoszenie terabajtów danych w scenariuszach bez łączności lub z ograniczoną łącznością z siecią. Przenoszenie danych może być jednorazowe lub okresowe. Może być to również początkowy transfer danych zbiorczych, po którym następują transfery okresowe. 

- **Migracja jednorazowa** — przenoszenie dużej ilości danych lokalnych na platformę Azure. Może być to na przykład przenoszenie danych z taśm w trybie offline do danych archiwalnych w chłodnym magazynie platformy Azure.
- **Transfer przyrostowy** — po zakończeniu początkowego transferu zbiorczego przeprowadzonego za pomocą urządzenia Data Box Disk (inicjatora) następują transfery przyrostowe za pośrednictwem sieci. Na przykład rozwiązania Commvault i Data Box Disk są używane do przenoszenia kopii zapasowych na platformę Azure. Po tej migracji następuje kopiowanie danych przyrostowych za pośrednictwem sieci do usługi Azure Storage. 
- **Okresowe operacje przekazywania** — gdy duże ilości danych są generowane okresowo i trzeba je przenosić na platformę Azure. Może to dotyczyć na przykład scenariusza eksploracji zasobów energii, w którym jest generowana zawartość wideo przeznaczona dla platform wiertniczych i elektrowni wiatrowych.

## <a name="the-workflow"></a>Przepływ pracy

Typowy przepływ obejmuje następujące kroki:

1. **Zamawianie** — utwórz zamówienie w witrynie Azure Portal oraz podaj informacje wysyłkowe i docelowe konto magazynu platformy Azure dla danych. Jeśli dyski są dostępne, platforma Azure szyfruje, przygotuje i wysyła dyski z identyfikatorem śledzenia wysyłki.

2. **Odbieranie** — rozpakowywanie i łączenie dostarczonych dysków z komputerem zawierającym dane do skopiowania. Odblokuj dyski.
    
3. **Kopiowanie danych** — przeciągnij i upuść, aby skopiować dane na dyskach.

4. **Zwracanie** — przygotuj i wyślij dyski z powrotem do centrum danych platformy Azure.

5. **Przekazywanie** — dane są automatycznie kopiowane z dysków na platformę Azure. Dyski są bezpiecznie wymazywane zgodnie z wytycznymi Narodowego Instytutu Standaryzacji i Technologii (NIST, National Institute of Standards and Technology).

W trakcie tego procesu będziesz otrzymywać powiadomienia e-mail o wszystkich zmianach stanu. Aby uzyskać więcej informacji na temat szczegółowego przepływu, przejdź do tematu [Deploy Data Box Disks in Azure portal (Wdrażanie dysków usługi Data Box Disk w witrynie Azure Portal)](data-box-disk-quickstart-portal.md).


## <a name="benefits"></a>Korzyści

Rozwiązanie Data Box Disk jest przeznaczone do przenoszenia dużych ilości danych na platformę Azure bez wpływu na sieć. Oferuje ono następujące korzyści:

- **Szybkość** — urządzenie Data Box Disk używa połączenia USB 3.0 i umożliwia przenoszenie do 35 TB danych na platformę Azure w czasie krótszym niż tydzień.   

- **Łatwość użycia** — urządzenie Data Box to rozwiązanie proste do użycia.

    - Dyski używają połączeń USB, a czas ich instalacji jest minimalny.
    - Dyski mają niewielkie rozmiary, co ułatwia ich obsługę.
    - Dyski nie mają żadnych wymagań dotyczących zasilania zewnętrznego.
    - Dyski mogą być używane z serwerem centrum danych, komputerem stacjonarnym lub laptopem.
    - Rozwiązanie umożliwia kompleksowe śledzenie w witrynie Azure Portal.    

- **Zabezpieczanie** — usługa Data Box Disk ma wbudowane zabezpieczenia dysków, danych i usługi. 
    - Dyski są odporne na modyfikacje i obsługują możliwość bezpiecznej aktualizacji. 
    - Dane na dyskach są przez cały czas zabezpieczane za pomocą 128-bitowego szyfrowania AES. 
    - Dyski można odblokować tylko przy użyciu klucza podanego w witrynie Azure Portal. 
    - Usługa jest chroniona przy użyciu funkcji zabezpieczeń platformy Azure. 
    - Po przekazaniu danych do platformy Azure dyski są czyszczone zgodnie ze standardami NIST 800-88r1.  
    
Aby uzyskać więcej informacji, przejdź do tematu [Zabezpieczenia i ochrona danych w usłudze Azure Data Box Disk](data-box-disk-security.md).


## <a name="features-and-specifications"></a>Funkcje i specyfikacje


| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Waga                                                  | < 2 funty na opakowanie. Maksymalnie 5 dysków w opakowaniu                |
| Wymiary                                              | Dysk — SSD, 2,5 cala |            
| Kable                                                  | 1 kabel USB 3.1 na dysk|
| Pojemność magazynu na zamówienie                              | 40 TB (do wykorzystania ok. 35 TB)|
| Pojemność magazynu na dysku                                   | 8 TB (do wykorzystania ok. 7 TB)|
| Interfejs danych                                          | USB   |
| Bezpieczeństwo                                                | Wstępne szyfrowanie za pomocą funkcji BitLocker i zabezpieczone aktualizacje <br> Dyski chronione za pomocą klucza dostępu <br> Dane są zaszyfrowane przez cały czas  |
| Szybkość transferu danych                                      | Do 430 MB/s w zależności od rozmiaru pliku      |
|Zarządzanie                                               | Azure Portal |


## <a name="region-availability"></a>Dostępność w danym regionie

W okresie obowiązywania wersji zapoznawczej usługa Data Box Disk może przesyłać dane do następujących regionów świadczenia usługi Azure:


|Region platformy Azure  |Region platformy Azure  |
|---------|---------|
|Środkowo-zachodnie stany USA     |Kanada Środkowa       |        
|Zachodnie stany USA 2     |Kanada Wschodnia         |     
|Zachodnie stany USA     | Europa Zachodnia        |      
|Środkowo-południowe stany USA   |Europa Północna     |         
|Środkowe stany USA     |Australia Wschodnia|
|Środkowo-północne stany USA  |Australia Południowo-Wschodnia   |
|Wschodnie stany USA      |Australia Środkowa |
|Wschodnie stany USA 2     |Australia Środkowa 2|


## <a name="pricing"></a>Cennik

W okresie obowiązywania wersji zapoznawczej usługa Data Box Disk jest dostępna bezpłatnie. Zmieni się to, gdy usługa Data Box Disk stanie się ogólnie dostępna.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [wymaganiami rozwiązania Data Box Disk](data-box-disk-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Disk](data-box-disk-limits.md).
- Szybko wdróż usługę [Azure Data Box Disk](data-box-disk-quickstart-portal.md) w witrynie Azure Portal.
