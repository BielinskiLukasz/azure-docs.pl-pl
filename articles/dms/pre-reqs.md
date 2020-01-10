---
title: Wymagania wstępne dotyczące Azure Database Migration Service
description: Dowiedz się więcej na temat wymagań wstępnych dotyczących korzystania z Azure Database Migration Service w celu przeprowadzenia migracji bazy danych.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 7ba317da9524c322d47fe57a866d429ff8f7e952
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748739"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Przegląd wymagań wstępnych dotyczących używania Azure Database Migration Service

Istnieje kilka wymagań wstępnych, aby zapewnić bezproblemowy przebieg Azure Database Migration Service podczas przeprowadzania migracji baz danych. Niektóre wymagania wstępne dotyczą wszystkich scenariuszy (par Source-Target) obsługiwanych przez usługę, a inne wymagania wstępne są unikatowe dla konkretnego scenariusza.

Wymagania wstępne związane z używaniem Azure Database Migration Service są wymienione w poniższych sekcjach.

## <a name="prerequisites-common-across-migration-scenarios"></a>Wymagania wstępne typowe dla scenariuszy migracji

Azure Database Migration Service wymagania wstępne, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji, to m.in.:

* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej (sieciowej grupy zabezpieczeń) nie blokują następujących portów komunikacyjnych 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych może zajść potrzeba dodania reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Włączony protokół TCP/I (domyślnie wyłączony podczas instalacji programu SQL Server Express). Aby go włączyć, wykonaj czynności opisane w artykule [Enable or Disable a Server Network Protocol (Włączanie lub wyłączanie protokołu sieciowego serwera)](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Utworzenie wystąpienia Azure Database Migration Service wymaga dostępu do wirtualnych ustawień networt, które zwykle nie należą do tej samej grupy zasobów. W związku z tym użytkownik tworzący wystąpienie elementu DMS wymaga uprawnień na poziomie subskrypcji. Aby utworzyć wymagane role, które można przypisać w razie potrzeby, uruchom następujący skrypt:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.DataMigration/services/*/read", `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Wymagania wstępne dotyczące migrowania SQL Server do Azure SQL Database

Oprócz Azure Database Migration Service wymagań wstępnych, które są wspólne dla wszystkich scenariuszy migracji, istnieją także wymagania wstępne, które są stosowane w odniesieniu do jednego scenariusza lub innego.

W przypadku używania Azure Database Migration Service do przeprowadzenia SQL Server do migracji Azure SQL Database, oprócz wymagań wstępnych, które są wspólne dla wszystkich scenariuszy migracji, należy pamiętać o spełnieniu następujących dodatkowych wymagań wstępnych:

* Utwórz wystąpienie Azure SQL Database wystąpienia, które możesz wykonać, postępując zgodnie ze szczegółowymi informacjami w artykule C tworzenie[bazy danych SQL Azure w Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) w wersji 3.3 lub nowszej.
* Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla serwera usługi Azure SQL Database, aby umożliwić usłudze Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla Azure Database Migration Service.
* Sprawdź, czy poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem programu SQL Server mają uprawnienia [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Sprawdź, czy poświadczenia użyte do nawiązania połączenia z docelowym wystąpieniem usługi Azure SQL Database mają uprawnienie CONTROL DATABASE do docelowych baz danych Azure SQL Database.

   > [!NOTE]
   > Aby zapoznać się z pełną listą wymagań wstępnych wymaganych Azure Database Migration Service do przeprowadzenia migracji z SQL Server do Azure SQL Database, zapoznaj się z samouczkiem [migrowanie SQL Server do Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Wymagania wstępne dotyczące migrowania SQL Server do wystąpienia zarządzanego Azure SQL Database

* Utwórz Azure SQL Database wystąpienie zarządzane, postępując zgodnie ze szczegółowymi informacjami w artykule [tworzenie Azure SQL Database wystąpienia zarządzanego w Azure Portal](https://aka.ms/sqldbmi).
* Otwórz zaporę, aby zezwolić na ruch SMB na porcie 445 dla adresu IP lub zakresu podsieci Azure Database Migration Service.
* Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.
* Utworzenie udziału sieciowego umożliwiającego wykonanie kopii zapasowej źródłowej bazy danych za pomocą usługi Azure Database Migration Service.
* Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
* Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage w celu wykonania operacji przywracania.
* Utwórz kontener obiektów blob i Pobierz jego identyfikator URI sygnatury dostępu współdzielonego, wykonując kroki opisane w artykule [Zarządzanie zasobami BLOB Storage platformy Azure przy użyciu Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Pamiętaj, aby podczas tworzenia identyfikatora URI SAS wybrać opcję wszystkie uprawnienia (odczyt, zapis, usuwanie, lista) w oknie zasady.

   > [!NOTE]
   > Aby zapoznać się z pełną listą wymagań wstępnych potrzebnych do korzystania z Azure Database Migration Service w celu przeprowadzenia migracji z SQL Server do Azure SQL Database wystąpienia zarządzanego, zobacz samouczek [migrowanie SQL Server do Azure SQL Database wystąpienia zarządzanego](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure Database Migration Service i regionalnej dostępności, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).
