---
title: Azure IoT Hub i Event Grid | Microsoft Docs
description: Użyj Azure Event Grid, aby wyzwolić procesy w oparciu o akcje wykonywane w programie IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 2969791204474a7d73493ce6397c52255f7eab4a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151299"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagowanie na zdarzenia IoT Hub przy użyciu Event Grid do wyzwalania akcji

Platforma Azure IoT Hub integruje się z usługą Azure Event Grid, dzięki czemu możesz wysyłać powiadomienia o zdarzeniach do innych usług i wyzwalać procesy podrzędne. Skonfiguruj aplikacje biznesowe do nasłuchiwania zdarzeń IoT Hub, aby umożliwić reagowanie na krytyczne zdarzenia w niezawodny, skalowalny i bezpieczny sposób. Na przykład Kompilowanie aplikacji, która aktualizuje bazę danych, tworzy bilet służbowy i dostarcza powiadomienia e-mail za każdym razem, gdy nowe urządzenie IoT zostanie zarejestrowane w centrum IoT Hub.

[Azure Event Grid](../event-grid/overview.md) to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania/subskrybowania. Event Grid ma wbudowaną obsługę usług platformy Azure, takich jak [Azure Functions](../azure-functions/functions-overview.md) i [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), i mogą dostarczać alerty zdarzeń do usług innych niż Azure przy użyciu elementów webhook. Aby zapoznać się z pełną listą programów obsługi zdarzeń obsługiwanych przez Event Grid, zobacz [wprowadzenie do Azure Event Grid](../event-grid/overview.md).

