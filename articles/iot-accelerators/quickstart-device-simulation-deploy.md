---
title: Wypróbowywanie i uruchamianie rozwiązania do symulacji urządzenia — Azure | Microsoft Docs
description: W tym przewodniku Szybki start wdrożysz symulację urządzenia usługi IoT Azure i uruchomisz symulację
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/28/2018
ms.author: troyhop
ms.openlocfilehash: 12f993f606fc1ef44280142d5acfa2687cd4e043
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601597"
---
# <a name="quickstart-deploy-and-run-an-iot-device-simulation-in-azure"></a>Szybki start: wdrażanie i uruchamianie symulacji urządzenia IoT na platformie Azure

W tym przewodniku Szybki start przedstawiono sposób wdrażania symulacji urządzenia usługi Azure IoT w celu przetestowania rozwiązania usługi IoT. Po wdrożeniu akceleratora rozwiązań możesz uruchomić przykładową symulację, aby rozpocząć pracę.

Do wykonania kroków tego przewodnika Szybki start jest potrzebna aktywna subskrypcja platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="deploy-device-simulation"></a>Wdrażanie symulacji urządzenia

W przypadku wdrażania symulacji urządzenia w ramach subskrypcji platformy Azure musisz ustawić niektóre opcje konfiguracji.

Zaloguj się do witryny [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) przy użyciu poświadczeń konta platformy Azure.

Kliknij kafelek **Symulacja urządzenia**:

![Wybieranie symulacji urządzenia](./media/quickstart-device-simulation-deploy/devicesimulation.png)

Kliknij pozycję **Wypróbuj teraz** na stronie opisu symulacji urządzenia:

![Klikanie pozycji Wypróbuj teraz](./media/quickstart-device-simulation-deploy/devicesimulationPDP.png)

Na stronie **Tworzenie rozwiązania Symulacja urządzenia** wpisz unikatową **nazwę rozwiązania**.

W polach **Subskrypcja** i **Region** wybierz wartości, których chcesz użyć do wdrożenia akceleratora rozwiązania. Zwykle jest wybierany region znajdujący się najbliżej. Musisz być [użytkownikiem lub administratorem globalnym](iot-accelerators-permissions.md) w ramach subskrypcji.

Zaznacz pole, aby wdrożyć centrum IoT Hub, które będzie używane z rozwiązaniem do symulacji urządzeń. Zawsze możesz później zmienić centrum IoT Hub używane w symulacji.

Kliknij przycisk **Utwórz rozwiązanie**, aby rozpocząć aprowizację rozwiązania. Ten proces trwa co najmniej pięć minut:

![Szczegóły rozwiązania do symulacji urządzenia](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logowanie się do rozwiązania

Po zakończeniu procesu aprowizacji możesz zalogować się do wystąpienia symulacji urządzenia, klikając przycisk **Uruchom**:

![Otwieranie symulacji urządzenia](./media/quickstart-device-simulation-deploy/choosenew.png)

Kliknij pozycję **Zaakceptuj**, aby zaakceptować żądanie uprawnień. W przeglądarce zostanie wyświetlony pulpit nawigacyjny rozwiązania do symulacji urządzenia.

Po pierwszym otwarciu zostanie wyświetlony pulpit nawigacyjny symulacji urządzenia z przewodnikiem **Wprowadzenie**. Kliknij pierwszy kafelek, aby otworzyć przykładową symulację. Jeśli zamkniesz przewodnik **Wprowadzenie**, możesz otworzyć **prostą przykładową symulację** na pulpicie nawigacyjnym, klikając jej kafelek:

![Pulpit nawigacyjny rozwiązania](./media/quickstart-device-simulation-deploy/GettingStarted.png)

## <a name="sample-simulation"></a>Przykładowa symulacja

Ponieważ jest to symulacja przykładowa, nie można jej edytować. Symulacja jest konfigurowana z następującymi ustawieniami:

| Ustawienie             | Wartość                       |
| ------------------- | --------------------------- |
| Docelowa usługa IoT Hub      | Użyj dostarczonej wstępnie usługi IoT Hub |
| Model urządzenia        | Ciężarówka                       |
| Liczba urządzeń   | 10                          |
| Częstotliwość telemetrii | 10 sekund                  |
| Czas trwania symulacji | Uruchamianie bez ograniczeń            |

![Konfiguracja symulacji](./media/quickstart-device-simulation-deploy/SampleSimulation.png)

## <a name="run-the-simulation"></a>Uruchamianie symulacji

Kliknij pozycję **Rozpocznij symulację**. Symulacja będzie uruchamiana bez ograniczeń zgodnie z konfiguracją. Możesz w dowolnym momencie zatrzymać symulację, klikając pozycję **Zatrzymaj symulację**. Symulacja przedstawia statystyki dotyczące bieżącego uruchomienia.

![Przebieg symulacji](./media/quickstart-device-simulation-deploy/runningsimulation.png)

Z poziomu wystąpienia symulacji urządzenia w danym momencie można uruchomić tylko jedną symulację urządzenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz dalsze działanie, symulacja urządzenia powinna pozostać wdrożona.

Jeśli symulacja urządzenia nie jest już potrzebna, usuń ją na stronie [Aprowizowane rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard), klikając jej kafelek, a następnie klikając pozycję **Usuń rozwiązanie**:

![Usuwanie rozwiązania](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono symulację urządzenia i uruchomiono przykładową symulację urządzenia usługi IoT.

> [!div class="nextstepaction"]
> [Utwórz symulację z co najmniej jednym typem urządzenia](iot-accelerators-device-simulation-create-simulation.md)