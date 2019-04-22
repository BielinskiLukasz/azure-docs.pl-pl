---
title: Szablony usługi Azure Resource Manager dla usługi SQL Database | Microsoft Docs
description: Używaj szablonów usługi Azure Resource Manager do tworzenia i konfigurowania usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 1e2f55d334d432544ffc185e5c68dfe4325b2b09
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59356825"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Szablony usługi Azure Resource Manager dla usługi Azure SQL Database

Szablony usługi Azure Resource Manager pozwalają zdefiniować Twoją infrastrukturę jako kod, a następnie wdrożyć Twoje rozwiązania w chmurze platformy Azure.

## <a name="single-database--elastic-pool"></a>Pojedyncza baza danych i elastyczna pula

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Azure SQL Database.

| |  |
|---|---|
| [Pojedyncza baza danych](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Ten szablon usługi Azure Resource Manager tworzy pojedynczą bazę danych Azure SQL z serwerem logicznym i konfiguruje reguły zapory. |
| [Serwer logiczny](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Ten szablon usługi Azure Resource Manager tworzy serwer logiczny dla usługi Azure SQL Database. |
| [Elastyczna pula](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ten szablon umożliwia wdrożenie nowej elastycznej puli za pomocą jej nowo skojarzonego serwera SQL i nowych baz danych SQL do przypisania do niej. |
| [Grupy trybu failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ten szablon tworzy dwa serwery logiczne usługi Azure SQL, bazę danych SQL i grupę trybu failover.|
| [Advanced Threat Protection](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-advanced-threat-protection-server-policy) | Ten szablon umożliwia wdrożenie serwera logicznego usługi Azure SQL z włączoną usługą Advanced Threat Protection i opcjonalną usługą Azure SQL Database. Usługa SQL Advanced Threat Protection to ujednolicony pakiet zaawansowanych możliwości zabezpieczeń SQL.|
| [Wykrywanie zagrożeń](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ten szablon umożliwia wdrożenie serwera logicznego usługi Azure SQL i zestawu baz danych SQL Azure z włączonym wykrywaniem zagrożeń z adresem e-mail dla alertów dla każdej bazy danych. Wykrywanie zagrożeń jest częścią oferty SQL Advanced Threat Protection (ATP) i zapewnia warstwę zabezpieczeń, która reaguje na potencjalne zagrożenia dla serwerów SQL i baz danych.|
| [Przeprowadzanie inspekcji w usłudze Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ten szablon umożliwia wdrożenie serwera logicznego usługi Azure SQL z włączoną inspekcją w celu zapisywania dzienników inspekcji do magazynu obiektów blob. Inspekcja usługi Azure SQL Database śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji, który można umieścić na Twoim koncie magazynu platformy Azure, w obszarze roboczym pakietu OMS lub w centrach zdarzeń.|
| [Przeprowadzanie inspekcji w Centrum zdarzeń platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ten szablon umożliwia wdrożenie serwera usługi Azure SQL z włączoną inspekcją w celu zapisywania dzienników inspekcji do istniejącego Centrum zdarzeń. Aby wysyłać zdarzenia inspekcji do Centrum zdarzeń, ustaw ustawienia inspekcji za pomocą `Enabled` `State` i ustaw `IsAzureMonitorTargetEnabled` jako `true`. Ponadto skonfiguruj ustawienia diagnostyczne przy użyciu kategorii dzienników diagnostycznych `SQLSecurityAuditEvents` w bazie danych `master` (dla inspekcji na poziomie serwera). Inspekcja usługi Azure SQL Database i SQL Data Warehouse śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji, który można umieścić na koncie magazynu platformy Azure, w obszarze roboczym pakietu OMS lub centrach zdarzeń.|
| [Aplikacja internetowa platformy Azure z usługą SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Ten przykład tworzy bezpłatną aplikację internetową platformy Azure i bazę danych SQL na poziomie usługi „Podstawowa”.|
| [Aplikacja internetowa platformy Azure i pamięć podręczna Redis Cache z usługą SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ten szablon tworzy aplikację internetową, pamięć podręczną Redis Cache i bazę danych SQL w tej samej grupie zasobów oraz tworzy dwa zestawy parametrów połączenia w aplikacji internetowej dla bazy danych SQL i pamięci podręcznej Redis Cache.|
| [Importowanie danych z magazynu obiektów blob za pomocą usługi ADF w wersji 2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Ten szablon usługi Azure Resource Manager tworzy usługę Azure Data Factory w wersji 2, która kopiuje dane z usługi Azure Blob Storage do usługi SQL Database.|
| [Klaster HDInsight z usługą SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ten szablon umożliwia tworzenie klastra HDInsight, serwera bazy danych SQL, bazy danych SQL i dwóch tabel. Ten szablon jest używany przez artykuł [Używanie składnika Sqoop z usługą Hadoop w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Aplikacja logiki platformy Azure, która uruchamia procedurę składowaną SQL zgodnie z harmonogramem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ten szablon umożliwia tworzenie aplikacji logiki, która będzie uruchamiać procedurę składowaną SQL zgodnie z harmonogramem. Wszelkie argumenty dla procedury można umieścić w sekcji treści szablonu.|

## <a name="managed-instance"></a>Wystąpienie zarządzane

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Azure SQL Database — wystąpienie zarządzane.

| |  |
|---|---|
| [Wystąpienie zarządzane w nowej sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Ten szablon usługi Azure Resource Manager tworzy nową skonfigurowaną sieć wirtualną platformy Azure i wystąpienie zarządzane w sieci wirtualnej. |
| [Środowisko sieciowe dla wystąpienia zarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | To wdrożenie utworzy skonfigurowaną sieć wirtualną platformy Azure z dwiema podsieciami — jedną, która będzie przeznaczona do Twoich wystąpień zarządzanych, i drugą, gdzie można umieścić inne zasoby (na przykład maszyny wirtualne, środowiska usługi App Service, itd.). Ten szablon utworzy prawidłowo skonfigurowane środowisko sieciowe, w którym możesz wdrożyć wystąpienia zarządzane. |
| [Wystąpienie zarządzane z połączeniem P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | To wdrożenie utworzy sieć wirtualną platformy Azure z dwiema podsieciami `ManagedInstance` i `GatewaySubnet`. Wystąpienie zarządzane będzie wdrażane w podsieci ManagedInstance. Brama sieci wirtualnej zostanie utworzona w podsieci `GatewaySubnet` i skonfigurowana dla połączenia sieci VPN typu punkt-lokacja. |
| [Wystąpienie zarządzane z maszyną wirtualną](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | To wdrożenie utworzy sieć wirtualną platformy Azure z dwiema podsieciami `ManagedInstance` i `Management`. Wystąpienie zarządzane zostanie wdrożone w podsieci `ManagedInstance`. Maszyna wirtualna z najnowszą wersją programu SQL Server Management Studio (SSMS) zostanie wdrożona w podsieci `Management`. |
