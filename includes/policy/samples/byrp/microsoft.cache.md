---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 901b36e5b0e31752a9145245298d446e61a91aee
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85313230"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Pamięć podręczna platformy Azure dla Redis powinna znajdować się w sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Ta zasada przeprowadza inspekcję, czy zasób pamięci podręcznej platformy Azure dla usługi Redis ma niepubliczny punkt końcowy przez wdrożenie w sieci wirtualnej. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
