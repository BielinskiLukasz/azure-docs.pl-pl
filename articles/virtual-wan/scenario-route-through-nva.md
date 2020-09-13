---
title: 'Scenariusz: kierowanie ruchu przez wirtualne urządzenie sieciowe (urządzenie WUS)'
titleSuffix: Azure Virtual WAN
description: Kierowanie ruchem za pośrednictwem urządzenia NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 2fdc1cd36c037f163b6b04907248e08ef20e961d
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400028"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scenariusz: kierowanie ruchu przez urządzenie WUS

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu urządzenie WUS celem jest kierowanie ruchu przez urządzenie WUS (sieciowe urządzenie wirtualne) dla gałęzi do sieci wirtualnej i sieci wirtualnej do gałęzi. Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

> [!NOTE]
> Jeśli masz już skonfigurowaną funkcję z trasami wcześniejszymi niż nowe możliwości [konfigurowania routingu koncentratorów wirtualnych](how-to-virtual-hub-routing.md) , wykonaj kroki opisane w tych wersjach artykułów:
>* [Azure Portal artykuł](virtual-wan-route-table-nva-portal.md)
>* [Artykuł programu PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Projekt

W tym scenariuszu będziemy używać konwencji nazewnictwa:

* "Urządzenie WUS sieci wirtualnych" dla sieci wirtualnych, w których użytkownicy wdrażali urządzenie WUS i połączeni z innymi sieciami wirtualnymi jako szprych (Sieć wirtualna 2 i Sieć wirtualna 4 w **macierzy łączności**, poniżej).
* "Urządzenie WUS szprych" dla sieci wirtualnych połączonych z siecią wirtualną urządzenie WUS (Sieć wirtualna 5, Sieć wirtualna 6, Sieć wirtualna 7 i Sieć wirtualna 8 w **macierzy połączeń**).
* "Urządzenie WUS sieci wirtualnych" dla sieci wirtualnych podłączonych do wirtualnej sieci WAN, które nie mają urządzenie WUS lub innych sieci wirtualnych komunikacji równorzędnej (Sieć wirtualna 1 i Sieć wirtualna 3 w **macierzy łączności**, poniżej).
* "Centra" dla wirtualnych centrów sieci WAN zarządzanych przez firmę Microsoft, z którymi są połączone urządzenie WUS sieci wirtualnych. URZĄDZENIE WUS szprych sieci wirtualnych nie musi być podłączony do wirtualnych koncentratorów sieci WAN, tylko do urządzenie WUS sieci wirtualnych.

Następująca macierz łączności podsumowuje przepływy obsługiwane w tym scenariuszu:

**Macierz łączności**

| Źródło             | Do:|   *URZĄDZENIE WUS szprychy*|*URZĄDZENIE WUS sieci wirtualnych*|*URZĄDZENIE WUS sieci wirtualnych*|*Gałęzie*|
|---|---|---|---|---|---|
| **URZĄDZENIE WUS szprychy**   | &#8594; | 0/0 UDR  |  Komunikacja równorzędna |   0/0 UDR    |  0/0 UDR  |
| **URZĄDZENIE WUS sieci wirtualnych**    | &#8594; |   Static |      X   |        X     |      X    |
| **URZĄDZENIE WUS sieci wirtualnych**| &#8594; |   Static |      X   |        X     |      X    |
| **Gałęzie**     | &#8594; |   Static |      X   |        X     |      X    |

Każda z komórek w macierzy łączności zawiera opis, czy wirtualne połączenie sieci WAN ("od" po stronie przepływu, nagłówki wierszy w tabeli) uzyskuje informacje o prefiksie lokalizacji docelowej (po stronie "do" przepływu, nagłówkach kolumn w postaci kursywy w tabeli) dla określonego przepływu ruchu. "X" oznacza, że łączność jest zapewniana natywnie przez wirtualną sieć WAN, a "static" oznacza, że łączność jest zapewniana przez wirtualną sieć WAN przy użyciu tras statycznych. Rozważ następujące źródła:

* URZĄDZENIE WUS szprych nie są zarządzane przez wirtualną sieć WAN. W efekcie mechanizmy, z którymi będą komunikować się z innymi sieci wirtualnychami lub gałęziami, są obsługiwane przez użytkownika. Łączność z siecią wirtualną urządzenie WUS jest zapewniana przez komunikację równorzędną sieci wirtualnych, a domyślna trasa do 0.0.0.0/0 wskazuje, że urządzenie WUS jako następny przeskok powinien obejmować łączność z Internetem, z innymi szprychami i gałęziami
* URZĄDZENIE WUS sieci wirtualnych będzie wiedzieć o swoich własnych urządzenie WUS szprych, ale nie o urządzenie WUS szprych podłączonych do innych urządzenie WUS sieci wirtualnych. Na przykład w tabeli 1 Sieć wirtualna 2 wie o sieci VNet 5 i sieci wirtualnej 6, ale nie na temat innych szprych, takich jak sieć wirtualna 7 i Sieć wirtualna 8. Trasa statyczna jest wymagana do wprowadzenia prefiksów innych szprych do urządzenie WUS sieci wirtualnych
* Podobnie gałęzie i urządzenie WUS sieci wirtualnych nie wiedzą o żadnej urządzenie WUS szprychy, ponieważ urządzenie WUS szprych nie są połączone z koncentratorami VWAN. W związku z tym w tym miejscu będą również musiały istnieć trasy statyczne.

Biorąc pod uwagę, że urządzenie WUS szprych nie są zarządzane przez wirtualną sieć WAN, wszystkie pozostałe wiersze pokazują ten sam wzorzec łączności. W efekcie jedna tabela tras (domyślnie jedna) wykona następujące czynności:

* Sieci wirtualne (sieci wirtualnych inne niż Hub i koncentrator sieci wirtualnych):
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **domyślne**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **domyślne**

Jednak w tym scenariuszu musimy myśleć o tym, które trasy statyczne mają być skonfigurowane. Każda trasa statyczna będzie miała dwa składniki — jedna część w wirtualnym koncentratorze sieci WAN informuje wirtualne składniki sieci WAN, które są używane dla każdej szprychy, i drugi w tym konkretnym połączeniu wskazujące konkretny adres IP przypisany do urządzenie WUS (lub do modułu równoważenia obciążenia przed wieloma urządzeń WUS), jak pokazano na **rysunku 1** :

**Rysunek 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Rysunek 1":::

Dzięki temu trasy statyczne, które są potrzebne w tabeli domyślnej do wysyłania ruchu do urządzenie WUS szprych za urządzenie WUS sieci wirtualnej, są następujące:

| Opis | Tabela tras | Trasa statyczna              |
| ----------- | ----------- | ------------------------- |
| Sieć wirtualna 2       | Domyślny     | 10.2.0.0/16 — > eastusconn |
| Sieć wirtualna 4       | Domyślny     | 10.4.0.0/16 — > weconn     |

Teraz wirtualna sieć WAN wie, z którym połączeniem należy wysyłać pakiety, ale połączenie musi wiedzieć, co należy zrobić podczas otrzymywania tych pakietów: jest to miejsce, w którym są używane tabele tras połączeń. W tym miejscu będziemy używać krótszych prefiksów (/24 zamiast dłużej/16), aby upewnić się, że te trasy mają preferencję dotyczącą tras importowanych z urządzenie WUS sieci wirtualnych (Sieć wirtualna 2 i Sieć wirtualna 4):

| Opis | Połączenie | Trasa statyczna            |
| ----------- | ---------- | ----------------------- |
| Sieć wirtualna 5       | eastusconn | 10.2.1.0/24 — > 10.2.0.5 |
| Sieć wirtualna 6       | eastusconn | 10.2.2.0/24 — > 10.2.0.5 |
| Sieć wirtualna 7       | weconn     | 10.4.1.0/24 — > 10.4.0.5 |
| Sieć wirtualna 8       | weconn     | 10.4.2.0/24 — > 10.4.0.5 |

Teraz urządzenie WUS sieci wirtualnych, inne niż urządzenie WUS sieci wirtualnych, i rozgałęzienia wiedzą, jak dotrzeć do wszystkich urządzenie WUS szprych. Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Na **rysunku 2**znajdują się dwa centra: **Hub1** i **Hub2**.

* **Hub1** i **Hub2** są połączone bezpośrednio z urządzenie WUS sieci wirtualnych **VNET 2** i sieci **VNET 4**.

* **Sieć wirtualna 5** i **Sieć wirtualna 6** są połączone z siecią **wirtualną 2**.

* **Sieć wirtualna 7** i **Sieć wirtualna 8** są połączone za pomocą sieci **wirtualnej 4**.

* **Sieci wirtualnych 5, 6, 7,** są pośrednimi szprychami, a nie bezpośrednio połączonymi z koncentratorem wirtualnym.

**Rysunek 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Rysunek 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Przepływ pracy scenariusza

Aby skonfigurować Routing za pośrednictwem urządzenie WUS, poniżej przedstawiono kroki, które należy wziąć pod uwagę:

1. Zidentyfikuj połączenie sieci wirtualnej urządzenie WUS szprychy. Na **rysunku 2**są to **połączenia sieci VNET 2 (Eastusconn)** i **Sieć VNET 4 (weconn)**.

   Upewnij się, że skonfigurowano UDR:
   * Z sieci VNet 5 i sieci VNet 6 do sieci VNet 2 urządzenie WUS IP
   * Od sieci VNet 7 i sieci VNet 8 do sieci VNet 4 urządzenie WUS IP 
   
   Nie jest konieczne bezpośrednie łączenie sieci wirtualnych 5, 6, 7, 8 z centrami wirtualnymi. Upewnij się, że sieciowych grup zabezpieczeń w sieci wirtualnych 5, 6, 7, 8 zezwala na ruch dla gałęzi (VPN/ER/P2S) lub sieci wirtualnych połączony ze swoimi zdalnymi sieci wirtualnych. Na przykład sieci wirtualnych 5, 6 muszą zapewnić sieciowych grup zabezpieczeń Zezwalanie na ruch dla prefiksów adresów lokalnych i sieci wirtualnych 7, które są połączone z koncentratorem zdalnym 2.

2. Dodaj zagregowany wpis trasy statycznej dla sieci wirtualnych 2, 5, 6 do domyślnej tabeli tras centrum 1.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Przykład":::

3. Skonfiguruj trasę statyczną dla sieci wirtualnych 5, 6 w połączeniu z siecią wirtualną sieci wirtualnej 2. Aby skonfigurować konfigurację routingu dla połączenia sieci wirtualnej, zobacz [Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md#routing-configuration).

4. Dodaj zagregowany wpis trasy statycznej dla sieci wirtualnych 4, 7, 8 do domyślnej tabeli tras Hub 1.

5. Powtórz kroki 2, 3 i 4 dla domyślnej tabeli tras centrum 2.

Spowoduje to zmianę konfiguracji routingu, jak pokazano na **rysunku 3**poniżej.

**Rysunek 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Rysunek 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
