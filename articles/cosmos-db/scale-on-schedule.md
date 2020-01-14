---
title: Skalowanie Azure Cosmos DB według harmonogramu przy użyciu czasomierza Azure Functions
description: Dowiedz się, jak skalować zmiany w przepływności w Azure Cosmos DB przy użyciu programu PowerShell i Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75935170"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Skalowanie przepływności Azure Cosmos DB przy użyciu wyzwalacza Azure Functions czasomierza

Wydajność konta usługi Azure Cosmos zależy od ilości alokowanej przepływności wyrażonej w jednostkach żądań na sekundę (RU/s). Inicjowanie obsługi jest z drugiej stopnia szczegółowości i jest rozliczane na podstawie najwyższych jednostek RU/s na godzinę. Ten model zaprowizowaną pojemnością włącza usługę w celu zapewnienia przewidywalnych i spójne przepływności, gwarantowanych małych opóźnień i wysokiej dostępności. Większość obciążeń produkcyjnych te funkcje. Jednak w środowiskach deweloperskich i testowych, w których Azure Cosmos DB są używane tylko w godzinach pracy, można skalować przepustowość w górę i skalować ją z góry po godzinach pracy.

Przepływność można ustawić za pomocą [szablonów Azure Resource Manager](resource-manager-samples.md), [interfejsu wiersza polecenia platformy Azure](cli-samples.md)i [programu PowerShell](powershell-samples-sql.md)dla kont interfejsu API programu Core (SQL) lub przy użyciu Azure Cosmos DB zestawów SDK specyficznych dla języka. Zaletą korzystania z szablonów Menedżer zasobów, interfejsu wiersza polecenia platformy Azure lub programu PowerShell jest obsługa wszystkich Azure Cosmos DB interfejsów API modelu.

## <a name="throughput-scheduler-sample-project"></a>Przykładowy projekt harmonogramu przepływności

Aby uprościć proces skalowania Azure Cosmos DB według harmonogramu utworzyliśmy przykładowy projekt o nazwie [harmonogram przepływności usługi Azure Cosmos](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Ten projekt jest aplikacją Azure Functionsą z dwoma wyzwalaczami czasomierza — "ScaleUpTrigger" i "ScaleDownTrigger". Wyzwalacze uruchamiają skrypt programu PowerShell, który ustawia przepływność dla każdego zasobu, zgodnie z definicją w pliku `resources.json` w każdym wyzwalaczu. ScaleUpTrigger jest skonfigurowany do uruchamiania w godzinach 8 czasu UTC, a ScaleDownTrigger jest skonfigurowany do uruchamiania o godzinie 6 godzin UTC, a czasy te można łatwo aktualizować w pliku `function.json` dla każdego wyzwalacza.

Można sklonować ten projekt lokalnie, zmodyfikować go w celu określenia zasobów Azure Cosmos DB do skalowania w górę i w dół oraz harmonogramu do uruchomienia. Później możesz wdrożyć go w ramach subskrypcji platformy Azure i zabezpieczyć przy użyciu tożsamości usługi zarządzanej z uprawnieniami [Access Control opartych na rolach](role-based-access-control.md) (RBAC) z rolą "operator Azure Cosmos DB", aby ustawić przepływność na kontach usługi Azure Cosmos.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej i Pobierz przykład z usługi [Azure Cosmos DB Scheduler przepływności](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
