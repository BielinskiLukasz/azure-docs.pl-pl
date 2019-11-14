---
title: Konfigurowanie grupy dostępności w różnych regionach
description: W tym artykule wyjaśniono, jak skonfigurować SQL Server grupę dostępności na maszynach wirtualnych platformy Azure z repliką w innym regionie.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 96b7c3cf59f947d1476ad840ae81695356d869b6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037551"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Konfigurowanie grupy dostępności na platformie Azure SQL Server maszyny wirtualne w różnych regionach

W tym artykule wyjaśniono, jak skonfigurować usługę SQL Server zawsze włączona replika grupy dostępności na maszynach wirtualnych platformy Azure w zdalnej lokalizacji platformy Azure. Ta konfiguracja służy do obsługi odzyskiwania po awarii.

Ten artykuł ma zastosowanie do usługi Azure Virtual Machines w trybie Menedżer zasobów.

Na poniższej ilustracji przedstawiono typowe wdrożenie grupy dostępności na maszynach wirtualnych platformy Azure:

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

W tym wdrożeniu wszystkie maszyny wirtualne znajdują się w jednym regionie platformy Azure. Repliki grupy dostępności mogą mieć zatwierdzenie synchroniczne z automatycznym trybem failover w przypadku wersji SQL-1 i SQL-2. Aby skompilować tę architekturę, zobacz [szablon lub samouczek grupy dostępności](virtual-machines-windows-portal-sql-availability-group-overview.md).

