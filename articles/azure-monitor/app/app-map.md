---
title: Mapa aplikacji w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji złożonych topologii z mapy aplikacji
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 238b75681ec525187ea27f60ac8b21b05b13954d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063118"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikacji: Klasyfikacja aplikacji rozproszonych

Mapa aplikacji pomaga wąskich gardeł wydajności dodatkowych lub niepowodzenie obszarów nadmiernej aktywności dotyczące wszystkich składników aplikacji rozproszonej. Każdy węzeł na mapie reprezentuje składnik aplikacji lub jej zależności ma kondycji kluczowy wskaźnik wydajności i alerty stanu. Kliknij z dowolnego składnika do bardziej szczegółowe dane diagnostyczne, takie jak zdarzenia usługi Application Insights. Jeśli aplikacja korzysta z usług platformy Azure, możesz również kliknąć za pośrednictwem do diagnostyki platformy Azure, takie jak zalecenia usługi Advisor z bazy danych SQL.

## <a name="what-is-a-component"></a>Co to jest składnikiem?

Składniki są niezależne wdrażanym poszczególnych części aplikacji rozproszonych mikrousług. Zespoły deweloperów i operacyjne mają widoczność na poziomie kodu lub dostęp do telemetrii wygenerowanej przez te składniki aplikacji. 

* Składniki różnią się od "obserwowanych" zależności zewnętrzne, takie jak SQL, itp. Centrum zdarzeń, które zespół/organizacji nie mogą mieć dostęp do (kod lub telemetrii).
* Składniki są uruchomione na dowolną liczbę wystąpień kontenera server/roli.
* Składniki mogą być oddzielnych kluczy Instrumentacji usługi Application Insights (nawet jeśli różnią się subskrypcje) lub różne role, raportowanie do jednego klucza Instrumentacji usługi Application Insights. Środowisko podglądu tak mapy przedstawiono składniki, niezależnie od tego, jak ustawić.

## <a name="composite-application-map"></a>Mapa aplikacji złożonej

Możesz zobaczyć topologia pełnej aplikacji na wielu poziomach składników powiązane aplikacji. Składniki mogą być, różne zasoby usługi Application Insights lub różne role w pojedynczym zasobie. Mapa aplikacji umożliwia znalezienie składników przez następujące HTTP zależności wywołań między serwerami za pomocą zainstalować zestaw Application Insights SDK. 

To środowisko rozpoczyna się od progresywnego odnajdywania składników. Podczas pierwszego ładowania mapy aplikacji, zestawu zapytań są wyzwalane odnajdywania składników powiązane z tego składnika. Przycisk w lewym górnym rogu zostanie zaktualizowana o liczbę składników w aplikacji, zgodnie z ich odnalezieniu. 

Po kliknięciu przycisku "Aktualizuj składniki mapy", mapy zostaną odświeżone przy użyciu wszystkich składników wykryte do momentu na tym etapie. W zależności od stopnia złożoności aplikacji może to potrwać chwilę, aby załadować.

Jeśli wszystkie składniki są ról w ramach pojedynczego zasobu usługi Application Insights, ten krok odnajdowania nie jest wymagane. Ładowania początkowego dla takich aplikacji będzie miał wszystkie jego składniki.

![Zrzut ekranu z mapy aplikacji](media/app-map/001.png)

Jednym z celów klucza z tego środowiska jest możliwe do wizualizacji złożonych topologii z setkami składników.

Kliknij dowolny składnik, aby zobaczyć powiązane szczegółowe informacje, a następnie przejdź do wydajności i dokładniejszej klasyfikacji błędów danego składnika.

![Okno wysuwane](media/app-map/application-map-001.png)

### <a name="investigate-failures"></a>Zbadaj błędy

Wybierz **zbadaj błędy** można uruchomić z okienka błędów.

![Zrzut ekranu przedstawiający zbadaj błędy przycisku](media/app-map/investigate-failures.png)

![Zrzut ekranu przedstawiający obsługi błędów](media/app-map/failures.png)

### <a name="investigate-performance"></a>Zbadaj wydajność

Rozwiązywanie problemów z wydajnością, wybierz **Zbadaj wydajność**.

