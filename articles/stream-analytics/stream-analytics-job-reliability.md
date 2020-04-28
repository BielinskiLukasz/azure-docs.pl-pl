---
title: Unikaj przerw w działaniu usługi w Azure Stream Analytics zadania
description: W tym artykule opisano wskazówki dotyczące konfigurowania odporności Stream Analytics zadań.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75425988"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Gwarancja Stream Analytics niezawodność zadania podczas aktualizacji usługi

Częścią usługi jest w pełni zarządzaną usługą jest możliwość wprowadzenia nowych funkcji usługi i ulepszeń w szybkim tempie. W związku z tym Stream Analytics można wdrożyć aktualizację usługi co tydzień (lub kilka części). Bez względu na to, jak dużo testów jest wykonywane, istnieje ryzyko, że istniejące, uruchomione zadanie może zostać przerwane z powodu wprowadzenia błędu. W przypadku uruchamiania zadań o kluczowym znaczeniu należy unikać tych zagrożeń. Możesz zmniejszyć to ryzyko, wykonując na **[sparowanym modelu regionów](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** platformy Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak te zagadnienia są rozwiązywane w przypadku powiązanych regionów platformy Azure?

Stream Analytics gwarantuje, że zadania w sparowanych regionach są aktualizowane w oddzielnych partiach. W związku z tym istnieje wystarczająco dużo czasu między aktualizacjami do identyfikowania potencjalnych problemów i ich korygowania.

_Z wyjątkiem Indii indyjskich_ (w przypadku których w przypadku sparowanego regionu, Indie Południowe, nie ma obecności Stream Analytics), wdrożenie aktualizacji Stream Analytics nie wystąpi w tym samym czasie w zestawie sparowanych regionów. Wdrożenia w wielu regionach **w tej samej grupie** mogą wystąpić w tym **samym czasie**.

Artykuł dotyczący **[dostępności i sparowanych regionów](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** zawiera najbardziej aktualne informacje na temat tego, które regiony są sparowane.

Zaleca się wdrożenie identycznych zadań w obu sparowanych regionach. Następnie należy [monitorować te zadania](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) , aby otrzymywać powiadomienia o nieoczekiwanym wystąpieniu. Jeśli jedno z tych zadań kończy [się niepowodzeniem](https://docs.microsoft.com/azure/stream-analytics/job-states) po aktualizacji usługi Stream Analytics, możesz skontaktować się z działem pomocy technicznej, aby pomóc w zidentyfikowaniu głównej przyczyny. Należy również przełączać się do trybu failover dla wszystkich klientów podrzędnych w dobrej kondycji.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Stream Analytics](stream-analytics-introduction.md)
* [Wprowadzenie do usługi Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Stream Analytics Dokumentacja języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Dokumentacja interfejsu API REST zarządzania Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
