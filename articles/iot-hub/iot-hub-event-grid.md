---
title: Usługa Azure IoT Hub i Event Grid | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Event Grid, aby wyzwolić procesy oparte na akcje wykonywane w usłudze IoT Hub.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 14bdbb5d629cb5a3fccd6f874e30ded0648e0124
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249472"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagowanie na zdarzenia IoT Hub przy użyciu usługi Event Grid do wyzwalania akcji

Usługa Azure IoT Hub integruje się z usługi Azure Event Grid, dzięki czemu mogą wysyłać powiadomienia o zdarzeniach do innych usług i wyzwolić procesy podrzędne. Skonfiguruj swoje aplikacje biznesowe do nasłuchiwania zdarzeń usługi IoT Hub, dzięki czemu pozwala reagować na krytyczne zdarzenia w sposób niezawodny, skalowalny i bezpieczny. Na przykład Utwórz aplikację, aby wykonywać wiele akcji, takich jak aktualizacja bazy danych, tworzenia biletu i dostarczanie wiadomości e-mail z powiadomieniem za każdym razem, gdy nowych urządzeń IoT jest zarejestrowany do usługi IoT hub. 

[Usługa Azure Event Grid](../event-grid/overview.md) jest w pełni zarządzanej usługi routingu zdarzeń korzystającej Publikuj — Subskrybuj modelu. Usługa Event Grid ma wbudowaną obsługę usług Azure, takich jak [usługi Azure Functions](../azure-functions/functions-overview.md) i [usługi Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)i mogą dostarczać alerty zdarzeń do usługi spoza platformy Azure przy użyciu elementów webhook. Aby uzyskać pełną listę obsługi zdarzeń, które obsługuje usługi Event Grid, zobacz [wprowadzenie do usługi Azure Event Grid](../event-grid/overview.md). 

![Architektura usługi Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Dostępność regionalna

Integracja usługi Event Grid jest dostępna dla centrów IoT Hub znajdujących się w regionach, w którym są obsługiwane usługi Event Grid. Aby uzyskać najnowszą listę regionów, zobacz [wprowadzenie do usługi Azure Event Grid](../event-grid/overview.md). 

## <a name="event-types"></a>Typy zdarzeń

Usługa IoT Hub publikuje następujące typy zdarzeń: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Opublikowane po zarejestrowaniu urządzenia do usługi IoT hub. |
| Microsoft.Devices.DeviceDeleted | Opublikowana, gdy urządzenie zostanie usunięty z usługi IoT hub. |
| Microsoft.Devices.DeviceConnected | Gdy urządzenie jest podłączone do usługi IoT hub opublikowany. |
| Microsoft.Devices.DeviceDisconnected | Gdy urządzenie jest odłączony od usługi IoT hub opublikowany. |

Umożliwia skonfigurowanie zdarzeń do publikowania z każdej usługi IoT hub w portalu Azure lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać przykład, wypróbuj samouczek [wysyłanie wiadomości e-mail powiadomień o zdarzeniach usługi Azure IoT Hub przy użyciu aplikacji logiki](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schemat zdarzeń

Zdarzenia usługi IoT Hub zawierają wszystkie informacje potrzebne do reagowania na zmiany w cyklu życia urządzenia. Można zidentyfikować zdarzenia usługi IoT Hub, sprawdzając właściwości Typ zdarzenia, który rozpoczyna się od **Microsoft.Devices**. Aby uzyskać więcej informacji o tym, jak korzystać z właściwości zdarzeń usługi Event Grid, zobacz [schematu zdarzeń usługi Event Grid](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schemat podłączone urządzenia

Poniższy przykład przedstawia schematu zdarzeń do podłączonych urządzeń: 

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

### <a name="device-created-schema"></a>Utworzono urządzenie schematu

Poniższy przykład przedstawia schematu utworzonego zdarzenia urządzenia: 

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

Aby uzyskać szczegółowy opis każdej właściwości, zobacz [schematu zdarzeń usługi Azure Event Grid dla Centrum IoT Hub](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Filtruj zdarzenia

Subskrypcje zdarzeń usługi IoT Hub można filtrować zdarzenia na podstawie nazwy zdarzenia typu i urządzeń. Filtry tematu usługi Event Grid pracy oparte na **zaczyna się od** (prefiks) i **kończy się za pomocą** (sufiks) jest zgodna. Filtr korzysta `AND` operatora, dzięki czemu zdarzenia z tematem zgodne prefiks i sufiks są dostarczane do subskrybenta. 

Temat zdarzenia IoT w formacie:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Ograniczenia dotyczące urządzenie, połączonych i odłączony zdarzenia

Do odbierania podłączonych urządzeń i zdarzenia urządzenia odłączony, należy otworzyć łącze D2C lub łącze C2D dla Twojego urządzenia. Jeśli urządzenie korzysta z protokołu MQTT, usługi IoT Hub zostanie zachowana C2D Otwórz link. Dla protokołu AMQP przez wywołanie metody można otworzyć łącza C2D [wyświetlany API Async](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). 

D2C link jest otwarty, w przypadku wysyłania danych telemetrycznych. Jeśli migotanie jest połączenie z urządzeniem, czyli urządzenie łączy i odłącza często, firma Microsoft nie będzie wysyłać każdego pojedynczego połączenia stanu, ale będą publikować stan połączenia, który jest z migawki na minutę. W przypadku awarii usługi IoT Hub opublikujemy odpowiednie stanu połączenia urządzenia jak najszybciej po zakończeniu awarii. Jeśli podczas tej niedostępności rozłączy się urządzenie, zdarzenia odłączonego urządzenia zostaną opublikowane w ciągu 10 minut.

## <a name="tips-for-consuming-events"></a>Wskazówki dotyczące używania zdarzenia

Aplikacje obsługujące zdarzeń usługi IoT Hub, należy wykonać następujące sugerowane rozwiązania:

* Dlatego ważne jest, aby założyć, że zdarzenia pochodzą z określonego źródła, nie można skonfigurować wiele subskrypcji do kierowanie zdarzeń do tego samego programu obsługi zdarzeń. Zawsze sprawdzaj temat wiadomość, aby upewnić się, że pochodzi on z usługi IoT hub, których oczekujesz. 

* Nie wolno zakładać, że wszystkie zdarzenia, które otrzymujesz są typy, których można oczekiwać. Przed rozpoczęciem przetwarzania wiadomości, Zawsze sprawdzaj typu zdarzenia.

* Komunikaty mogą pojawić się poza kolejnością, lub z opóźnieniem. Aby zrozumieć aktualnych informacji o obiektach, należy użyć pola element etag.

## <a name="next-steps"></a>Kolejne kroki

* [Wypróbuj samouczek zdarzeń usługi IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Dowiedz się, jak porządkować zdarzenia podłączania i odłączania urządzeń](iot-hub-how-to-order-connection-state-events.md)
* [Dowiedz się więcej na temat usługi Event Grid](../event-grid/overview.md)
* [Porównaj różnice między routingu zdarzeń usługi IoT Hub i komunikaty](iot-hub-event-grid-routing-comparison.md)
