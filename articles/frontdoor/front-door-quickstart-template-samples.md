---
title: Przykłady szablonów usługi Azure Resource Manager — drzwi frontowe platformy Azure
description: Przykłady szablonów usługi Azure Resource Manager dla drzwi y frontowej platformy Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2020
ms.author: sharadag
ms.openlocfilehash: 61ce63b15d2126a25b444e97acc8a3ea3964296b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985817"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Szablony modelu wdrażania usługi Azure Resource Manager dla usługi Azure Front Door

Poniższa tabela zawiera łącza do szablonów modelu wdrażania usługi Azure Resource Manager dla drzwi frontowych platformy Azure. 

| | |
| ---| ---|
| [Tworzenie podstawowej usługi Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Tworzy podstawową konfigurację usługi Front Door z jednym zapleczem. |
| [Tworzenie usługi Front Door z wieloma zapleczami i pulami zapleczy oraz routingiem opartym na adresach URL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Tworzy usługę Front Door z równoważeniem obciążenia skonfigurowanym pod kątem wielu zapleczy w puli zapleczy oraz w wielu zapleczach opartych na ścieżce adresu URL. |
| [Wbudowana domena niestandardowa z drzwiami frontowymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Dodawanie do usługi Front Door domeny niestandardowej. |
| [Tworzenie drzwi przednich z filtrem geograficznym](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Utwórz drzwi frontowe, które zezwalają/blokują ruch z niektórych krajów/regionów. |
| [Kontrolowanie sond kondycji zapleczy w usłudze Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Zaktualizuj usługę Front Door, aby zmienić ustawienia sondy kondycji, aktualizując ścieżkę sondowania oraz odstępy czasu wysyłania sond. |
| [Tworzenie usługi Front Door z aktywną konfiguracją zaplecza lub konfiguracją zaplecza w stanie wstrzymania](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Tworzy usługę Front Door, która przedstawia oparty na priorytetach routing na potrzeby topologii aplikacji aktywnej/w stanie wstrzymania. Oznacza to domyślne wysyłanie całego ruchu do zaplecza głównego (z najwyższym priorytetem), aż do momentu, gdy stanie się ono niedostępne. |
| [Tworzenie usługi Front Door z buforowaniem włączonym dla niektórych tras](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Tworzy usługę Front Door z buforowaniem włączonym dla zdefiniowanej konfiguracji routingu. Oznacza to buforowanie wszystkich statycznych elementów zawartości dostępnych dla obciążenia. |
| [Konfigurowanie koligacji sesji dla nazw hostów usługi Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Aktualizuje usługę Front Door, aby włączyć koligację sesji w obrębie hosta frontonu i umożliwić w ten sposób wysyłanie dalszego ruchu z tej samej sesji użytkownika do tego samego zaplecza. |
| [Konfigurowanie usługi Front Door na potrzeby umieszczania adresów IP na liście dozwolonych lub zabronionych](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Konfiguruje usługę Front Door tak, aby ograniczała ruch związany z adresami IP określonych klientów przy użyciu funkcji kontroli dostępu niestandardowego korzystającej z adresów IP klientów. |
| [Konfigurowanie drzwi przednich do działania z określonymi parametrami http](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Konfiguruje usługę Front Door tak, aby zezwalała na określonych ruch lub blokowała go na podstawie parametrów protokołu HTTP w żądaniu przychodzącym za pomocą niestandardowych reguł kontroli dostępu, które stosują parametry protokołu HTTP. |
| [Konfigurowanie ograniczania szybkości drzwi przednich](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Konfiguruje usługę Front Door tak, aby ograniczała szybkość ruchu przychodzącego dla danego hosta frontonu. |
| | |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
