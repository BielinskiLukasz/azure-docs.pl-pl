---
title: Ujednolicenie wielu zasobów Application Insights Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera szczegółowe informacje dotyczące używania funkcji w dziennikach Azure Monitor do wykonywania zapytań dotyczących wielu zasobów Application Insights i wizualizacji tych danych.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137496"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Ujednolicenie wielu zasobów Application Insights Azure Monitor 
W tym artykule opisano sposób wykonywania zapytań i wyświetlania wszystkich danych dziennika Application Insights w jednym miejscu, nawet w przypadku, gdy znajdują się one w różnych subskrypcjach platformy Azure, jako zamiennik dla zaniechania Application Insights Connector. Liczba zasobów Application Insights, które można uwzględnić w pojedynczym zapytaniu, jest ograniczona do 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Zalecane podejście do wykonywania zapytań dotyczących wielu zasobów Application Insights 
Wyświetlanie listy wielu zasobów Application Insights w zapytaniu może być uciążliwe i trudne do utrzymania. Zamiast tego można wykorzystać funkcję do oddzielenia logiki zapytania od zakresu aplikacji.  

W tym przykładzie pokazano, jak można monitorować wiele zasobów Application Insights i wizualizować liczbę nieudanych żądań według nazwy aplikacji.

Utwórz funkcję używającą operatora UNION z listą aplikacji, a następnie Zapisz zapytanie w obszarze roboczym jako funkcję z aliasem *applicationsScoping*. 

W dowolnym momencie możesz modyfikować wymienione aplikacje w portalu, przechodząc do Eksploratora zapytań w obszarze roboczym i wybierając funkcję do edycji, a następnie zapisując lub używając polecenia cmdlet `SavedSearch` PowerShell. 

>[!NOTE]
>Ta metoda nie może być używana z alertami dziennika, ponieważ sprawdzanie dostępu do zasobów reguły alertów, w tym obszarów roboczych i aplikacji, odbywa się w czasie tworzenia alertu. Dodawanie nowych zasobów do funkcji po utworzeniu alertu nie jest obsługiwane. Jeśli wolisz używać funkcji dla określania zakresu zasobów w alertach dziennika, musisz zmodyfikować regułę alertu w portalu lub z szablonem Menedżer zasobów, aby zaktualizować zasoby w zakresie. Alternatywnie możesz dołączyć listę zasobów do zapytania alertu dziennika.

Polecenie `withsource= SourceApp` dodaje kolumnę do wyników, które wyznaczają aplikację, która wysłała dziennik. Operator Parse jest opcjonalny w tym przykładzie i służy do wyodrębniania nazwy aplikacji z właściwości SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Teraz można przystąpić do używania funkcji applicationsScoping w kwerendzie obejmującej wiele zasobów:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Zapytanie używa schematu Application Insights, mimo że zapytanie jest wykonywane w obszarze roboczym, ponieważ funkcja applicationsScoping zwraca strukturę danych Application Insights. Alias funkcji zwraca Unię żądań ze wszystkich zdefiniowanych aplikacji. Zapytanie następnie filtruje żądania nieudane i wizualizowa trendy według aplikacji.

![Przykład wyników między zapytaniami](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[Zapytanie między zasobami](../log-query/cross-workspace-query.md) w ramach alertów dziennika jest obsługiwane w nowym [interfejsie API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie Azure Monitor używa [starszego interfejsu API alertów log Analytics](../platform/api-alerts.md) na potrzeby tworzenia nowych reguł alertów dziennika z Azure Portal, chyba że zostanie przełączony w [STARSZEJ wersji interfejsu API alertów dziennika](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po przełączeniu nowy interfejs API zostanie ustawiony jako domyślny dla nowych reguł alertów w Azure Portal i umożliwia tworzenie reguł alertów dziennika zapytań dla wielu zasobów. Można tworzyć reguły alertów dziennika [zapytań dla wielu zasobów](../log-query/cross-workspace-query.md) bez przełączenia przy użyciu [szablonu ARM dla interfejsu API scheduledQueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , ale ta reguła alertu jest możliwa do zarządzania, chociaż [interfejs API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , a nie z Azure Portal.

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights i Log Analytics różnice w schemacie obszaru roboczego
W poniższej tabeli przedstawiono różnice schematu między Log Analytics i Application Insights.  

| Log Analytics właściwości obszaru roboczego| Właściwości zasobów Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | sygnatura czasowa |
| Przeglądarka | client_browser |
| Miasto | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Kraj | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| Typ urządzenia | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| Typ | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| System operacyjny | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | powodzenie | 
| ResponseCode | resultCode | 
| Rola | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| Identyfikator sesji | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| Adres URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Następne kroki

Użyj [wyszukiwania w dzienniku](../../azure-monitor/log-query/log-query-overview.md) , aby wyświetlić szczegółowe informacje dotyczące Application Insights aplikacji.
