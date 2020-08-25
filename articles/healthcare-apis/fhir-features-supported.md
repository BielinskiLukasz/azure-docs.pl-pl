---
title: Obsługiwane funkcje FHIR na platformie Azure — interfejs API platformy Azure dla FHIR
description: W tym artykule wyjaśniono, które funkcje specyfikacji FHIR wdrożone w interfejsie API platformy Azure dla FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 46568bf3969d050fd964c85278debd9d599db266
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796565"
---
# <a name="features"></a>Funkcje

Interfejs API platformy Azure dla usługi FHIR zapewnia w pełni zarządzane wdrożenie serwera programu Microsoft FHIR dla platformy Azure. Serwer jest implementacją standardu [FHIR](https://hl7.org/fhir) . Ten dokument zawiera listę głównych funkcji serwera FHIR.

## <a name="fhir-version"></a>Wersja FHIR

Najnowsza obsługiwana wersja: `4.0.1`

Obecnie obsługiwane są również poprzednie wersje: `3.0.2`

## <a name="rest-api"></a>Interfejs API REST

| interfejs API                            | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| przeczytaj                           | Tak       | Tak       | Tak       |                                                     |
| vread                          | Tak       | Tak       | Tak       |                                                     |
| update                         | Tak       | Tak       | Tak       |                                                     |
| Aktualizuj przy użyciu optymistycznego blokowania | Tak       | Tak       | Tak       |                                                     |
| Aktualizuj (warunkowo)           | Tak       | Tak       | Tak       |                                                     |
| wysłana                          | Nie        | Nie        | Nie        |                                                     |
| delete                         | Tak       | Tak       | Tak       |                                                     |
| Usuń (warunkowe)           | Nie        | Nie        | Nie        |                                                     |
| create                         | Tak       | Tak       | Tak       | Obsługa funkcji POST/PUT                               |
| Utwórz (warunkowo)           | Tak       | Tak       | Tak       |                                                     |
| search                         | Częściowe   | Częściowe   | Częściowe   | Zobacz poniżej                                           |
| Wyszukiwanie łańcuchowe                 | Nie        | Tak       | Nie        |                                           |
| Wyszukiwanie w łańcuchu wstecznym         | Nie        | Nie        | Nie        |                                            |
| możliwości                   | Tak       | Tak       | Tak       |                                                     |
| partia                          | Tak       | Tak       | Tak       |                                                     |
| Transaction                    | Nie        | Tak       | Nie        |                                                     |
| historia                        | Tak       | Tak       | Tak       |                                                     |
| stronicowania                         | Częściowe   | Częściowe   | Częściowe   | `self` i `next` są obsługiwane                     |
| pośredników                 | Nie        | Nie        | Nie        |                                                     |

## <a name="search"></a>Wyszukaj

Wszystkie typy parametrów wyszukiwania są obsługiwane. 

| Typ parametru wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-----------------------|-----------|-----------|-----------|---------|
| Liczba                | Tak       | Tak       | Tak       |         |
| Data/godzina         | Tak       | Tak       | Tak       |         |
| Ciąg                | Tak       | Tak       | Tak       |         |
| Token                 | Tak       | Tak       | Tak       |         |
| Dokumentacja             | Tak       | Tak       | Tak       |         |
| Złożenie             | Tak       | Tak       | Tak       |         |
| Ilość              | Tak       | Tak       | Tak       |         |
| URI                   | Tak       | Tak       | Tak       |         |
| Jako               | Nie        | Nie        | Nie        |         |


| Modyfikatory             | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Tak       | Tak       | Tak       |         |
|`:exact`               | Tak       | Tak       | Tak       |         |
|`:contains`            | Tak       | Tak       | Tak       |         |
|`:text`                | Tak       | Tak       | Tak       |         |
|`:in` klucza          | Nie        | Nie        | Nie        |         |
|`:below` klucza       | Nie        | Nie        | Nie        |         |
|`:above` klucza       | Nie        | Nie        | Nie        |         |
|`:not-in` klucza      | Nie        | Nie        | Nie        |         |
|`:[type]` odwoła  | Nie        | Nie        | Nie        |         |
|`:below` adresu         | Tak       | Tak       | Tak       |         |
|`:not`                 | Nie        | Nie        | Nie        |         |
|`:above` adresu         | Nie        | Nie        | Nie        | [#158](https://github.com/Microsoft/fhir-server/issues/158) problemu |

| Typowy parametr wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Tak       | Tak       | Tak       |         |
| `_lastUpdated`          | Tak       | Tak       | Tak       |         |
| `_tag`                  | Tak       | Tak       | Tak       |         |
| `_profile`              | Tak       | Tak       | Tak       |         |
| `_security`             | Tak       | Tak       | Tak       |         |
| `_text`                 | Nie        | Nie        | Nie        |         |
| `_content`              | Nie        | Nie        | Nie        |         |
| `_list`                 | Tak       | Tak       | Tak       |         |
| `_has`                  | Nie        | Nie        | Nie        |         |
| `_type`                 | Tak       | Tak       | Tak       |         |
| `_query`                | Nie        | Nie        | Nie        |         |

| Operacje wyszukiwania       | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | Nie        | Nie        | Nie        |         |
| `_sort`                 | Nie        | Nie        | Nie        |         |
| `_score`                | Nie        | Nie        | Nie        |         |
| `_count`                | Tak       | Tak       | Tak       |         |
| `_summary`              | Częściowe   | Częściowe   | Częściowe   | `_summary=count` jest obsługiwana |
| `_include`              | Nie        | Tak       | Nie        |         |
| `_revinclude`           | Nie        | Tak       | Nie        | Uwzględnione elementy są ograniczone do 100. |
| `_contained`            | Nie        | Nie        | Nie        |         |
| `_elements`             | Nie        | Nie        | Nie        |         |

## <a name="persistence"></a>Trwałość

Serwer programu Microsoft FHIR ma podłączany moduł trwałości (zobacz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Obecnie kod "open-source" serwera FHIR zawiera implementację [Azure Cosmos DB](../cosmos-db/index-overview.md) i [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB to baza danych oparta na globalnie rozproszonym modelu (SQL API, MongoDB API itp.). Obsługuje różne [poziomy spójności](../cosmos-db/consistency-levels.md). Domyślny szablon wdrożenia konfiguruje serwer FHIR ze `Strong` spójnością, ale zasady spójności mogą być modyfikowane (zwykle swobodne) na żądanie przez podstawę żądania przy użyciu `x-ms-consistency-level` nagłówka żądania.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Serwer FHIR używa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) na potrzeby kontroli dostępu. W konkretnym przypadku Access Control oparte na rolach (RBAC) są wymuszane, jeśli `FhirServer:Security:Enabled` parametr konfiguracji jest ustawiony na `true` , a wszystkie żądania (z wyjątkiem `/metadata` ) do serwera FHIR muszą mieć `Authorization` nagłówek żądania ustawiony na `Bearer <TOKEN>` . Token musi zawierać co najmniej jedną rolę zdefiniowaną w ramach tego `roles` żądania. Żądanie będzie dozwolone, jeśli token zawiera rolę, która zezwala na określoną akcję dla określonego zasobu.

Obecnie dozwolone akcje dla danej roli są stosowane *globalnie* w interfejsie API.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o obsługiwanych funkcjach FHIR w interfejsie API platformy Azure dla FHIR. Następnie wdróż interfejs API platformy Azure dla usługi FHIR.
 
>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)
