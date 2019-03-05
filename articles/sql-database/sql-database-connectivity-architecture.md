---
title: Kierowanie ruchu platformy Azure do usługi Azure SQL Database i SQL Data Warehouse | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano usługi Azure SQL Database i SQL Data Warehouse architektura łączności z w obrębie platformy Azure lub z spoza platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: dde54894867811a34340f6dd4d0236b6b54e8144
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307721"
---
# <a name="azure-sql-connectivity-architecture"></a>Architektura łączności usługi Azure SQL

W tym artykule opisano usługi Azure SQL Database i SQL Data Warehouse architektura łączności również, jak różne składniki funkcji do kierowania ruchu do swojego wystąpienia usługi Azure SQL. Tych funkcji składniki łączności do kierowania ruchu sieciowego do usługi Azure SQL Database lub SQL Data Warehouse z klientów łączących się z w obrębie platformy Azure i klientów łączących się z spoza platformy Azure. Ten artykuł zawiera także przykłady skryptów, aby zmienić sposób występuje łączności i zagadnienia związane z Zmienianie domyślnych ustawień połączenia.

> [!IMPORTANT]
> **[Nadchodzącą zmianą] Dla połączeń punkt końcowy usługi z serwerami usługi Azure SQL `Default` zachowanie łączności zmienia `Redirect`.**
> Aby utworzyć nowe serwery i ustaw istniejącymi klientami przy użyciu typu połączenia jawnie ustawione przekierowania (preferowany) lub serwera Proxy w zależności od ich architektura łączności doradza się klientów.
>
> Aby uniemożliwić łączność za pośrednictwem punktu końcowego usługi podziału w istniejących środowiskach, w wyniku tej zmiany, używamy telemetrii wykonaj następujące czynności:
>
> - W przypadku serwerów, które zostanie wykryte, które były dostępne za pośrednictwem punktów końcowych usługi przed zmianą, firma Microsoft przełącznika typ połączenia na `Proxy`.
> - Dla innych serwerów, możemy przełączyć połączenia typu zostaną przełączeni na `Redirect`.
>
> Nadal może mieć wpływ na użytkowników punktu końcowego usługi w następujących scenariuszach:
>
> - Aplikacja łączy się z istniejącego serwera rzadko, nasza telemetria nie przechwyciła informacji o tych aplikacjach
> - Logika automatycznego wdrażania tworzy serwer bazy danych SQL, przy założeniu, że to domyślne zachowanie dla połączeń punkt końcowy usługi `Proxy`
>
> Jeśli nie można ustanowić połączenia punktu końcowego usługi serwera Azure SQL i są podejrzeń, że dotyczy ta zmiana, sprawdź, czy typ połączenia jawnie ustawiono `Redirect`. Jeśli jest to możliwe, należy otworzyć reguły zapory na maszynie Wirtualnej i sieciowych grup zabezpieczeń (NSG) dla wszystkich adresów IP platformy Azure w regionie, które należą do bazy danych Sql [tag usługi](../virtual-network/security-overview.md#service-tags) dla portów 11000 12000. Jeśli nie jest dostępną opcją w, przełączyć serwer jawnie na `Proxy`.
> [!NOTE]
> W tym temacie mają zastosowanie do serwerów usługi Azure SQL Database hostingu pojedynczych baz danych i elastycznych pul i baz danych SQL Data Warehouse. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

## <a name="connectivity-architecture"></a>Architektura łączności

Na poniższym diagramie przedstawiono ogólny przegląd architektury połączenia usługi Azure SQL Database.

![Omówienie architektury](./media/sql-database-connectivity-architecture/connectivity-overview.png)

W poniższych krokach opisano, jak jest nawiązywane połączenie z bazą danych Azure SQL:

- Klienci połączenia z bramą, ma publiczny adres IP, która nasłuchuje na porcie 1433.
- Brama w zależności od zasad połączenia skuteczne, przekierowuje lub serwery proxy ruchu do klastra właściwej bazy danych.
- Wewnątrz bazy danych ruchu klastra jest przekazywany do odpowiedniej bazy danych Azure SQL.

## <a name="connection-policy"></a>Zasady połączenia

Usługa Azure SQL Database obsługuje poniższe trzy opcje ustawienia zasad połączenia z serwerem bazy danych SQL:

- **Przekierowanie (zalecane):** Klienci ustanowić połączenia bezpośrednio z węzeł, w którym baza danych. Aby włączyć łączność, klienci muszą zezwalać na reguły zapory dla ruchu wychodzącego dla wszystkich adresów IP platformy Azure w regionie przy użyciu grup zabezpieczeń sieci (NSG) za pomocą [tagów usług](../virtual-network/security-overview.md#service-tags)) dla portów 11000 12000, nie tylko adres IP bramy usługi Azure SQL Database adresy na porcie 1433. Ponieważ pakietów przejść bezpośrednio do bazy danych, opóźnienia i przepływności mają większą wydajność.
- **Serwer proxy:** W tym trybie wszystkie połączenia są przekazywane za pośrednictwem bramy usługi Azure SQL Database. Aby włączyć łączność, klient musi mieć wychodzących reguł zapory zezwalających wyłącznie bramy usługi Azure SQL Database, adresy IP (zazwyczaj dwa adresy IP na region). Wybór ten tryb może spowodować wyższe opóźnienie i niższych przepływności, w zależności od rodzaju obciążenia. Zdecydowanie zaleca się `Redirect` zasad połączenia za pośrednictwem `Proxy` zasady połączenia do najmniejszego opóźnienia i najwyższy przepływność.
- **Wartość domyślna:** To połączenie zasady obowiązywać na wszystkich serwerach po utworzeniu, chyba że jawnie zmienić zasady połączenia albo `Proxy` lub `Redirect`. Efektywnych zasad zależy od tego, czy połączenia pochodzą z w obrębie platformy Azure (`Redirect`) lub spoza platformy Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Łączność z w obrębie platformy Azure

Jeśli łączysz się z w obrębie platformy Azure połączenia mają zasady połączenia `Redirect` domyślnie. Zasady `Redirect` oznacza, że po ustanowieniu sesji TCP do bazy danych Azure SQL sesji klienta jest następnie przekierowywane do klastra właściwej bazy danych w przypadku zmiany docelowego wirtualnego adresu IP niż w przypadku bramy usługi Azure SQL Database klaster. Dzięki temu wszystkie kolejne pakiety przepływ bezpośrednio do klastra, z pominięciem bramy usługi Azure SQL Database. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Omówienie architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Łączność z spoza platformy Azure

Jeśli nawiązujesz połączenie spoza platformy Azure, Twoje połączenia mają zasady połączenia `Proxy` domyślnie. Zasady `Proxy` oznacza, że zostanie utworzona sesja protokołu TCP, za pośrednictwem bramy usługi Azure SQL Database i wszystkie kolejne pakiety przepływu za pośrednictwem bramy. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Omówienie architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresy IP bramy usługi Azure SQL Database

Aby nawiązać połączenie z bazą danych Azure SQL z zasobami lokalnymi, należy zezwolić na wychodzący ruch sieciowy do bramy usługi Azure SQL Database dla regionu platformy Azure. Połączenia go tylko za pośrednictwem bramy podczas nawiązywania połączenia w `Proxy` tryb, w którym jest domyślną kolekcją podczas nawiązywania połączenia z zasobów lokalnych.

Poniższa tabela zawiera listę podstawowych i pomocniczych adresów IP bramy usługi Azure SQL Database dla wszystkich obszarach danych. W niektórych regionach istnieją dwa adresy IP. W tych regionach podstawowy adres IP jest bieżący adres IP bramy, a drugi adres IP jest adresem IP trybu failover. Adres trybu failover jest adresem, do którego możemy przenieść serwer zapewnienie wysokiej dostępności usługi. W tych regionach zaleca się zezwolenie wychodzące adresy IP. Drugi adres IP jest własnością firmy Microsoft i nie będzie nasłuchiwać na wszystkie usługi, dopóki nie zostanie aktywowany przez usługę Azure SQL Database do akceptowania połączeń.

| Nazwa regionu | Podstawowy adres IP | Adres IP pomocniczego |
| --- | --- |--- |
| Australia Wschodnia | 13.75.149.87 | 40.79.161.1 |
| Australia Południowo-Wschodnia | 191.239.192.109 | 13.73.109.251 |
| Brazylia Południowa | 104.41.11.5 | |
| Kanada Środkowa | 40.85.224.249 | |
| Kanada Wschodnia | 40.86.226.166 | |
| Środkowe stany USA | 23.99.160.139 | 13.67.215.62 |
| Chiny wschodnie 1 | 139.219.130.35 | |
| Chiny Wschodnie 2 | 40.73.82.1 | |
| Chiny Północne 1 | 139.219.15.17 | |
| Chiny Północne 2 | 40.73.50.0 | |
| Azja Wschodnia | 191.234.2.139 | 52.175.33.150 |
| Wschodnie stany USA 1 | 191.238.6.43 | 40.121.158.30 |
| Wschodnie stany USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Francja Środkowa | 40.79.137.0 | 40.79.129.1 |
| Niemcy Środkowe | 51.4.144.100 | |
| Niemcy północny wschód | 51.5.144.179 | |
| Indie Środkowe | 104.211.96.159 | |
| Indie Południowe | 104.211.224.146 | |
| Indie Zachodnie | 104.211.160.80 | |
| Japonia Wschodnia | 191.237.240.43 | 13.78.61.196 |
| Japonia Zachodnia | 191.238.68.11 | 104.214.148.156 |
| Korea Środkowa | 52.231.32.42 | |
| Korea Południowa | 52.231.200.86 |  |
| Środkowo-północne stany USA | 23.98.55.75 | 23.96.178.199 |
| Europa Północna | 191.235.193.75 | 40.113.93.91 |
| Środkowo-południowe stany USA | 23.98.162.75 | 13.66.62.124 |
| Azja Południowo-Wschodnia | 23.100.117.95 | 104.43.15.0 |
| Południowe Zjednoczone Królestwo | 51.140.184.11 | |
| Środkowo-zachodnie stany USA | 13.78.145.25 | |
| Europa Zachodnia | 191.237.232.75 | 40.68.37.158 |
| Zachodnie stany USA 1 | 23.99.34.75 | 104.42.238.205 |
| Zachodnie stany USA 2 | 13.66.226.202 | |
||||

\* **UWAGA:** *Wschodnie stany USA 2* ma również trzeciorzędny adres IP `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Zmień zasady połączenia usługi Azure SQL Database

Aby zmienić zasady połączenia usługi Azure SQL Database dla serwera usługi Azure SQL Database, użyj [zasad połąc](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) polecenia.

- Jeśli ustawiono zasady połączenia `Proxy`, wszystkich sieci przepływu pakietów za pośrednictwem bramy usługi Azure SQL Database. To ustawienie, należy zezwolić na ruch wychodzący do IP bramy usługi Azure SQL Database. Przy użyciu ustawień programu `Proxy` ma więcej opóźnienia, niż ustawienie `Redirect`.
- Jeśli to ustawienie zasad połączenia `Redirect`, wszystkich sieci przepływu pakietów bezpośrednio z klastrem bazy danych. To ustawienie, należy zezwolić na ruch wychodzący do wielu adresów IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skrypt, aby zmienić ustawienia połączenia za pośrednictwem programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Ten skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell pokazuje, jak zmienić zasady połączenia.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skrypt, aby zmienić ustawienia połączenia za pośrednictwem wiersza polecenia platformy Azure

> [!IMPORTANT]
> Ten skrypt wymaga [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Poniższy skrypt interfejsu wiersza polecenia pokazuje, jak zmienić zasady połączenia.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje na temat zmiany zasad połączenia usługi Azure SQL Database dla serwera usługi Azure SQL Database, zobacz [zasad połąc](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Dla informacji o zachowanie połączenia usługi Azure SQL Database dla klientów korzystających z platformy ADO.NET 4.5 lub nowszej wersji, zobacz [portów wyższych niż 1433 dla platformy ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Informacje ogólne programowania ogólnego zastosowania, zobacz [omówienie tworzenia aplikacji bazy danych SQL](sql-database-develop-overview.md).