Ta architektura jest narażona na przestoje w przypadku niedostępności regionu platformy Azure. Aby wyeliminować tę lukę w zabezpieczeniach, Dodaj replikę w innym regionie świadczenia usługi Azure. Na poniższym diagramie przedstawiono sposób, w jaki będzie wyglądać Nowa architektura:

   ![Grupa dostępności — DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Poprzedni diagram przedstawia nową maszynę wirtualną o nazwie SQL-3. SQL-3 znajduje się w innym regionie świadczenia usługi Azure. Do klastra trybu failover systemu Windows Server jest dodawana wartość SQL-3. Program SQL-3 może obsługiwać replikę grupy dostępności. Na koniec należy zauważyć, że region platformy Azure dla programu SQL-3 ma nowy moduł równoważenia obciążenia platformy Azure.

>[!NOTE]
> Zestaw dostępności platformy Azure jest wymagany, gdy więcej niż jedna maszyna wirtualna znajduje się w tym samym regionie. Jeśli w regionie znajduje się tylko jedna maszyna wirtualna, zestaw dostępności nie jest wymagany. Maszynę wirtualną można umieścić w zestawie dostępności w czasie jego tworzenia. Jeśli maszyna wirtualna jest już w zestawie dostępności, można później dodać maszynę wirtualną do dodatkowej repliki.

W tej architekturze replika w regionie zdalnym jest zwykle konfigurowana z trybem dostępności zatwierdzania asynchronicznego i trybem ręcznej pracy awaryjnej.

Gdy repliki grup dostępności znajdują się na maszynach wirtualnych platformy Azure w różnych regionach świadczenia usługi Azure, każdy region wymaga:

* Brama sieci wirtualnej
* Połączenie bramy sieci wirtualnej

Na poniższym diagramie przedstawiono, jak sieci komunikują się między centrami danych.

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Ta architektura wiąże się z opłatami za dane wychodzące dla danych replikowanych między regionami platformy Azure. Zobacz [Cennik przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Utwórz replikę zdalną

Aby utworzyć replikę w zdalnym centrum danych, wykonaj następujące czynności:

1. [Utwórz sieć wirtualną w nowym regionie](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Skonfiguruj połączenie między sieciami wirtualnymi przy użyciu Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >W niektórych przypadkach może być konieczne użycie programu PowerShell do utworzenia połączenia między sieciami wirtualnymi. Jeśli na przykład używasz różnych kont platformy Azure, nie możesz skonfigurować połączenia w portalu. W takim przypadku zobacz [Konfigurowanie połączenia między sieciami wirtualnymi przy użyciu Azure Portal](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Utwórz kontroler domeny w nowym regionie](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Ten kontroler domeny zapewnia uwierzytelnianie, Jeśli kontroler domeny w lokacji głównej jest niedostępny.

1. [Utwórz SQL Server maszynę wirtualną w nowym regionie](virtual-machines-windows-portal-sql-server-provision.md).

1. [Utwórz moduł równoważenia obciążenia platformy Azure w sieci w nowym regionie](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Ten moduł równoważenia obciążenia musi:

   - Należy do tej samej sieci i podsieci co Nowa maszyna wirtualna.
   - Mieć statyczny adres IP dla odbiornika grupy dostępności.
   - Uwzględnij pulę zaplecza składającą się tylko z maszyn wirtualnych znajdujących się w tym samym regionie co moduł równoważenia obciążenia.
   - Użyj sondy portu TCP określonego dla adresu IP.
   - Mieć regułę równoważenia obciążenia specyficzną dla SQL Server w tym samym regionie.  
   - Usługa Load Balancer w warstwie Standardowa, jeśli maszyny wirtualne w puli zaplecza nie są częścią pojedynczego zestawu dostępności lub zestawu skalowania maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [standardowe omówienie Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Dodaj funkcję Klaster trybu failover do nowej SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Dołącz do domeny nowe SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Ustaw nowe konto usługi SQL Server, aby korzystało z konta domeny](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Dodaj nowe SQL Server do klastra trybu failover z systemem Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Utwórz zasób adresu IP w klastrze.

   Zasób adresu IP można utworzyć w Menedżer klastra trybu failover. Kliknij prawym przyciskiem myszy rolę grupy dostępności, kliknij pozycję **Dodaj zasób**, **więcej zasobów**, a następnie kliknij pozycję **adres IP**.

   ![Utwórz adres IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Skonfiguruj ten adres IP w następujący sposób:

   - Użyj sieci z zdalnego centrum danych.
   - Przypisz adres IP z nowego modułu równoważenia obciążenia platformy Azure. 

1. Na nowych SQL Server w SQL Server Configuration Manager [Włącz zawsze włączone grupy dostępności](https://msdn.microsoft.com/library/ff878259.aspx).

1. [Otwórz porty zapory na nowym SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Numery portów, które należy otworzyć, zależą od danego środowiska. Otwórz porty dla punktu końcowego dublowania i sondy kondycji modułu równoważenia obciążenia platformy Azure.

1. [Dodaj replikę do grupy dostępności na nowym SQL Server](https://msdn.microsoft.com/library/hh213239.aspx).

   W przypadku repliki w zdalnym regionie świadczenia usługi Azure ustaw ją na replikację asynchroniczną z ręcznym trybem failover.  

1. Dodaj zasób adresu IP jako zależność dla klastra punktu dostępu klienta odbiornika (nazwy sieciowej).

   Poniższy zrzut ekranu przedstawia prawidłowo skonfigurowany zasób klastra adresów IP:

   ![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Grupa zasobów klastra obejmuje oba adresy IP. Oba adresy IP są zależnościami od punktu dostępu klienta odbiornika. Użyj operatora **or** w konfiguracji zależności klastra.

1. [Ustaw parametry klastra w programie PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Uruchom skrypt programu PowerShell z nazwą sieci klastra, adresem IP i portem sondy skonfigurowanym w module równoważenia obciążenia w nowym regionie.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Ustaw połączenie dla wielu podsieci

Replika w zdalnym centrum danych jest częścią grupy dostępności, ale znajduje się w innej podsieci. Jeśli replika zostanie repliką podstawową, mogą wystąpić limity czasu połączenia aplikacji. Takie zachowanie jest takie samo jak lokalna Grupa dostępności w ramach wdrożenia z obsługą kilku podsieci. Aby zezwolić na połączenia z aplikacji klienckich, zaktualizuj połączenie klienta lub skonfiguruj buforowanie rozpoznawania nazw dla zasobu Nazwa sieciowa klastra.

Najlepiej zaktualizować parametry połączenia klienta, aby ustawić `MultiSubnetFailover=Yes`. Zobacz [nawiązywanie połączenia z usługą MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Jeśli nie można zmodyfikować parametrów połączenia, można skonfigurować buforowanie rozpoznawania nazw. Zobacz [błąd limitu czasu i nie można nawiązać połączenia z odbiornikiem grupy dostępności funkcji AlwaysOn SQL Server 2012 w środowisku z obsługą kilku podsieci](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Przechodzenie w tryb failover do zdalnego regionu

Aby przetestować łączność odbiornika z regionem zdalnym, można przełączyć replikę w tryb failover do regionu zdalnego. Gdy replika jest asynchroniczna, tryb failover jest narażony na potencjalną utratę danych. Aby przełączyć się do trybu failover bez utraty danych, Zmień tryb dostępności na synchroniczny i ustaw tryb pracy awaryjnej na automatyczny. Wykonaj następujące czynności:

1. W **Eksplorator obiektów**Połącz się z wystąpieniem SQL Server, które obsługuje replikę podstawową.
1. W obszarze **zawsze włączone grupy dostępności**, **grupy dostępności**, kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij pozycję **Właściwości**.
1. Na stronie **Ogólne** w obszarze **repliki dostępności**Ustaw replikę pomocniczą w lokacji odzyskiwania w taki sposób, aby korzystała z **synchronicznego trybu dostępności zatwierdzania** i trybu **automatycznego** trybu failover.
1. Jeśli istnieje replika pomocnicza w tej samej lokacji co replika podstawowa w celu zapewnienia wysokiej dostępności, ustaw tę replikę na **asynchroniczne zatwierdzenie** i **ręcznie**.
1. Kliknij przycisk OK.
1. W **Eksplorator obiektów**kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij pozycję **Pokaż pulpit nawigacyjny**.
1. Na pulpicie nawigacyjnym Sprawdź, czy replika w witrynie DR jest synchronizowana.
1. W **Eksplorator obiektów**kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij pozycję **tryb failover...** . SQL Server Management Studios otwiera kreatora w celu przełączenia w tryb failover SQL Server.  
1. Kliknij przycisk **dalej**, a następnie wybierz wystąpienie SQL Server w witrynie Dr. Kliknij ponownie przycisk **dalej** .
1. Połącz się z wystąpieniem SQL Server w lokacji DR i kliknij przycisk **dalej**.
1. Na stronie **Podsumowanie** Sprawdź ustawienia i kliknij przycisk **Zakończ**.

Po przetestowaniu łączności należy przenieść replikę podstawową z powrotem do głównego centrum danych i ustawić tryb dostępności z powrotem na normalne ustawienia operacyjne. W poniższej tabeli przedstawiono normalne ustawienia operacyjne dla architektury opisanej w tym dokumencie:

| Lokalizacja | Wystąpienie serwera | Rola | Tryb dostępności | Tryb pracy awaryjnej
| ----- | ----- | ----- | ----- | -----
| Podstawowe centrum danych | SQL-1 | Podstawowy | Wykonywane | Automatyczny
| Podstawowe centrum danych | SQL-2 | Pomocniczy | Wykonywane | Automatyczny
| Pomocnicze lub zdalne centrum danych | SQL-3 | Pomocniczy | Komunikacji | Ręcznie


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Więcej informacji o planowanym i wymuszonym ręcznym przejściu w tryb failover

Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Wykonaj zaplanowaną ręczną pracę awaryjną grupy dostępności (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Wykonaj wymuszoną ręczną pracę awaryjną grupy dostępności (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Dodatkowe linki

* [Zawsze włączone grupy dostępności](https://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Moduły równoważenia obciążenia platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Zestawy dostępności platformy Azure](../manage-availability.md)
