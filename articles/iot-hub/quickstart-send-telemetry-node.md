---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start (Node.js) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje środowiska Node.js wysyłające symulowane dane telemetryczne do centrum IoT oraz odczytujące dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/19/2018
ms.author: dobett
ms.openlocfilehash: 379d6cf589012e7b7e4d0f1ec2dc3ba40cec075f
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514931"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-nodejs"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT oraz odczytywanie danych telemetrycznych z centrum przy użyciu aplikacji zaplecza (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem usługi IoT Hub do aplikacji zaplecza w celu ich przetworzenia.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji środowiska Node.js, jednej do wysyłania danych telemetrycznych oraz jednej do odczytywania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane przy użyciu środowiska Node.js. Potrzebujesz środowiska Node.js w wersji v4.x.x lub nowszej na komputerze deweloperskim.

Możesz pobrać środowisko Node.js dla wielu platform ze strony [nodejs.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

Pobierz przykładowy projekt Node.js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip i wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby dodać rozszerzenie interfejsu wiersza polecenia usługi IoT Hub i utworzyć tożsamość urządzenia. 

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT.

   **MyNodeDevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj elementu MyNodeDevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```
 
    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

1. Potrzebne będą także _parametry połączenia usługi_, aby umożliwić aplikacji zaplecza nawiązywanie połączenia z centrum IoT w celu pobrania komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:
   
   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name YourIoTHubName --output table
    ```
     
    Zanotuj parametry połączenia usługi, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Parametry połączenia usługi różnią się od parametrów połączenia urządzenia.


## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. Otwórz lokalne okno terminalu i przejdź do folderu głównego przykładowego projektu Node.js. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

1. Otwórz plik **SimulatedDevice.js** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `connectionString` parametrami połączenia urządzenia zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **SimulatedDevice.js**.

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja zaplecza łączy się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Aplikacja odbiera komunikaty urządzenie-chmura wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

1. Otwórz inne okno terminalu i przejdź do folderu głównego przykładowego projektu Node.js. Następnie przejdź do folderu **iot-hub\Quickstarts\read-d2c-messages**.

1. Otwórz plik **ReadDeviceToCloudMessages.js** w wybranym edytorze.

    Zastąp wartość zmiennej `connectionString` parametrami połączenia usługi zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **ReadDeviceToCloudMessages.js**.

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić aplikację zaplecza:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez aplikację zaplecza danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

    ![Uruchamianie aplikacji zaplecza](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT, zarejestrowano urządzenie, wysłano dane telemetryczne do centrum przy użyciu aplikacji Node.js oraz odczytano dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT](quickstart-control-device-node.md)
