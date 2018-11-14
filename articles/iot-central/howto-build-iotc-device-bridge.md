---
title: Tworzenie usługi Azure IoT Central Mostek urządzenia | Dokumentacja firmy Microsoft
description: Twórz IoT Central Mostek urządzenia do łączenia z innych chmur IoT (Sigfox, cząstek itp. elementy sieci) do aplikacji IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/8/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 83f053a8815f31803f536920497fdc42e72d2a2d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629112"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Tworzenie IoT Central Mostek urządzenia IoT Central nawiązać połączenia innych chmur IoT

*W tym temacie mają zastosowanie do administratorów.*

Mostek urządzenia IoT Central to rozwiązanie typu open source, które łączy swoje Sigfox, cząstek, elementy sieci i innych chmur do swojej aplikacji IoT Central. Czy używasz śledzenia urządzeń podłączonych do sieci firmy Sigfox niski zasilania na poziomie zasobów, używając jakości powietrza monitorowanie urządzeń w chmurze urządzenia cząstki lub za pomocą wilgoci glebę, monitorowanie urządzeń w TTN, można bezpośrednio korzystać z możliwości IoT Środkowe za pomocą mostka urządzenia IoT Central. Mostek urządzenie łączy z innych chmur IoT przy użyciu IoT Central, przekazując dane, które urządzenia wysyłania do innych chmur za pośrednictwem aplikacji IoT Central. W swojej aplikacji IoT Central można reguły kompilacji i uruchamiać funkcje analityczne na te dane, tworzyć przepływy pracy w usłudze Microsoft Flow i Azure Logic apps, wyeksportować te dane i wiele innych. Pobierz [Mostek urządzenia IoT Central](https://aka.ms/iotcentralgithubdevicebridge) z usługi Github

## <a name="what-is-it-and-how-does-it-work"></a>Co to jest i jak to działa?
Mostek urządzenia IoT Central to rozwiązanie typu open source w serwisie Github. Jest gotowe do współpracy z przycisku "Wdróż na platformie Azure", który służy do wdrażania szablonu niestandardowego usługi Azure Resource Manager za pomocą kilku zasobów platformy Azure w ramach subskrypcji platformy Azure. Zasoby obejmują:
-   Aplikacja funkcji platformy Azure
-   Konto usługi Azure Storage
-   Plan usługi App Service (warstwa S1)
-   Usługa Azure Key Vault aplikacja funkcji jest krytycznego mostka urządzenia. Odebraniu żądania HTTP POST z innych platform IoT lub dowolnego niestandardowego platformach przy użyciu integracji prostego elementu webhook. Udostępniliśmy przykłady pokazujące, jak połączyć się z Sigfox, cząstek i TTN chmury. Możesz łatwo rozszerzyć tego rozwiązania, połączyć się z niestandardowego chmury IoT, jeśli platformą wysyłać żądania HTTP POST do aplikacji funkcji.
Aplikacja funkcji przekształca dane w formacie zaakceptowane przez IoT Central i przekazuje go wzdłuż za pośrednictwem interfejsów API usługi DPS.

![Zrzut ekranu usługi Azure functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Jeśli aplikacja IoT Central rozpoznaje Identyfikatora przez urządzenia przekazany dalej komunikat nowej miary będzie wyświetlana dla tego urządzenia. Jeśli identyfikator urządzenia nigdy nie został napotkany przez aplikację IoT Central, aplikacja funkcji będzie próbował zarejestrować nowe urządzenie o takim identyfikatorze urządzenia i będzie on wyświetlany jako "nieskojarzonego urządzenia z systemem" w swojej aplikacji IoT Central. 

## <a name="how-do-i-set-it-up"></a>Jak je skonfigurować?
Instrukcje są wymienione w szczegóły w pliku README w repozytorium Github. 

## <a name="pricing"></a>Cennik
To wszystko znajduje się w Twojej subskrypcji platformy Azure. Większość szacowany koszt aprowizowane zasoby pochodzą z [cena standardowy Plan usługi App Service]( https://azure.microsoft.com/en-us/pricing/details/app-service/windows/). Dowiedz się więcej na ten temat oraz potencjalnych zmniejszyć to w pliku README.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wyjaśniono sposób tworzenia mostka urządzenia IoT Central, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzanie urządzeniami](howto-manage-devices.md)