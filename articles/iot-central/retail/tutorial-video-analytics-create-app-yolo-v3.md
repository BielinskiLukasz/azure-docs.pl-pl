---
title: Samouczek — Tworzenie aplikacji do wykrywania filmów i obiektów wideo na platformie Azure IoT Central (YOLO v3)
description: W tym samouczku pokazano, jak utworzyć aplikację do analizy wideo w IoT Central. Tworzysz go, dostosowujesz i nawiązujesz połączenie z innymi usługami platformy Azure. W tym samouczku jest stosowany system wykrywania obiektów w czasie rzeczywistym w programie YOLO v3.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: 3994b05f613cbebcf6daa05cf8db3ef429b52407
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428066"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Samouczek: Tworzenie aplikacji do wykrywania filmów i obiektów wideo na platformie Azure IoT Central (YOLO v3)

Korzystając z konstruktora rozwiązań, Dowiedz się, jak utworzyć aplikację analityczną wideo z szablonem aplikacji IoT Central *Analiza filmów wideo — obiekt i ruch* , Azure IoT Edge urządzenia, Azure Media Services i system wykrywania ruchu w czasie rzeczywistym Yolo v3. Rozwiązanie używa magazynu detalicznego, aby pokazać, jak spełnić typowe potrzeby biznesowe do monitorowania kamer zabezpieczeń. Rozwiązanie używa automatycznego wykrywania obiektów w kanale wideo, aby szybko identyfikować i lokalizować interesujące zdarzenia.

> [!TIP]
> Aby użyć OpenVINO &trade; zamiast Yolo v3 na potrzeby wykrywania ruchu, zobacz [Samouczek: Tworzenie aplikacji do analizy filmów wideo i obiektów oraz wykrywania ruchu w usłudze Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> Repozytorium GitHub obejmuje również kod źródłowy dla modułów **IoT Edge i** **lvaYolov3** . Aby uzyskać więcej informacji na temat pracy z kodem źródłowym, zobacz [Tworzenie modułów bramy LVA](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Edytowanie manifestu wdrożenia

Moduł IoT Edge jest wdrażany przy użyciu manifestu wdrożenia. W IoT Central można zaimportować manifest jako szablon urządzenia, a następnie pozwolić IoT Central na zarządzanie wdrożeniem modułu.

Aby przygotować manifest wdrożenia:

1. Otwórz *deployment.amd64.jsw* pliku, który został zapisany w folderze *LVA-Configuration* przy użyciu edytora tekstów.

1. Znajdź `LvaEdgeGatewayModule` Ustawienia i Zmień nazwę obrazu, jak pokazano w poniższym fragmencie kodu:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Dodaj nazwę konta Media Services w `env` węźle w `LvaEdgeGatewayModule` sekcji. Nazwa konta została zanotowana w pliku *scratchpad.txt* :

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Szablon nie uwidacznia tych właściwości w IoT Central, dlatego należy dodać wartości konfiguracji Media Services do manifestu wdrożenia. Znajdź `lvaEdge` moduł i Zastąp symbole zastępcze wartościami, które zostały zanotowane w pliku *scratchpad.txt* podczas tworzenia konta Media Services.

    `azureMediaServicesArmId`Jest to **Identyfikator zasobu** , który został zanotowany w pliku *scratchpad.txt* podczas tworzenia konta Media Services.

    `aadTenantId` `aadServicePrincipalAppId` `aadServicePrincipalSecret` Podczas tworzenia jednostki usługi dla konta Media Services należy zanotować, i w pliku *scratchpad.txt* :

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Zapisz zmiany.

Ten samouczek konfiguruje rozwiązanie do używania modułu YOLO v3 na potrzeby wykrywania obiektów i ruchu. Poniższy fragment kodu przedstawia konfigurację modułu:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Zastąp manifest

Na stronie **LVA graniczny Gateway v2** wybierz pozycję **+ Zamień manifest**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Zastąp manifest":::

Przejdź do folderu *LVA-Configuration* i wybierz *deployment.amd64.jsw* pliku manifestu, który był wcześniej edytowany. Wybierz pozycję **Przekaż**. Po zakończeniu walidacji wybierz pozycję **Zamień**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
