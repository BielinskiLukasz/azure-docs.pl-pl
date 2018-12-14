---
title: Usługa Azure zarządzanie urządzeniami IoT przy użyciu rozszerzenia Azure IoT Hub Toolkit dla programu Visual Studio Code | Dokumentacja firmy Microsoft
description: Użyj rozszerzenia Azure IoT Hub Toolkit dla programu Visual Studio Code do zarządzania urządzeniami Azure IoT Hub, metod bezpośrednich i opcje zarządzania żądane właściwości bliźniaczej reprezentacji.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 8/3/2018
ms.author: junhan
ms.openlocfilehash: 4b7de0652172de5120e88e7c597fc31037ddbbb3
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339573"
---
# <a name="use-azure-iot-hub-toolkit-extension-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Użyj rozszerzenia Azure IoT Hub Toolkit dla programu Visual Studio Code do zarządzania urządzeniami Azure IoT Hub

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[Usługa Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dawniej Azure IoT Toolkit) jest przydatne rozszerzenia programu Visual Studio Code, które ułatwia zarządzanie IoT Hub. Chodzi o opcje zarządzania, które służą do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                    |
|----------------------------|--------------------------------|
| Metody bezpośrednie             | Należy skonfigurować urządzenie działania, takie jak uruchamianie lub zatrzymywanie wysyłanie wiadomości lub ponowne uruchamianie urządzenia.                                        |
| Bliźniacza reprezentacja urządzenia odczytu           | Pobierz stan zgłoszonego urządzenia. Na przykład urządzenie zgłasza diody LED jest teraz migające.                                    |
| Zaktualizować bliźniaczej reprezentacji urządzenia         | Urządzenia należy umieścić w określonych stanach, takie jak ustawienie DIODĘ zielony lub ustawienie interwału wysyłania danych telemetrycznych do 30 minut.         |
| Komunikaty z chmury do urządzenia   | Wysyłanie powiadomienia do urządzenia. Na przykład "jest bardzo prawdopodobne deszczowa już dziś. Nie zapomnij przenieść parasola."              |

Aby uzyskać bardziej szczegółowe objaśnienia na temat różnic oraz wskazówki na temat korzystania z tych opcji, zobacz [wskazówki dotyczące komunikacji urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) i [wskazówki dotyczące komunikacji chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Usługa IoT Hub utrzymuje bliźniaczą reprezentację urządzenia dla każdego urządzenia, który nawiązuje z nim połączenie. Aby uzyskać więcej informacji dotyczących bliźniaczych reprezentacji urządzeń, zobacz [wprowadzenie do bliźniaków urządzeń](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiedz się, przy użyciu rozszerzenia Azure IoT Hub Toolkit dla programu Visual Studio Code z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co należy zrobić

Uruchom rozszerzenie Azure IoT Hub Toolkit dla programu Visual Studio Code z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Co jest potrzebne

* Aktywna subskrypcja platformy Azure.
* Usługi Azure IoT hub w ramach Twojej subskrypcji.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Zestaw narzędzi do usługi Azure IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT hub

1. W **Explorer** widoku programu VS Code, rozwiń **Azure IoT Hub Devices** sekcji w lewym dolnym rogu.

2. Kliknij przycisk **wybierz Centrum IoT Hub** w menu kontekstowym.

3. Okno podręczne będzie wyświetlana w prawym dolnym rogu w umożliwia logowanie do platformy Azure po raz pierwszy.

4. Po zalogowaniu się w Twoja lista subskrypcji platformy Azure zostaną wyświetlone, a następnie wybierz subskrypcję platformy Azure i usługa IoT Hub.

5. Na liście będą wyświetlane w **Azure IoT Hub Devices** kartę w ciągu kilku sekund.

   > [!Note]
   > Można również ukończyć konfigurację, wybierając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź parametry połączenia dla Centrum IoT, które urządzenia IoT, łączy w oknie podręcznym.

## <a name="direct-methods"></a>Metody bezpośrednie

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **wywoływanie metody bezpośredniej**. 

2. Wprowadź nazwę metody oraz ładunek w polu wejściowym.

3. Wyniki będą wyświetlane w **dane wyjściowe** > **Azure IoT Hub Toolkit** widoku.

## <a name="read-device-twin"></a>Bliźniacza reprezentacja urządzenia odczytu

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **Edytuj bliźniaczą reprezentację urządzenia**. 

2. **Azure-iot urządzenie twin.json** plik zostanie otwarty z zawartością bliźniaczej reprezentacji urządzenia.

## <a name="update-device-twin"></a>Zaktualizować bliźniaczej reprezentacji urządzenia

1. Niektóre zmiany edycyjne z **tagi** lub **properties.desired** pola.

2. Kliknij prawym przyciskiem myszy **azure-iot urządzenie twin.json** pliku.

3. Wybierz **aktualizacji bliźniaczej reprezentacji urządzenia** można zaktualizować bliźniaczej reprezentacji urządzenia.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z usługi IoT hub do urządzenia, wykonaj następujące kroki:
 
1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **wysyłania komunikatu C2D urządzeniu**. 

2. Wprowadź komunikat w polu wejściowym.

3. Wyniki będą wyświetlane w **dane wyjściowe** > **Azure IoT Hub Toolkit** widoku.

## <a name="next-steps"></a>Kolejne kroki

Wyjaśniono sposób korzystania z rozszerzenia Azure IoT Hub Toolkit dla programu Visual Studio Code z różnymi opcjami zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
