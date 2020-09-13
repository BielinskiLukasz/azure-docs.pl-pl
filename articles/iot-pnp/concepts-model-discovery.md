---
title: Używanie modeli Plug and Play IoT w rozwiązaniu | Microsoft Docs
description: Jako Konstruktor rozwiązań dowiesz się, jak korzystać z modeli Plug and Play IoT w rozwiązaniu IoT.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9752589c8863cc911369225d268035d9f61c0273
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032031"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>Używanie modeli Plug and Play IoT w rozwiązaniu IoT

W tym artykule opisano, jak w rozwiązaniu IoT można zidentyfikować Identyfikator modelu urządzenia Plug and Play IoT, a następnie pobrać jego definicję modelu.

Istnieją dwie szerokie kategorie rozwiązania IoT:

- *Rozwiązanie utworzone w celach* działa ze znanym zestawem modeli dla urządzeń Plug and Play IoT, które będą łączyć się z rozwiązaniem. Te modele są używane podczas opracowywania rozwiązania.

- Rozwiązanie *oparte na modelu* może współpracować z modelem dowolnego urządzenia Plug and Play IoT. Tworzenie rozwiązań opartych na modelu jest bardziej skomplikowane, ale korzyść polega na tym, że rozwiązanie działa z wszystkimi urządzeniami, które mogą zostać dodane w przyszłości. Oparte na modelu rozwiązanie IoT Pobiera model i używa go do określania danych telemetrycznych, właściwości i poleceń zaimplementowanych przez urządzenie.

Aby korzystać z modelu IoT Plug and Play, rozwiązanie IoT:

1. Identyfikuje identyfikator modelu zaimplementowanego przez urządzenie Plug and Play IoT podłączone do rozwiązania.

1. Używa identyfikatora modelu do pobrania definicji modelu podłączonego urządzenia z repozytorium modelu lub magazynu niestandardowego.

## <a name="identify-model-id"></a>Identyfikator modelu

Gdy urządzenie IoT Plug and Play nawiązuje połączenie z IoT Hub, rejestruje Identyfikator modelu modelu, który implementuje z IoT Hub.

IoT Hub powiadamia rozwiązanie o IDENTYFIKATORze modelu urządzenia w ramach przepływu połączenia urządzenia.

Rozwiązanie może uzyskać identyfikator modelu urządzenia Plug and Play IoT, korzystając z jednej z następujących trzech metod:

### <a name="get-device-twin-api"></a>Pobierz interfejs API z urządzeniami bliźniaczymi

Rozwiązanie może korzystać z interfejsu API " [Get Device bliźniaczy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?view=azure-java-stable) " w celu pobrania identyfikatora modelu urządzenia Plug and Play IoT.

W poniższym fragmencie kodu odpowiedzi z urządzeń z sznurem `modelId` zawiera identyfikator modelu urządzenia Plug and Play IoT:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>Pobierz interfejs API Digital bliźniaczy

Rozwiązanie może korzystać z interfejsu API [uzyskiwania cyfrowej postaci wieloosiowej](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) w celu pobrania identyfikatora modelu zaimplementowanego przez urządzenie Plug and Play IoT.

W poniższym fragmencie kodu odpowiedzi Digital bliźniaczym `$metadata.$model` zawiera identyfikator modelu urządzenia Plug and Play IoT:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>Powiadomienie o zdarzeniu zmiany z cyfrowego przędzy

Połączenie z urządzeniem powoduje [dwucyfrowe powiadomienie o zdarzeniu zmiany](concepts-digital-twin.md#digital-twin-change-events) . Rozwiązanie musi subskrybować to powiadomienie o zdarzeniu. Aby dowiedzieć się, jak włączyć routing dla zdarzeń cyfrowych przędzy, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Rozwiązanie może użyć zdarzenia pokazanego w poniższym fragmencie kodu, aby dowiedzieć się więcej o urządzeniu IoT Plug and Play, które nawiązuje połączenie i pobrać jego identyfikator modelu:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>Pobieranie definicji modelu

Rozwiązanie używa identyfikatora modelu identyfikowanego powyżej do pobrania odpowiedniej definicji modelu.

Rozwiązanie może pobrać definicję modelu przy użyciu jednej z następujących opcji:

### <a name="model-repository"></a>Repozytorium modeli

Rozwiązania mogą używać [repozytorium modelu](concepts-model-repository.md) do pobierania modeli. Konstruktory urządzeń lub konstruktory rozwiązań muszą wcześniej przekazać swoje modele do repozytorium, aby można było je pobrać.

Po zidentyfikowaniu identyfikatora modelu dla nowego połączenia z urządzeniem wykonaj następujące czynności:

1. Pobierz definicję modelu przy użyciu identyfikatora modelu z repozytorium modeli. Aby uzyskać więcej informacji, zobacz [Pobierz modele](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync).

1. Korzystając z definicji modelu połączonego urządzenia, można wyliczyć możliwości urządzenia.

1. Korzystając z wyliczonych możliwości urządzenia, można zezwolić użytkownikom na [współpracującie z urządzeniem](quickstart-service-node.md).

### <a name="custom-store"></a>Magazyn niestandardowy

Rozwiązania mogą przechowywać te definicje modeli w lokalnym systemie plików, w publicznym magazynie plików lub użyć implementacji niestandardowej.

Po zidentyfikowaniu identyfikatora modelu dla nowego połączenia z urządzeniem wykonaj następujące czynności:

1. Pobierz definicję modelu przy użyciu identyfikatora modelu z magazynu niestandardowego.

1. Korzystając z definicji modelu połączonego urządzenia, można wyliczyć możliwości urządzenia. 

1. Korzystając z wyliczonych możliwości urządzenia, można zezwolić użytkownikom na [współpracującie z urządzeniem](quickstart-service-node.md).  

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zintegrować modele IoT Plug and Play w rozwiązaniu IoT, Oto kilka sugerowanych następnych kroków:

- [Interakcja z urządzeniem z rozwiązania](quickstart-service-node.md)
- [Interfejs API REST cyfrowego przędzy IoT](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Eksplorator IoT Azure](howto-use-iot-explorer.md)
