---
title: "O mapowanie sieci w przypadku replikacji maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM z usługą Site Recovery | Dokumentacja firmy Microsoft"
description: "W tym artykule opisano, jak skonfigurować mapowanie sieci w przypadku replikacji maszyn wirtualnych funkcji Hyper-V zarządzane w chmurach VMM z usługą Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2018
ms.author: raynew
ms.openlocfilehash: 5b8ebf3bd118a7b082949b3f3c6ef60a07641ba1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
---
# <a name="about-network-mapping-for-hyper-v-vm-with-vmm-replication"></a>Mapowanie sieci dla maszyny Wirtualnej funkcji Hyper-V z replikacji programu VMM — informacje 


Ten artykuł pomaga zrozumieć i Zaplanuj do mapowania sieci podczas replikacji maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

Mapowanie sieci jest używany podczas replikowania maszyn wirtualnych funkcji Hyper-V zarządzane w chmurze programu VMM, do dodatkowej chmury VMM lub na platformie Azure.

## <a name="prepare-network-mapping-for-replication-to-azure"></a>Przygotowanie mapowania sieci dla replikacji do platformy Azure

Jeśli przeprowadzasz replikację do platformy Azure, mapowanie sieci działa między sieciami maszyn wirtualnych na źródłowym serwerze programu VMM i docelowych sieci wirtualnych platformy Azure. Mapowanie wykonuje następujące czynności:
    -  **Połączenie sieciowe**— zapewnia czy replikowane maszyny wirtualne Azure są podłączone do mapowanej sieci. Wszystkie komputery, które w tryb failover w tej samej sieci może się ze sobą łączyć, nawet jeśli ich przejścia w tryb failover w planie odzyskiwania inny.
    - **Brama sieci**— Jeśli brama sieci jest skonfigurowana w docelowej sieci platformy Azure, maszyny wirtualne podłączyć do innych maszyn wirtualnych lokalnie.

Mapowanie sieci działa w następujący sposób:

- Źródłowej sieci maszyny Wirtualnej VMM są mapowane na sieć wirtualną platformy Azure.
- Po przejściu w tryb failover maszyn wirtualnych platformy Azure w lokalizacji źródłowej sieci zostaną podłączone do sieci wirtualnej zamapowanego docelowego.
- Nowe maszyny wirtualne dodawane do źródłowej sieci maszyny Wirtualnej są podłączone do mapowanej sieci platformy Azure, podczas replikacji.
- Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę, jak podsieć, w której znajduje się źródłowa maszyna wirtualna, replika maszyny wirtualnej jest łączona z tą docelową podsiecią po przejściu do trybu failover.
- Jeśli nie istnieje docelowa podsieć o takiej samej nazwie, maszyna wirtualna jest łączona z pierwszą podsiecią w sieci.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Przygotowanie mapowania sieci dla replikacji do lokacji dodatkowej

Jeśli przeprowadzasz replikację do lokacji dodatkowej, mapowanie sieci działa między sieciami maszyn wirtualnych na źródłowym serwerze programu VMM i sieci maszyn wirtualnych na docelowym serwerze VMM. Mapowanie wykonuje następujące czynności:

- **Połączenie sieciowe**— łączy maszyn wirtualnych do odpowiedniej sieci po pracy awaryjnej. Maszyna wirtualna repliki zostaną podłączone do sieci docelowej, który jest zamapowany na Sieć źródłowa.
- **Optymalne umieszczania maszyny Wirtualnej**— optymalnie umieszcza repliki maszyny wirtualne na serwerach hostów funkcji Hyper-V. Maszyny wirtualne repliki są umieszczane na hostach, które mogą uzyskiwać dostęp do mapowanej sieci maszyny Wirtualnej.
- **Brak mapowania sieci**— Jeśli nie skonfigurujesz mapowania sieci, repliki maszyn wirtualnych nie będzie podłączona do sieci maszyn wirtualnych po pracy awaryjnej.

Mapowanie sieci działa w następujący sposób:

- Mapowanie sieci można skonfigurować między sieciami maszyn wirtualnych na dwóch serwerach VMM lub na jednym serwerze programu VMM, gdy dwie lokacje są zarządzane przez ten sam serwer.
- Podczas mapowania jest poprawnie skonfigurowany i replikacja jest włączona, w lokalizacji głównej maszyny Wirtualnej zostaną podłączone do sieci i jej replika w lokalizacji docelowej zostaną podłączone do mapowanej sieci.
- Po wybraniu sieć maszyny Wirtualnej docelową podczas mapowania sieci w usłudze Site Recovery chmur programu VMM źródła, które używają źródłowej sieci maszyny Wirtualnej będą wyświetlane, wraz z dostępnych sieci maszyn wirtualnych w chmurach docelowych, które są używane do ochrony.
- Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę jak podsieć, na którym znajduje się źródłowa maszyna wirtualna, następnie repliki maszyny Wirtualnej zostanie podłączona do tej docelowej podsieci po pracy awaryjnej. Jeśli nie ma żadnych docelowa podsieć o pasującej nazwie, maszyna wirtualna zostanie podłączona do pierwszej podsieci w sieci.

