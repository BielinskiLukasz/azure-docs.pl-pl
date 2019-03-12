---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554076"
---
| Zasób | Limit |
| --- | --- |
| Rozmiar pamięci podręcznej |530 GB |
| Bazy danych |64 |
| Maksymalna liczba podłączonych klientów |40,000 |
| Pamięć podręczna systemu Azure dla repliki pamięci podręcznej Redis, w celu zapewnienia wysokiej dostępności |1 |
| Fragmentów w cache w warstwie premium z klastrowaniem |10 |

Ogranicza pamięć podręczna systemu Azure dla usługi Redis i rozmiary różnią się dla każdej warstwy cenowej. Aby wyświetlić warstw cenowych i ich skojarzone rozmiarów, zobacz [pamięci podręcznej Azure Redis cennik](https://azure.microsoft.com/pricing/details/cache/).

Aby uzyskać więcej informacji na temat usługi Azure Cache limitów konfiguracji pamięci podręcznej Redis, zobacz [Redis domyślna konfiguracja serwera](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Ponieważ konfiguracji i zarządzania usługi Azure cache dla wystąpień usługi Redis odbywa się przez firmę Microsoft, nie wszystkie polecenia usługi Redis są obsługiwane w pamięci podręcznej Azure dla usługi Redis. Aby uzyskać więcej informacji, zobacz [polecenia nie są obsługiwane w usłudze Azure Cache, dla usługi Redis Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

