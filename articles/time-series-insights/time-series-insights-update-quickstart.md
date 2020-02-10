---
title: 'Szybki Start: Eksplorowanie środowiska demonstracyjnego w wersji zapoznawczej — Azure Time Series Insights | Microsoft Docs'
description: Poznaj kluczowe funkcje środowiska demonstracyjnego w wersji zapoznawczej Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 02/07/2020
ms.openlocfilehash: dc4a8da69b0398c6487008c106a9f5bcdb8a885e
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110237"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Szybki Start: Eksplorowanie środowiska demonstracyjnego w wersji zapoznawczej Azure Time Series Insights

Ten przewodnik Szybki Start umożliwia rozpoczęcie pracy z programem Azure Time Series Insights w wersji zapoznawczej. Bezpłatna wersja demonstracyjna zawiera najważniejsze funkcje, które zostały dodane do Time Series Insights wersji zapoznawczej.

Środowisko demonstracyjne w wersji zapoznawczej programu Time Series Insights zawiera firmę firmy Contoso, która działa w dwóch farmach z wiatru. Każda Farma ma 10 turbin. Każda turbina jest wyposażona w 20 czujników raportujących dane co minutę do usługi Azure IoT Hub. Czujniki zbierają informacje o warunkach pogodowych, skoku bloku i położeniu Yaw. Rejestrowane są również informacje na temat wydajności generatora, zachowania skrzyni biegów i monitorów bezpieczeństwa.

W tym przewodniku szybki start dowiesz się, jak używać Time Series Insights, aby znaleźć szczegółowe informacje umożliwiające podejmowanie działań w danych firmy Contoso. Przeprowadzamy również krótką analizę głównych przyczyn w celu lepszego przewidywania błędów krytycznych i przeprowadzenia konserwacji.

> [!IMPORTANT]
> Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , jeśli go nie masz.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Poznawanie eksploratora usługi Time Series Insights w środowisku pokazowym

W Eksploratorze Time Series Insights w wersji zapoznawczej przedstawiono dane historyczne i analizę głównych przyczyn. Aby rozpocząć:

