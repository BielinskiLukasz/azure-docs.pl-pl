---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5e3f25727204343de107bacb9fc99d6cfb77d76f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021281"
---
Ważne jest, aby zrozumieć, jak i gdzie maszyny wirtualne działają na platformie Azure, a także poznać opcje maksymalizowania wydajności, dostępności i nadmiarowości. Ten artykuł zawiera omówienie funkcji dostępności i nadmiarowości platformy Azure.


## <a name="what-are-azure-regions"></a>Co to są regiony platformy Azure?
Platforma Azure działa w wielu centrach danych na całym świecie. Te centra danych są grupowane w regiony geograficzne, dzięki czemu można elastycznie wybierać miejsca do kompilowania aplikacji. 

Zasoby platformy Azure są tworzą w zdefiniowanych regionach geograficznych, takich jak "Zachodnie stany USA", "Europa północna" lub "Azja Południowo-Wschodnia". Możesz zapoznać się z [listą regionów i ich lokalizacji](https://azure.microsoft.com/regions/). W każdym regionie istnieje wiele centrów danych, co zapewnia nadmiarowość i dostępność. Takie podejście zapewnia elastyczność podczas projektowania aplikacji do tworzenia maszyn wirtualnych najbliżej użytkowników i spełniają wszelkie prawne, zgodności lub celów podatkowych.

## <a name="special-azure-regions"></a>Specjalne regiony platformy Azure
Platforma Azure ma kilka specjalnych regionów, które mogą być używane podczas tworzenia aplikacji do celów zgodności lub prawnych. Te regiony specjalne to:

* **US Gov Wirginia** i **US Gov Iowa**
  * Wystąpienie platformy Azure odizolowane fizycznie i na poziomie sieci logicznej dla instytucji rządowych oraz obsługiwane przez sprawdzony pod kątem bezpieczeństwa personel z obywatelstwem Stanów Zjednoczonych. Uwzględnia dodatkowe certyfikaty zgodności, takie jak [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) i [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Dowiedz się więcej o [platformie Azure Government](https://azure.microsoft.com/features/gov/).
* **Chiny Północne** i **Chiny Wschodnie**
  * Te regiony są dostępne dzięki unikatowemu partnerstwu firm Microsoft i 21Vianet, w ramach którego firma Microsoft nie zarządza bezpośrednio centrami danych. Zobacz więcej informacji o [usłudze Azure China 21Vianet](https://www.windowsazure.cn/).
* **Niemcy Środkowe** i **Niemcy Północno-Wschodnie**
  * Regiony te są dostępne za pośrednictwem modelu powiernika danych, w którym dane klientów pozostają w Niemczech pod kontrolą T-Systems, spółki Deutsche Telekom, działającej jako niemiecki powiernik danych.

## <a name="region-pairs"></a>Pary regionów
Każdy region platformy Azure jest powiązany z innym regionem w obrębie tego samego obszaru geograficznego (takiego jak Stany Zjednoczone, Europa i Azja). To podejście umożliwia replikację zasobów, takich jak maszyny wirtualne, między obszarami geograficznymi, co powinno zmniejszyć prawdopodobieństwo tego, że klęski żywiołowe, niepokoje społeczne, przerwy w dostawie prądu lub awarie sieci fizycznych będą wpływać na obydwa regiony na raz. Dodatkowe korzyści wynikające z tworzenia par regionów:

* W przypadku awarii platformy Azure o większym zasięgu jeden region z każdej pary ma przydzielany wyższy priorytet, co pomaga zredukować czas wymagany do przywrócenia aplikacji. 
* Zaplanowane aktualizacje platformy Azure są wdrażane w powiązanych regionach pojedynczo, aby zminimalizować przestoje i ryzyko awarii aplikacji.
* Dla celów związanych z podatkami i egzekwowaniem prawa dane przez cały czas znajdują się w tym samym obszarze geograficznym, co para (z wyjątkiem regionu Brazylia Południowa).

Przykłady par regionów:

| Podstawowy | Pomocniczy |
|:--- |:--- |
| Zachodnie stany USA |Wschodnie stany USA |
| Europa Północna |Europa Zachodnia |
| Azja Południowo-Wschodnia |Azja Wschodnia |

Zobacz pełną [listę par regionów tutaj](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Dostępność funkcji
Niektóre usługi lub funkcje maszyn wirtualnych, takie jak określone rozmiary maszyn wirtualnych lub typy magazynu, są dostępne tylko w określonych regionach. Niektóre globalne usługi platformy Azure, takie jak [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) lub [Azure DNS](../articles/dns/dns-overview.md), nie wymagają wybrania określonego regionu. Aby ułatwić sobie projektowanie środowiska aplikacji, sprawdź [dostępność usług Azure w poszczególnych regionach](https://azure.microsoft.com/regions/#services). Można również [programowo wysyłać zapytania do obsługiwanych rozmiarów i ograniczeń maszyn wirtualnych w każdym regionie](../articles/azure-resource-manager/templates/error-sku-not-available.md).

## <a name="storage-availability"></a>Dostępność magazynu
Zrozumienie sposobu działania regionów i obszarów geograficznych platformy Azure staje się ważne, jeśli chcesz skorzystać z dostępnych opcji replikacji magazynu. W zależności od typu magazynu masz do wyboru różne opcje replikacji.

**Dyski zarządzane platformy Azure**
* Magazyn lokalnie nadmiarowy (LRS)
  * Umożliwia trzykrotne replikowanie danych w regionie, w którym utworzono konto magazynu.

**Dyski oparte na koncie magazynu**
* Magazyn lokalnie nadmiarowy (LRS)
  * Umożliwia trzykrotne replikowanie danych w regionie, w którym utworzono konto magazynu.
* Magazyn strefowo nadmiarowy (ZRS)
  * Umożliwia trzykrotne replikowanie danych w ramach dwóch lub trzech obiektów, w jednym lub dwóch regionach.
* Magazyn geograficznie nadmiarowy (GRS)
  * Umożliwia replikowanie danych do regionu pomocniczego, który jest oddalony od regionu podstawowego o setki kilometrów.
* Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)
  * Umożliwia replikowanie danych do regionu pomocniczego, podobnie jak w przypadku magazynu GRS, ale oferuje także dostęp tylko do odczytu do danych w lokalizacji pomocniczej.

W poniższej tabeli przedstawiono krótkie podsumowanie różnic między typami replikacji magazynu:

| Strategia replikacji | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Dane są replikowane między wieloma obiektami. |Nie |Tak |Tak |Tak |
| Dane mogą być odczytywane z lokalizacji pomocniczej i z lokalizacji podstawowej. |Nie |Nie |Nie |Tak |
| Liczba kopii danych obsługiwanych w osobnych węzłach. |3 |3 |6 |6 |

Aby uzyskać więcej informacji, zobacz [opcje replikacji magazynu Azure Storage tutaj](../articles/storage/common/storage-redundancy.md). Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Koszty magazynowania
Ceny różnią się w zależności od wybranego typu magazynu i dostępności.

**Dyski zarządzane platformy Azure**
* Dyski zarządzane w wersji Premium są wspierane przez dyski SSD, a standardowe dyski zarządzane są wspierane przez zwykłe dyski wirujące. Opłaty za dyski funkcji Dyski zarządzane w warstwach Premium i Standardowa są naliczane zgodnie z aprowizowaną pojemnością dysku.

**Dyski niezarządzane**
* Pamięć masowa w wersji Premium jest wspierana przez dyski SSD i jest naliczana na podstawie pojemności dysku.
* Magazyn w warstwie Standardowa opiera się na zwykłych dyskach obrotowych, a opłaty są naliczane na podstawie używanej pojemności i żądanej dostępności magazynu.
  * W przypadku magazynów RA-GRS istnieje dodatkowa opłata za transfer danych replikacji geograficznej związana z przepustowością wykorzystywaną do replikowania tych danych do innego regionu platformy Azure.

Zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/), aby uzyskać informacje na temat różnych typów magazynów i opcji dostępności.

