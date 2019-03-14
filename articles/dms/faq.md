---
title: Często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service | Dokumentacja firmy Microsoft
description: Dowiedz się, często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service do przeprowadzania migracji bazy danych.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: a3b32fcb7d776bd127db55ba06cfc526574e839b
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790881"
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service
Ten artykuł zawiera listę często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service wraz z powiązane odpowiedzi.

### <a name="q-what-is-azure-database-migration-service"></a>PYTANIE: Co to jest usługa Azure Database Migration Service?
Azure Database Migration Service to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemową migrację z wielu źródłowych baz danych na danych na platformę Azure przy minimalnych przestojach. Usługa jest obecnie ogólnie dostępna, za pomocą ciągłego programistycznych koncentruje się na:
- Niezawodność i wydajność.
- Iteracyjne dodatkowych par źródło cel.
- Dalsze inwestycje w bezproblemowe migracje.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>PYTANIE: Jakie par źródło cel usługi Azure Database Migration Service obsługuje obecnie?
Usługa obecnie obsługuje wiele różnych scenariuszy migracji. Aby uzyskać pełną listę stan poszczególnych scenariuszy migracji dostępne, zobacz artykuł [stan scenariusze migracji z obsługiwanych przez usługi Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status). Inne scenariusze migracji znajdują się w ograniczonej wersji zapoznawczej i wymaga przesyłanie nominacji za pośrednictwem witryny DMS (wersja zapoznawcza). Aby uzyskać pełną listę scenariuszy, w ograniczonej wersji zapoznawczej i rejestrowania do wzięcia udziału w jednym z tych ofert, zobacz [witryny w wersji zapoznawczej usługi DMS](https://aka.ms/dms-preview/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>PYTANIE: Jak wygląda porównanie usługi Azure Database Migration Service do innych narzędzi migracji bazy danych firmy Microsoft, takich jak bazy danych Migration Assistant (DMA) lub SQL Server Migration Assistant (SSMA)?
Azure Database Migration Service jest preferowaną metodą migracji bazy danych w systemie Microsoft Azure na dużą skalę. Aby uzyskać więcej szczegółów na porównanie usługi Azure Database Migration Service do innych firmy Microsoft bazy danych narzędzi migracji, a następnie uzyskać zalecenia dotyczące używania usługi dla różnych scenariuszy, zobacz ogłaszania blogu [rozróżnianie baza danych firmy Microsoft Narzędzia i usługi migracji](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>PYTANIE: Jak wygląda porównanie usługi Azure Database Migration Service do oferty usługi Azure Migrate?
Usługi Azure Migrate obsługuje migrację lokalnych maszyn wirtualnych IaaS platformy Azure. Usługa ocenia przydatność migracji i ustalania rozmiaru na podstawie wydajności i zapewnia szacowane koszty uruchamiania lokalnych maszyn wirtualnych na platformie Azure. Usługa Azure Migrate jest przydatne w przypadku migracji lift-and-shift lokalnych obciążeń opartych na maszynę Wirtualną do maszyn wirtualnych IaaS platformy Azure. Jednak w przeciwieństwie do usługi Azure Database Migration Service, usługa Azure Migrate nie usługa migracji bazy danych specjalne przeznaczone dla platform relacyjnej bazy danych modelu PaaS platformy Azure, takich jak Azure SQL Database lub SQL Azure lub wystąpienia zarządzanego Azure SQL Database.

### <a name="q-what-versions-of-sql-server-does-the-azure-database-migration-service-support-as-a-source"></a>PYTANIE: Jakie wersje programu SQL Server, Azure Database Migration Service obsługuje jako źródła?
Podczas migracji z programu SQL Server, Azure Database Migration Service obsługuje program SQL Server 2005 za pomocą programu SQL Server 2017.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>PYTANIE: Co to jest podsumowanie kroków wymaganych do przeprowadzenia migracji bazy danych przy użyciu usługi Azure Database Migration Service?
Podczas migracji typowych, prostych bazy danych możesz:
1.  Tworzenie bazy danych docelowych.
2.  Migrowanie schematu bazy danych przy użyciu [Asystenta migracji bazy danych](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Tworzenie wystąpienia usługi Azure Database Migration Service.
4.  Utwórz projekt migracji, określając źródłowych baz danych, docelowej bazy danych i tabele do migrowania.
5.  Zainicjuj pełne ładowanie.
6.  Wybierz następne sprawdzania poprawności.
7.  Wykonaj ręczne przełączenie środowiska produkcyjnego do nowej bazy danych opartej na chmurze. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>PYTANIE: Jakie są wymagania wstępne dotyczące korzystania z usługi Azure Database Migration Service?
Istnieje kilka wymagań wstępnych wymagane w celu zapewnienia, że usługi Azure Database Migration Service działa sprawnie podczas przeprowadzania migracji bazy danych. Pewne warunki wstępne mają zastosowanie we wszystkich scenariuszach (par źródło cel) obsługiwane przez usługę, podczas gdy inne wymagania wstępne są unikatowe dla konkretnego scenariusza.
Azure Database Migration Service wstępnie wymagane składniki, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji obejmują konieczność:
- Sieć wirtualna dla usługi Azure Database Migration Service utworzona przy użyciu modelu wdrożenia usługi Azure Resource Manager, która zapewnia łączność między lokacjami dla lokalnych serwerów źródłowych, z użyciem usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub sieci [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Sprawdzenie, czy reguły sieciowej grupy zabezpieczeń sieci wirtualnej Azure Virtual Network nie blokują następujących portów komunikacji: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń usługi Azure VNET, zapoznaj się z artykułem [Filter network traffic with network security groups (Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
 
Aby uzyskać listę wszystkich wymagań wstępnych dotyczących konkurować scenariusze dotyczące migracji za pomocą usługi Azure Database Migration Service, zobacz powiązanych samouczków w usłudze Azure Database Migration Service [dokumentacji](https://docs.microsoft.com/azure/dms/dms-overview) na witryny docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>PYTANIE: Jak znaleźć adres IP dla usługi Azure Database Migration Service, dzięki czemu można utworzyć listę dozwolonych reguł zapory umożliwiające dostęp do bazy danych źródłowego do migracji?
Może być konieczne dodanie reguły zapory, dzięki czemu Azure Database Migration Service dostęp do źródłowej bazy danych do migracji. Adres IP dla usługi jest dynamiczny, ale jeśli używasz programu Express Route, ten adres prywatnie jest przypisywany przez sieci firmowej. Najprostszym sposobem identyfikacji odpowiedniego adresu IP, który jest do wyszukania w tej samej grupie zasobów aprowizowanych zasobu usługi Azure Database Migration Service można znaleźć skojarzonego interfejsu sieciowego. Zazwyczaj nazwą zasobu interfejsu sieciowego zaczyna się od prefiksu karty Sieciowej i następuje znak unikatowy i numer sekwencji, na przykład jj6tnztnmarpsskr82rbndyp karty Sieciowej. Wybranie tego zasobu interfejsu sieciowego, możesz zobaczyć adres IP, który musi być uwzględniony na liście dozwolonych na Przegląd zasobów stronę witryny Azure portal.

Może być również konieczne obejmują źródło portu programu SQL Server nasłuchuje na liście dozwolonych. Domyślnie tego portu 1433, ale źródła, które można skonfigurować programu SQL Server w celu nasłuchiwania również innych portów. W takim przypadku konieczne jest uwzględnienie tych portów z listy dozwolonych. Można określić numer portu, którego nasłuchuje serwer SQL za pomocą zapytania dynamicznego widoku zarządzania:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Można także określić numer portu, którego nasłuchuje serwer SQL, badając dziennik błędów programu SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>PYTANIE: Czy istnieją wszystkie zalecenia dotyczące optymalizacji wydajności usługi Azure Database Migration Service?
Możesz zrobić kilka rzeczy, aby przyspieszyć migrację bazy danych przy użyciu usługi:
- Podczas tworzenia wystąpienia usługi, aby umożliwić usłudze móc korzystać z wielu procesorów wirtualnych dla przetwarzania równoległego i szybszy transfer danych za pomocą wielu Procesora ogólnego przeznaczenia warstwy cenowej.
- Tymczasowo skalowania w górę wystąpienia docelowej usługi Azure SQL Database do warstwy Premium jednostki SKU podczas operacji migracji danych, aby zminimalizować usługi Azure SQL Database, ograniczanie przepustowości, która może mieć wpływ na działania transferu danych podczas korzystania z jednostki SKU niższego poziomu.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>PYTANIE: Jak skonfigurować usługi Azure Virtual Network?
Podczas wiele samouczków firmy Microsoft, które prowadzi użytkownika przez proces konfigurowania sieci Wirtualnej platformy Azure, oficjalnej dokumentacji pojawia się w artykule [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>PYTANIE: Niedostępna lub zatrzymana, dlaczego jest Moje Azure Database Migration Service?
Użytkownik jawnie zatrzymuje usługi Azure Database Migration Service (DMS) lub usługi jest nieaktywny przez okres 24 godzin, usługa będzie znajdować się w zatrzymania lub auto stan wstrzymania. W obu przypadkach usługa będzie niedostępne i znajduje się w stanie zatrzymania.  Aby wznowić migracje aktywne, uruchom ponownie usługę.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>PYTANIE: Gdzie można pozostawić opinię na temat usługi Azure Database Migration Service
Chcemy poznać Twoją opinię. Wyślij opinię i / pomysły masz dotyczące usługi Azure Database Migration Service przy użyciu User Voice [tutaj](https://feedback.azure.com/forums/906100-azure-database-migration-service), lub kontaktując się z zespołem pod adresem [poproś migracje baz danych Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać omówienie usługi Azure Database Migration Service i dostępności regionalnej, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md). 