![Zrzut ekranu przedstawiający badanie wydajności przycisku](media/app-map/investigate-performance.png)

![Zrzut ekranu przedstawiający wydajność](media/app-map/performance.png)

### <a name="go-to-details"></a>Przejdź do szczegółów

Wybierz **przejdź do szczegółów** Eksplorowanie środowiska transakcji end-to-end, które może zaoferować widoków gotowe, aby poziom stosu wywołań.

![Zrzut ekranu przedstawiający przycisk Przejdź do szczegółów](media/app-map/go-to-details.png)

![Zrzut ekranu przedstawiający szczegóły transakcji end-to-end](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Wyświetl w usłudze Analytics

Kliknij, aby zapytania i danych aplikacji dalsze badanie **Wyświetl w obszarze analiza**.

![Zrzut ekranu przedstawiający widok przycisk Analiza](media/app-map/view-in-analytics.png)

![Zrzut ekranu przedstawiający środowisko analizy](media/app-map/analytics.png)

### <a name="alerts"></a>Alerty

Zaznacz, aby wyświetlić aktywne alerty i podstawowych reguł, które alerty wyzwalane **alerty**.

![Zrzut ekranu przedstawiający przycisk alertów](media/app-map/alerts.png)

![Zrzut ekranu przedstawiający środowisko analizy](media/app-map/alerts-view.png)

## <a name="set-cloudrolename"></a>Zestaw cloud_RoleName

Mapa aplikacji używa `cloud_RoleName` właściwość do identyfikacji składniki na mapie. Zestaw SDK usługi Application Insights automatycznie dodaje `cloud_RoleName` właściwości telemetrii wyemitowane przez składniki. Na przykład dodać zestaw SDK w sytuacji, nazwa witryny sieci web lub nazwę usługi, rola `cloud_RoleName` właściwości. Jednakże istnieją przypadki, w których możesz chcieć zastąpić wartość domyślną. Aby zastąpić cloud_RoleName i zmiany, jakie wyświetlona na mapie aplikacji:

### <a name="net"></a>.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "RoleName";
            }
        }
    }
}
```

**Ładowanie usługi inicjatora**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Alternatywna metoda jest utworzenie wystąpienia inicjatora w kodzie, na przykład w pliku Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatywna metoda dla środowiska Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Jeśli używasz aplikacji Spring Boot za pomocą Application Insights platformy Spring Boot starter tylko wymagana zmiana jest można ustawić nazwę niestandardowego dla aplikacji w pliku application.properties pliku.

`spring.application.name=<name-of-app>`

Spring Boot starter automatycznie przypisze cloudRoleName wartość wprowadzona dla właściwości spring.application.name.

Więcej informacji na temat korelacji Java oraz sposób konfigurowania cloudRoleName do wyewidencjonowania aplikacje inne niż SpringBoot to [sekcji](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) na korelacji.

### <a name="clientbrowser-side-javascript"></a>/ Przeglądarki klienta kodu JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

Aby uzyskać więcej informacji o tym, jak zastąpić właściwości cloud_RoleName z inicjatorami telemetrii, zobacz [dodać właściwości: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują problemy z mapy aplikacji, aby działać zgodnie z oczekiwaniami, wykonaj poniższe kroki:

1. Upewnij się, że używasz oficjalnie obsługiwanym zestawem SDK. Zestawy SDK nieobsługiwany społeczności mogą nie obsługiwać korelacji.

    Zapoznaj się z tym [artykułu](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) Aby uzyskać listę obsługiwanych zestawów SDK.

2. Uaktualnij wszystkie składniki do najnowszej wersji zestawu SDK.

3. Jeśli używasz usługi Azure Functions przy użyciu C#Przeprowadź uaktualnienie do [funkcje w wersji 2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Upewnij się, [cloud_RoleName](app-map.md#Set-cloud-RoleName) jest poprawnie skonfigurowana.

5. Jeśli masz brakująca zależność, upewnij się, jest na liście [automatycznie zebranych zależności](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Jeśli nie, można nadal śledzić je ręcznie przy użyciu [śledzić wywołanie zależności](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="portal-feedback"></a>Opinie w portalu
Aby przekazać opinię, użyj opcji opinii.

![Obraz MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Kolejne kroki

* [Zrozumienie korelację](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
