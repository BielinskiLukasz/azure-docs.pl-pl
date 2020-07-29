---
title: Migracja IoT Hub platformy Azure do ustawień diagnostycznych | Microsoft Docs
description: Jak aktualizować IoT Hub platformy Azure, aby używać ustawień diagnostyki platformy Azure zamiast monitorowania operacji do monitorowania stanu operacji w centrum IoT w czasie rzeczywistym.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75750700"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrowanie IoT Hub z monitorowania operacji do ustawień diagnostycznych

Klienci korzystający z [monitorowania operacji](iot-hub-operations-monitoring.md) do śledzenia stanu operacji w IoT Hub mogą migrować ten przepływ pracy do [ustawień diagnostycznych platformy Azure](../azure-monitor/platform/platform-logs-overview.md), funkcję Azure monitor. Ustawienia diagnostyki dostarczają informacji diagnostycznych na poziomie zasobów dla wielu usług platformy Azure.

**Funkcja monitorowania operacji IoT Hub jest przestarzała**i została usunięta z portalu. W tym artykule przedstawiono procedurę przenoszenia obciążeń z monitorowania operacji do ustawień diagnostycznych. Aby uzyskać więcej informacji na temat osi czasu wycofania, zobacz [monitorowanie rozwiązań usługi Azure IoT za pomocą Azure monitor i Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>IoT Hub aktualizacji

Aby zaktualizować IoT Hub w Azure Portal, najpierw włącz ustawienia diagnostyczne, a następnie wyłącz monitorowanie operacji.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Wyłącz monitorowanie operacji

> [!NOTE]
> Od 11 marca 2019 funkcja monitorowania operacji jest usuwana z Azure Portal interfejsu IoT Hub. Poniższe kroki nie mają już zastosowania. Aby przeprowadzić migrację, upewnij się, że w ustawieniach diagnostyki Azure Monitor powyżej są włączone odpowiednie kategorie.

Po przetestowaniu nowych ustawień diagnostycznych w przepływie pracy można wyłączyć funkcję monitorowania operacji. 

1. W menu IoT Hub wybierz pozycję **monitorowanie operacji**.

2. W obszarze każdej kategorii monitorowania zaznacz opcję **Brak**.

3. Zapisz zmiany monitorowania operacji.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualizowanie aplikacji korzystających z monitorowania operacji

Schematy dla ustawień monitorowania i diagnostyki operacji różnią się nieznacznie. Ważne jest, aby w celu zamapowania na schemat używany przez ustawienia diagnostyki zaktualizować aplikacje używające monitorowania operacji. 

Ponadto ustawienia diagnostyki oferują pięć nowych kategorii do śledzenia. Po zaktualizowaniu aplikacji dla istniejącego schematu Dodaj również nowe kategorie:

* Operacje na sznurze z chmury do urządzenia
* Operacje wieloosiowe między urządzeniami a chmurą
* Zapytania bliźniaczye
* Operacje zadań
* Metody bezpośrednie

Aby uzyskać informacje o określonych strukturach schematu, zobacz [Omówienie schematu dla ustawień diagnostycznych](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitorowanie zdarzeń łączenia i rozłączania urządzeń z małymi opóźnieniami

Aby monitorować zdarzenia łączenia i rozłączania urządzeń w środowisku produkcyjnym, zalecamy subskrybowanie [zdarzenia **odłączenia urządzenia** ](iot-hub-event-grid.md#event-types) w Event Grid w celu uzyskania alertów i monitorowania stanu połączenia urządzenia. Skorzystaj z tego [samouczka](iot-hub-how-to-order-connection-state-events.md) , aby dowiedzieć się, jak zintegrować urządzenia połączone i odłączone urządzenia z IoT Hub w rozwiązaniu IoT.

## <a name="next-steps"></a>Następne kroki

[Monitorowanie kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów](iot-hub-monitor-resource-health.md)
