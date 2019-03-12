---
title: Ustaw zmienną działania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć działania Ustaw zmienną można ustawić wartość istniejącej zmiennej zdefiniowanych w potoku usługi fabryka danych
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 71abfdff629f36b278488851b546c7371353a4d9
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575294"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustaw zmienną działania w usłudze Azure Data Factory

Użyj działania Ustaw zmienną można ustawić wartość istniejącej zmiennej typu String, Bool lub tablica, zdefiniowanych w potoku usługi fabryka danych.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Yes
description | Tekst opisujący, co działanie robi | nie
type | Typ działania jest SetVariable | tak
wartość | Ciąg literału lub wyrażenie obiektu wartość używana do ustawiania określonej zmiennej | tak
nazwa_zmiennej | Nazwa zmiennej, która zostanie ustawiona przez to działanie | tak


## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat obsługiwanych przez usługę Data Factory działanie przepływu sterowania pokrewne: 

- [Dołącz działania zmiennej](control-flow-append-variable-activity.md)
