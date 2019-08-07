---
title: Planowanie zadań przy użyciu usługi Azure IoT Hub (węzeł) | Microsoft Docs
description: Jak zaplanować zadanie IoT Hub platformy Azure w celu wywołania metody bezpośredniej na wielu urządzeniach. Za pomocą zestawów SDK usługi Azure IoT dla środowiska Node. js można zaimplementować aplikacje symulowanych urządzeń i aplikację usługi, aby uruchomić zadanie.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.openlocfilehash: 243f4e63cc04bca018c2bf69492dccf163e92b73
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780828"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Planowanie i emitowanie zadań (Node. js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub to w pełni zarządzana usługa, która umożliwia aplikacji zaplecza tworzenie i śledzenie zadań, które umożliwiają planowanie i aktualizowanie milionów urządzeń.  Zadania mogą służyć do wykonywania następujących czynności:

* Aktualizowanie żądanych właściwości
* Aktualizowanie tagów
* Wywoływanie metod bezpośrednich

Koncepcyjnie, zadanie otacza jedną z tych akcji i śledzi postęp wykonywania w odniesieniu do zestawu urządzeń, który jest zdefiniowany przez zapytanie o pojedyncze urządzenie.  Na przykład aplikacja zaplecza może użyć zadania do wywołania metody ponownego rozruchu na urządzeniach 10 000, określonych przez zapytanie o sznurki urządzenia i zaplanowane w przyszłości. Następnie aplikacja może śledzić postęp, ponieważ każdy z tych urządzeń otrzyma i wykona metodę ponownego uruchomienia.

Dowiedz się więcej o każdej z tych funkcji w następujących artykułach:

* Sznurki i właściwości urządzenia: [Wprowadzenie do bliźniaczych reprezentacji urządzeń](iot-hub-node-node-twin-getstarted.md) i [samouczek: Jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)

* Metody bezpośrednie: [Przewodnik dla deweloperów IoT Hub — bezpośrednie metody](iot-hub-devguide-direct-methods.md) i [Samouczek: metody bezpośrednie](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację symulowanego urządzenia w języku Node. js, która ma metodę bezpośrednią, która umożliwia **lockDoor**, która może być wywoływana przez zaplecze rozwiązania.

* Tworzenie aplikacji konsolowej środowiska Node. js, która wywołuje metodę **lockDoor** Direct w aplikacji symulowanego urządzenia przy użyciu zadania i aktualizuje odpowiednie właściwości przy użyciu zadania urządzenia.

Na końcu tego samouczka masz dwie aplikacje Node. js:

* **simDevice. js**, który łączy się z Centrum IoT z tożsamością urządzenia i odbiera metodę Direct **lockDoor** .

* **scheduleJobService. js**, który wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia i aktualizuje odpowiednie właściwości sznurka urządzenia przy użyciu zadania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko Node. js w wersji 10.0. x lub nowszej [przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) zawiera opis sposobu instalowania platformy Node. js na potrzeby tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową środowiska Node. js, która reaguje na bezpośrednią metodę wywoływaną przez chmurę, która wyzwala symulowaną metodę **lockDoor** .

1. Utwórz nowy pusty folder o nazwie **simDevice**.  W folderze **simDevice** Utwórz plik Package. JSON przy użyciu następującego polecenia z poziomu wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

   ```
   npm init
   ```

2. W wierszu polecenia w folderze **simDevice** Uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **Azure-IoT-Device** i pakiet **Azure-IoT-Device-MQTT** :
   
   ```
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Za pomocą edytora tekstów Utwórz nowy plik **simDevice. js** w folderze **simDevice** .

4. Dodaj następujące instrukcje "Wymagaj" na początku pliku **simDevice. js** :
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Dodaj następującą funkcję, aby obsłużyć metodę **lockDoor** .
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

7. Dodaj następujący kod, aby zarejestrować procedurę obsługi dla metody **lockDoor** .

   ```
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Zapisz i zamknij plik **simDevice. js** .

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań związanych z wywoływaniem metody bezpośredniej i aktualizowaniem właściwości sznurka urządzenia

W tej sekcji utworzysz aplikację konsolową środowiska Node. js, która inicjuje zdalne **lockDoor** na urządzeniu przy użyciu metody Direct i aktualizuje właściwości sznurka urządzenia.

1. Utwórz nowy pusty folder o nazwie **scheduleJobService**.  W folderze **scheduleJobService** Utwórz plik Package. JSON przy użyciu następującego polecenia z poziomu wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```
    npm init
    ```

2. W wierszu polecenia w folderze **scheduleJobService** Uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **Azure-iothub** i pakiet **Azure-IoT-Device-MQTT** :
   
    ```
    npm install azure-iothub uuid --save
    ```

3. Za pomocą edytora tekstów Utwórz nowy plik **scheduleJobService. js** w folderze **scheduleJobService** .

4. Dodaj następujące instrukcje "Wymagaj" na początku pliku **dmpatterns_gscheduleJobServiceetstarted_service. js** :
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Dodaj następujące deklaracje zmiennych i Zastąp wartości symboli zastępczych:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Dodaj następującą funkcję, która jest używana do monitorowania wykonywania zadania:
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Dodaj następujący kod, aby zaplanować zadanie wywołujące metodę urządzenia:
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Dodaj następujący kod, aby zaplanować zadanie aktualizowania sznurka urządzenia:
   
    ```
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Zapisz i zamknij plik **scheduleJobService. js** .

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze **simDevice** Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniego ponownego uruchomienia.
   
    ```
    node simDevice.js
    ```

2. W wierszu polecenia w folderze **scheduleJobService** Uruchom następujące polecenie, aby wyzwolić zadania w celu zablokowania drzwi i zaktualizowania sznurka
   
    ```
    node scheduleJobService.js
    ```

3. Odpowiedź urządzenia na metodę bezpośrednią w konsoli programu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania do zaplanowania metody bezpośredniej dla urządzenia i aktualizacji właściwości sznurka urządzenia.

Aby nadal zacząć korzystać z wzorców IoT Hub i zarządzania urządzeniami, takich jak Zdalna aktualizacja oprogramowania układowego, [zobacz Samouczek: Jak wykonać aktualizację](tutorial-firmware-update.md)oprogramowania układowego.

Aby kontynuować wprowadzenie do IoT Hub, zobacz [wprowadzenie do Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
