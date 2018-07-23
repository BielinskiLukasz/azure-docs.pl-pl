---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub z węzłem | Dokumentacja firmy Microsoft
description: Sposób przekazywania plików z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla środowiska Node.js. Przekazane pliki są przechowywane w kontenerze obiektów blob usługi Azure storage.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 936063e1419d5e2261033ea74d75687eade928e8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187330"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ten samouczek opiera się na kodzie w [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-node-node-c2d.md) samouczka, aby dowiesz się, jak używać [pliku przekazywania możliwościami usługi IoT Hub](iot-hub-devguide-file-upload.md) można przekazać pliku do [obiektów blob platformy Azure Magazyn](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

- Bezpiecznie przekazać urządzenia z systemem Azure identyfikator URI obiektu blob przekazywania pliku.
- Powiadomienia przekazywania pliku usługi IoT Hub umożliwia wyzwalanie przetwarzania pliku w aplikacji zaplecza.

[Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md) samouczku przedstawiono podstawowe funkcje obsługi komunikatów urządzenia do chmury usługi IoT Hub. Jednak w niektórych scenariuszach nie pozwala na łatwe mapowanie danych wysyłanych przez urządzenia do stosunkowo mały wiadomości urządzenia do chmury, które akceptuje usługi IoT Hub. Na przykład:

* Duże pliki, które zawierają obrazy
* Filmy wideo
* Wibracje danych próbkowania o wysokiej częstotliwości
* Pewnego rodzaju wstępnie przetworzonych danych.

Te pliki to typowo wsadowego przetwarzania w chmurze przy użyciu narzędzi takich jak [usługi Azure Data Factory](../data-factory/introduction.md) lub [Hadoop](../hdinsight/index.yml) stosu. Gdy zachodzi potrzeba wyżynne plików z urządzenia, można nadal używać zabezpieczeń i niezawodności usługi IoT Hub.

Na końcu tego samouczka, możesz uruchomić dwie aplikacje konsolowe środowiska Node.js:

* **SimulatedDevice.js**, która przekazuje plik do magazynu przy użyciu identyfikatora URI sygnatury dostępu Współdzielonego, dostarczone przez Centrum IoT hub.
* **ReadFileUploadNotification.js**, która odbiera powiadomienia o przekazywania plików z usługi IoT hub.

> [!NOTE]
> Centrum IoT Hub obsługuje wiele platform urządzeń i językach (w tym C, .NET, Javascript, Python i Java) za pomocą zestawów SDK urządzeń Azure IoT. Zapoznaj się [Centrum deweloperów Azure IoT] instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do usługi Azure IoT Hub.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko Node.js w wersji 4.0.x lub nowszej.
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](http://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekaż plik z aplikacji urządzenia

W tej sekcji opisano tworzenie aplikacji urządzenia, aby przekazać plik do usługi IoT hub.

1. Utwórz pusty folder o nazwie ```simulateddevice```.  W folderze ```simulateddevice``` utwórz plik package.json, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

1. W wierszu polecenia w folderze ```simulateddevice``` uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iot-device** i pakiet **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Za pomocą edytora tekstów utwórz plik **SimulatedDevice.js** w folderze ```simulateddevice```.

1. Dodaj następujące instrukcje ```require``` na początku pliku **SimulatedDevice.js**:

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Dodaj zmienną ```deviceconnectionstring``` i użyj jej do utworzenia wystąpienia **Client**.  Zastąp ```{deviceconnectionstring}``` nazwą urządzenia utworzonego w _Tworzenie Centrum IoT_ sekcji:

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Dla uproszczenia należy parametry połączenia znajduje się w kodzie: nie jest to zalecana praktyka i w zależności od przypadku użycia i architektura warto wziąć pod uwagę bezpieczniejsze sposoby zapisywania tego wpisu tajnego.

1. Dodaj następujący kod do połączenia klienta:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Utwórz wywołanie zwrotne i użyj **uploadToBlob** funkcję, aby przekazać plik.

    ```nodejs
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. Zapisz i zamknij plik **SimulatedDevice.js**.

1. Skopiuj plik obrazu do `simulateddevice` folder i zmień jego nazwę `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Otrzymywać powiadomienie o przekazywaniu pliku

W tej sekcji utworzysz aplikację konsoli środowiska Node.js, która odbiera komunikaty powiadomień przekazywania pliku z usługi IoT Hub.

Możesz użyć **iothubowner** parametry połączenia z Centrum IoT Hub do ukończenia tej sekcji. Można znaleźć w ciągu połączenia [witryny Azure portal](https://portal.azure.com/) na **zasady dostępu współdzielonego** bloku.

1. Utwórz pusty folder o nazwie ```fileuploadnotification```.  W folderze ```fileuploadnotification``` utwórz plik package.json, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

1. W wierszu polecenia w ```fileuploadnotification``` folder, uruchom następujące polecenie, aby zainstalować **azure-iothub** pakiet zestawu SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Za pomocą edytora tekstów Utwórz **FileUploadNotification.js** w pliku ```fileuploadnotification``` folderu.

1. Dodaj następujący kod ```require``` instrukcji na początku **FileUploadNotification.js** pliku:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Dodaj zmienną ```iothubconnectionstring``` i użyj jej do utworzenia wystąpienia **Client**.  Zastąp ```{iothubconnectionstring}``` przy użyciu parametrów połączenia Centrum IoT utworzonego w _Tworzenie Centrum IoT_ sekcji:

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Dla uproszczenia należy parametry połączenia znajduje się w kodzie: nie jest to zalecana praktyka i w zależności od przypadku użycia i architektura warto wziąć pod uwagę bezpieczniejsze sposoby zapisywania tego wpisu tajnego.

1. Dodaj następujący kod do połączenia klienta:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Otwórz klienta i użyj **getFileNotificationReceiver** funkcję, aby otrzymywać aktualizacje stanu.

    ```nodejs
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Zapisz i Zamknij **FileUploadNotification.js** pliku.

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

W wierszu polecenia w `fileuploadnotification` folder, uruchom następujące polecenie:

```cmd/sh
node FileUploadNotification.js
```

W wierszu polecenia w `simulateddevice` folder, uruchom następujące polecenie:

```cmd/sh
node SimulatedDevice.js
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z **SimulatedDevice** aplikacji:

![Dane wyjściowe z aplikacji symulowane urządzenia](./media/iot-hub-node-node-file-upload/simulated-device.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z **FileUploadNotification** aplikacji:

![Dane wyjściowe z aplikacji odczytu-— — powiadomienie o przekazywaniu pliku](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Aby wyświetlić przekazany plik w kontenerze magazynu, które zostały skonfigurowane, można użyć portalu:

![Przekazany plik](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików usługi IoT Hub można uproszczenie przekazywania plików z urządzeń. Możesz kontynuować poznawanie funkcji Centrum IoT i scenariusze z następujących artykułów:

* [Programistyczne tworzenie Centrum IoT hub][lnk-create-hub]
* [Wprowadzenie do zestawu SDK języka C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

<!-- Links -->
[Centrum deweloperów Azure IoT]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
