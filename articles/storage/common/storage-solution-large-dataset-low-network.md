---
title: Opcje usługi Azure Data Transfer dla dużych zestawów danych z niską lub bez przepustowości sieci | Microsoft Docs
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure na potrzeby transferu danych, gdy w środowisku nie ograniczono przepustowości sieci i planujesz transfer dużych zestawów danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: cad76677464ba4895141e53ea45b98f55cc2f655
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515745"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Transfer dużych zestawów danych w przypadku niskiej przepustowości sieci lub jej braku
 
Ten artykuł zawiera omówienie rozwiązań transferu danych, które są ograniczone do braku przepustowości sieci w danym środowisku i planujesz transfer dużych zestawów danych. W tym scenariuszu opisano również zalecane opcje transferu danych oraz odpowiednie kluczowe klasy macierzy.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do pozycji [Wybierz rozwiązanie do transferu danych Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Transfer w trybie offline lub transfer sieciowy

Duże zestawy danych oznacza, że masz kilka usług TBs do kilku książek telefonicznych. Masz ograniczoną przepustowość sieci, Sieć jest powolna lub jest niezawodna. Ponadto:

- Masz ograniczone koszty transferu sieciowego od usługodawców internetowych.
- Zasady zabezpieczeń i organizacyjne nie zezwalają na połączenia wychodzące podczas pracy z danymi poufnymi.

We wszystkich powyższych wystąpieniach należy użyć urządzenia fizycznego do jednorazowego transferu danych zbiorczych. Wybieraj spośród Data Box Disk, urządzenie Data Box, Data Box Heavy urządzeń dostarczanych przez firmę Microsoft lub Importuj/Eksportuj przy użyciu własnych dysków.

Aby sprawdzić, czy urządzenie fizyczne jest w odpowiedniej opcji, Skorzystaj z poniższej tabeli. Pokazuje przewidywany czas transferu danych w sieci dla różnych dostępnych przepustowości (przy założeniu użycia 90%). Jeśli transfer sieciowy jest zbyt wolny, należy użyć urządzenia fizycznego.  

![Transfer sieciowy lub transfer w trybie offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Zalecane opcje

Opcjami dostępnymi w tym scenariuszu są urządzenia do Azure Data Box transferu w trybie offline lub importowania/eksportowania platformy Azure.

- **Azure Data Box rodzina do transferów w trybie offline** — używanie urządzeń z urządzeń urządzenie Data Box dostarczonych przez firmę Microsoft do przenoszenia dużych ilości danych na platformę Azure, gdy są one ograniczone przez czas, dostępność sieci lub koszty. Skopiuj dane lokalne przy użyciu narzędzi, takich jak Robocopy. W zależności od rozmiaru danych przeznaczonego do transferu można wybrać jedną z Data Box Disk, urządzenie Data Box lub Data Box Heavy.
- **Azure Import/Export** — usługa Azure Import/Export umożliwia dostarczenie własnych stacji dysków w celu bezpiecznego zaimportowania dużych ilości danych do usługi Azure Blob storage i Azure Files. Ta usługa może również służyć do transferowania danych z usługi Azure Blob Storage do stacji dysków i dostarczania ich do lokacji lokalnych.

## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

Poniższa tabela zawiera podsumowanie różnic między kluczowymi funkcjami.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Rozmiar danych                        |    Do 35 usług TBs                 |    Do 80 usług TBs na urządzenie                       |    Do 800 TB na urządzenie               |    Zmienna                            |
|    Typ danych                        |    Obiekty blob platformy Azure                  |    Obiekty blob platformy Azure<br>Azure Files                    |    Obiekty blob platformy Azure<br>Azure Files            |    Obiekty blob platformy Azure<br>Azure Files          |
|    Współczynnik postaci                      |    5 dysków SSD na zamówienie             |    1 X 50-funtów urządzenie o rozmiarze stacjonarnym na zamówienie    |    1 X ~ 500-funty. duże urządzenie na zamówienie    |    Do 10 HDD/dysków SSD na zamówienie        |
|    Czas wstępnej instalacji               |    Małe <br>(15 minut)            |    Od niska do umiarkowanych <br> (<30 minut)               |    Umiarkowane<br>(1-2 godzin)               |    Umiarkowane, aby trudne<br>zmiennej |
|    Wysyłanie danych do platformy Azure               |    Tak                          |    Tak                                           |    Tak                                   |    Tak                                 |
|    Eksportowanie danych z platformy Azure           |    Nie                           |    Nie                                            |    Nie                                    |    Yes                                 |
|    Szyfrowanie                       |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    Sprzęt                         |     Dostarczone przez firmę Microsoft          |    Dostarczone przez firmę Microsoft                            |    Dostarczone przez firmę Microsoft                    |    Dostarczone przez klienta                   |
|    Interfejs sieciowy                |    USB 3.1/SATA                 |    PORT RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    Integracja z partnerami              |    Niektóre                         |    [Wysoki](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Wysoki](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Niektóre                                |
|    Wysyłka                         |    Zarządzane przez firmę Microsoft            |    Zarządzane przez firmę Microsoft                             |    Zarządzane przez firmę Microsoft                     |    Zarządzane przez klienta                    |
| Użyj podczas przenoszenia danych         |Granica handlowa|Granica handlowa|Granica handlowa|Między granicami geograficznymi, np. do UE|
|    Cennik                          |    [Cennik](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Cennik](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem

    - [Transferuj dane przy użyciu Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferuj dane przy użyciu urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Transferuj dane przy użyciu importu/eksportu](/azure/storage/common/storage-import-export-data-to-blobs).
