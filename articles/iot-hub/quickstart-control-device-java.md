---
title: 'Szybki start: sterowanie urządzeniem z usługi Azure IoT Hub za pomocą oprogramowania Java'
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje Java. Jedna z aplikacji to aplikacja zaplecza, która może zdalnie kontrolować urządzenia podłączone do centrum. Druga z aplikacji symuluje urządzenie podłączone do centrum, które można kontrolować zdalnie.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 96d30d283ec1565e512be77f55d15305fa226db4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675350"
---
# <a name="quickstart-control-a-device-connected-to-an-azure-iot-hub-with-java"></a>Szybki start: sterowanie urządzeniem połączonym z centrum Usługi Azure IoT za pomocą oprogramowania Java

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

W tym przewodniku Szybki start można użyć metody bezpośredniej do kontrolowania symulowanego urządzenia połączonego z usługą Azure IoT Hub za pomocą aplikacji Java. Usługa IoT Hub to usługa platformy Azure, która umożliwia zarządzanie urządzeniami IoT z chmury i pozyskiwania dużych ilości danych telemetrycznych urządzeń do chmury w celu przechowywania lub przetwarzania. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT. Ten przewodnik Szybki start korzysta z dwóch aplikacji Java: symulowanej aplikacji urządzenia, która odpowiada na bezpośrednie metody wywoływane z aplikacji zaplecza i aplikacji usługi, która wywołuje metodę bezpośrednią na symulowanym urządzeniu.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Zestaw java se development 8. W [języku Java długoterminowa pomoc techniczna dla platformy Azure i usługi Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)w obszarze **Długoterminowa pomoc techniczna**wybierz opcję **Java 8**.

* [Apache Maven 3](https://maven.apache.org/download.cgi).

* [Przykładowy projekt Java](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* Port 8883 otwarty w zaporze. Próbka urządzenia w tym przewodniku Szybki start korzysta z protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

Możesz sprawdzić bieżącą wersję języka Java na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
java -version
```

Możesz sprawdzić bieżącą wersję narzędzia Maven na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
mvn --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Dodawanie rozszerzenia IoT platformy Azure

Uruchom następujące polecenie, aby dodać rozszerzenie IoT usługi Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell. Rozszerzenie IoT dodaje IoT Hub, IoT Edge i Usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS) polecenia do interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-java.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-java.md), możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyJavaDevice**: To jest nazwa zarejestrowanego urządzenia. Zaleca się stosowanie **MyJavaDevice,** jak pokazano. Jeśli wybierzesz inną nazwę urządzenia, musisz również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyJavaDevice \
      --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="retrieve-the-service-connection-string"></a>Pobieranie parametrów połączenia usługi

Potrzebne będą także _parametry połączenia usługi_, aby umożliwić aplikacji zaplecza nawiązywanie połączenia z centrum IoT i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

**YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Zanotuj parametry połączenia usługi, które wyglądają następująco:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Ten ciąg połączenia usługi różni się od ciągu połączenia urządzenia, który został odnotowany w poprzednim kroku.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Aplikacja urządzenia symulowanego łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Symulowane urządzenie wysyła potwierdzenie z powrotem do koncentratora po wykonaniu metody bezpośredniej.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device-2**.

2. Otwórz plik **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** w wybranym edytorze tekstów.

    Zastąp `connString` wartość zmiennej ciągiem połączenia urządzenia, który został zanotowany wcześniej. Następnie zapisz zmiany w **pliku SimulatedDevice.java**.

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i skompilować aplikację urządzenia symulowanego:

    ```cmd/sh
    mvn clean package
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Dane wyjściowe z danych telemetrycznych wysyłanych przez urządzenie do centrum IoT hub](./media/quickstart-control-device-java/iot-hub-application-send-telemetry-output.png)

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja zaplecza łączy się z punktem końcowym po stronie usługi w usłudze IoT Hub. Aplikacja wykonuje bezpośrednie wywołania metody do urządzenia za pośrednictwem centrum IoT hub i nasłuchuje potwierdzeń. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **iot-hub\Quickstarts\back-end-application**.

2. Otwórz plik **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** w wybranym edytorze tekstów.

    Zastąp `iotHubConnectionString` wartość zmiennej ciągiem połączenia usługi, który został zanotowany wcześniej. Następnie zapisz zmiany w **pliku BackEndApplication.java**.

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i skompilować aplikację zaplecza:

    ```cmd/sh
    mvn clean package
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację zaplecza:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe, gdy aplikacja wywołuje metodę bezpośrednią do urządzenia i otrzymuje potwierdzenie:

    ![Dane wyjściowe, ponieważ aplikacja wykonuje bezpośrednie wywołanie metody za pośrednictwem centrum IoT hub](./media/quickstart-control-device-java/iot-hub-direct-method-call-output.png)

    Po uruchomieniu aplikacji zaplecza zobaczysz komunikat w oknie konsoli uruchomionym na urządzeniu symulowanym, a także zobaczysz, że zmienia się prędkość wysyłania komunikatów:

    ![Komunikat konsoli z urządzenia pokazuje szybkość, z jaką się zmienia](./media/quickstart-control-device-java/iot-hub-sent-message-change-rate.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wywołano metodę bezpośrednią na urządzeniu z aplikacji zaplecza oraz odpowiedziano na wywołanie metody bezpośredniej w aplikacji urządzenia symulowanego.

Aby dowiedzieć się, jak przekierowywać komunikaty urządzenie-chmura do innych miejsc docelowych w chmurze, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: przekierowywanie danych telemetrycznych do innych punktów końcowych w celu przetwarzania](tutorial-routing.md)
