---
title: Azure Files cele dotyczące skalowalności i wydajności | Microsoft Docs
description: Poznaj elementy docelowe skalowalności i wydajności dla Azure Files, w tym pojemność, szybkość żądania oraz limity przepustowości ruchu przychodzącego i wychodzącego.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 766dacb69a3f1857197684f552d05a1376e94509
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514867"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure Files cele skalowalności i wydajności

[Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. W tym artykule omówiono elementy docelowe skalowalności i wydajności dla Azure Files i Azure File Sync.

Elementy docelowe skalowalności i wydajności wymienione w tym miejscu są obiektami docelowymi wysokiej klasy, ale mogą mieć wpływ inne zmienne we wdrożeniu. Na przykład przepływność pliku może być również ograniczona przez dostępną przepustowość sieci, a nie tylko serwery obsługujące usługę Azure Files. Zdecydowanie zalecamy testowanie wzorca użycia w celu określenia, czy skalowalność i wydajność Azure Files spełniają Twoje wymagania. Ponadto firma Microsoft dokłada starań, aby zwiększyć te limity w miarę upływu czasu. Nie niechętnie zezwalają, aby przekazać nam swoją opinię, albo w poniższych komentarzach lub w [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), o które limity chcesz zobaczyć.

## <a name="azure-storage-account-scale-targets"></a>Cele skalowania konta usługi Azure Storage

Zasób nadrzędny dla udziału plików platformy Azure to konto usługi Azure Storage. Konto magazynu reprezentuje pulę magazynu na platformie Azure, która może być używana przez wiele usług magazynu, w tym Azure Files, do przechowywania danych. Inne usługi, które przechowują dane na kontach magazynu, to Azure Blob Storage, Azure queue storage i Azure Table Storage. Następujące cele dotyczą wszystkich usług magazynu przechowujących dane na koncie magazynu:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Użycie konta magazynu ogólnego zastosowania z innych usług magazynu ma wpływ na udziały plików platformy Azure na Twoim koncie magazynu. Jeśli na przykład osiągniesz maksymalną pojemność konta magazynu za pomocą usługi Azure Blob Storage, nie będziesz mieć możliwości tworzenia nowych plików w udziale plików platformy Azure, nawet jeśli udział plików platformy Azure jest mniejszy niż maksymalny rozmiar udziału.

## <a name="azure-files-scale-targets"></a>Elementy docelowe skalowania Azure Files

Istnieją trzy kategorie ograniczeń, które należy wziąć pod uwagę w Azure Files: konta magazynu, udziały i pliki.

Na przykład: z udziałami plików w warstwie Premium pojedynczy udział może osiągać 100 000 operacji we/wy, a pojedynczy plik może skalować do 5 000 operacji we/wy na sekundę. Dlatego jeśli masz trzy pliki w jednym udziale, Maksymalna liczba IOPS, którą można uzyskać z tego udziału, to 15 000.

### <a name="standard-storage-account-limits"></a>Limity kont magazynu w warstwie Standardowa

Zapoznaj się z sekcją [cele skalowania konta usługi Azure Storage](#azure-storage-account-scale-targets) , aby uzyskać te limity.

### <a name="premium-filestorage-account-limits"></a>Limity kont FileStorage Premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limity konta magazynu dotyczą wszystkich udziałów. Skalowanie w górę do maksimum dla kont FileStorage jest osiągalne tylko wtedy, gdy istnieje tylko jeden udział na konto FileStorage.

### <a name="file-share-and-file-scale-targets"></a>Elementy docelowe udziału plików i skalowania plików

> [!NOTE]
> Standardowe udziały plików większe niż 5 TiB mają pewne ograniczenia i ograniczenia regionalne.
> Listę ograniczeń, informacje regionalne i instrukcje dotyczące włączania większych rozmiarów udziałów plików można znaleźć w sekcji Dołączanie [do większych udziałów plików](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) w przewodniku planowania.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Elementy docelowe skalowania Azure File Sync

Azure File Sync został zaprojektowany z myślą o celu nieograniczonego użycia, ale nieograniczone użycie nie zawsze jest możliwe. Poniższa tabela wskazuje granice testowania firmy Microsoft, a także wskazuje, które cele są sztywne:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metryki wydajności Azure File Sync

Ponieważ agent Azure File Sync działa na komputerze z systemem Windows Server, który nawiązuje połączenie z udziałami plików platformy Azure, efektywna wydajność synchronizacji zależy od wielu czynników w infrastrukturze: Windows Server i podstawowa Konfiguracja dysku, przepustowość sieci między serwerem i magazynem platformy Azure, rozmiarem pliku, łącznym rozmiarem zestawu danych i aktywnością w zestawie danych. Ponieważ Azure File Sync działa na poziomie pliku, charakterystyka wydajności rozwiązania opartego na Azure File Sync jest lepiej mierzona w liczbie obiektów (plików i katalogów) przetworzonych na sekundę.

W przypadku Azure File Sync wydajność ma kluczowe znaczenie w dwóch etapach:

1. **Początkowe wstępne Inicjowanie obsługi**: aby zoptymalizować wydajność początkowej aprowizacji, zapoznaj się z tematem [dołączania do Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) , aby uzyskać szczegółowe informacje na temat optymalnego wdrożenia.
2. Aktualna **Synchronizacja**: po początkowym umieszczeniu danych w udziałach plików platformy Azure Azure File Sync synchronizuje wiele punktów końcowych.

Aby ułatwić Planowanie wdrożenia dla każdego z etapów, poniżej przedstawiono wyniki zaobserwowane podczas wewnętrznego testowania w systemie z konfiguracją

| Konfiguracja systemu |  |
|-|-|
| Procesor CPU | 64 rdzeni wirtualnych z 64 MiB L3 pamięci podręcznej |
| Pamięć | 128 GiB |
| Dysk | Dyski SAS z macierzą RAID 10 z zapasową pamięcią podręczną |
| Sieć | Sieć 1 GB/s |
| Obciążenie | Ogólnego przeznaczenia serwera plików|

| Początkowe Inicjowanie obsługi po raz pierwszy  |  |
|-|-|
| Liczba obiektów | 25 000 000 obiektów |
| Rozmiar zestawu danych| ~ 4,7 TiB |
| Średni rozmiar pliku | ~ 200 KiB (największy plik: 100 GiB) |
| Przepływność przekazywania | 20 obiektów na sekundę |
| Przepływność pobierania przestrzeni nazw * | 400 obiektów na sekundę |

\* Po utworzeniu nowego punktu końcowego serwera Agent Azure File Sync nie pobiera żadnej zawartości pliku. Najpierw synchronizuje pełną przestrzeń nazw, a następnie wyzwala odwołanie w tle w celu pobrania plików, w całości lub, jeśli włączono obsługę warstw w chmurze, do zasad obsługi warstw w chmurze ustawionych w punkcie końcowym serwera.

| Synchronizacja bieżąca  |   |
|-|--|
| Liczba zsynchronizowanych obiektów| 125 000 obiektów (~ 1% zmian) |
| Rozmiar zestawu danych| 50 GiB |
| Średni rozmiar pliku | ~ 500 KiB |
| Przepływność przekazywania | 30 obiektów na sekundę |
| Przepływność pełnego pobierania * | 60 obiektów na sekundę |

\* Jeśli włączono obsługę warstw w chmurze, prawdopodobnie zobaczysz lepszą wydajność, ponieważ pobierane są tylko niektóre z danych plików. Azure File Sync pobiera tylko dane plików z pamięci podręcznej, gdy zostaną zmienione na dowolnym z punktów końcowych. W przypadku wszystkich warstwowych lub nowo utworzonych plików Agent nie pobiera danych plików, a zamiast tego synchronizuje przestrzeń nazw ze wszystkimi punktami końcowymi serwera. Agent obsługuje również częściowe pobieranie plików warstwowych, które są dostępne dla użytkownika. 

> [!Note]  
> Powyższe numery nie są wskaźnikami wydajności, które będą używane. Rzeczywista wydajność będzie zależeć od wielu czynników, jak opisano na początku tej sekcji.

Ogólnie rzecz biorąc, należy pamiętać o kilku kwestiach:

- Przepływność obiektu jest około skalowana proporcjonalnie do liczby grup synchronizacji na serwerze. Dzielenie danych na wiele grup synchronizacji na serwerze daje lepszą przepływność, co jest również ograniczone przez serwer i sieć.
- Przepływność obiektu jest odwrotnie proporcjonalna do przepływności MiB na sekundę. W przypadku mniejszych plików można zwiększyć przepływność w zakresie liczby obiektów przetworzonych na sekundę, ale mniejszą przepływność MiB na sekundę. Z kolei w przypadku większych plików można uzyskać mniej obiektów przetworzonych na sekundę, ale wyższą przepływność MiB na sekundę. Przepływność MiB na sekundę jest ograniczona przez elementy docelowe skalowania Azure Files.

## <a name="see-also"></a>Zobacz także

- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Elementy docelowe skalowalności i wydajności dla innych usług magazynu](../common/storage-scalability-targets.md)