1. Przejdź do środowiska [demonstracyjnego farmy wiatrów firmy Contoso](https://insights.timeseries.azure.com/preview/samples) .  

1. Jeśli zostanie wyświetlony monit, zaloguj się do Eksploratora Time Series Insights przy użyciu poświadczeń konta platformy Azure.

## <a name="work-with-historical-data"></a>Pracuj z danymi historycznymi

1. W firmie **contoso 1**zapoznaj się z tematem wiatr turbin **W7**.  

   1. Zmień zakres widoku na **1/1/17 20:00:00.00 do 3/10/17 20:00:00.00 (UTC)** .
   1. Aby wybrać czujnik, wybierz pozycję **contoso roślina 1** > **W7** > **system generatora** > **GeneratorSpeed**. Następnie przejrzyj wyświetlane wartości.

      [![W7 w firmie Contoso 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Niedawno firma Contoso wykryła pożar w turbinie **W7**. Opinie różnią się w zależności od tego, co spowodowało pożar. W Time Series Insights zostanie wyświetlony czujnik alertów o zapłonie, który został aktywowany podczas pożaru.

   1. Zmień zakres widoku na **3/9/17 20:00:00.00 do 3/10/17 20:00:00.00 (UTC)** .
   1. Wybierz pozycję **system bezpieczeństwa** > **FireAlert**.

      [![firma Contoso znalazła ogień w programie wiatr turbin W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Zapoznaj się z innymi zdarzeniami w czasie działania, aby zrozumieć, co się stało. Wykorzystanie ropy naftowej i aktywne ostrzeżenia, które zostały wprowadzone tuż przed pożarem.

   1. Wybierz **system przechyłów** > **HydraulicOilPressure**.
   1. Wybierz **system przechyłów** > **ActiveWarning**.

      [![przeglądać inne zdarzenia w tym samym czasie](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Czujniki ropy naftowej i aktywne ostrzeżenia są napowietrzane bezpośrednio przed pożarem. Rozwiń wyświetlaną serię czasową, aby przejrzeć inne oznaki, które były oczywiste, co prowadzi do pożaru. Obie czujniki zmieniają się w miarę upływu czasu. Wahania wskazują, że wzorzec trwały i worrisome.

    * Zmień zakres widoku na **2/24/17 20:00:00.00 do 3/10/17 20:00:00.00 (UTC)** .

      [![nacisk oleju i aktywne czujniki ostrzeżeń również](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Badanie dwóch lat danych historycznych ujawnia inne zdarzenie pożaru, które miało takie same wahania czujnika.

    * Zmień zakres widoku na **1/1/16 na 12/31/17** (wszystkie dane).

      [![poszukaj wzorców historycznych](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Korzystając z Time Series Insights i danych telemetrycznych czujnika, wykryliśmy długoterminowy trend ukryty w danych historycznych. Dzięki tym nowym szczegółowym informacjom możemy:

* Wyjaśnij, co się stało.
* Rozwiąż problem.
* Umieść lepsze systemy powiadomień o alertach.

## <a name="root-cause-analysis"></a>Analiza głównej przyczyny

1. Niektóre scenariusze wymagają zaawansowanej analizy, aby odkryć wskazówki dotyczące danych. Wybierz Windmill **W6** w dniu **6/25**.

    1. Zmień zakres widoku na **6/1/17 20:00:00.00 do 7/1/17 20:00:00.00 (UTC)** .
    1. Wybierz pozycję **contoso roślina 1** > **W6** > **bezpieczeństwo systemu** > **VoltageActuatorSwitchWarning**.

       [![zmienić zakres widoku i wybrać W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Ostrzeżenie wskazuje na problem z napięciem z generatora. Ogólne dane wyjściowe mocy generatora są w normalnych parametrach w bieżącym interwale. Przez zwiększenie naszego interwału okazuje się, że kolejny wzór zostanie spowodowany. Inicjał jest oczywisty.

    1. Usuń czujnik **VoltageActuatorSwitchWarning** .
    1. Wybierz **system generatora** > **ActivePower**.
    1. Zmień interwał na **3D**.

       [![zmienić interwał na 3D](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Zwiększając zakres czasu, możemy określić, czy problem został zatrzymany, czy nadal.

    * Zwiększ przedział czasu do 60 dni.

      [![zwiększyć czas przedziału czasu do 60 dni](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Inne punkty danych czujników można dodać, aby zapewnić większy kontekst. Im więcej czujników jest wyświetlanych, tym pełniejsze zrozumienie problemu jest. Upuśćmy znacznik, aby wyświetlić rzeczywiste wartości. 

    1. Wybierz pozycję **system generatora**, a następnie wybierz pozycję trzy czujniki: **GridVoltagePhase1**, **GridVoltagePhase2**i **GridVoltagePhase3**.
    1. Ustaw znacznik na ostatnim punkcie danych w widocznym obszarze.

       [![upuść znacznik](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Dwie czujniki napięcia działają w sposób porównywalny i w ramach zwykłych parametrów. Wygląda na to, że czujnik **GridVoltagePhase3** jest przyczyna.

1. Po dodaniu wysoce kontekstowych danych, lista rozwijana fazy 3 pojawia się jeszcze bardziej, aby rozwiązać ten problem. Teraz mamy dobry lider w zakresie przyczyny ostrzeżenia. Jesteśmy gotowi do rozpatrzenia problemu z naszym zespołem obsługi.  

    * Zmień widok tak, aby wszystkie czujniki **systemu generatora** były nakładane na tę samą skalę wykresu.

      [![zmienić wyświetlania, aby uwzględnić wszystko](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu przewodnika Szybki Start Wyczyść utworzone zasoby:

1. Z menu po lewej stronie w obszarze [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**i Znajdź Azure Time Series Insights grupę zasobów.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

Wszystko jest gotowe do utworzenia własnego środowiska Time Series Insights w wersji zapoznawczej. Aby rozpocząć:

> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights w wersji zapoznawczej](time-series-insights-update-plan.md)

Dowiedz się, jak korzystać z demonstracji i jej funkcji:

> [!div class="nextstepaction"]
> [Eksplorator Time Series Insights w wersji zapoznawczej](time-series-insights-update-explorer.md)