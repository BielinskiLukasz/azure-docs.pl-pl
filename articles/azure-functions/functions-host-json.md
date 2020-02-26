---
title: Dokumentacja pliku host. JSON dla Azure Functions 2. x
description: Dokumentacja referencyjna dotycząca pliku Azure Functions hosta. JSON z użyciem środowiska uruchomieniowego v2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 949d4f2c5d8c1d8034ccc392915bc40f1f2fddda
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605138"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Dokumentacja pliku host. JSON dla Azure Functions 2. x i nowszych 

> [!div class="op_single_selector" title1="Wybierz wersję środowiska uruchomieniowego Azure Functions, którego używasz: "]
> * [Wersja 1](functions-host-json-v1.md)
> * [Wersja 2 +](functions-host-json.md)

Plik metadanych *hosta. JSON* zawiera globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje aplikacji funkcji. W tym artykule wymieniono dostępne ustawienia zaczynające się od wersji 2. x środowiska uruchomieniowego Azure Functions.  

> [!NOTE]
> Ten artykuł jest przeznaczony dla Azure Functions 2. x i nowszych.  Aby uzyskać odwołanie do pliku host. JSON w funkcjach 1. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions 1. x](functions-host-json-v1.md).

Inne opcje konfiguracji aplikacji funkcji są zarządzane w [ustawieniach aplikacji](functions-app-settings.md) (dla wdrożonych aplikacji) lub pliku [Local. JSON](functions-run-local.md#local-settings-file) (na potrzeby lokalnego tworzenia).

Konfiguracje w pliku host. JSON powiązane z powiązaniami są stosowane równie do poszczególnych funkcji w aplikacji funkcji. 

## <a name="sample-hostjson-file"></a>Przykładowy plik host. JSON

Następujący przykładowy plik *host. JSON* dla wersji 2. x + ma wszystkie możliwe opcje (z wyjątkiem tych, które są przeznaczone tylko do użytku wewnętrznego).

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

W poniższych sekcjach tego artykułu opisano każdą właściwość najwyższego poziomu. Wszystkie są opcjonalne, o ile nie wskazano inaczej.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

To ustawienie jest elementem podrzędnym [rejestrowania](#logging).

Opcje kontrolki dla Application Insights, w tym [Opcje próbkowania](./functions-monitoring.md#configure-sampling).

Aby uzyskać pełną strukturę JSON, zobacz wcześniejszy [przykład pliku host. JSON](#sample-hostjson-file).

> [!NOTE]
> Próbkowanie dziennika może spowodować, że niektóre wykonania nie są wyświetlane w bloku monitora Application Insights. Aby uniknąć próbkowania dziennika, Dodaj `samplingExcludedTypes: "Request"` do wartości `applicationInsights`.

| Właściwość | Domyślny | Opis |
| --------- | --------- | --------- | 
| samplingSettings | Nie dotyczy | Zobacz [applicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| samplingExcludedTypes | {1&gt;null&lt;1} | Rozdzielana średnikami lista typów, które nie mają być próbkowane. Rozpoznawane typy to: zależność, zdarzenie, wyjątek, PageView, żądanie, śledzenie. Wszystkie wystąpienia określonych typów są przesyłane; typy, które nie są określone, są próbkowane. |
| samplingIncludedTypes | {1&gt;null&lt;1} | Rozdzielana średnikami lista typów, które mają być próbkowane; pusta lista implikuje wszystkie typy. Typ wymieniony w `samplingExcludedTypes` typy przesłonięcia wymienione w tym miejscu. Rozpoznawane typy to: zależność, zdarzenie, wyjątek, PageView, żądanie, śledzenie. Wszystkie wystąpienia określonych typów są przesyłane; typy, które nie są określone, są próbkowane. |
| enableLiveMetrics | {1&gt;true&lt;1} | Włącza zbieranie metryk na żywo. |
| enableDependencyTracking | {1&gt;true&lt;1} | Włącza śledzenie zależności. |
| enablePerformanceCountersCollection | {1&gt;true&lt;1} | Włącza Zbieranie liczników wydajności kudu. |
| liveMetricsInitializationDelay | 00:00:15 | Przeznaczone tylko do użytku wewnętrznego. |
| httpAutoCollectionOptions | Nie dotyczy | Zobacz [applicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | Nie dotyczy | Zobacz [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

|Właściwość | Domyślny | Opis |
| --------- | --------- | --------- | 
| isEnabled | {1&gt;true&lt;1} | Włącza lub wyłącza próbkowanie. | 
| maxTelemetryItemsPerSecond | 20 | Docelowa liczba elementów telemetrii zarejestrowanych na sekundę na każdym hoście serwera. Jeśli aplikacja działa na wielu hostach, Zmniejsz tę wartość, aby pozostała w ogólnym docelowym wskaźniku ruchu. | 
| evaluationInterval | 01:00:00 | Interwał, w którym bieżąca stawka danych telemetrycznych jest przeszacowana. Obliczanie jest wykonywane jako średnia przenoszona. Możesz chcieć skrócić ten interwał, jeśli dane telemetryczne są odpowiedzialne za nagłe rozerwania. |
| initialSamplingPercentage| 1.0 | Procent początkowej próbki stosowany na początku procesu próbkowania, aby dynamicznie zmieniać wartość procentową. Nie Redukuj wartości podczas debugowania. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Gdy wartość procentowa próbkowania ulegnie zmianie, ta właściwość określa, jak wkrótce Application Insights może ponownie wywołać procent próbkowania, aby przechwycić więcej danych. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Gdy wartość procentowa próbkowania ulegnie zmianie, ta właściwość określa, jak wkrótce później Application Insights można ponownie obniżyć procent próbkowania, aby przechwycić mniejszą ilość danych. |
| minSamplingPercentage | 0.1 | W miarę jak procent próbkowania różni się, ta właściwość określa minimalny dozwolony procent próbkowania. |
| maxSamplingPercentage | 0.1 | W miarę jak procent próbkowania różni się, ta właściwość określa maksymalny dozwolony procent próbkowania. |
| movingAverageRatio | 1.0 | Przy obliczaniu średniej przenoszonej waga przypisana do najnowszej wartości. Użyj wartości równej lub mniejszej od 1. Mniejsze wartości sprawiają, że algorytm jest mniej aktywny w nagłych zmianach. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. httpAutoCollectionOptions

|Właściwość | Domyślny | Opis |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | {1&gt;true&lt;1} | Włącza lub wyłącza rozszerzone informacje żądania HTTP dla wyzwalaczy HTTP: nagłówki korelacji żądań przychodzących, obsługa kluczy wielu instrumentacji, metoda HTTP, ścieżka i odpowiedź. |
| enableW3CDistributedTracing | {1&gt;true&lt;1} | Włącza lub wyłącza obsługę protokołu śledzenia rozproszonego W3C (i włącza starszą wersję schematu korelacji). Włączone domyślnie, jeśli `enableHttpTriggerExtendedInfoCollection` ma wartość true. Jeśli `enableHttpTriggerExtendedInfoCollection` ma wartość false, ta flaga ma zastosowanie tylko do żądań wychodzących, a nie do żądań przychodzących. |
| enableResponseHeaderInjection | {1&gt;true&lt;1} | Włącza lub wyłącza iniekcję nagłówków korelacji wieloskładnikowej do odpowiedzi. Włączenie iniekcji umożliwia Application Insights konstruowanie mapy aplikacji w przypadku użycia kilku kluczy Instrumentacji. Włączone domyślnie, jeśli `enableHttpTriggerExtendedInfoCollection` ma wartość true. To ustawienie nie ma zastosowania, jeśli `enableHttpTriggerExtendedInfoCollection` ma wartość false. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

Aby uzyskać więcej informacji na temat migawek, zobacz [debugowanie migawek na wyjątkach w aplikacjach .NET](/azure/azure-monitor/app/snapshot-debugger) i [Rozwiązywanie problemów z włączaniem Application Insights Snapshot Debugger lub wyświetlania migawek](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Właściwość | Domyślny | Opis |
| --------- | --------- | --------- | 
| agentEndpoint | {1&gt;null&lt;1} | Punkt końcowy używany do nawiązywania połączenia z usługą Application Insights Snapshot Debugger. Jeśli wartość jest równa null, używany jest domyślny punkt końcowy. |
| captureSnapshotMemoryWeight | 0.5 | Waga określona dla bieżącego rozmiaru pamięci procesu podczas sprawdzania, czy jest wystarczająca ilość pamięci, aby wykonać migawkę. Oczekiwana wartość jest większa niż 0 prawidłowy ułamek (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | Limit liczby żądań zakończonych niepowodzeniem do żądania migawek przed wyłączeniem procesora telemetrii.|
| handleUntrackedExceptions | {1&gt;true&lt;1} | Włącza lub wyłącza śledzenie wyjątków, które nie są śledzone przez Application Insights dane telemetryczne. |
| isEnabled | {1&gt;true&lt;1} | Włącza lub wyłącza kolekcje migawek | 
| isEnabledInDeveloperMode | {1&gt;false&lt;1} | Włącza lub wyłącza kolekcje migawek jest włączona w trybie dewelopera. |
| isEnabledWhenProfiling | {1&gt;true&lt;1} | Włącza lub wyłącza tworzenie migawek, nawet jeśli Application Insights Profiler zbiera szczegółowe sesji profilowania. |
| isExceptionSnappointsEnabled | {1&gt;false&lt;1} | Włącza lub wyłącza filtrowanie wyjątków. |
| isLowPrioritySnapshotUploader | {1&gt;true&lt;1} | Określa, czy proces SnapshotUploader ma być uruchamiany przy normalnym priorytecie. |
| maximumCollectionPlanSize | 50 | Maksymalna liczba problemów, które możemy śledzić w dowolnym momencie w zakresie od 1 do 9999. |
| maximumSnapshotsRequired | 3 | Maksymalna liczba migawek zebranych w przypadku pojedynczego problemu w zakresie od 1 do 999. Problem może być uważany za poszczególne instrukcje throw w aplikacji. Gdy liczba migawek zbieranych dla danego problemu osiągnie tę wartość, nie będzie zbieranych kolejnych migawek dla tego problemu, dopóki nie zostaną zresetowane liczniki problemów (zobacz `problemCounterResetInterval`), a limit `thresholdForSnapshotting` zostanie osiągnięty ponownie. |
| problemCounterResetInterval | 24:00:00 | Jak często resetować liczniki problemu w zakresie od 1 minuty do siedmiu dni. Po osiągnięciu tego interwału wszystkie liczby problemów są resetowane do zera. Istniejące problemy, które osiągnęły już próg dla tworzenia migawek, ale nie wygenerowały jeszcze liczby migawek w `maximumSnapshotsRequired`, pozostają aktywne. |
| provideAnonymousTelemetry | {1&gt;true&lt;1} | Określa, czy wysyłać anonimowe użycie i dane telemetryczne błędu do firmy Microsoft. Te dane telemetryczne mogą być używane w przypadku kontaktowania się z firmą Microsoft w celu ułatwienia rozwiązywania problemów z Snapshot Debugger. Służy również do monitorowania wzorców użycia. |
| reconnectInterval | 00:15:00 | Jak często ponownie nawiązujemy połączenie z punktem końcowym Snapshot Debugger. Dozwolony zakres to jedna minuta na jeden dzień. |
| shadowCopyFolder | {1&gt;null&lt;1} | Określa folder, który ma być używany do kopiowania plików binarnych w tle. Jeśli nie zostanie ustawiona, foldery określone przez następujące zmienne środowiskowe są podejmowane w kolejności: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | {1&gt;true&lt;1} | Jeśli wartość jest równa true, tylko jedno wystąpienie elementu SnapshotUploader będzie zbierać i przekazywać migawki dla wielu aplikacji, które współużytkują InstrumentationKey. W przypadku ustawienia wartości false SnapshotUploader będzie unikatowy dla każdej kolekcji (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | {1&gt;true&lt;1} | Określa, czy przetwarzać migawki w wątku niskiego priorytetu we/wy. Tworzenie migawki jest szybką operacją, ale w celu przekazania migawki do usługi Snapshot Debugger należy najpierw zapisać ją na dysku jako minizrzutu. Dzieje się tak w procesie SnapshotUploader. Ustawienie tej wartości na true powoduje użycie operacji we/wy o niskim priorytecie w celu zapisania minizrzutu, która nie będzie konkurować z aplikacją dla zasobów. Ustawienie tej wartości na wartość false powoduje przyspieszenie minizrzutu tworzenia w kosztach spowolnienia działania aplikacji. |
| snapshotsPerDayLimit | 30 | Maksymalna liczba migawek dozwolonych w ciągu jednego dnia (24 godziny). Ten limit jest również wymuszany po stronie usługi Application Insights. Operacje przekazywania mają szybkość ograniczoną do 50 dziennie dla aplikacji (czyli na klucz Instrumentacji). Ta wartość pomaga zapobiegać tworzeniu dodatkowych migawek, które ostatecznie zostaną odrzucone podczas przekazywania. Wartość zerowa powoduje całkowite usunięcie limitu, co nie jest zalecane. |
| snapshotsPerTenMinutesLimit | 1 | Maksymalna liczba migawek dozwolona w ciągu 10 minut. Chociaż nie ma górnej granicy tej wartości, należy zachować ostrożność w obciążeniu produkcyjnym, ponieważ może to mieć wpływ na wydajność aplikacji. Tworzenie migawki jest szybkie, ale utworzenie minizrzutu migawki i przekazanie jej do usługi Snapshot Debugger jest znacznie wolniejszą operacją, która spowoduje konkurowanie z aplikacją dla zasobów (zarówno procesora CPU, jak i we/wy). |
| tempFolder | {1&gt;null&lt;1} | Określa folder, w którym mają zostać zapisane pliki dziennika minizrzutów i obiektu przekazującego. Jeśli nie jest ustawiona, zostanie użyta wartość *%temp%\Dumps* . |
| thresholdForSnapshotting | 1 | Ile razy Application Insights musi zobaczyć wyjątek przed wyświetleniem monitu o migawki. |
| uploaderProxy | {1&gt;null&lt;1} | Zastępuje serwer proxy używany w procesie obiektu przekazującego migawek. Może być konieczne użycie tego ustawienia, jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy. Snapshot Collector działa w ramach procesu aplikacji i będzie używać tych samych ustawień serwera proxy. Jednak migawka obiektu przekazującego działa jako oddzielny proces i może być konieczne ręczne skonfigurowanie serwera proxy. Jeśli ta wartość jest równa null, Snapshot Collector spróbuje automatycznie wykryć adres serwera proxy, sprawdzając system .NET. WebRequest. DefaultWebProxy i przekazując wartość do migawki obiektu przekazującego. Jeśli ta wartość nie ma wartości null, wykrywanie autowykrywania nie jest używane i serwer proxy określony w tym miejscu zostanie użyty w migawce obiektu przekazującego. |

## <a name="cosmosdb"></a>cosmosDb

Ustawienie konfiguracji można znaleźć w [Cosmos DB wyzwalacze i powiązania](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="durabletask"></a>durableTask

Ustawienie konfiguracji można znaleźć w obszarze [powiązania dla Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Ustawienia konfiguracji można znaleźć w temacie [wyzwalacze i powiązania centrum zdarzeń](functions-bindings-event-hubs-output.md#host-json). 

## <a name="extensions"></a>rozszerzenia

Właściwość zwracająca obiekt, który zawiera wszystkie ustawienia specyficzne dla powiązania, takie jak [http](#http) i [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Zestawy rozszerzeń umożliwiają dodawanie zgodnego zestawu funkcji powiązania do aplikacji funkcji. Aby dowiedzieć się więcej, zobacz [zestawy rozszerzeń na potrzeby lokalnego tworzenia](functions-bindings-register.md#extension-bundles)oprogramowania.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>— funkcje

Lista funkcji uruchomionych przez hosta zadań. Pusta tablica oznacza uruchamianie wszystkich funkcji. Przeznaczone do użytku tylko w przypadku [uruchamiania lokalnego](functions-run-local.md). W aplikacjach funkcji na platformie Azure zamiast tego należy wykonać kroki opisane w temacie [Jak wyłączyć funkcje w Azure Functions](disable-function.md) , aby wyłączyć określone funkcje zamiast używać tego ustawienia.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Wskazuje czas trwania dla wszystkich funkcji. Jest on zgodny z formatem ciągu TimeSpan. W planie zużycia bezserwerowego prawidłowy zakres to od 1 sekundy do 10 minut, a wartość domyślna to 5 minut.  

W planie Premium prawidłowy zakres to od 1 sekundy do 60 minut, a wartość domyślna to 30 minut.

W przypadku planu dedykowanego (App Service) nie ma żadnego całkowitego limitu, a wartość domyślna to 30 minut. Wartość `-1` wskazuje niepowiązane wykonywanie, ale jest zalecane zapewnienie stałego górnego ograniczenia.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Ustawienia konfiguracji dla [monitora kondycji hosta](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------| 
|dostępny|{1&gt;true&lt;1}|Określa, czy funkcja jest włączona. | 
|healthCheckInterval|10 sekund|Przedział czasu między okresowymi kontrolami kondycji w tle. | 
|healthCheckWindow|2 minuty|Przedział czasu, który jest używany w połączeniu z ustawieniem `healthCheckThreshold`.| 
|healthCheckThreshold|6|Maksymalna liczba przypadków, w których Sprawdzenie kondycji może zakończyć się niepowodzeniem przed zainicjowaniem odtwarzania hosta.| 
|counterThreshold|0,80|Próg, w którym licznik wydajności będzie traktowany jako w złej kondycji.| 

## <a name="http"></a>http

Ustawienia konfiguracji można znaleźć w tematach [wyzwalacze i powiązania HTTP](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>rejestrowanie

Steruje zachowaniem rejestrowania aplikacji funkcji, w tym Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Określa, jaki poziom rejestrowania plików jest włączony.  Dostępne są `never`, `always`, `debugOnly`. |
|logLevel|Nie dotyczy|Obiekt, który definiuje filtrowanie kategorii dzienników dla funkcji w aplikacji. W wersji 2. x i nowszych postępuj zgodnie z układem ASP.NET Core dla filtrowania kategorii dzienników. To ustawienie umożliwia filtrowanie rejestrowania dla określonych funkcji. Aby uzyskać więcej informacji, zobacz [filtrowanie dzienników](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) w dokumentacji ASP.NET Core. |
|console|Nie dotyczy| Ustawienie rejestrowania [konsoli](#console) . |
|applicationInsights|Nie dotyczy| Ustawienie [applicationInsights](#applicationinsights) . |

## <a name="console"></a>console

To ustawienie jest elementem podrzędnym [rejestrowania](#logging). Kontroluje Rejestrowanie konsoli, gdy nie jest w trybie debugowania.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------| 
|isEnabled|{1&gt;false&lt;1}|Włącza lub wyłącza rejestrowanie konsoli.| 

## <a name="manageddependency"></a>managedDependency

Zależność zarządzana to funkcja, która jest obecnie obsługiwana tylko w przypadku funkcji opartych na programie PowerShell. Umożliwia automatyczne zarządzanie zależnościami przez usługę. Gdy właściwość `enabled` jest ustawiona na `true`, plik `requirements.psd1` jest przetwarzany. Zależności są aktualizowane, gdy zostaną wydane jakiekolwiek wersje pomocnicze. Aby uzyskać więcej informacji, zobacz [zależność zarządzana](functions-reference-powershell.md#dependency-management) w artykule dotyczącym programu PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>kolejki

Ustawienia konfiguracji można znaleźć w temacie [wyzwalacze i powiązania kolejki magazynu](functions-bindings-storage-queue-output.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Ustawienie konfiguracji można znaleźć w [SendGrid wyzwalaczach i powiązaniach](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Ustawienie konfiguracji można znaleźć w [Service Bus wyzwalacze i powiązania](functions-bindings-service-bus-output.md#host-json).

## <a name="singleton"></a>Pojedynczego

Ustawienia konfiguracji dla zachowania pojedynczej blokady. Aby uzyskać więcej informacji, zobacz [temat problem z usługą GitHub dotyczący pojedynczej pomocy technicznej](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------| 
|lockPeriod|00:00:15|Okres, w którym są wykonywane blokady poziomu funkcji. Blokady autorenew.| 
|listenerLockPeriod|00:01:00|Okres, w którym są wykonywane blokady odbiornika.| 
|listenerLockRecoveryPollingInterval|00:01:00|Przedział czasu używany do odzyskiwania blokady odbiornika, jeśli nie można uzyskać blokady odbiornika podczas uruchamiania.| 
|lockAcquisitionTimeout|00:01:00|Maksymalny czas, przez jaki środowisko uruchomieniowe podejmie próbę uzyskania blokady.| 
|lockAcquisitionPollingInterval|Nie dotyczy|Interwał między kolejnymi próbami przejęcia blokady.| 

## <a name="version"></a>wersja

Ta wartość wskazuje wersję schematu pliku host. JSON. Ciąg wersji `"version": "2.0"` jest wymagany dla aplikacji funkcji, która jest przeznaczona dla środowiska uruchomieniowego v2 lub nowszej wersji. Brak zmian schematu pliku host. JSON między wersjami 2 i v3.

## <a name="watchdirectories"></a>watchDirectories

Zestaw [udostępnionych katalogów kodu](functions-reference-csharp.md#watched-directories) , które powinny być monitorowane pod kątem zmian.  Zapewnia, że gdy kod w tych katalogach zostanie zmieniony, zmiany są pobierane przez funkcje.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o aktualizowaniu pliku host. JSON](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobacz ustawienia globalne w zmiennych środowiskowych](functions-app-settings.md)
