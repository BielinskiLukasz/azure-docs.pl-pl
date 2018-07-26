---
title: Wystąpienie usługi Azure SQL Database Managed łączenie aplikacji | Dokumentacja firmy Microsoft
description: W tym artykule omówiono sposób łączenia aplikacji z wystąpienia zarządzanego Azure SQL Database.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: c9d656908d265aeb6143e857b0ea4f635203bdd9
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258732"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Połącz aplikację z wystąpieniem zarządzanym usługi Azure SQL Database

Obecnie masz wiele opcji do wyboru podczas podejmowania decyzji o sposób i miejsce hostowania aplikacji. 
 
Istnieje możliwość hostowania aplikacji w chmurze, przy użyciu usługi Azure App Service lub niektórych opcji sieć wirtualną (VNet), zintegrowane platformy Azure, takich jak zestaw skalowania maszyn wirtualnych w usłudze Azure App Service Environment, maszyny wirtualnej. Można również wykonać chmura hybrydowa i zachować swoje aplikacje w środowisku lokalnym. 
 
Niezależnie od wyboru, które zostały wprowadzone, można połączyć go do wystąpienia zarządzanego (wersja zapoznawcza).  

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Łączenie aplikacji w tej samej sieci wirtualnej 

Ten scenariusz jest najprostsza. Maszynami wirtualnymi w sieci wirtualnej można połączyć ze sobą bezpośrednio, nawet jeśli znajdują się w różnych podsieciach. Oznacza to, że wszystko, czego potrzebujesz do łączenia z aplikacji wewnątrz środowiska aplikacji platformy Azure lub maszyny wirtualnej jest odpowiednie ustawienie parametrów połączenia.  
 
W przypadku, gdy nie można ustanowić połączenia, sprawdź, czy sieciowa grupa zabezpieczeń nastavit podsieci aplikacji. W takim przypadku należy otworzyć połączenia wychodzącego na SQL porcie 1433, a także 11000 12000 zakres portów dla przekierowania. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Łączenie aplikacji wewnątrz innej sieci wirtualnej 

Ten scenariusz jest nieco bardziej skomplikowane, ponieważ wystąpienie zarządzane jest prywatny adres IP we własnej sieci wirtualnej. Nawiązać połączenie, aplikacja musi mieć dostęp do sieci wirtualnej wdrożonym wystąpienia zarządzanego. Dlatego najpierw należy utworzyć połączenie między aplikacją i sieci wirtualnej wystąpienia zarządzanego. Sieci wirtualne nie muszą znajdować się w tej samej subskrypcji, aby zrealizować ten scenariusz. 
 
Dostępne są dwie opcje łączenia sieci wirtualnych: 
- [Komunikacja równorzędna sieci wirtualnych platformy Azure](../virtual-network/virtual-network-peering-overview.md) 
- Brama sieci VPN typu sieć wirtualna-sieć wirtualna ([witryny Azure portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
Ta opcja komunikacji równorzędnej jest preferowane jeden, ponieważ komunikację równorzędną używa sieci szkieletowej firmy Microsoft w związku z punktu widzenia łączności, nie ma żadnej różnicy zauważalnego opóźnienia między maszynami wirtualnymi w wirtualnej sieci równorzędnej i w tej samej sieci wirtualnej. Komunikacja równorzędna sieci wirtualnych jest ograniczona do sieci, w tym samym regionie.  
 
> [!IMPORTANT]
> Scenariusz komunikacji równorzędnej sieci wirtualnej dla wystąpienia zarządzanego jest ograniczona do sieci, w tym samym regionie, ze względu na [ograniczenia globalne wirtualne sieci równorzędne](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). 

## <a name="connect-an-on-premises-application"></a>Łączenie aplikacji w środowisku lokalnym 

Wystąpienie zarządzane jest możliwy tylko za pośrednictwem prywatnego adresu IP. Aby uzyskać do niego dostęp ze środowiska lokalnego, musisz utworzyć połączenie lokacja-lokacja między aplikacją i sieci wirtualnej wystąpienia zarządzanego. 
 
Jak połączyć lokalnych z siecią wirtualną platformy Azure dostępne są dwie opcje: 
- Połączenie sieci VPN typu lokacja-lokacja ([witryny Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [Usługa ExpressRoute](../expressroute/expressroute-introduction.md) połączenia  
 
Jeśli nie można ustanowić połączenia do wystąpienia zarządzanego środowiska lokalnego do platformy Azure połączenie zostało ustanowione pomyślnie, sprawdź, czy zapory ma otwarte połączeń wychodzących w SQL porcie 1433, a także 11000 12000 zakres portów dla przekierowania. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Łączenie aplikacji hostowanej usługi Azure App Service 

Wystąpienia zarządzanego jest możliwy wyłącznie za pośrednictwem prywatnego adresu IP, więc w celu uzyskania dostępu do usługi Azure App Service musisz najpierw utworzyć połączenie między aplikacją i sieci wirtualnej wystąpienia zarządzanego. Zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).  
 
Do rozwiązywania problemów, zobacz [aplikacji i rozwiązywanie problemów z sieciami wirtualnymi](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Jeśli nie można ustanowić połączenia, spróbuj [synchronizowanie konfiguracji sieci](sql-database-managed-instance-sync-network-configuration.md). 
 
Przypadek specjalny nawiązywania połączenia z usługi Azure App Service do wystąpienia zarządzanego jest po użytkownik zintegrowane usługi Azure App Service, aby sieć połączona z zarządzanych sieci wirtualnej wystąpienia. Ten przypadek wymaga następującej konfiguracji do skonfigurowania: 

- Zarządzane wystąpienia sieci wirtualnej nie może mieć bramy  
- Zarządzane wystąpienia sieci wirtualnej musi mieć ustawioną opcję bram zdalnych Użyj 
- Skomunikowanej równorzędnie sieci wirtualnej musi mieć opcję tranzyt bramy Zezwalaj ustawioną 
 
W tym scenariuszu przedstawiono na poniższym diagramie:

![Komunikacja równorzędna w zintegrowanej aplikacji](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Łączenie aplikacji w usłudze box deweloperów 

Wystąpienie zarządzane jest możliwy tylko za pośrednictwem prywatnego adresu IP tak aby można było uzyskać do niego dostęp z Twojego pola dla deweloperów, najpierw musisz utworzyć połączenie między usługami box usługi dla deweloperów i sieci wirtualnej wystąpienia zarządzanego.  
 
Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu artykułów dotyczących uwierzytelniania natywnych certyfikatu platformy Azure ([witryny Azure portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) w szczegółach jak może być wykonana.  

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Aby uzyskać samouczek omawiający Tworzenie nowego wystąpienia zarządzanego, zobacz [utworzysz wystąpienie zarządzane](sql-database-managed-instance-create-tutorial-portal.md).
