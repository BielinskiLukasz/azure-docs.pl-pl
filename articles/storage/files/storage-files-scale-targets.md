---
title: Usługa Azure cele skalowalności i wydajności plików | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o cele skalowalności i wydajności dla usługi Azure Files, w tym pojemności, liczba żądań i limity przepustowości dla ruchu przychodzącego i wychodzącego.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 7/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 57929b23e437e17ceb90196e3cfa59c16d518f5a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527441"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Usługa Azure cele skalowalności i wydajności plików
[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. W tym artykule omówiono cele skalowalności i wydajności dla usługi Azure Files i usługi Azure File Sync.

Cele dotyczące skalowalności i wydajności, wymienione w tym miejscu są cele wysokiej klasy, ale mogą mieć wpływ na inne zmienne w danym wdrożeniu. Na przykład przepływność dla pliku może być ona ograniczona przez dostępną przepustowość sieci, nie tylko serwerów obsługujących usługę Azure Files. Zdecydowanie zaleca się testowanie usługi wzorca użycia, aby ustalić, czy skalowalności i wydajności usługi Azure Files własnych wymagań. Ponadto staramy się zwiększyć te limity, wraz z upływem czasu. Zachęcamy do przesłać nam swoją opinię, albo w komentarzach poniżej lub na [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), o limitach, które chcesz zobaczyć nam zwiększyć.

## <a name="azure-storage-account-scale-targets"></a>Obiekty docelowe skalowanie konta usługi Azure storage
Zasób nadrzędny dla udziału plików platformy Azure jest konto magazynu platformy Azure. Konto magazynu reprezentuje pulę magazynu na platformie Azure, który może służyć przez wielu usług magazynu, w tym usługi Azure Files do przechowywania danych. Inne usługi, które przechowują dane w ramach kont magazynu są usługi Azure Blob storage, Azure Queue storage i Azure Table storage. Następujące elementy docelowe mają zastosowanie wszystkich usług magazynu przechowywania danych w ramach konta magazynu:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Użycie konta magazynu z innymi usługami magazynu ma wpływ na udziałów plików platformy Azure w ramach konta magazynu. Na przykład w przypadku osiągnięcia maksymalnej pojemności konta za pomocą usługi Azure Blob storage nie będzie mogła tworzyć nowe pliki w udziale plików platformy Azure, nawet jeśli udział plików platformy Azure znajduje się poniżej rozmiaru maksymalnego udziału.

## <a name="azure-files-scale-targets"></a>Usługa Azure celów skalowania plików
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync
Za pomocą usługi Azure File Sync mają próbowaliśmy jak najszerzej się projektowania pod kątem użycia nieograniczone, jednak nie zawsze jest to możliwe. Poniższa tabela wskazuje granice Nasze testy i które elementy docelowe są faktycznie stałych limitów:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metryki wydajności usługi Azure File Sync
Ponieważ agent usługi Azure File Sync jest uruchomiony na komputerze z serwerem systemu Windows, który nawiązuje połączenie z udziałami plików platformy Azure, wydajność synchronizacji obowiązujące zależy od wielu czynników, w ramach infrastruktury: Windows Server i podstawowych konfiguracji dysku, przepustowość sieci między serwerem i usługi Azure storage, rozmiar pliku, rozmiar całkowitej zestawu danych a działania w zestawie danych. Ponieważ usługi Azure File Sync działa na poziomie plików, charakterystyki wydajności, rozwiązania opartej na usłudze Azure File Sync lepiej jest mierzony w liczbę obiektów (pliki i katalogi) przetwarzanych na sekundę. 
 
Dla usługi Azure File Sync wydajność jest szczególnie ważne w dwóch etapach:
1. **Początkowe jednorazowe Inicjowanie obsługi administracyjnej**: w celu zoptymalizowania wydajności początkowego udostępnienia, dotyczą [dołączania przy użyciu usługi Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) szczegóły optymalizację wdrażania.
2. **Trwającą synchronizacji**: po danych jest początkowo zasilany w udziałach plików platformy Azure, usługi Azure File Sync zapewnia wiele punktów końcowych w synchronizacji.

Aby ułatwić planowanie wdrożenia dla każdego z etapów, poniżej wyniki przestrzegane są podczas testowania wewnętrznego w systemie przy użyciu konfiguracji
| Konfiguracja systemu |  |
|-|-|
| Procesor CPU | 64 rdzeni wirtualnych za pomocą 64 pamięci podręcznej MiB L3 |
| Memory (Pamięć) | 128 GiB |
| Dysk | Dyski SAS z macierzy RAID 10 z baterii kopii pamięci podręcznej |
| Sieć | Sieć 1 GB/s |
| Obciążenie | Serwer plików ogólnego przeznaczenia|

| Początkowa jednorazowe Inicjowanie obsługi administracyjnej  |  |
|-|-|
| Liczba obiektów | 10 mln obiektów | 
| Rozmiar zestawu danych| TiB ~ 4 |
| Średni rozmiar plików | KiB ~ 500 (największy plik: 100 GiB) |
| Przekaż przepływności | obiekty 15 na sekundę |
| Namespace pobierania przepływności * | 350 obiektów na sekundę |
 
* Po utworzeniu nowego punktu końcowego serwera agenta usługi Azure File Sync nie pobiera żadnej zawartości pliku. Najpierw synchronizuje pełną przestrzeni nazw i następnie wyzwalaczy w tle odwołania do pobierania plików, albo w całości lub, jeśli obsługa warstw w chmurze jest włączony, aby zasady obsługi warstw w chmurze ustawiony w punkcie końcowym serwera.

| Trwającą synchronizacji  |   |
|-|--|
| Liczba obiektów synchronizowane| 125 000 obiektów (około 1% zmian) | 
| Rozmiar zestawu danych| 50 giB |
| Średni rozmiar plików | ~ 500 KiB |
| Przekaż przepływności | 20 obiektów na sekundę |
| Pobierz pełną przepływność * | obiekty 30 na sekundę |
 
* Jeśli chmura warstw jest włączona, najprawdopodobniej będzie obserwować lepszą wydajność, jako część pliku danych zostanie pobrana. Usługa Azure File Sync pobiera tylko dane plików w pamięci podręcznej po zmianie na żadnym z punktów końcowych. Dla wszystkich plików warstwowych lub być nowo utworzoną agenta nie pobiera dane z pliku, a zamiast tego synchronizuje tylko przestrzeń nazw w celu wszystkie punkty końcowe serwera. Agent obsługuje również częściowe pobieranie plików warstwowych, ponieważ są one używane przez użytkownika. 
 
> [!Note]  
> Powyższe liczby nie są dane dotyczące wydajności, który wystąpi. Rzeczywista wydajność będzie zależeć od wielu czynników, tak jak pokazano na początku tej sekcji.

Jako ogólnej wskazówki dla danego wdrożenia należy zachować kilka rzeczy, należy pamiętać:
- Przepływność obiektu około skaluje się proporcjonalnie do liczby grup synchronizacji na serwerze. Dzielenie danych na wiele grup synchronizacji na serwerze daje lepszą przepustowość, który również jest ograniczony przez serwer i sieć.
- Przepływność obiektu jest odwrotnie proporcjonalne do MiB na drugim przepływności. Mniejsze pliki będą napotykać większą przepływność pod względem liczby obiektów przetwarzanych na drugi, ale niższe MiB na drugim przepływności. Z drugiej strony w przypadku plików większych otrzymasz mniejszą liczbę obiektów przetwarzanych na drugi, ale wyższy MiB na drugim przepływności. MiB na drugim przepływność jest ograniczona przez obiekty docelowe skalowania usługi Azure Files. 

## <a name="see-also"></a>Zobacz także
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Cele dotyczące skalowalności i wydajności dla innych usług magazynu](../common/storage-scalability-targets.md)