## <a name="example"></a>Przykład

Oto przykład ilustrujący ten mechanizm. Spójrzmy organizacji z dwóch lokalizacji w Nowym Jorku i Chicago.

**Lokalizacja** | **Serwer VMM** | **Sieci maszyn wirtualnych** | **Mapowane na**
---|---|---|---
Nowy Jork | VMM-NewYork| VMNetwork1-NewYork | Mapowany do VMNetwork1 Chicago
 |  | VMNetwork2-NewYork | Nie zostały zamapowane
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapowany do NowyJork VMNetwork1
 | | VMNetwork1-Chicago | Nie zostały zamapowane

W tym przykładzie:

- Gdy replika maszyny Wirtualnej jest tworzona dla żadnej maszyny Wirtualnej, która jest połączona z VMNetwork1 NowyJork, zostaną podłączone do VMNetwork1 Chicago.
- Gdy replika maszyny Wirtualnej jest tworzona dla NowyJork VMNetwork2 lub VMNetwork2 Chicago, nie będzie podłączona do żadnej sieci.

Oto, jak chmur programu VMM są zainstalowane w naszym przykładzie organizacji i sieci logiczne skojarzone z chmury.

### <a name="cloud-protection-settings"></a>Ustawienia ochrony chmury

**Chronionej chmurze** | **Ochrona chmury** | **Sieć logiczna (Nowy Jork)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>Nie dotyczy</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>Nie dotyczy</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Ustawienia sieci logicznych, jak i maszyny Wirtualnej

**Lokalizacja** | **Sieć logiczna** | **Skojarzone sieci maszyny Wirtualnej**
---|---|---
Nowy Jork | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Ustawienia sieci docelowej

Na podstawie tych ustawień, gdy wybrana sieć docelowa maszyna wirtualna, w poniższej tabeli przedstawiono opcje, które będą dostępne.

**Wybierz** | **Chronionej chmurze** | **Ochrona chmury** | **Sieć docelowa jest dostępna**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Dostępna
 | GoldCloud1 | GoldCloud2 | Dostępna
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Niedostępne
 | GoldCloud1 | GoldCloud2 | Dostępna


Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę jak podsieć, na którym znajduje się źródłowa maszyna wirtualna, następnie repliki maszyny wirtualnej zostanie podłączona do tej docelowej podsieci po pracy awaryjnej. Jeśli nie istnieje docelowa podsieć o takiej samej nazwie, maszyna wirtualna zostanie podłączona do pierwszej podsieci w sieci.


### <a name="failback-behavior"></a>Zachowanie w przypadku powrotu po awarii

Aby zobaczyć, co się stanie w przypadku powrotu po awarii (replikacja odwrotna), załóżmy, że czy NowyJork VMNetwork1 jest zamapowana do VMNetwork1 Chicago, z następującymi ustawieniami.


**VM** | **Połączone z siecią maszyny Wirtualnej**
---|---
Maszyna wirtualna 1 | VMNetwork1 sieci
Maszyny VM2 (repliki VM1) | VMNetwork1-Chicago

Przy użyciu tych ustawień umożliwia przeglądanie, co dzieje się w kilku możliwych scenariuszach.

**Scenariusz** | **Wynik**
---|---
Brak zmian właściwości sieci maszyny Wirtualnej 2 po pracy awaryjnej. | 1 maszyna wirtualna pozostaje połączony z siecią źródła.
Właściwości sieci maszyny Wirtualnej 2 są zmieniane po pracy awaryjnej i jest odłączony. | 1 maszyna wirtualna jest odłączony.
Właściwości sieci maszyny Wirtualnej 2 są zmieniane po pracy awaryjnej i jest połączona z VMNetwork2 Chicago. | Jeśli nie jest zamapowana VMNetwork2 Chicago, 1 maszyna wirtualna zostanie odłączony.
Mapowanie sieci VMNetwork1 Chicago zostanie zmieniona. | 1 maszyna wirtualna zostanie podłączona do sieci, w obecnie mapowane do VMNetwork1 Chicago.



## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o](hyper-v-vmm-networking.md) adresowania IP po przejściu w tryb failover dodatkowej lokacji programu VMM.
