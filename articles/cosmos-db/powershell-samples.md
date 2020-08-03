---
title: Przykłady sla programu Azure PowerShell dla usługi Azure Cosmos DB
description: Pobierz przykłady Azure PowerShell, aby wykonywać typowe zadania w Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506658"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Przykłady sla programu Azure PowerShell dla usługi Azure Cosmos DB

Poniższa tabela zawiera linki do często używanych skryptów Azure PowerShell dla Azure Cosmos DB. Użyj linków po prawej stronie, aby przejść do przykładów interfejsu API. Typowe przykłady są takie same dla wszystkich interfejsów API. W [odwołaniu Azure PowerShell](/powershell/module/az.cosmosdb)dostępne są strony referencyjne dla wszystkich poleceń cmdlet programu PowerShell w programie Azure Cosmos DB. Sprawdź `Az.CosmosDB` regularnie aktualizacje. Możesz również utworzyć rozwidlenie tych przykładów programu PowerShell dla Cosmos DB z naszego repozytorium GitHub, [Cosmos DB przykłady programu PowerShell w witrynie GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Typowe przykłady

|Zadanie | Opis |
|---|---|
|[Aktualizowanie konta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie domyślnego poziomu spójności konta Cosmos DB. |
|[Aktualizowanie regionów konta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie regionów konta Cosmos DB. |
|[Zmiana priorytetu trybu failover lub wyzwolenie trybu failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień priorytet pracy w trybie failover dla konta usługi Azure Cosmos lub Wyzwól ręczną pracę awaryjną. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie Wygeneruj klucz konta Azure Cosmos DBego konta. |
|[Tworzenie konta Cosmos za pomocą zapory IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB z włączoną zaporą IP. |
|||

## <a name="core-sql-api-samples"></a>Przykłady interfejsu API programu Core (SQL)

|Zadanie | Opis |
|---|---|
|[Tworzenie konta, bazy danych i kontenera](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB, bazę danych i kontener. |
|[Tworzenie konta, bazy danych i kontenera z funkcją automatycznego skalowania](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto Azure Cosmos DB, bazę danych i kontener przy użyciu funkcji automatycznego skalowania. |
|[Tworzenie kontenera z dużym kluczem partycji](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz kontener z dużym kluczem partycji. |
|[Tworzenie kontenera bez zasad indeksu](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Utwórz kontener usługi Azure Cosmos z wyłączonymi zasadami indeksu.|
|[Wyświetlanie listy lub pobieranie baz danych lub kontenerów](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetlanie listy lub pobieranie bazy danych lub kontenerów. |
|[Uzyskiwanie informacji o przepływności](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uzyskaj przepływność dla bazy danych lub kontenera. |
|[Przepustowość aktualizacji](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie przepływności dla bazy danych lub kontenera. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||

## <a name="cassandra-api-samples"></a>Przykłady interfejs API Cassandra

|Zadanie | Opis |
|---|---|
|[Tworzenie konta, przestrzeni kluczy i tabeli](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, przestrzeń kluczy i tabelę. |
|[Tworzenie konta, przestrzeni kluczy i tabeli przy użyciu funkcji automatycznego skalowania](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, przestrzeń kluczy i tabelę z funkcją automatycznego skalowania. |
|[Wyświetlanie lub pobieranie miejsc lub tabel](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetlanie lub pobieranie miejsc lub tabel. |
|[Uzyskiwanie informacji o przepływności](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uzyskaj przepływność dla przestrzeni kluczy lub tabeli. |
|[Przepustowość aktualizacji](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie przepływności dla przestrzeni kluczy lub tabeli. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||

## <a name="mongo-db-api-samples"></a>Przykłady interfejsu API usługi Mongo DB

|Zadanie | Opis |
|---|---|
|[Tworzenie konta, bazy danych i kolekcji](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto, bazę danych i kolekcję usługi Azure Cosmos. |
|[Tworzenie konta, bazy danych i kolekcji z funkcją automatycznego skalowania](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, bazę danych i kolekcję z funkcją automatycznego skalowania. |
|[Wyświetlanie listy lub pobieranie baz danych lub kolekcji](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetlanie listy lub pobieranie bazy danych lub kolekcji. |
|[Uzyskiwanie informacji o przepływności](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uzyskaj przepływność dla bazy danych lub kolekcji. |
|[Przepustowość aktualizacji](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie przepływności dla bazy danych lub kolekcji. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||

## <a name="gremlin-api-samples"></a>Przykłady interfejsu API Gremlin

|Zadanie | Opis |
|---|---|
|[Tworzenie konta, bazy danych i grafu](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, bazę danych i Graf. |
|[Tworzenie konta, bazy danych i grafu przy użyciu funkcji automatycznego skalowania](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos, bazę danych i Graf z automatycznym skalowaniem. |
|[Wyświetlanie listy lub pobieranie baz danych lub wykresów](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetl lub Pobierz bazę danych lub Graf. |
|[Uzyskiwanie informacji o przepływności](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uzyskaj przepływność dla bazy danych lub grafu. |
|[Przepustowość aktualizacji](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie przepływności dla bazy danych lub grafu. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||

## <a name="table-api-samples"></a>Przykłady interfejs API tabel

|Zadanie | Opis |
|---|---|
|[Tworzenie konta i tabeli](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto i tabelę platformy Azure Cosmos. |
|[Tworzenie konta i tabeli przy użyciu funkcji automatycznego skalowania](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto usługi Azure Cosmos i automatyczne skalowanie tabeli. |
|[Wyświetlanie listy lub Pobieranie tabel](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wyświetl listę lub Pobierz tabele. |
|[Uzyskiwanie informacji o przepływności](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uzyskaj przepływność dla tabeli. |
|[Przepustowość aktualizacji](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie przepływności dla tabeli. |
|[Zablokuj zasoby przed usunięciem](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zapobiegaj usunięciu zasobów z blokadami zasobów. |
|||
