---
title: Omówienie usługi Azure HPC cache
description: Opisuje pamięć podręczną platformy Azure HPC, rozwiązanie dostępu do pliku do obliczeń o wysokiej wydajności
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 05/29/2020
ms.author: v-erkel
ms.openlocfilehash: 3cbde9564e17dc67d2b41f138efb4d8622d84abd
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343590"
---
# <a name="what-is-azure-hpc-cache"></a>Co to jest usługa Azure HPC Cache?

Pamięć podręczna Azure HPC umożliwia przyspieszenie dostępu do danych na potrzeby zadań obliczeniowych o wysokiej wydajności (HPC). Buforowanie plików na platformie Azure powoduje, że pamięć podręczna Azure HPC zapewnia skalowalność przetwarzania w chmurze w istniejącym przepływie pracy. Tej usługi można używać nawet w przypadku przepływów pracy, w których dane są przechowywane w łączach sieci WAN, takich jak w lokalnym środowisku magazynu (NAS) podłączonym do sieci centrum danych.

Pamięć podręczna Azure HPC jest łatwa do uruchomienia i monitorowania z Azure Portal. Istniejący magazyn NFS lub nowe kontenery obiektów BLOB mogą stać się częścią zagregowanej przestrzeni nazw, co sprawia, że dostęp klienta jest prosty nawet w przypadku zmiany miejsca docelowego magazynu zaplecza.

## <a name="overview-video"></a>Klip wideo z omówieniem

[![Miniatura wideo: Przegląd pamięci podręcznej platformy Azure HPC — kliknij, aby odwiedzić stronę wideo](media/video1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Kliknij obraz powyżej, aby obejrzeć [krótkie omówienie pamięci podręcznej platformy Azure HPC](https://azure.microsoft.com/resources/videos/hpc-cache-overview/).

## <a name="use-cases"></a>Przypadki zastosowań

Pamięć podręczna Azure HPC zwiększa produktywność w przypadku przepływów pracy takich jak:

* Przepływ pracy dostępu do pliku z dużym dostępem do odczytu
* Dane przechowywane w magazynie dostępnym NFS, obiekt blob platformy Azure lub oba
* Farmy obliczeniowe do 75 000 rdzeni procesora CPU

Pamięć podręczna Azure HPC można dodać do wielu różnych przepływów pracy w wielu branżach. Wszystkie systemy, w których duża liczba maszyn musi uzyskać dostęp do zestawu plików na dużą skalę i z małym opóźnieniem, będzie korzystne dla tej usługi. Poniższe sekcje zawierają konkretne przykłady.

### <a name="visual-effects-vfx-rendering"></a>Renderowanie efektów wizualnych (VFX)

W przypadku multimediów i rozrywki pamięć podręczna Azure HPC może przyspieszyć dostęp do danych dla projektów renderowania o kluczowym znaczeniu. Przepływy pracy renderowania VFX często wymagają przetwarzania ostatnich minut przez dużą liczbę węzłów obliczeniowych. Dane dla tych przepływów pracy zwykle znajdują się w lokalnym środowisku usługi NAS. Pamięć podręczna Azure HPC może buforować te dane plików w chmurze w celu zmniejszenia opóźnień i zwiększenia elastyczności renderowania na żądanie.

### <a name="life-sciences"></a>Nauki przyrodnicze

Wiele przepływów pracy nauki przyrodniczej może korzystać z buforowania plików skalowalnych w poziomie.

Instytut badawczy, który chce przenieść swoje przepływy pracy analizy genomiki do platformy Azure, może je łatwo przenieść przy użyciu pamięci podręcznej platformy Azure HPC. Ponieważ pamięć podręczna zapewnia dostęp do plików POSIX, nie są konieczne żadne zmiany po stronie klienta, aby uruchomić swój istniejący przepływ pracy klienta w chmurze.

Pamięć podręczna Azure HPC może być również wykorzystywana w celu zwiększenia wydajności zadań, takich jak analiza pomocnicza, Symulacja farmakologiczna lub Analiza obrazu oparta na AI.

### <a name="financial-services-analytics"></a>Analiza usług finansowych

Wdrożenie pamięci podręcznej platformy Azure HPC może ułatwić przyspieszenie obliczeń analizy ilościowej, obciążeń związanych z analizą ryzyka i symulacji Monte Carlo, aby zapewnić firmom usług finansowych lepszy wgląd w podejmowanie decyzji strategicznych.

## <a name="region-availability"></a>Dostępność w danym regionie

Pamięć podręczna Azure HPC jest dostępna w następujących regionach platformy Azure:

| Ameryka Północna      | Europa         | Azja            | Australia      |
|--------------------|----------------|-----------------|----------------|
| Wschodnie stany USA            | Europa Północna   | Korea Środkowa   | Australia Wschodnia |
| Wschodnie stany USA 2          | Europa Zachodnia    | Azja Południowo-Wschodnia  |               |
| Południowo-środkowe stany USA | | | |
| Zachodnie stany USA 2        | | | |

[Funkcja kluczy zarządzanych przez klienta](customer-keys.md) jest obsługiwana tylko w następujących regionach:

* Wschodnie stany USA
* Południowo-środkowe stany USA
* Zachodnie stany USA 2

Aby uzyskać najnowsze informacje o dostępności, należy zapoznać się ze [stroną produktu Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) .

## <a name="service-availability"></a>Dostępność usługi

Musisz zażądać dostępu dla każdej subskrypcji, która będzie używana z pamięcią podręczną platformy Azure HPC. To ograniczenie pomaga zapewnić jakość usług.

Zażądaj dostępu, wypełniając [ten formularz](https://aka.ms/onboard-hpc-cache). Po dodaniu subskrypcji do listy dostępu można utworzyć pamięci podręczne.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [stronę produktu usługi Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) , aby dowiedzieć się więcej o jej możliwościach
* Informacje o [wymaganiach wstępnych](hpc-cache-prereqs.md) dotyczących produktu
* [Tworzenie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md) z poziomu Azure Portal
