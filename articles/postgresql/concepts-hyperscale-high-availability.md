---
title: Wysoka dostępność w Azure Database for PostgreSQL — skalowanie (Citus)
description: Pojęcia dotyczące wysokiej dostępności i odzyskiwania po awarii
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616b5bff735f52d137c12c58ac6023c38a2d4044
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514747"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Wysoka dostępność w Azure Database for PostgreSQL — skalowanie (Citus)

Wysoka dostępność (HA) pozwala uniknąć przestojów bazy danych przez utrzymywanie replik w trybie rezerwy dla każdego węzła w grupie serwerów. Jeśli węzeł ulegnie awarii, przełączają połączenia przychodzące z węzła niepowodzenie do stanu wstrzymania. Tryb failover występuje w ciągu kilku minut, a podwyższone węzły zawsze mają nowe dane za pomocą synchronicznej replikacji przesyłania strumieniowego PostgreSQL.

Aby korzystać z wysokiej dostępności na węźle koordynatora, aplikacje bazy danych muszą wykrywać i ponawiać próby porzucenia połączeń i transakcji zakończonych niepowodzeniem. Nowo podwyższony koordynator będzie dostępny z tymi samymi parametrami połączenia.

Odzyskiwanie można podzielić na trzy etapy: wykrywanie, przełączanie do trybu failover i pełne odzyskiwanie.  Funkcja wieloskalowania uruchamia okresowe kontrole kondycji w każdym węźle, a po czterech nieprawidłowych sprawdzeniach określa, że węzeł nie działa. Skalowanie następnie promuje stan wstrzymania do stanu węzła podstawowego (tryb failover) i Inicjuje nowe wstrzymanie.
Rozpocznie się replikacja przesyłania strumieniowego, co spowoduje, że nowy węzeł jest aktualny.  Gdy wszystkie dane zostały zreplikowane, węzeł osiągnął pełne odzyskiwanie.

### <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [włączyć wysoką dostępność](howto-hyperscale-high-availability.md) w grupie serwerów w skali.
