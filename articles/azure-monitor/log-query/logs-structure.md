---
title: Struktura dzienników Azure Monitor | Microsoft Docs
description: Wymagane jest zapytanie dziennika do pobrania danych dziennika z Azure Monitor.  W tym artykule opisano sposób używania nowych zapytań dzienników w Azure Monitor i przedstawiono koncepcje, które należy zrozumieć przed utworzeniem jednego.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662080"
---
# <a name="structure-of-azure-monitor-logs"></a>Struktura dzienników Azure Monitor
Możliwość szybkiego uzyskiwania wglądu w dane przy użyciu [zapytania dziennika](log-query-overview.md) jest zaawansowaną funkcją Azure monitor. Aby tworzyć wydajne i użyteczne zapytania, należy zapoznać się z podstawowymi pojęciami, takimi jak miejsce, w którym znajdują się dane i jak są one strukturalne. Ten artykuł zawiera podstawowe pojęcia, które należy wykonać, aby rozpocząć pracę.

## <a name="overview"></a>Omówienie
Dane w dziennikach Azure Monitor są przechowywane w obszarze roboczym Log Analytics lub w aplikacji Application Insights. Oba są obsługiwane przez [usługę Azure Eksplorator danych](/azure/data-explorer/) , co oznacza, że korzystają z zaawansowanego aparatu danych i języka zapytań.

Dane w obu obszarach roboczych i aplikacjach są zorganizowane w tabele, z których każdy przechowuje różne rodzaje danych i ma własny unikatowy zestaw właściwości. Większość [źródeł danych](../platform/data-sources.md) będzie zapisywać w własnych tabelach w log Analytics obszarze roboczym, podczas gdy Application Insights będzie zapisywać do wstępnie zdefiniowanego zestawu tabel w aplikacji Application Insights. Zapytania dzienników są bardzo elastyczne, co pozwala na łatwe łączenie danych z wielu tabel, a nawet użycie zapytania między zasobami w celu łączenia danych z tabel w wielu obszarach roboczych lub do pisania zapytań, które łączą dane obszaru roboczego i aplikacji.

Na poniższej ilustracji przedstawiono przykłady źródeł danych, które zapisują w różnych tabelach, które są używane w przykładowych zapytaniach.

