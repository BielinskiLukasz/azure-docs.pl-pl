---
title: Szybki start — wystąpienie zarządzane usługi Azure SQL Database | Microsoft Docs
description: Dowiedz się, jak szybko rozpocząć pracę z usługą Azure SQL Database — wystąpienie zarządzane
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 3bf0f62b0a8d909231ad747435ce363e6686fe80
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874753"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Wprowadzenie do wystąpienia zarządzanego usługi Azure SQL Database

Opcja wdrożenia [wystąpienia zarządzanego](sql-database-managed-instance-index.yml) tworzy bazę danych w niemal 100% zgodną z najnowszą wersją lokalną aparatu bazy danych programu SQL Server (Enterprise Edition), zapewniając natywne wdrożenie [sieci wirtualnej (VNet)](../virtual-network/virtual-networks-overview.md), co rozwiązuje typowe problemy dotyczące zabezpieczeń, a także [model biznesowy](https://azure.microsoft.com/pricing/details/sql-database/) korzystny dla klientów z lokalnym programem SQL Server. W tym artykule dowiesz się, jak szybko skonfigurować i utworzyć wystąpienie zarządzane oraz migrować bazy danych.

## <a name="quickstart-overview"></a>Omówienie przewodnika Szybki start

Następujące przewodniki Szybki start umożliwiają szybkie utworzenie wystąpienia zarządzanego, skonfigurowanie maszyny wirtualnej lub połączenia sieci VPN typu punkt-lokacja dla aplikacji klienckiej oraz przywrócenie bazy danych w nowym wystąpieniu zarządzanym przy użyciu pliku `.bak`.

### <a name="configure-environment"></a>Konfigurowanie środowiska
Pierwszym krokiem powinno być utworzenie pierwszego wystąpienia zarządzanego przy użyciu środowiska sieciowego, w którym zostanie ono umieszczone, i włączenie połączenia z komputera lub maszyny wirtualnej, gdzie są wykonywane zapytania do wystąpienia zarządzanego. Możesz użyć następujących wytycznych:

- [Tworzenie wystąpienia zarządzanego przy użyciu witryny Azure Portal](sql-database-managed-instance-get-started.md). W witrynie Azure Portal skonfigurujesz wymagane parametry (nazwa użytkownika, hasło, liczba rdzeni i maksymalna pojemność magazynu) i automatycznie utworzysz środowisko sieciowe platformy Azure bez znajomości szczegółów sieci i wymagań dotyczących infrastruktury. Musisz jedynie upewnić się, że używany [typ subskrypcji](sql-database-managed-instance-resource-limits.md#supported-subscription-types) aktualnie zapewnia możliwość utworzenia wystąpienia zarządzanego. Jeśli masz własną sieć, której chcesz użyć, lub jeśli chcesz dostosować sieć, zobacz [konfigurowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-configure-vnet-subnet.md) lub [tworzenie sieci wirtualnej dla wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-create-vnet-subnet.md).
- Wystąpienie zarządzane jest tworzone we własnej sieci wirtualnej bez publicznego punktu końcowego. Na potrzeby dostępu aplikacji klienckiej możesz **utworzyć maszynę wirtualną w tej samej sieci wirtualnej (w innej podsieci)** lub **utworzyć połączenie sieci VPN typu punkt-lokacja z tą siecią wirtualną z komputera klienckiego** przy użyciu jednego z tych przewodników Szybki start:

  - Utwórz [maszynę wirtualną platformy Azure w sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-configure-vm.md) na potrzeby łączności aplikacji klienckiej, w tym programu SQL Server Management Studio.
  - Skonfiguruj [połączenie sieci VPN typu punkt-lokacja z wystąpieniem zarządzanym](sql-database-managed-instance-configure-p2s.md) na komputerze klienckim, na którym używasz programu SQL Server Management Studio i innych aplikacji łączności klienta. Jest to druga z dwóch opcji łączności z wystąpieniem zarządzanym i jego siecią wirtualną.

  > [!NOTE]
  > Możesz też użyć usługi ExpressRoute lub połączenia lokacja-lokacja z sieci lokalnej, lecz te podejścia są poza zakresem tych przewodników Szybki start.

### <a name="migrate-your-databases"></a>Migrowanie baz danych 
Po utworzeniu wystąpienia zarządzanego i skonfigurowaniu dostępu możesz rozpocząć migrowanie baz danych z programu SQL Server pracującego lokalnie lub na maszynach wirtualnych platformy Azure. Migracja zakończy się niepowodzeniem, jeśli migrowana źródłowa baza danych obejmuje nieobsługiwane funkcje. Aby uniknąć błędów i sprawdzić zgodność, możesz zainstalować narzędzie [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), które przeanalizuje wszystkie bazy danych w programie SQL Server i znajdzie wszelkie problemy mogące blokować migrację do wystąpień zarządzanych, takie jak obecność funkcji [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) lub wiele plików dziennika. Po rozwiązaniu tych problemów bazy danych będą gotowe na migrację do wystąpienia zarządzanego. [Asystent eksperymentowania z bazą danych](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) to kolejne przydatne narzędzie, które umożliwia rejestrowanie obciążeń programu SQL Server i odtwarzanie ich w wystąpieniu zarządzanym, co pozwala na określenie, czy po migracji do wystąpienia zarządzanego będą miały miejsce jakiekolwiek problemy z wydajnością.

Po upewnieniu się, że można migrować bazę danych do wystąpienia zarządzanego, możesz za pomocą natywnych możliwości przywracania programu SQL Server przywrócić bazę danych w wystąpieniu zarządzanym z pliku `.bak`. Tej metody możesz użyć do migracji baz danych z aparatu bazy danych programu SQL Server zainstalowanego lokalnie lub na maszynie wirtualnej platformy Azure. Aby szybko rozpocząć pracę, zobacz [Restore from backup to a managed instance](sql-database-managed-instance-get-started-restore.md) (Przywracanie z kopii zapasowej w wystąpieniu zarządzanym). W tym przewodniku Szybki start wykonasz przywracanie z pliku `.bak` przechowywanego w usłudze Azure Blob Storage przy użyciu polecenia języka Transact-SQL `RESTORE`. 

> [!TIP]
> Aby utworzyć kopię zapasową bazy danych w usłudze Azure Blob Storage za pomocą polecenia języka Transact-SQL `BACKUP`, zobacz [SQL Server backup to URL (Tworzenie kopii zapasowej programu SQL Server pod określonym adresem URL)](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Te przewodniki Szybki start umożliwiają szybkie utworzenie, skonfigurowanie i przywrócenie kopii zapasowej bazy danych w wystąpieniu zarządzanym. W przypadku niektórych scenariuszy należy dostosować lub zautomatyzować wdrożenie wystąpienia zarządzanego i wymaganego środowiska sieciowego. Te scenariusze są opisane poniżej.

## <a name="customize-network-environment"></a>Dostosowywanie środowiska sieciowego

Mimo że sieć wirtualną i podsieć można automatycznie skonfigurować podczas tworzenia wystąpienia przy użyciu witryny Azure Portal, przydatne może być utworzenie ich przed rozpoczęciem tworzenia wystąpień zarządzanych, ponieważ można wtedy skonfigurować parametry sieci wirtualnej i podsieci. Najprostszym sposobem utworzenia i skonfigurowania środowiska sieciowego jest użycie szablonu [wdrażania zasobów platformy Azure](sql-database-managed-instance-create-vnet-subnet.md), który utworzy oraz skonfiguruje sieć i podsieć dla wystąpienia zarządzanego. Wystarczy nacisnąć przycisk wdrażania usługi Azure Resource Manager i podać parametry w formularzu. 

Jako alternatywy możesz użyć [skryptu programu PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) do zautomatyzowania tworzenia sieci.

Jeśli masz już sieć wirtualną i podsieć, w których chcesz wdrożyć wystąpienie zarządzane, upewnij się, że sieć wirtualna i podsieć spełniają [wymagania dotyczące sieci](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Użyj tego [skryptu programu PowerShell, aby zweryfikować, czy podsieć jest poprawnie skonfigurowana](sql-database-managed-instance-configure-vnet-subnet.md). Ten skrypt zweryfikuje sieć i zgłosi problemy, określi, co należy zmienić, oraz zaoferuje możliwość wprowadzenia koniecznych zmian sieci wirtualnej lub podsieci. Uruchom ten skrypt, jeśli nie chcesz ręcznie konfigurować sieci wirtualnej i podsieci. Należy go także uruchomić po każdej znacznej zmianie konfiguracji infrastruktury sieciowej. Jeśli chcesz utworzyć i skonfigurować swoją własną sieć, zapoznaj się z informacjami o [architekturze łączności](sql-database-managed-instance-connectivity-architecture.md) oraz z tym [kompletnym przewodnikiem po tworzeniu i konfigurowaniu środowiska wystąpienia zarządzanego](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-a-managed-instance"></a>Automatyzowanie tworzenia wystąpienia zarządzanego

 Jeśli nie utworzono środowiska sieciowego zgodnie z opisem w poprzednim kroku, witryna Azure Portal może to zrobić za Ciebie — jedyną wadą tego rozwiązania jest fakt, że zostanie ono skonfigurowane przy użyciu wartości domyślnych dla niektórych parametrów, a tych wartości nie będzie można zmienić później. Ewentualnie możesz użyć następujących narzędzi:

- [Program PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
- [Program PowerShell z szablonem usługi Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [Interfejs wiersza polecenia platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>Migrowanie do wystąpienia zarządzanego z minimalnym przestojem

Artykuły w tych przewodnikach Szybki start umożliwiają szybkie skonfigurowanie wystąpienia zarządzanego i przeniesienie baz danych przy użyciu natywnej funkcji `RESTORE`. Jednak w przypadku natywnej funkcji `RESTORE` należy poczekać na przywrócenie baz danych (i skopiowanie ich do usługi Azure Blob Storage, jeśli nie są tam jeszcze przechowywane). Może to spowodować przestój aplikacji, szczególnie jeśli bazy danych są duże. Aby przenieść produkcyjną bazę danych, użyj usługi [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) w celu migrowania bazy danych z minimalnym przestojem. Usługa DMS realizuje to przez przyrostowe wypychanie zmian wprowadzanych w źródłowej bazie danych do bazy danych przywracanej w wystąpieniu zarządzanym. Dzięki niej można szybko przełączyć aplikację ze źródłowej do docelowej bazy danych z możliwie najkrótszym przestojem.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [ogólną listę obsługiwanych funkcji wystąpienia zarządzanego](sql-database-features.md) oraz [informacje szczegółowe i znane problemy](sql-database-managed-instance-transact-sql-information.md).
- Dowiedz się więcej o [charakterystykach technicznych wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
- Więcej instrukcji zaawansowanych można znaleźć w [dokumentacji używania wystąpienia zarządzanego w usłudze Azure SQL Database](sql-database-howto-managed-instance.md). 
