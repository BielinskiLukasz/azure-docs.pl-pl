---
title: Informacje o migracji w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób migrowania lokalnych i maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 2cc030bf9724e98c1ef0a43134c5066c81b38b6b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213092"
---
# <a name="about-migration"></a>Informacje o migracji

Przeczytaj ten artykuł, aby uzyskać szybki przegląd sposób [usługi Azure Site Recovery](site-recovery-overview.md) service ułatwia migrację maszyn. 

Oto co można przeprowadzić migrację za pomocą Site Recovery:

- **Migrowanie ze środowiska lokalnego na platformę Azure**: Migrowanie lokalnych maszyn wirtualnych z funkcją Hyper-V, maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Po zakończeniu migracji obciążenia uruchomione na maszynach lokalnych będą działać na maszynach wirtualnych platformy Azure. 
- **Przeprowadzić migrację na platformie Azure**: przeprowadź migrację maszyn wirtualnych platformy Azure między regionami świadczenia usługi Azure. 
- **Przeprowadzić migrację usługi AWS**: przeprowadź migrację wystąpień usługi AWS dla systemu Windows do maszyn wirtualnych IaaS platformy Azure. 


## <a name="what-do-we-mean-by-migration"></a>Co mamy na myśli przez migrację?

Oprócz używania Site Recovery do odzyskiwania po awarii lokalnych i maszyn wirtualnych platformy Azure, można użyć usługi Site Recovery do przeprowadzenia migracji. Jaka jest różnica?

- Do odzyskiwania po awarii maszyn w regularnych odstępach czasu jest replikacja do platformy Azure. Gdy wystąpi awaria, pracy awaryjnej maszyn z lokacji głównej do lokacji dodatkowej dla platformy Azure i uzyskiwać do nich dostęp z tego miejsca. Gdy lokacja główna będzie znowu dostępna, powrotu po awarii z platformy Azure.
- W przypadku migracji replikacji maszyn lokalnych na platformie Azure lub maszyn wirtualnych platformy Azure do regionu pomocniczego. Następnie należy przełączyć maszynę Wirtualną w tryb failover z lokacji głównej do regionu pomocniczego i ukończyć proces migracji. Nie ma powrotu po awarii.  


## <a name="migration-scenarios"></a>Scenariusze migracji

**Scenariusz** | **Szczegóły**
--- | ---
**Migrowanie ze środowiska lokalnego na platformę Azure** | Można migrować lokalnych maszyn wirtualnych z programu VMware, maszyny wirtualne funkcji Hyper-V i serwerów fizycznych na platformę Azure. W tym celu należy wykonać prawie te same kroki podobnie jak w przypadku odzyskiwania po awarii w pełnej. Po prostu nie zakończyć się niepowodzeniem maszynami powrót po awarii z platformy Azure do lokacji lokalnej.
**Migracja między regionami platformy Azure** | Można migrować maszyny wirtualne platformy Azure między regionami platformy Azure do innego. Po zakończeniu migracji można skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure, obecnie dostępna w regionie pomocniczym, do którego przeprowadzono migrację.
**Migrowanie usługi AWS na platformę Azure** | Możesz zmigrować wystąpienia usługi AWS na maszyny wirtualne platformy Azure. Usługa Site Recovery traktuje wystąpień usługi AWS, jako serwerów fizycznych na potrzeby migracji. 

## <a name="next-steps"></a>Kolejne kroki

- [Migrowanie maszyn lokalnych na platformę Azure](migrate-tutorial-on-premises-azure.md)
- [Migrowanie maszyn wirtualnych między regionami świadczenia usługi Azure](azure-to-azure-tutorial-migrate.md)
- [Migrowanie usługi AWS na platformę Azure](migrate-tutorial-aws-azure.md)
