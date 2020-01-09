---
title: Monitorowanie zdarzeń klastra systemu Linux w usłudze Azure Service Fabric
description: Informacje o monitorowaniu zdarzeń klastra Service Fabric Linux przez zapisanie zdarzeń platformy Service Fabric do dziennika systemowego.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645756"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Zdarzenia klastra Service Fabric systemu Linux w dzienniku systemu

Service Fabric uwidacznia zestaw zdarzeń platformy w celu poinformowania o ważnych działaniach w klastrze. Pełna lista zdarzeń, które są uwidocznione, jest dostępna [tutaj](service-fabric-diagnostics-event-generation-operational.md). Istnieją różne sposoby używania tych zdarzeń. W tym artykule omówiono sposób konfigurowania Service Fabric zapisywania tych zdarzeń w dzienniku systemowym.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Wprowadzenie

W wersji 6,4 SyslogConsumer został wprowadzony do wysyłania zdarzeń platformy Service Fabric do dziennika systemowego dla klastrów systemu Linux. Po włączeniu zdarzenia będą automatycznie przepływać do dziennika systemowego, który będzie można zbierać i wysyłać przez agenta Log Analytics.

Każde zdarzenie dziennika systemu zawiera 4 składniki
* Funkcji
* Tożsamość
* Wiadomość
* Ważność

SyslogConsumer zapisuje wszystkie zdarzenia platformy przy użyciu `Local0`funkcji. Możesz zaktualizować do dowolnego prawidłowego obiektu, zmieniając konfigurację konfiguracji. Używana tożsamość jest `ServiceFabric`. Pole Message zawiera całe zdarzenie serializowane w formacie JSON, tak aby można było wykonywać zapytania i korzystać z różnych narzędzi. 

## <a name="enable-syslogconsumer"></a>Włącz SyslogConsumer

Aby włączyć SyslogConsumer, należy przeprowadzić uaktualnienie klastra. Należy zaktualizować sekcję `fabricSettings` przy użyciu następującego kodu. Uwaga ten kod zawiera tylko sekcje powiązane z SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Poniżej przedstawiono zmiany, które należy wywoływać
1. We wspólnej sekcji istnieje nowy parametr o nazwie `LinuxStructuredTracesEnabled`. **Jest to wymagane, aby zdarzenia systemu Linux były strukturalne i serializowane podczas wysyłania do dziennika systemowego.**
2. W sekcji Diagnostyka został dodany nowy ConsumerInstance: SyslogConsumer. Oznacza to, że platforma jest innym konsumentem zdarzeń. 
3. Nowa sekcja SyslogConsumer musi mieć `IsEnabled` jako `true`. Jest ona konfigurowana do automatycznego używania funkcji Local0. Można to zastąpić przez dodanie innego parametru.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integracja dzienników Azure Monitor
Te zdarzenia dziennika systemowego można odczytać w narzędziu do monitorowania, takim jak dzienniki Azure Monitor. Obszar roboczy Log Analytics można utworzyć przy użyciu witryny Azure Marketplace, korzystając z następujących [instrukcji]. (.. /Azure-monitor/Learn/Quick-Create-Workspace.MD) musisz również dodać agenta Log Analytics do klastra, aby zebrać i wysłać te dane do obszaru roboczego. Jest to ten sam Agent, który służy do zbierania liczników wydajności. 

1. Przejdź do bloku `Advanced Settings`

    ![Ustawienia obszaru roboczego](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Kliknij pozycję `Data`.
3. Kliknij pozycję `Syslog`.
4. Skonfiguruj Local0 jako funkcję do śledzenia. Możesz dodać kolejną funkcję, jeśli została zmieniona w fabricSettings

    ![Konfigurowanie usługi Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Przejdź do Eksploratora zapytań, klikając `Logs` w menu zasobów obszaru roboczego, aby rozpocząć wykonywanie zapytań

    ![Dzienniki obszaru roboczego](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Możesz wykonywać zapytania względem tabeli `Syslog`, szukając `ServiceFabric` jako elementu ProcessName. Poniższe zapytanie przedstawia przykład sposobu analizowania kodu JSON w zdarzeniu i wyświetlania jego zawartości

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Zapytanie dziennika systemowego](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Powyższy przykład dotyczy zdarzenia NodeDown. Pełną listę zdarzeń można zobaczyć [tutaj](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Następne kroki
* [Wdróż agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) na węzły do zbierania liczników wydajności i zbieranie statystyk platformy docker i dzienniki kontenerów
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) , które są oferowane w ramach dzienników Azure monitor
* [Używanie projektanta widoków do tworzenia widoków niestandardowych w dziennikach Azure Monitor](../log-analytics/log-analytics-view-designer.md)
* Informacje dotyczące sposobu [Azure Monitornia dzienników integracja z dziennikiem](../log-analytics/log-analytics-data-sources-syslog.md)systemowym.
