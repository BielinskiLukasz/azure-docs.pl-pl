---
title: 'Scenariusz: dowolne'
titleSuffix: Azure Virtual WAN
description: Scenariusze routingu-dowolnego-do-dowolnego
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f8127b485d8fea5aedccd472e209a448e3076b5b
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400079"
---
# <a name="scenario-any-to-any"></a>Scenariusz: dowolne

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W dowolnym scenariuszu każdy szprych może nawiązać połączenie z innym szprychą. Gdy istnieje wiele centrów, routing między centrami (znany również jako Inter-Hub) jest domyślnie włączony w standardowej wirtualnej sieci WAN. Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Projekt

Aby ustalić, ile tabel tras będzie potrzebnych w wirtualnym scenariuszu sieci WAN, można utworzyć macierz łączności, w której każda komórka reprezentuje, czy źródło (wiersz) może komunikować się z miejscem docelowym (kolumna). Macierz łączności w tym scenariuszu jest prosta, ale dodaliśmy ją w celu zapewnienia spójności z innymi scenariuszami.

| Źródło |   Działanie |  *Sieci wirtualnych* | *Gałęzie* |
| -------------- | -------- | ---------- | ---|
| Sieci wirtualnych     | &#8594;|      X     |     X    |
| Gałęzie   | &#8594;|    X     |     X    |

Każda z komórek w powyższej tabeli opisuje, czy wirtualne połączenie sieci WAN ("od" po stronie przepływu, nagłówki wierszy w tabeli) uzyskuje prefiks docelowy (po stronie "do" przepływu, nagłówki kolumn w postaci kursywy w tabeli) dla określonego przepływu ruchu, gdzie "X" oznacza, że łączność jest zapewniana przez wirtualną sieć WAN.

Ponieważ wszystkie połączenia zarówno z sieci wirtualnych, jak i gałęzi (VPN, ExpressRoute i VPN użytkownika) mają takie same wymagania dotyczące łączności, wymagana jest pojedyncza tabela tras. W związku z tym wszystkie połączenia będą skojarzone i propagowane do tej samej tabeli tras, domyślnej tabeli tras:

* Sieci wirtualne:
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **domyślne**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **domyślne**

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Na **rysunku 1**wszystkie sieci wirtualnych i gałęzie (VPN, EXPRESSROUTE, P2S) mogą się łączyć ze sobą. W koncentratorze wirtualnym połączenia działają w następujący sposób:

* Połączenie sieci VPN łączy lokację sieci VPN z bramą sieci VPN.
* Połączenie sieci wirtualnej łączy sieć wirtualną z koncentratorem wirtualnym. Router koncentratora wirtualnego zapewnia funkcjonalność tranzytu między sieci wirtualnych.
* Połączenie ExpressRoute łączy obwód usługi ExpressRoute z bramą ExpressRoute.

Te połączenia (domyślnie podczas tworzenia) są skojarzone z domyślną tabelą tras, chyba że skonfigurowano konfigurację routingu połączenia do **żadnej**lub niestandardowej tabeli tras. Te połączenia również propagują trasy domyślnie do domyślnej tabeli tras. Dzięki temu można dowolnie dowolnie korzystać z dowolnego scenariusza, w którym wszystkie szprychy (Sieć wirtualna, sieci VPN, ER, P2S) mogą dotrzeć do siebie nawzajem.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Rysunek 1":::

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Ten scenariusz jest domyślnie włączony dla standardowej wirtualnej sieci WAN. Jeśli ustawienie rozgałęzienia do gałęzi jest wyłączone w konfiguracji sieci WAN, to nie będzie można nawiązać połączenia między szprychami. Sieci VPN/ExpressRoute/użytkownika są traktowane jako szprychy gałęzie w wirtualnej sieci WAN

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
