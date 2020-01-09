---
title: Dodatkowe linki dotyczące avere vFXT dla platformy Azure
description: Linki do dodatkowych informacji na temat avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: rohogue
ms.openlocfilehash: fbd763827383c5ded2bb4ef0047ea3c5f650d78e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75416326"
---
# <a name="additional-documentation"></a>Dodatkowa dokumentacja

Ten artykuł zawiera linki do dodatkowej dokumentacji dotyczącej interfejsu zarządzania avere panelu sterowania i tematów pokrewnych.

## <a name="avere-cluster-documentation"></a>Dokumentacja klastra avere

Dodatkową dokumentację dotyczącą klastrów avere można znaleźć w witrynie sieci Web na <https://azure.github.io/Avere/>. Te dokumenty mogą pomóc zrozumieć możliwości klastra i sposób konfigurowania jego ustawień.

* [Przewodnik tworzenia klastra FXT](<https://azure.github.io/Avere/#fxt_cluster>) jest przeznaczony dla klastrów składających się z fizycznych węzłów sprzętowych, ale niektóre informacje zawarte w dokumencie dotyczą również klastrów vFXT. W szczególności nowe Administratorzy klastrów vFXT mogą skorzystać z odczytania tych sekcji:
  * [Dostosowanie ustawień obsługi i monitorowania](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) wyjaśnia, jak dostosować obsługę ustawień przekazywania i włączyć zdalne monitorowanie.
  * [Konfigurowanie VServers i globalna przestrzeń nazw](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) zawiera informacje o tworzeniu przestrzeni nazw dla klienta.
  * [Konfigurowanie systemu DNS dla klastra avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) objaśnia sposób konfigurowania usługi DNS w trybie okrężnym.
  * [Dodawanie dokumentów magazynu zaplecza](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) jak dodać podstawowe pliki.

* [Przewodnik konfiguracji klastra](<https://azure.github.io/Avere/#operations>) to kompletne odwołanie do ustawień i opcji klastra avere. Klaster vFXT używa podzestawu tych opcji, ale większość z tych samych stron konfiguracji ma zastosowanie.

* [Przewodnik po pulpicie nawigacyjnym](<https://azure.github.io/Avere/#operations>) wyjaśnia, jak używać funkcji monitorowania klastra w panelu sterowania avere.

## <a name="vfxt-creation-and-management-documentation"></a>Dokumentacja tworzenia i zarządzania vFXT

Pełny przewodnik po użyciu narzędzia vfxt.py, tworzenia i zarządzania klastrem w chmurze znajduje się w witrynie GitHub: [Zarządzanie klastrem w chmurze za pomocą usługi vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