![Tabele](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Wszystkie dane zbierane przez dzienniki Azure Monitor z wyjątkiem Application Insights są przechowywane w [log Analytics obszarze roboczym](../platform/manage-access.md). W zależności od konkretnych wymagań można utworzyć co najmniej jeden obszar roboczy. [Źródła danych](../platform/data-sources.md) , takie jak dzienniki aktywności i dzienniki zasobów, z zasobów platformy Azure, agentów na maszynach wirtualnych i danych z rozwiązań szczegółowych i monitorowania będą zapisywać dane do co najmniej jednego obszaru roboczego skonfigurowanego w ramach dołączania. Inne usługi, takie jak [Azure Security Center](/azure/security-center/) i [Azure](/azure/sentinel/) , wykorzystują również obszar roboczy log Analytics do przechowywania danych, dzięki czemu można je analizować przy użyciu zapytań dzienników oraz dane monitorowania z innych źródeł.

Różne rodzaje danych są przechowywane w różnych tabelach w obszarze roboczym, a każda tabela ma unikatowy zestaw właściwości. Standardowy zestaw tabel jest dodawany do obszaru roboczego, gdy zostanie utworzony, a nowe tabele są dodawane do różnych źródeł danych, rozwiązań i usług w miarę ich dołączania. Można również tworzyć tabele niestandardowe przy użyciu [interfejsu API modułu zbierającego dane](../platform/data-collector-api.md).

Możesz przeglądać tabele w obszarze roboczym i ich schemat na karcie **schemat** w log Analytics obszaru roboczego.

![Schemat obszaru roboczego](media/scope/workspace-schema.png)

Użyj poniższego zapytania, aby wyświetlić listę tabel w obszarze roboczym oraz liczbę rekordów zebranych w każdym dniu w ciągu poprzedniego dnia. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Zapoznaj się z dokumentacją dla każdego źródła danych, aby uzyskać szczegółowe informacje o utworzonych tabelach. Przykłady obejmują artykuły dotyczące [źródeł danych agentów](../platform/agent-data-sources.md), [dzienników zasobów](../platform/diagnostic-logs-schema.md)i [rozwiązań do monitorowania](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Uprawnienia obszaru roboczego
Zapoznaj się z artykułem [projektowanie wdrożenia dzienników Azure monitor](../platform/design-logs-deployment.md) , aby poznać strategię kontroli dostępu i zalecenia dotyczące zapewnienia dostępu do danych w obszarze roboczym. Poza udzieleniem dostępu do samego obszaru roboczego można ograniczyć dostęp do poszczególnych tabel przy użyciu funkcji [RBAC na poziomie tabeli](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplikacja Application Insights
W przypadku tworzenia aplikacji w Application Insights w dziennikach Azure Monitor zostanie automatycznie utworzona odpowiednia aplikacja. Do zbierania danych nie jest wymagana żadna konfiguracja, a aplikacja będzie automatycznie zapisywać dane monitorowania, takie jak wyświetlanie stron, żądania i wyjątki.

W przeciwieństwie do obszaru roboczego Log Analytics, aplikacja Application Insights ma stały zestaw tabel. Nie można skonfigurować innych źródeł danych do zapisu w aplikacji, aby nie można było tworzyć żadnych dodatkowych tabel. 

| Tabela | Opis | 
|:---|:---|
| availabilityResults | Dane podsumowujące z testów dostępności. |
| browserTimings      | Dane dotyczące wydajności klienta, takie jak czas przetwarzania danych przychodzących. |
| customEvents        | Zdarzenia niestandardowe utworzone przez aplikację. |
| customMetrics       | Metryki niestandardowe utworzone przez aplikację. |
| tamten        | Wywołania z aplikacji do składników zewnętrznych. |
| wyjątki          | Wyjątki zgłoszone przez środowisko uruchomieniowe aplikacji. |
| pageViews           | Dane o każdym widoku witryny sieci Web z informacjami o przeglądarce. |
| Liczniki wydajności | Pomiary wydajności z zasobów obliczeniowych obsługujących aplikację. |
| żądania            | Szczegóły każdego żądania aplikacji.  |
| Ścieżki              | Wyniki z rozproszonego śledzenia. |

Możesz wyświetlić schemat dla każdej tabeli na karcie **schemat** w log Analytics aplikacji.

![Schemat aplikacji](media/scope/application-schema.png)

## <a name="standard-properties"></a>Właściwości standardowe
Chociaż każda tabela w dziennikach Azure Monitor ma swój własny schemat, istnieją standardowe właściwości współużytkowane przez wszystkie tabele. Aby uzyskać szczegółowe informacje o każdej z nich, zobacz temat [standardowe właściwości w dziennikach Azure monitor](../platform/log-standard-properties.md) .

| Obszar roboczy usługi Log Analytics | Aplikacja Application Insights | Opis |
|:---|:---|:---|
| TimeGenerated | sygnatura czasowa  | Data i godzina utworzenia rekordu. |
| Typ          | ItemType   | Nazwa tabeli, z której został pobrany rekord. |
| _ResourceId   |            | Unikatowy identyfikator zasobu, z którym jest skojarzony rekord. |
| _IsBillable   |            | Określa, czy są naliczane opłaty za pozyskiwane dane. |
| _BilledSize   |            | Określa rozmiar w bajtach danych, które będą rozliczane. |

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat używania [log Analytics do tworzenia i edytowania wyszukiwań w dziennikach](../log-query/portals.md).
- Zapoznaj się z [samouczkiem dotyczącym pisania zapytań](../log-query/get-started-queries.md) przy użyciu nowego języka zapytań.
