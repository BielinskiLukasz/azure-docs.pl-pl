---
title: Urządzenie raspberry Pi w chmurze (C) - łączenie urządzenia Raspberry Pi dla usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i połączyć urządzenia Raspberry Pi do usługi Azure IoT Hub dla urządzenia Raspberry Pi w celu wysyłania danych do platformy w chmurze w ramach tego samouczka.
author: rangv
manager: ''
keywords: Usługa Azure iot urządzenia raspberry pi, usługi iot hub dla urządzenia raspberry pi, urządzenia raspberry pi wysyłania danych do chmury, urządzenia raspberry pi w chmurze
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 8962f41d971ac88134f639a61ef0b42c60bbd419
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161496"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Łączenie urządzenia Raspberry Pi do IoT Hub (C) platformy Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku rozpoczniesz od podstawy pracy z urządzeniem Raspberry Pi, w którym działa Raspbian uczenia. Następnie dowiesz się, jak bezproblemowe łączenie urządzeń do chmury przy użyciu [usługi Azure IoT Hub](about-iot-hub.md). Aby uzyskać przykłady Windows 10 IoT Core, przejdź do [Centrum deweloperów Windows](http://www.windowsondevices.com/).

Nie masz jeszcze zestawu? Spróbuj [symulatora w trybie online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Lub Kup nowy zestaw [tutaj](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co należy zrobić

* Tworzenie Centrum IoT.
* Zarejestruj urządzenie Pi w usłudze IoT hub.
* Konfiguracja urządzenia Raspberry Pi.
* Uruchom przykładową aplikację na Pi do wysyłania danych czujnika do usługi IoT hub.

Łączenie urządzenia Raspberry Pi do usługi IoT hub, którą tworzysz. Następnie uruchom przykładową aplikację na Pi, aby zbierać dane temperatury i wilgotności z czujnika BME280. Na koniec wysyłania danych czujnika do usługi IoT hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum Azure IoT hub i uzyskać nowe parametry połączenia urządzenia.
* Jak połączyć Pi z czujnikiem BME280.
* Jak zbierać dane z czujników po uruchomieniu aplikacji przykładowej na Pi.
* Jak wysyłać danych czujnika do usługi IoT hub.

## <a name="what-you-need"></a>Co jest potrzebne

![Co jest potrzebne](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 lub Raspberry Pi 3 tablicy.
* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu kilku minut.
* Monitor, USB klawiatury i myszy łączących się z Pi.
* Komputer Mac lub komputerze z systemem Windows lub Linux.
* Połączenie internetowe.
* 16 GB lub nowszej karcie microSD.
* USB-karty lub microSD na kartach SD Nagraj obraz systemu operacyjnego na karcie microSD.
* Podaj potęgą 2 i 5 wolt za pomocą kabla USB wczesnych metr 6.

Opcjonalne są następujące elementy:

* Zmontowanych Adafruit BME280 temperatury, wykorzystanie i wilgotności czujnika.
* Breadboard.
* Przewodów jumper 6 F/M.
* Rozproszona LED 10 mm.


> [!NOTE] 
Te elementy są opcjonalne, ponieważ obsługa próbki kodu symulowane dane czujników.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Konfigurowanie urządzenia Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Zainstaluj system operacyjny Raspbian pi

Przygotuj karcie microSD instalacji obrazu Raspbian.

1. Pobierz Raspbian.
   1. [Pobierz Raspbian Jessie za pomocą programu Desktop](https://www.raspberrypi.org/downloads/raspbian/) (plik zip).
   1. Wyodrębnij obraz Raspbian do folderu na komputerze.
1. Zainstaluj Raspbian karcie microSD.
   1. [Pobierz i zainstaluj narzędzie nagrywarka karty Etcher SD](https://etcher.io/).
   1. Uruchom Etcher, a następnie wybierz obraz Raspbian, który został wyodrębniony w kroku 1.
   1. Wybierz dysk karty microSD. Należy pamiętać, że Etcher może została już wybrana poprawnego dysku.
   1. Kliknij przycisk Flash, aby zainstalować Raspbian na karcie microSD.
   1. Karta microSD należy usunąć z komputera po zakończeniu instalacji. Jest bezpiecznie usunąć karcie microSD bezpośrednio, ponieważ Etcher automatycznie wysuwa lub odinstalowuje karcie microSD po jego ukończeniu.
   1. Wstaw karcie microSD do Pi.

### <a name="enable-ssh-and-spi"></a>Włączanie protokołu SSH i SPI

1. Nawiązać połączenie z Pi monitora, klawiatury i myszy, uruchom Pi i następnie zaloguj za pomocą Raspbian `pi` jako nazwy użytkownika i `raspberry` jako hasło.
1. Kliknij ikonę Raspberry > **preferencje** > **Raspberry Pi konfiguracji**.

   ![W menu Preferencje Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Na **interfejsów** kartę, należy ustawić **SPI** i **SSH** do **Włącz**, a następnie kliknij przycisk **OK**. Jeśli nie masz fizycznego czujniki i chcesz użyć danych z symulowanych czujników, ten krok jest opcjonalny.

   ![Włącz SPI i ustawieniami SSH na urządzeniu Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Aby włączyć protokół SSH i SPI, można znaleźć więcej dokumentów odwołania na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Łączenie z czujnika do Pi

Użyj przewodów breadboard i jumper nawiązać DIODĘ i BME280 Pi w następujący sposób. Jeśli nie masz czujnik [pominąć tę sekcję](#connect-pi-to-the-network).

![Połączenie urządzenia Raspberry Pi i czujnik](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane temperatury i wilgotności. I diody LED będzie blink w przypadku komunikacji między urządzeniem i chmurą. 

Czujnik numery PIN można użyć następujących połączeń:

| Uruchom (czujnik & LED)     | Końcowy (tablica)            | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD LED (Pin 5G)         | Interfejs GPIO 4 (Pin 7)         | Kabel biały   |
| GND LED (Pin 6G)         | GND (Pin 6)            | Czarnego przewodu   |
| VDD (Pin 18F)            | 3, 3V PWR (Pin 17)      | Kabel biały   |
| GND (Pin 20F)            | GND (Pin 20)           | Czarnego przewodu   |
| SCK (Pin 21F)            | SPI0 SCLK (Pin 23)     | Kabel pomarańczowy  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Żółty kabel  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Kabel zielony   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Niebieski kabel    |

Kliknij, aby wyświetlić [Raspberry Pi 2 i 3 mapowania kodu Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) użytkownikowi.

Po pomyślnym nawiązaniu BME280 urządzenia Raspberry Pi, należy go jak poniżej obrazu.

![Pi połączonych i BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Pi się z siecią

Włącz Pi przy użyciu micro kabla USB i zasilacz. Użyj kabla Ethernet do łączenia Pi sieci przewodowej lub postępuj zgodnie z [instrukcji z urządzeniem Raspberry Pi podstawę](https://www.raspberrypi.org/learning/software-guide/wifi/) do Pi nawiązać połączenie z sieci bezprzewodowej. Po Twojej Pi została pomyślnie podłączona do sieci, należy pamiętać o [adres IP Twojego Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Podłączone do sieci przewodowej](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Uruchamianie przykładowej aplikacji na Pi

### <a name="login-to-your-raspberry-pi"></a>Zaloguj się do urządzenia Raspberry Pi

1. Aby nawiązać połączenie z urządzeniem Raspberry Pi, użyj jednej z następujących klientów SSH z komputera hosta.
   
   **Użytkownicy Windows**
   1. Pobierz i zainstaluj [PuTTY](http://www.putty.org/) for Windows. 
   1. Skopiuj adres IP w sekcji Pi do nazwy hosta (lub adres IP), a następnie wybierz typ połączenia SSH.
   
   ![Programu puTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac i użytkownicy systemu Ubuntu**
   
   Użyj wbudowanego klienta SSH w systemie Ubuntu lub z systemem macOS. Może być konieczne uruchomienie `ssh pi@<ip address of pi>` połączyć Pi za pośrednictwem protokołu SSH.
   > [!NOTE] 
   Domyślna nazwa użytkownika to `pi` , a hasło to `raspberry`.


### <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Klonowanie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Uruchom skrypt instalacji:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Jeśli możesz **nie masz fizycznego BME280**, można użyć "--symulowane dane" jako parametr wiersza polecenia, aby zasymulować dane temperatury i wilgotności. `sudo ./setup.sh --simulated-data`

### <a name="build-and-run-the-sample-application"></a>Kompilowanie i uruchamianie przykładowej aplikacji

1. Tworzenie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   cmake . && make
   ```
   ![Dane wyjściowe kompilacji](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Uruchamianie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Upewnij się, możesz kopiowania i wklejania parametry połączenia urządzenia w apostrofy.


Powinny być widoczne następujące dane wyjściowe, dane czujników i komunikaty, które są wysyłane do usługi IoT hub.

![Dane wyjściowe — dane czujników wysyłanych z urządzenia Raspberry Pi do Centrum IoT hub](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Kolejne kroki

Po uruchomieniu aplikacji przykładowej, zbieranie danych z czujników i wysyłać je do Centrum IoT hub. Aby wyświetlić wiadomości wysłanych do usługi IoT hub lub wysyłania wiadomości do urządzenia Raspberry Pi urządzenia Raspberry Pi, zobacz [rozszerzenie użycia usługi Azure IoT Toolkit dla programu Visual Studio Code do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
