---
title: Połącz ogólnego klienta aplikacji Node.js usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako deweloper w urządzeniu jak połączyć ogólny urządzenia środowiska Node.js do aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/04/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4d2701f078a26c22f52aebd0ef562dd60eaca923
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097978"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Łączenie aplikacji klienckiej ogólnego aplikację usługi Azure IoT Central (Node.js)

W tym artykule opisano jak Deweloper urządzenia do łączenia z ogólnych aplikacji Node.js reprezentujący rzeczywistego urządzenia do aplikacji Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja usługi Azure IoT Central. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
1. Jest maszyna deweloperska z [Node.js](https://nodejs.org/) wersji 4.0.0 lub nowszej. Możesz uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

W aplikacji usługi Azure IoT Central potrzebne są szablon urządzenia z następujących pomiarów i zdefiniowane właściwości urządzenia:

### <a name="telemetry-measurements"></a>Pomiary dotyczące prawdziwych danych telemetrycznych

Dodaj następujące dane telemetryczne w **pomiarów** strony:

| Nazwa wyświetlana | Nazwa pola  | Jednostki | Min. | Maks. | Miejsca dziesiętne |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperature | F     | 60  | 110 | 0              |
| Wilgotność     | humidity    | %     | 0   | 100 | 0              |
| Ciśnienie     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
>   Typ danych miary telemetrii jest zmiennoprzecinkowy numer punktu.

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenia, dane telemetryczne nie można wyświetlić w aplikacji.

### <a name="state-measurements"></a>Pomiary stanu

Dodaj następujący stan w **pomiarów** strony:

| Nazwa wyświetlana | Nazwa pola  | Wartość 1 | Nazwa wyświetlana | Wartość 2 | Nazwa wyświetlana |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Tryb wentylatora     | fanmode     | 1       | Działanie      | 0       | Zatrzymano      |

> [!NOTE]
>   Typ danych miary stanu jest ciągiem.

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenia, stan, nie można wyświetlić w aplikacji.

### <a name="event-measurements"></a>Zdarzenie pomiarów

Dodaj następujące zdarzenie w **pomiarów** strony:

| Nazwa wyświetlana | Nazwa pola  | Ważność |
| ------------ | ----------- | -------- |
| Przegrzaniu  | przegrzeje się    | Błąd    |

> [!NOTE]
>   Typ danych miary zdarzeń jest ciągiem.

### <a name="device-properties"></a>Właściwości urządzenia

Dodaj następujące właściwości urządzenia w **strona właściwości**:

| Nazwa wyświetlana        | Nazwa pola        | Typ danych |
| ------------------- | ----------------- | --------- |
| Numer seryjny       | serialNumber      | tekst      |
| Producent urządzenia | producent      | tekst      |

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenie, aplikacja nie może wyświetlić wartość właściwości urządzenia.

### <a name="settings"></a>Ustawienia

Dodaj następujący kod **numer** ustawienia w **strony ustawień**:

| Nazwa wyświetlana    | Nazwa pola     | Jednostki | Miejsca dziesiętne | Min. | Maks.  | Początkowa |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Wentylator szybkości       | fanSpeed       | obr. / min   | 0        | 0   | 3000 | 0       |
| Ustaw temperaturę | setTemperature | F     | 0        | 20  | 200  | 80      |

Wprowadź nazwę pola dokładnie tak jak pokazano w tabeli do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenie, urządzenie nie może odbierać wartości ustawienia.

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central należy dodać rzeczywistego urządzenia za pomocą szablonu urządzenia, tworzenie i zanotuj parametry połączenia urządzenia. Aby uzyskać instrukcje krok po kroku dotyczące łączenia aplikacji Node.js IoT Central, zobacz [wygenerować parametry połączenia dla rzeczywistego urządzenia z poziomu aplikacji](tutorial-add-device.md#generate-connection-string) i [przygotować kod klienta](tutorial-add-device.md#prepare-the-client-code) samouczków > Dodawanie urządzenia.

### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js

Poniższe kroki pokazują jak utworzyć aplikację kliencką, która implementuje rzeczywistego urządzenia, które dodanych do aplikacji. W tym miejscu aplikacji Node.js reprezentuje rzeczywistego urządzenia. 

1. Utwórz folder o nazwie `connected-air-conditioner-adv` na maszynie. Przejdź do tego folderu w środowisku wiersza polecenia.

1. Aby zainicjować projektu środowiska Node.js, uruchom następujące polecenia:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Utwórz plik o nazwie **connectedAirConditionerAdv.js** w `connected-air-conditioner-adv` folderu.

1. Dodaj następujący kod `require` instrukcji na początku **connectedAirConditionerAdv.js** pliku:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Dodaj następujące deklaracje zmiennych do pliku:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    > [!NOTE]
    > Usługa Azure IoT Central przeszła do przy użyciu usługi Azure IoT Hub Device Provisioning (DPS) dla wszystkich połączeń urządzeń, wykonaj te instrukcje, aby [pobieranie parametrów połączenia urządzenia](concepts-connectivity.md#get-a-connection-string) i kontynuować pracę z pozostałej części tego samouczka. Aby uzyskać dodatkową pomoc, możesz również znaleźć zestaw szczegółowych instrukcji w [przygotować kod klienta](tutorial-add-device.md#prepare-the-client-code) w samouczkach > Dodaj urządzenie.

    Aktualizuj symbol zastępczy `{your device connection string}` przy użyciu parametrów połączenia urządzenia. W tym przykładzie, możemy zainicjować `targetTemperature` na zero, można opcjonalnie wykonać bieżącej odczytu z urządzenia lub wartość z bliźniaczej reprezentacji urządzenia. 

1. Aby wysyłać pomiary dotyczące prawdziwych danych telemetrycznych, stanu i zdarzeń z aplikacją usługi Azure IoT Central, dodaj następującą funkcję do pliku:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Aby wysłać właściwości urządzenia z aplikacją usługi Azure IoT Central, dodaj następującą funkcję do pliku:

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Aby zdefiniować ustawienia, które odpowiada urządzenie, dodaj następującą definicję:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Aby obsłużyć zaktualizowanych ustawień z poziomu aplikacji usługi Azure IoT Central, Dodaj następujący element do pliku:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Dodaj następujące polecenie, aby nawiązać połączenie usługi Azure IoT Central i obsługiwać funkcje w kodzie klienta:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Uruchamianie aplikacji Node.js

W środowisku wiersza polecenia, uruchom następujące polecenie:

```cmd/sh
node connectedAirConditionerAdv.js
```

Operator w aplikacji usługi Azure IoT Central rzeczywistego urządzenia możesz wykonywać następujące czynności:

* Wyświetlanie danych telemetrycznych na **pomiarów** strony:

    ![Wyświetlanie danych telemetrycznych](media/howto-connect-nodejs/viewtelemetry.png)

* Wyświetlanie wartości właściwości urządzenia, które są wysyłane z urządzenia **właściwości** strony. Właściwości Kafelki aktualizowania urządzenia, jeśli połączenie zostanie nawiązane.

    ![Wyświetl właściwości urządzenia](media/howto-connect-nodejs/viewproperties.png)

* Ustaw wentylator temperatury szybkość i docelowej z **ustawienia** strony. Wartości ustawienia synchronizacji, jeśli połączenie zostanie nawiązane.

    ![Wentylator prędkości](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć ogólnego klienta Node.js z aplikacji usługi Azure IoT Central, Oto zalecane kolejne kroki:
* [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
