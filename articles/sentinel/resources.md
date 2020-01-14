---
title: Przydatne zasoby podczas pracy z platformą Azure — wskaźnikiem Microsoft Docs
description: Ten dokument zawiera listę przydatnych zasobów podczas pracy z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: 98c76b2be6a5cc801f9e7b31357cbeb158a966fe
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771295"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Przydatne zasoby do pracy z platformą Azure — wskaźnikiem



W tym artykule wymieniono zasoby, które mogą pomóc uzyskać więcej informacji na temat pracy z platformą Azure — wskaźnikiem.

Łączniki Azure Logic Apps: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Inspekcja i raportowanie
Dzienniki inspekcji platformy Azure są przechowywane w [dziennikach aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

Możliwe jest przeprowadzenie inspekcji następujących obsługiwanych operacji.

|Nazwa operacji|    Typ zasobu|
|----|----|
|Utwórz lub zaktualizuj skoroszyt  |Microsoft. Insights/skoroszyty|
|Usuń skoroszyt    |Microsoft. Insights/skoroszyty|
|Ustaw przepływ pracy   |Microsoft.Logic/workflows|
|Usuń przepływ pracy    |Microsoft.Logic/workflows|
|Utwórz zapisane wyszukiwanie    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Usuń zapisane wyszukiwanie    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Aktualizowanie reguł alertów |Microsoft. SecurityInsights/alertRules|
|Usuń reguły alertów |Microsoft. SecurityInsights/alertRules|
|Aktualizuj akcje odpowiedzi reguły alertu |Microsoft. SecurityInsights/alertRules|
|Usuń akcje odpowiedzi reguły alertu |Microsoft. SecurityInsights/alertRules|
|Aktualizuj zakładki   |Microsoft. SecurityInsights/zakładki|
|Usuń zakładki   |Microsoft. SecurityInsights/zakładki|
|Przypadki aktualizacji   |Microsoft. SecurityInsights/sprawy|
|Aktualizacja badania przypadku  |Microsoft. SecurityInsights/sprawy|
|Utwórz Komentarze do wielkości liter   |Microsoft. SecurityInsights/sprawy|
|Aktualizuj łączniki danych |Microsoft. SecurityInsights/dataconnecters|
|Usuń łączniki danych |Microsoft. SecurityInsights/dataconnecters|
|Aktualizuj ustawienia    |Microsoft. SecurityInsights/ustawienia|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Wyświetlanie danych inspekcji i raportowania na platformie Azure — wskaźnik

Te dane można wyświetlić, przesyłając je strumieniowo z dziennika aktywności platformy Azure do platformy Azure

1. Połącz źródło danych [aktywności platformy Azure](connect-azure-activity.md) . Po wykonaniu tej czynności zdarzenia inspekcji są przesyłane strumieniowo do nowej tabeli na ekranie **dzienników** o nazwie Azure.
2. Następnie wykonaj zapytanie o dane przy użyciu KQL, tak jak w przypadku każdej innej tabeli.



## <a name="vendor-documentation"></a>Dokumentacja dostawcy

| **Dostawca**  | **Korzystanie z zdarzeń na platformie Azure — wskaźnik** | **Link**|
|----|----|----|
| Witryna GitHub| Służy do uzyskiwania dostępu do strony społeczności| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Konfigurowanie CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kurs językowy zapytania Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogi i fora

Opublikuj swoje pytania w [obszarze TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) na potrzeby platformy Azure.

Wyświetl wpisy w blogu dotyczące platformy Azure wskaźnikowego z [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) i [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono listę zasobów, które są przydatne podczas pracy z platformą Azure — wskaźnikiem. Dodatkowe informacje na temat zabezpieczeń i zgodności platformy Azure można znaleźć na [blogu Microsoft Azure zabezpieczenia i zgodność](https://blogs.msdn.com/b/azuresecurity/).