![Architektura Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Dostępność regionalna

Integracja Event Grid jest dostępna dla centrów IoT znajdujących się w regionach, w których Event Grid jest obsługiwana. Aby uzyskać najnowszą listę regionów, zapoznaj [się z wprowadzeniem do Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Typy zdarzeń

IoT Hub publikuje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Opublikowano, gdy urządzenie jest zarejestrowane w usłudze IoT Hub. |
| Microsoft.Devices.DeviceDeleted | Opublikowano, gdy urządzenie zostanie usunięte z Centrum IoT Hub. |
| Microsoft.Devices.DeviceConnected | Opublikowano, gdy urządzenie jest połączone z usługą IoT Hub. |
| Microsoft.Devices.DeviceDisconnected | Opublikowano po odłączeniu urządzenia od centrum IoT Hub. |
| Microsoft.Devices.DeviceTelemetry | Opublikowano, gdy komunikat telemetrii urządzenia jest wysyłany do centrum IoT Hub |

Użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby skonfigurować zdarzenia do opublikowania w każdym Centrum IoT Hub. Przykładowo Wypróbuj samouczek [wysyłanie powiadomień e-mail dotyczących zdarzeń IoT Hub platformy Azure przy użyciu Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schemat zdarzeń

Zdarzenia IoT Hub zawierają wszystkie informacje potrzebne do reagowania na zmiany w cyklu życia urządzenia. Można zidentyfikować zdarzenie IoT Hub, sprawdzając, czy właściwość eventType zaczyna się od **Microsoft. Devices**. Aby uzyskać więcej informacji o sposobach używania właściwości zdarzenia Event Grid, zobacz [Event Grid schematu zdarzeń](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schemat połączony z urządzeniem

Poniższy przykład przedstawia schemat zdarzenia połączonego z urządzeniem:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Schemat telemetrii urządzenia

Komunikat telemetrii urządzenia musi znajdować się w prawidłowym formacie JSON z atrybutem ContentType ustawionym na wartość **Application/JSON** i ContentEncoding na **UTF-8** we [właściwościach systemu](iot-hub-devguide-routing-query-syntax.md#system-properties)komunikatów. W obu tych właściwościach jest rozróżniana wielkość liter. Jeśli kodowanie zawartości nie jest ustawione, IoT Hub będzie zapisywać komunikaty w standardowym formacie 64.

Zdarzenia telemetrii urządzeń można wzbogacać przed ich opublikowaniem w Event Grid, wybierając punkt końcowy jako Event Grid. Aby uzyskać więcej informacji, zobacz [Omówienie wzbogacania komunikatów](iot-hub-message-enrichments-overview.md).

Poniższy przykład przedstawia schemat zdarzenia telemetrii urządzenia:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Schemat utworzony przez urządzenie

Poniższy przykład przedstawia schemat zdarzenia utworzonego przez urządzenie:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Aby uzyskać szczegółowy opis każdej właściwości, zobacz [Azure Event Grid schemacie zdarzeń dla IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrowanie zdarzeń

IoT Hub subskrypcje zdarzeń mogą filtrować zdarzenia na podstawie typu zdarzenia, zawartości danych i tematu, który jest nazwą urządzenia.

Event Grid włącza [filtrowanie](../event-grid/event-filtering.md) typów zdarzeń, tematów i zawartości danych. Podczas tworzenia subskrypcji Event Grid możesz wybrać opcję subskrybowania wybranych zdarzeń IoT. Filtry podmiotu w Event Grid pracy oparte na parametrach **zaczynają** się od (prefix) i **kończą się znakiem** (sufiksem). Filtr używa operatora `AND`, więc zdarzenia z podmiotem, które pasują do tego prefiksu i sufiksu, są dostarczane do subskrybenta.

Temat zdarzeń IoT używa formatu:

```json
devices/{deviceId}
```

Event Grid umożliwia także filtrowanie atrybutów poszczególnych zdarzeń, w tym zawartości danych. Dzięki temu można wybrać, jakie zdarzenia są dostarczane na podstawie zawartości komunikatu telemetrii. Zobacz [Zaawansowane filtrowanie](../event-grid/event-filtering.md#advanced-filtering) , aby wyświetlić przykłady. W celu filtrowania treści wiadomości telemetrycznych należy ustawić Właściwość ContentType na wartość **Application/JSON** i ContentEncoding na **UTF-8** we [właściwościach systemu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)komunikatów. W obu tych właściwościach jest rozróżniana wielkość liter.

W przypadku zdarzeń telemetrii, takich jak DeviceConnected, DeviceDisconnected, DeviceCreated i DeviceDeleted, można użyć filtrowania Event Grid podczas tworzenia subskrypcji. W przypadku zdarzeń telemetrii oprócz filtrowania w Event Grid użytkownicy mogą również odfiltrować bliźniaczych reprezentacji urządzeń, właściwości komunikatów i treści za pomocą zapytania routingu wiadomości. 

Gdy subskrybujesz zdarzenia telemetryczne za pośrednictwem Event Grid, IoT Hub tworzy domyślną trasę komunikatów do wysyłania komunikatów urządzeń typu źródło danych do Event Grid. Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz [IoT Hub Routing komunikatów](iot-hub-devguide-messages-d2c.md). Ta trasa będzie widoczna w portalu w obszarze IoT Hub > Routing komunikatów. Tylko jedna trasa do Event Grid jest tworzona bez względu na liczbę PRZYKŁADowych subskrypcji utworzonych dla zdarzeń telemetrii. Dlatego jeśli potrzebujesz kilku subskrypcji z różnymi filtrami, możesz użyć operatora OR w tych zapytaniach w tej samej trasie. Tworzenie i usuwanie trasy jest kontrolowane przez subskrypcję zdarzeń telemetrii za pośrednictwem Event Grid. Nie można utworzyć lub usunąć trasy do Event Grid przy użyciu IoT Hub Routing komunikatów.

Aby filtrować komunikaty przed wysłaniem danych telemetrycznych, można zaktualizować [zapytanie routingu](iot-hub-devguide-routing-query-syntax.md). Należy pamiętać, że zapytanie routingu można zastosować do treści wiadomości tylko wtedy, gdy treść jest JSON. Należy również ustawić Właściwość ContentType na wartość **Application/JSON** i ContentEncoding na **UTF-8** we [właściwościach systemu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)komunikatów.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Ograniczenia dotyczące zdarzeń podłączonych do urządzenia i rozłączonych urządzeń

Aby odbierać zdarzenia połączone z urządzeniem i odłączone urządzenia, należy otworzyć link D2C lub link C2D dla urządzenia. Jeśli urządzenie korzysta z protokołu MQTT, IoT Hub będzie mieć otwarte łącze C2D. W przypadku AMQP można otworzyć łącze C2D, wywołując [asynchroniczny interfejs API odbierania](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

Link D2C jest otwarty w przypadku wysyłania danych telemetrycznych. 

Jeśli połączenie z urządzeniem jest migotane, co oznacza, że urządzenie jest często nawiązywane i rozłączane, nie wyśle wszystkich stanów pojedynczego połączenia, ale będzie publikować *ostatni* stan połączenia, który jest ostatecznie spójny. Na przykład jeśli urządzenie zostało początkowo w stanie połączonym, następnie przeprowadzono ruchy w ciągu kilku sekund, a następnie zostanie ono przywrócone w stanie połączonym. Żadne nowe zdarzenia stanu połączenia z urządzeniem nie zostaną opublikowane od momentu początkowego stanu połączenia. 

W przypadku awarii IoT Hub stan połączenia urządzenia zostanie opublikowany zaraz po przekroczeniu awarii. Jeśli urządzenie rozłączy się podczas tej awarii, zdarzenie odłączone urządzenia zostanie opublikowane w ciągu 10 minut.

## <a name="tips-for-consuming-events"></a>Porady dotyczące używania zdarzeń

W aplikacjach, które obsługują zdarzenia IoT Hub, powinny być przestrzegane następujące zalecenia:

* Można skonfigurować wiele subskrypcji do kierowania zdarzeń do tego samego programu obsługi zdarzeń, dlatego nie należy zakładać, że zdarzenia pochodzą z konkretnego źródła. Należy zawsze sprawdzić temat komunikatu, aby upewnić się, że pochodzi on z usługi IoT Hub, której oczekujesz.

* Nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwane. Zawsze sprawdzaj typ zdarzenia przed przetworzeniem komunikatu.

* Komunikaty mogą być przychodzące z kolejności lub po opóźnieniu. Użyj pola ETag, aby zrozumieć, czy informacje o obiektach są aktualne dla zdarzeń utworzonych przez urządzenie lub urządzenia.

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj samouczek zdarzeń IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Dowiedz się, jak porządkować zdarzenia podłączania i odłączania urządzeń](iot-hub-how-to-order-connection-state-events.md)

* [Dowiedz się więcej o Event Grid](../event-grid/overview.md)

* [Porównanie różnic między zdarzeniami i komunikatami IoT Hub routingu](iot-hub-event-grid-routing-comparison.md)

* [Dowiedz się, jak używać zdarzeń telemetrii IoT do implementowania analizy przestrzennej usługi IoT przy użyciu Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
