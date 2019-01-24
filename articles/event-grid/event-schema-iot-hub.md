---
title: Schemat usługi Azure Event Grid dla Centrum IoT Hub | Dokumentacja firmy Microsoft
description: Strona odwołań format schematu zdarzeń i właściwości działania usługi IoT Hub
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: df1c0f8256b49e23b720df47c513fba8c62677b5
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475207"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Schemat zdarzeń Azure Event Grid dla Centrum IoT Hub

Ten artykuł zawiera właściwości i schematów zdarzeń usługi Azure IoT Hub. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md). 

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródła zdarzeń usługi IoT Hub](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Usługa Azure IoT Hub emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Opublikowane po zarejestrowaniu urządzenia do usługi IoT hub. |
| Microsoft.Devices.DeviceDeleted | Opublikowana, gdy urządzenie zostanie usunięty z usługi IoT hub. | 
| Microsoft.Devices.DeviceConnected | Gdy urządzenie jest podłączone do usługi IoT hub opublikowany. |
| Microsoft.Devices.DeviceDisconnected | Gdy urządzenie jest odłączony od usługi IoT hub opublikowany. | 

## <a name="example-event"></a>Przykład zdarzenia

Schemat zdarzeń DeviceConnected i DeviceDisconnected mieć tę samą strukturę. To zdarzenie próbki przedstawia schematu zdarzenia wywoływane, gdy urządzenie jest podłączone do usługi IoT hub:

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
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

Schemat zdarzeń DeviceCreated i DeviceDeleted mieć tę samą strukturę. To zdarzenie próbki przedstawia schematu zdarzenia wywoływane, gdy urządzenie jest zarejestrowane w Centrum IoT:

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
      "deviceEtag": "null",
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

### <a name="event-properties"></a>Właściwości zdarzenia

Wszystkie zdarzenia zawierają te same dane najwyższego poziomu: 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| temat | ciąg | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| dane | obiekt | Dane zdarzeń usługi IoT Hub.  |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Dla wszystkich zdarzeń usługi IoT Hub obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| hubName | ciąg | Nazwa usługi IoT Hub, gdy urządzenie utworzony lub został usunięty. |
| deviceId | ciąg | Unikatowy identyfikator urządzenia. Ten ciąg uwzględniający wielkość liter może mieć długości maksymalnie 128 znaków i obsługuje znaki ASCII 7-bitowe znaki alfanumeryczne oraz następujące znaki specjalne: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Zawartość obiektu danych są różne dla każdego wydawcy zdarzeń. Aby uzyskać **urządzenie połączone** i **Urządzenie odłączone** zdarzeń usługi IoT Hub, obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| moduleId | ciąg | Unikatowy identyfikator modułu. To pole jest dane wyjściowe tylko na urządzeniach z modułu. Ten ciąg uwzględniający wielkość liter może mieć długości maksymalnie 128 znaków i obsługuje znaki ASCII 7-bitowe znaki alfanumeryczne oraz następujące znaki specjalne: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | obiekt | Informacje zdarzeń o stanie połączenia urządzenia
| sequenceNumber | ciąg | Liczba, która pomaga wskazać kolejność podłączone urządzenie lub urządzenia odłączony zdarzenia. Najnowsze zdarzenia będzie mieć numer sekwencji jest wyższa niż poprzednie zdarzenie. Ten numer może ulec zmianie przez więcej niż 1, ale jest dokładnie rosnącej. Zobacz [sposób użycia numer sekwencyjny](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Zawartość obiektu danych są różne dla każdego wydawcy zdarzeń. Aby uzyskać **utworzyć urządzenia** i **urządzenie usunięte** zdarzeń usługi IoT Hub, obiekt danych zawiera następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| twin | obiekt | Informacje na temat bliźniaczej reprezentacji urządzenia, czyli represenation chmury metadanych urządzenia w aplikacji. | 
| deviceID | ciąg | Unikatowy identyfikator bliźniaczej reprezentacji urządzenia. | 
| etag | ciąg | Moduł weryfikacji dla zapewnienia spójności aktualizacji bliźniaczej reprezentacji urządzenia. Każdy element etag jest musi być unikatowa dla każdej bliźniaczej reprezentacji urządzenia. |  
| deviceEtag| ciąg | Moduł weryfikacji dla zapewnienia spójności aktualizacji do rejestru urządzeń. Każdy deviceEtag może być unikatowy dla rejestru urządzeń. |
| status | ciąg | Bliźniacza reprezentacja urządzenia jest włączone czy wyłączone. | 
| statusUpdateTime | ciąg | Zaktualizuj ISO8601 sygnatura czasowa ostatniego stanu bliźniaczej reprezentacji urządzenia. |
| connectionState | ciąg | Czy urządzenie jest połączone lub odłączone. | 
| lastActivityTime | ciąg | ISO8601 sygnaturę czasową ostatniej aktywności. | 
| cloudToDeviceMessageCount | liczba całkowita | Liczba komunikatów urządzeń, wysyłane do tego urządzenia w chmurze. | 
| Element authenticationType | ciąg | Typ uwierzytelniania używany dla tego urządzenia: albo `SAS`, `SelfSigned`, lub `CertificateAuthority`. |
| x509Thumbprint | ciąg | Odcisk palca jest unikatową wartość dla x509 certyfikatu, często używane, można znaleźć określonego certyfikatu w magazynie certyfikatów. Odcisk palca jest generowana dynamicznie przy użyciu algorytmu SHA1, a nie istnieje fizycznie w certyfikacie. | 
| primaryThumbprint | ciąg | Podstawowy odcisk palca dla x509 certyfikatu. |
| secondaryThumbprint | ciąg | Pomocniczy odcisk palca dla x509 certyfikatu. | 
| version | liczba całkowita | Liczba całkowita, która jest zwiększany o jeden każdy Godzina aktualizacji bliźniaczej reprezentacji. |
| desired | obiekt | Część właściwości, które mogą być zapisywane tylko przez serwer zaplecza aplikacji i odczytywane przez urządzenie. | 
| zgłoszone | obiekt | Część właściwości, które mogą być zapisywane tylko przez urządzenia i odczytywane przez serwer zaplecza aplikacji. |
| lastUpdated | ciąg | Zaktualizuj ISO8601 sygnatura czasowa ostatniej właściwości bliźniaczej reprezentacji urządzenia. | 

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby dowiedzieć się, jak usługa IoT Hub i Event Grid współpracują ze sobą, zobacz [reagować na zdarzenia IoT Hub przy użyciu usługi Event Grid do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md).