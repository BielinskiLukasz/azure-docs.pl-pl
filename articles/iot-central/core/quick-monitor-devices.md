---
title: Szybki Start — monitorowanie urządzeń na platformie Azure IoT Central
description: Dowiedz się, jak używać aplikacji IoT Central platformy Azure do monitorowania urządzeń.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e56b733a567c706c2a15f2c30ed93a47c244db11
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168720"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Szybki Start: korzystanie z usługi Azure IoT Central do monitorowania urządzeń

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Ten przewodnik Szybki Start przedstawia Cię jako operatora, jak używać aplikacji Microsoft Azure IoT Central do monitorowania urządzeń i zmieniania ustawień.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać trzy poprzednie Przewodniki Szybki Start [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md), [dodać symulowane urządzenie do aplikacji IoT Central](./quick-create-pnp-device.md) i [skonfigurować reguły i akcje dla urządzenia](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Odbieranie powiadomienia

Usługa Azure IoT Central wysyła powiadomienia dotyczące urządzeń jako wiadomości e-mail. Konstruktor dodał regułę do wysyłania powiadomienia, gdy temperatura w czujniku połączonego urządzenia przekroczyła wartość progową. Sprawdź wiadomości e-mail wysłane na konto wybrane przez konstruktora do odbierania powiadomień.

Otwórz wiadomość e-mail otrzymaną po zakończeniu [konfigurowania reguł i akcji dla](quick-configure-rules.md) szybkiego startu urządzenia. W wiadomości e-mail wybierz link do urządzenia:

![Wiadomość e-mail z alertem](media/quick-monitor-devices/email.png)

Widok **omówienia** symulowanego urządzenia utworzonego w poprzednich przewodnikach Szybki Start zostanie otwarty w przeglądarce:

![Urządzenie, które wyzwoliło wiadomość e-mail z powiadomieniem](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Badanie problemu

Jako operator można wyświetlić informacje o urządzeniu w widokach **Przegląd**, **informacje**i **polecenia** . Konstruktor utworzył widok **Zarządzanie urządzeniem** służący do edytowania informacji o urządzeniu i ustawiania właściwości urządzeń.

Wykres na pulpicie nawigacyjnym pokazuje wykreśloną temperaturę urządzenia. Należy określić, że temperatura urządzenia jest zbyt wysoka.

## <a name="remediate-an-issue"></a>Rozwiązywanie problemu

Aby wprowadzić zmianę na urządzeniu, użyj strony **Zarządzanie urządzeniem** .

Zmień **szybkość wentylatora** na 500, aby schłodzić urządzenie. Wybierz pozycję **Zapisz** , aby zaktualizować urządzenie. Gdy urządzenie potwierdzi zmianę ustawień, stan właściwości zmieni się na **zsynchronizowany**:

![Aktualizowanie ustawień](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Odbieranie powiadomienia
* Badanie problemu
* Rozwiązywanie problemu

Teraz, gdy teraz wiesz już, jak monitorować urządzenie, sugerowanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Kompiluj szablon urządzenia i Zarządzaj nim](howto-set-up-template.md).
