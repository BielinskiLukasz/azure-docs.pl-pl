---
title: Wysyłanie komunikatów do serwera MQTT przy użyciu biblioteki klienckiej usługi Azure MQTT | Microsoft Docs
description: Dowiedz się, jak wysyłać komunikaty do brokera MQTT przy użyciu biblioteki klienta MQTT. Dowiedz się również, jak skonfigurować zestawu deweloperskiego IoT DevKit jako klienta MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 4de8b0910a2be48cc626775c5c9bc873c6e780dc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889546"
---
# <a name="send-messages-to-an-mqtt-server"></a>Wysyłanie komunikatów do serwera MQTT

Systemy Internet rzeczy (IoT) często zajmują się ciągłą, niską jakością lub powolnymi połączeniami internetowymi. MQTT to protokół łączności między maszynami (M2M), który został opracowany z uwzględnieniem takich wyzwań. 

Używana tutaj Biblioteka klienta MQTT jest częścią projektu [zaćmienie PAHO](https://www.eclipse.org/paho/) , który oferuje interfejsy API do używania MQTT w wielu środkach transportu.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym projekcie dowiesz się:
- Jak wysyłać komunikaty do brokera MQTT przy użyciu biblioteki klienta MQTT.
- Jak skonfigurować zestawu deweloperskiego IoT DevKit jako klienta MQTT.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [przewodnik wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) , aby:

* Połączono z usługą DevKit z siecią Wi-Fi
* Przygotowywanie środowiska deweloperskiego

## <a name="open-the-project-folder"></a>Otwieranie folderu projektu

1. Jeśli DevKit już nawiązuje połączenie z komputerem, odłącz go.

2. Rozpocznij VS Code.

3. Połącz DevKit z komputerem.

## <a name="open-the-mqttclient-sample"></a>Otwieranie przykładu MQTTClient

Rozwiń sekcję **przykłady Arduino** lewej strony, przejdź do **przykładów dla zestawu DEWELOPERSKIEGO AZ3166 > MQTT**, a następnie wybierz pozycję **MQTTClient**. Zostanie otwarte nowe okno VS Code z folderem projektu.

> [!NOTE]
> Możesz również otworzyć przykład z palety poleceń. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`), aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie Znajdź i wybierz **Arduino: przykłady**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Kompiluj i przekaż szkic Arduino do DevKit

Wpisz `Ctrl+P` (macOS: `Cmd+P`), aby uruchomić `task device-upload`. Po zakończeniu przekazywania DevKit ponownie uruchamia i uruchamia szkic.

![przekazywanie urządzenia](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Może zostać wyświetlony komunikat o błędzie "błąd: AZ3166: nieznany pakiet". Ten błąd występuje, gdy indeks pakietu tablicy nie został poprawnie odświeżony. Aby rozwiązać ten problem, zapoznaj się z [sekcją opracowywanie często ZAdawanych pytań dotyczących IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testowanie projektu

W VS Code wykonaj następującą procedurę, aby otworzyć i skonfigurować Monitor seryjny:

1. Kliknij `COM[X]` Word na pasku stanu, aby ustawić odpowiedni port COM z `STMicroelectronics`: ![Set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kliknij ikonę zasilania na pasku stanu, aby otworzyć Monitor szeregowy: ![](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg) monitora
  
3. Na pasku stanu kliknij liczbę reprezentującą szybkość transmisji i ustaw ją na `115200`: ![Set-bodów-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

W monitorze seryjnym są wyświetlane wszystkie komunikaty wysyłane przez przykładowy szkic. Szkic łączy DevKit z siecią Wi-Fi. Po pomyślnym nawiązaniu połączenia Wi-Fi szkic wyśle wiadomość do brokera usługi MQTT. Następnie przykład wielokrotnie wysyła dwa komunikaty "iot.eclipse.org" przy użyciu ustawień QoS 0 i QoS 1.

![dane wyjściowe](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z tematem [często zadawanych pytań dotyczących usługi IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub Połącz się przy użyciu następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Zobacz też

* [Łączenie usługi IoT DevKit AZ3166 z platformą Azure IoT Hub w chmurze](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Wstrząsanie i wstrząsanie tweetu](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować usługę zestawu deweloperskiego IoT DevKit jako klienta MQTT i użyć biblioteki klienta MQTT do wysyłania komunikatów do brokera programu MQTT, poniżej przedstawiono sugerowane następne kroki:

* [Omówienie akceleratora rozwiązań do monitorowania zdalnego usługi Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Łączenie urządzenia zestawu deweloperskiego IoT DevKit z aplikacją usługi Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)
