---
title: Rozszerzona pomoc techniczna dla SQL Server 2008 i SQL Server 2008 R2 z platformą Azure
description: Dowiedz się, jak rozszerzyć wsparcie dla SQL Server 2008 i SQL Server 2008 R2 przez Migrowanie wystąpienia SQL Server na platformę Azure lub zakup rozszerzonej pomocy technicznej, aby zachować wystąpienia lokalnie.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: adba197b4412177d0655fb6835cfdf8671a81f4e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846123"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Rozszerzona pomoc techniczna dla SQL Server 2008 i SQL Server 2008 R2 z platformą Azure

SQL Server 2008 i SQL Server 2008 R2 zbliżają się do [końca cyklu życia pomocy technicznej (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Ponieważ wielu klientów nadal używa obu wersji, udostępniamy kilka opcji, aby kontynuować pomoc techniczną. Lokalne wystąpienia SQL Server można migrować do usługi Azure Virtual Machines (VM), przeprowadzić migrację do Azure SQL Database lub zachować dostęp do lokalnych aktualizacji zabezpieczeń.

W przeciwieństwie do wystąpienia zarządzanego migracja do maszyny wirtualnej platformy Azure nie wymaga ponownej certyfikacji aplikacji. I w przeciwieństwie do lokalnego korzystania z usług lokalnych, migracja do maszyny wirtualnej platformy Azure spowoduje otrzymanie bezpłatnych rozszerzonych poprawek zabezpieczeń.

Pozostała część tego artykułu zawiera zagadnienia dotyczące migrowania wystąpienia SQL Server na maszynę wirtualną platformy Azure.

## <a name="provisioning"></a>Aprowizowanie

Istnieje płatność zgodnie z rzeczywistym użyciem, **SQL Server 2008 R2 w obrazie systemu Windows Server 2008 R2** dostępnym w witrynie Azure Marketplace.

Klienci korzystający z SQL Server 2008 będą musieli samodzielnie zainstalować lub uaktualnić do SQL Server 2008 R2. Podobnie klienci korzystający z systemu Windows Server 2008 będą musieli wdrożyć maszynę wirtualną na podstawie niestandardowego wirtualnego dysku twardego lub uaktualnić do systemu Windows Server 2008 R2.

Obrazy wdrożone za pomocą witryny Azure Marketplace są dostarczane z wstępnie zainstalowanym rozszerzeniem SQL IaaS. Rozszerzenie SQL IaaS jest wymaganiem do elastycznego licencjonowania i zautomatyzowanego stosowania poprawek. Klienci, którzy wdrażają samoinstalujące się maszyny wirtualne, będą musieli ręcznie zainstalować rozszerzenie SQL IaaS. Rozszerzenie SQL IaaS nie jest obsługiwane w systemie Windows Server 2008.

> [!NOTE]
> Mimo że SQL Server **tworzenia** i **zarządzania** blokami będzie działała z obrazem SQL Server 2008 R2 w Azure Portal, następujące funkcje _nie są obsługiwane_: Automatyczne kopie zapasowe, integracja Azure Key Vault, usługi języka R i Konfiguracja magazynu.

## <a name="licensing"></a>Licencjonowanie
Wdrożenia z opcją płatność zgodnie z rzeczywistym użyciem SQL Server 2008 R2 mogą być konwertowane na [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Aby przekonwertować licencję opartą na oprogramowaniu Software Assurance (SA) na płatność zgodnie z rzeczywistym użyciem, klienci powinni zarejestrować się u [dostawcy zasobów](virtual-machines-windows-sql-register-with-resource-provider.md)maszyny wirtualnej SQL. Po tej rejestracji typ licencji SQL będzie można zamiennie zmienić między Korzyść użycia hybrydowego platformy Azure i płatność zgodnie z rzeczywistym użyciem.

Samoinstalowane SQL Server 2008 lub SQL Server 2008 R2 wystąpień na maszynie wirtualnej platformy Azure można zarejestrować u dostawcy zasobów maszyny wirtualnej SQL i przekonwertować typ licencji na płatność zgodnie z rzeczywistym użyciem.

## <a name="migration"></a>Migracja
Można migrować wystąpienia EOS SQL Server na maszynę wirtualną platformy Azure przy użyciu metod ręcznego tworzenia kopii zapasowej/przywracania. Jest to najbardziej typowa Metoda migracji z lokalizacji lokalnej do maszyny wirtualnej platformy Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

W przypadku migracji zbiorczych zaleca się [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) usługi. Dzięki Azure Site Recovery klienci mogą replikować całą maszynę wirtualną, w tym SQL Server z lokalizacji lokalnej do maszyny wirtualnej platformy Azure.

Aby zagwarantować odzyskiwanie, SQL Server wymaga spójnych Azure Site Recovery migawek na poziomie aplikacji. Azure Site Recovery obsługuje migawki spójne z aplikacjami z co najmniej 1-godzinnym interwałem. Minimalny cel punktu odzyskiwania (RPO) możliwy dla SQL Server z Azure Site Recovery migracji wynosi 1 godzinę. Cel czasu odzyskiwania (RTO) to 2 godziny, a SQL Server czas odzyskiwania.

### <a name="database-migration-service"></a>Database Migration Service

[Database Migration Service](/azure/dms/dms-overview) jest opcją dla klientów w przypadku migrowania z lokalizacji lokalnej do maszyny wirtualnej platformy Azure przez uaktualnienie SQL Server do wersji 2012 lub nowszej.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Rozwiązania do odzyskiwania po awarii dla EOS SQL Server na maszynie wirtualnej platformy Azure są następujące:

- **SQL Server kopie zapasowe**: Użyj Azure Backup, aby pomóc w ochronie SQL Server EOS w odniesieniu do oprogramowania wymuszającego okup, przypadkowego usunięcia i uszkodzenia. Rozwiązanie jest obecnie dostępne w wersji zapoznawczej usługi EOS SQL Server i obsługuje SQL Server 2008 i 2008 R2 uruchomione w systemie Windows 2008 R2 z dodatkiem SP1. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2).
- **Wysyłanie dziennika**: Replikę wysyłania dziennika można utworzyć w innej strefie lub regionie platformy Azure z ciągłymi przywracaniami, aby zmniejszyć RTO. Musisz ręcznie skonfigurować wysyłanie dziennika.
- **Azure Site Recovery**: Możesz replikować maszynę wirtualną między strefami i regionami za poorednictwem Azure Site Recovery replikacji. SQL Server wymaga migawek spójnych z aplikacjami w celu zagwarantowania odzyskiwania w przypadku awarii. Azure Site Recovery oferuje co najmniej 1-godzinny cel punktu odzyskiwania oraz 2-godzinny (plus SQL Server) RTO na potrzeby odzyskiwania po awarii SQL Server.

## <a name="security-patching"></a>Stosowanie poprawek zabezpieczeń
Rozszerzone aktualizacje zabezpieczeń dla maszyn wirtualnych SQL Server są dostarczane za pośrednictwem kanałów Microsoft Update po zarejestrowaniu maszyny wirtualnej SQL Server z [dostawcą zasobów](virtual-machines-windows-sql-register-with-resource-provider.md)maszyny wirtualnej SQL. Poprawki można pobrać ręcznie lub automatycznie.

Opcja *Automatyczne stosowanie poprawek* jest domyślnie włączona. Automatyczne stosowanie poprawek umożliwia platformie Azure automatyczne stosowanie poprawek programu SQL Server i systemu operacyjnego. Jeśli zainstalowano rozszerzenie IaaS SQL Server, można określić dzień tygodnia, godzinę i czas trwania okna obsługi. Platforma Azure stosuje poprawki w tym oknie obsługi. Harmonogram okna obsługi korzysta z ustawień regionalnych godziny maszyny wirtualnej.  Aby uzyskać więcej informacji, zobacz [zautomatyzowane stosowanie poprawek dla SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Następne kroki

Migrowanie maszyny wirtualnej SQL Server na platformę Azure:

* [Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)

Rozpocznij pracę z usługą SQL Server na platformie Azure Virtual Machines:

* [Tworzenie maszyny wirtualnej z programem SQL Server w witrynie Azure Portal](quickstart-sql-vm-create-portal.md)

Uzyskaj odpowiedzi na często zadawane pytania dotyczące SQL Server maszyn wirtualnych:

* [Często zadawane pytania dotyczące SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)
