---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API MongoDB
description: Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524567"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB API MongoDB

Poniższa tabela zawiera linki do przykładowych skryptów interfejsu wiersza polecenia platformy Azure służących do Azure Cosmos DB interfejsu API MongoDB. Strony referencyjne dla wszystkich poleceń dostępnych w interfejsie wiersza polecenia usługi Azure Cosmos DB są dostępne w [dokumentacji dotyczącej interfejsu wiersza polecenia platformy Azure](/cli/azure/cosmosdb). Wszystkie przykłady skryptów interfejsu wiersza polecenia Azure Cosmos DB można znaleźć w [repozytorium GitHub Azure Cosmos DB interfejsu wiersza polecenia](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Obecnie można utworzyć tylko 3,2 wersję (czyli konta korzystające z punktu końcowego w formacie `*.documents.azure.com`) interfejsu API Azure Cosmos DB dla kont MongoDB przy użyciu szablonów programu PowerShell, interfejsu wiersza polecenia i Menedżer zasobów. Aby utworzyć 3,6 wersji kont, należy zamiast tego użyć Azure Portal.

| |  |
|---|---|
| [Tworzenie konta, bazy danych i kolekcji usługi Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto Azure Cosmos DB, bazę danych i kolekcję dla interfejsu API MongoDB. |
| [Zmień przepływność](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Zaktualizuj RU/s w bazie danych i kolekcji.|
| [Dodawanie regionów lub przełączanie do trybu failover](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Dodaj region, Zmień priorytet trybu failover i Wyzwól ręczny tryb failover.|
| [Klucze konta i parametry połączenia](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Wyświetl listę kluczy konta, klucze tylko do odczytu, Wygeneruj ponownie klucze i Wyświetl listę parametrów połączenia.|
| [Zabezpieczanie za pomocą zapory IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto Cosmos z skonfigurowaną zaporą IP.|
| [Zabezpiecz nowe konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto Cosmos i zabezpiecz je za pomocą punktów końcowych usługi.|
| [Zabezpiecz istniejące konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Zaktualizuj konto usługi Cosmos, aby zabezpieczyć za pomocą punktów końcowych, gdy podsieć jest ostatecznie skonfigurowana.|
|||
