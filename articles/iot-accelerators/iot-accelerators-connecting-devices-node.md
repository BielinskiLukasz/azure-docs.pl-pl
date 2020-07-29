---
title: Udostępnianie urządzeń do zdalnego monitorowania w Node.js — Azure | Microsoft Docs
description: Opisuje sposób podłączenia urządzenia do akceleratora rozwiązania do monitorowania zdalnego przy użyciu aplikacji, która jest zapisywana w Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt
ms.openlocfilehash: 76692c7d923e261d1e3beefa0e2ea76b94282a46
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683987"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Połącz urządzenie z akceleratorem rozwiązania do zdalnego monitorowania (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku pokazano, jak podłączyć rzeczywiste urządzenie do akceleratora rozwiązania do monitorowania zdalnego. W tym samouczku użyjesz Node.js, co jest dobrą opcją dla środowisk z minimalnymi ograniczeniami zasobów.

Jeśli wolisz symulować urządzenie, zobacz [Tworzenie i testowanie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="create-a-nodejs-solution"></a>Tworzenie rozwiązania Node.js

Upewnij się, że na komputerze deweloperskim jest zainstalowana [Node.js](https://nodejs.org/) wersja 4.0.0 lub nowsza. `node --version`Aby sprawdzić wersję programu, można uruchomić polecenie w wierszu polecenia.

1. Utwórz folder o nazwie `remotemonitoring` na komputerze deweloperskim. Przejdź do tego folderu w środowisku wiersza polecenia.

1. Aby pobrać i zainstalować pakiety potrzebne do ukończenia przykładowej aplikacji, uruchom następujące polecenia:

    ```cmd/sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. W `remotemonitoring` folderze Utwórz plik o nazwie **remote_monitoring.js**. Otwórz ten plik w edytorze tekstu.

1. W pliku **remote_monitoring.js** Dodaj następujące `require` instrukcje:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Dodaj następujące deklaracje zmiennych po instrukcji `require`. Zastąp wartość symbolu zastępczego `{device connection string}` wartością zanotowaną dla urządzenia obsługiwanego w rozwiązaniu do zdalnego monitorowania:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Aby zdefiniować podstawowe dane telemetrii, Dodaj następujące zmienne:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Aby zdefiniować niektóre wartości właściwości, Dodaj następujące zmienne:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Dodaj następującą zmienną, aby zdefiniować zgłoszone właściwości do wysłania do rozwiązania. Te właściwości obejmują metadane, które mają być wyświetlane w interfejsie użytkownika sieci Web:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Aby wydrukować wyniki operacji, Dodaj następującą funkcję pomocnika:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Dodaj następującą funkcję pomocnika, aby użyć programu do losowego generowania wartości telemetrycznych:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Dodaj następującą funkcję rodzajową, aby obsłużyć bezpośrednie wywołania metod z rozwiązania. Funkcja wyświetla informacje o metodzie bezpośredniej, która została wywołana, ale w tym przykładzie nie modyfikuje urządzenia w żaden sposób. Rozwiązanie używa metod bezpośrednich do działania na urządzeniach:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Dodaj następującą funkcję, aby obsłużyć wywołania metody bezpośredniej **FirmwareUpdate** z rozwiązania. Funkcja weryfikuje parametry przesłane w ładunku metody bezpośredniej, a następnie asynchronicznie uruchamia symulację aktualizacji oprogramowania układowego:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Dodaj następującą funkcję, aby symulować długotrwały przepływ aktualizacji oprogramowania układowego, który raportuje postęp z powrotem do rozwiązania:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Dodaj następujący kod, aby wysłać dane telemetryczne do rozwiązania. Aplikacja kliencka dodaje do komunikatu właściwości w celu zidentyfikowania schematu komunikatu:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Dodaj następujący kod, aby utworzyć wystąpienie klienta:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Dodaj następujący kod do:

    * Otwórz połączenie.
    * Skonfiguruj procedurę obsługi dla żądanych właściwości.
    * Wyślij zgłoszone właściwości.
    * Procedury obsługi rejestru dla metod bezpośrednich. W przykładzie zastosowano osobną procedurę obsługi dla metody bezpośredniej aktualizacji oprogramowania układowego.
    * Rozpocznij wysyłanie danych telemetrycznych.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Zapisz zmiany w pliku **remote_monitoring.js** .

1. Aby uruchomić przykładową aplikację, uruchom następujące polecenie w wierszu polecenia:

     ```cmd/sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
