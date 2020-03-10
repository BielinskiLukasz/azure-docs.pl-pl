---
title: Standardowe właściwości w dzienniku Azure Monitor rekordy | Microsoft Docs
description: Opisuje właściwości, które są wspólne dla wielu typów danych w dziennikach Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372292"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Standardowe właściwości w dziennikach Azure Monitor
Dane w dziennikach Azure Monitor są [przechowywane jako zestaw rekordów w obszarze roboczym log Analytics lub w aplikacji Application Insights](../log-query/logs-structure.md), z których każdy ma określony typ danych, który ma unikatowy zestaw właściwości. Wiele typów danych będzie zawierać standardowe właściwości, które są wspólne dla wielu typów. W tym artykule opisano te właściwości i przedstawiono przykłady korzystania z nich w zapytaniach.

> [!NOTE]
> Niektóre z właściwości standardowych nie będą wyświetlane w widoku schematu ani IntelliSense w Log Analytics i nie będą wyświetlane w wynikach zapytania, chyba że jawnie określisz właściwość w danych wyjściowych.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated i znacznik czasu
Właściwości **TimeGenerated** (obszar roboczy log Analytics) i **sygnatura czasowa** (aplikacja Application Insights Application) zawierają datę i godzinę utworzenia rekordu przez źródło danych. Aby uzyskać więcej informacji, zobacz czas pozyskiwania [danych dziennika w Azure monitor](data-ingestion-time.md) .

**TimeGenerated** i **sygnatura czasowa** zapewniają wspólną Właściwość służącą do filtrowania lub podsumowywania według czasu. Po wybraniu przedziału czasu dla widoku lub pulpitu nawigacyjnego w Azure Portal do filtrowania wyników służy TimeGenerated lub znacznik czasu. 

### <a name="examples"></a>Przykłady

Następujące zapytanie zwraca liczbę zdarzeń błędów utworzonych dla każdego dnia w poprzednim tygodniu.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Następujące zapytanie zwraca liczbę wyjątków utworzonych dla każdego dnia w poprzednim tygodniu.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
Właściwość **\_TimeReceived** zawiera datę i godzinę odebrania rekordu przez punkt pozyskiwania Azure monitor w chmurze platformy Azure. Może to być przydatne do identyfikowania problemów opóźnienia między źródłem danych i chmurą. Przykładem może być problem z siecią, powodujący opóźnienie przesyłania danych z agenta. Aby uzyskać więcej informacji, zobacz czas pozyskiwania [danych dziennika w Azure monitor](data-ingestion-time.md) .

Następujące zapytanie zwraca średni czas oczekiwania (według godziny) dla rekordów zdarzeń z agenta. Obejmuje to czas od agenta do chmury oraz całkowity czas dostępności rekordu dla zapytań dzienników.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ i itemType
Właściwości **Type** (log Analytics Workspace) i **ItemType** (Application Insights Application) zawierają nazwę tabeli, z której został pobrany rekord, który może być również uważany za typ rekordu. Ta właściwość jest przydatna w zapytaniach, które łączą rekordy z wielu tabel, takich jak te, które używają operatora `search`, aby rozróżnić rekordy różnych typów. **$Table** można używać zamiast **typu** w niektórych miejscach.

### <a name="examples"></a>Przykłady
Następujące zapytanie zwraca liczbę rekordów według typu zebranych w ciągu ostatniej godziny.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>Identyfikator elementu \_
Właściwość **ItemId\_** posiada unikatowy identyfikator rekordu.


## <a name="_resourceid"></a>\_ResourceId
Właściwość **ResourceId\_** posiada unikatowy identyfikator zasobu, z którym jest skojarzony rekord. Zapewnia to standardową Właściwość służącą do określania zakresu zapytania tylko do rekordów z określonego zasobu lub do łączenia się z danymi powiązanymi między wieloma tabelami.

W przypadku zasobów platformy Azure wartość **_ResourceId** jest [adresem URL identyfikatora zasobu platformy Azure](../../azure-resource-manager/templates/template-functions-resource.md). Właściwość jest obecnie ograniczona do zasobów platformy Azure, ale zostanie rozszerzona o zasoby spoza platformy Azure, takie jak komputery lokalne.

> [!NOTE]
> Niektóre typy danych mają już pola zawierające identyfikator zasobu platformy Azure lub co najmniej te elementy, takie jak identyfikator subskrypcji. Chociaż te pola są utrzymywane w celu zapewnienia zgodności z poprzednimi wersjami, zaleca się używanie _ResourceId do wykonywania wzajemnej korelacji, ponieważ będzie ona bardziej spójna.

### <a name="examples"></a>Przykłady
Poniższe zapytanie dołącza dane dotyczące wydajności i zdarzeń dla każdego komputera. Pokazuje wszystkie zdarzenia o IDENTYFIKATORze _101_ i użycia procesora przekraczającym 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Następujące zapytanie sprzęga rekordy _platformy Azure_ z rekordami _SecurityEvent_ . Pokazuje wszystkie operacje działania z użytkownikami, którzy zostali zarejestrowani na tych komputerach.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

Poniższe zapytanie analizuje **_ResourceId** i agreguje rozliczane woluminy danych na subskrypcję platformy Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Te `union withsource = tt *` zapytania są oszczędne, ponieważ wykonywanie skanowania między typami danych jest kosztowne.

## <a name="_isbillable"></a>\_isobciążany
Właściwość **\_Isbilld** określa, czy są naliczane opłaty za pobrane dane. Dane z **\_Ismiliarded** równa _false_ są zbierane bezpłatnie i nie są naliczane za Twoje konto platformy Azure.

### <a name="examples"></a>Przykłady
Aby uzyskać listę komputerów wysyłających typy danych, należy użyć następującej kwerendy:

> [!NOTE]
> Używaj zapytań z `union withsource = tt *` oszczędnie, ponieważ można wykonywać skanowanie między typami danych. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Można to rozszerzyć, aby zwrócić liczbę komputerów na godzinę, które wysyłają typy danych rozliczanych:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
Właściwość **\_BilledSize** określa rozmiar w bajtach danych, które będą rozliczane na konto platformy Azure, jeśli **\_ismiliard** ma wartość true.


### <a name="examples"></a>Przykłady
Aby wyświetlić wielkość rozliczania zdarzeń pobieranych na komputer, użyj właściwości `_BilledSize`, która zapewnia rozmiar w bajtach:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Aby wyświetlić wielkość rozliczania zdarzeń pobieranych na subskrypcję, użyj następującego zapytania:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Aby wyświetlić rozmiar pozyskanych zdarzeń rozliczanych według grupy zasobów, należy użyć następującego zapytania:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Aby wyświetlić liczbę zdarzeń pozyskanych na komputer, użyj następującego zapytania:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Aby zobaczyć liczbę zdarzeń rozliczanych pobieranych na komputer, użyj następującego zapytania: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Aby wyświetlić liczbę typów danych rozliczanych z określonego komputera, użyj następującego zapytania:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat sposobu [przechowywania danych dziennika Azure monitor](../log-query/log-query-overview.md).
- Zapoznaj się z lekcjami dotyczącymi [pisania zapytań dzienników](../../azure-monitor/log-query/get-started-queries.md).
- Zapoznaj się z lekcji na [sprzęganie tabel w zapytaniach dziennika](../../azure-monitor/log-query/joins.md).
