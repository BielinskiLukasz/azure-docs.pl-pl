---
title: Usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: fbeece7bf9e460453bc3c1c6a9df1f19f767ce48
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725033"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korelowanie danych usługi Application Insights z niestandardowymi źródłami danych

Application Insights gromadzi kilka różnych typów danych: wyjątki, ślady, wyświetleń stron i inne. Często jest wystarczająca do badania wydajności, niezawodności i użycia aplikacji, istnieją przypadki, gdy jest ona przydatna w celu skorelowania danych przechowywanych w usłudze Application Insights do innych całkowicie niestandardowych zestawów danych.

Sytuacje, w których możesz chcieć niestandardowe dane obejmują:

- Tabele danych wzbogacenia lub wyszukiwania: na przykład uzupełnienia nazwy serwera z właścicielem serwera i lokalizacji laboratorium, w którym można znaleźć 
- Korelacja ze źródłami danych — usługa Application Insights: na przykład zostały korelowanie danych dotyczących zakupu w sklepie sieci web przy użyciu informacji z usługi realizacji zakupu w celu określenia, jak dokładny szacowania czasu wysyłania 
- Dane niestandardowe całkowicie: liczbę naszym klientom lubisz język zapytań i wydajność platformy danych usługi Log Analytics, która będzie tworzyć kopię usługi Application Insights, a chcesz go użyć do zapytania o dane, w ogóle nie jest powiązany z usługi Application Insights. Na przykład śledzić wydajność słoneczny panelu inteligentnego instalacja głównego w ramach opisanych [tutaj]( http://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Sposób korelowania danych niestandardowych z danych usługi Application Insights 

Ponieważ usługa Application Insights jest objęta zaawansowana platforma danych usługi Log Analytics, możemy użyć pełnego zestawu funkcji usługi Log Analytics do pozyskiwania danych. Następnie napiszemy zapytań przy użyciu operatora "Dołącz", który będzie skorelować te dane niestandardowe dane, które są dostępne dla nas w usłudze Log Analytics. 

## <a name="ingesting-data"></a>Dane wprowadzane

W tej sekcji omówimy się, jak pobierać dane do usługi Log Analytics.

Jeśli nie masz jeszcze jedną, aprowizacja nowego obszaru roboczego usługi Log Analytics postępując zgodnie z [w instrukcjach]( https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm) za pomocą, a w tym kroku "Tworzenie obszaru roboczego".

Aby rozpocząć wysyłanie danych do usługi Log Analytics. Istnieje kilka opcji:

- Mechanizm synchronicznej, można albo bezpośrednio wywołać [interfejs API modułu zbierającego dane](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) lub użyć naszego łącznika aplikacji logiki — po prostu wyszukaj "Azure Log Analytics" i wybierz opcję "Wyślij dane":

 ![Zrzut ekranu, wybierz i akcji](./media/app-insights-custom-data-correlation/01-logic-app-connector.png)  

- Z opcji asynchronicznego Użyj interfejsu API modułu zbierającego dane do tworzenia potoku przetwarzania. Zobacz [w tym artykule](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) Aby uzyskać szczegółowe informacje.

## <a name="correlating-data"></a>Korelowanie danych

Usługa Application Insights opiera się na platformie danych usługi Log Analytics. Możemy więc użyć [sprzężeń między zasobami](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) do skorelowania wszelkich danych pozyskanych do usługi Log Analytics firma Microsoft z danych usługi Application Insights.

Na przykład możemy pozyskiwać nasze laboratorium spisu i lokalizacji do tabeli o nazwie "LabLocations_CL" w obszarze roboczym usługi Log Analytics o nazwie "myLA". Jeśli chcemy następnie przejrzyj nasz żądań śledzone w aplikacji usługi Application Insights o nazwie "myAI" i korelowanie nazw maszyn, które obsłużone żądania do lokalizacji tych maszyn, przechowywane w tabeli niestandardowej wspomniano wcześniej, można uruchomić następujące zapytanie z Kontekst usługi Application Insights ani Log Analytics:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [interfejsu API modułu zbierającego dane](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) odwołania.
- Aby uzyskać więcej informacji na temat [sprzężeń między zasobami](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
