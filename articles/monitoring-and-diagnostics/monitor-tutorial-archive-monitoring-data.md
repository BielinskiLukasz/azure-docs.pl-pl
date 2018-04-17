---
title: Archiwizowanie danych monitorowania platformy Azure | Microsoft Docs
description: Dane dzienników i metryk utworzone w obrębie platformy Azure można zarchiwizować na koncie magazynu.
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: b44bbd9cb2f54107d2593b1ab7f07f07fcc41e57
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="archive-azure-monitoring-data"></a>Archiwizowanie danych monitorowania platformy Azure

Dane dzienników i metryk, tworzone w kilku warstwach środowiska platformy Azure, można zarchiwizować na koncie usługi Azure Storage. Pozwala to zachować historię danych monitorowania w niedrogim magazynie, który nie zapewnia możliwości wyszukiwania, po upływie okresu przechowywania tych danych w usłudze Log Analytics lub Azure Monitor. W tym samouczku przedstawiono kroki konfigurowania środowiska platformy Azure pod kątem archiwizowania danych na koncie magazynu.

> [!div class="checklist"]
> * Tworzenie konta magazynu do przechowywania danych monitorowania
> * Przekierowywanie dzienników subskrypcji do konta magazynu
> * Przekierowywanie danych zasobów do konta magazynu
> * Przekierowywanie danych maszyny wirtualnej (systemu operacyjnego gościa) do konta magazynu
> * Wyświetlanie danych monitorowania na koncie magazynu
> * Czyszczenie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Najpierw musisz skonfigurować konto magazynu, na którym będą archiwizowane dane monitorowania. Aby to zrobić, [wykonaj te kroki](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Przekierowywanie dzienników subskrypcji do konta magazynu

Możesz teraz rozpocząć konfigurowanie środowiska platformy Azure pod kątem przekierowywania danych monitorowania do konta magazynu. Najpierw trzeba skonfigurować przekierowywanie danych poziomu subskrypcji (znajdujących się w dzienniku aktywności platformy Azure) do konta magazynu. [**Dziennik aktywności platformy Azure**](monitoring-overview-activity-logs.md) zawiera historię zdarzeń na poziomie subskrypcji na platformie Azure. Możesz je przeglądać w witrynie Azure Portal, aby sprawdzić, *kto* i *kiedy* utworzył, zaktualizował lub usunął *jakie* zasoby.

1. Kliknij przycisk **Monitoruj** na liście w okienku nawigacji po lewej stronie, a następnie wybierz pozycję **Dziennik aktywności**.

   ![Sekcja dziennika aktywności](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. W wyświetlonej sekcji dziennika aktywności kliknij przycisk **Eksportuj**.

3. W sekcji **Eksportuj dziennik aktywności** zaznacz pole **Eksportuj na konto magazynu** i kliknij pozycję **Wybierz konto magazynu**.

   ![Eksport dziennika aktywności](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. W wyświetlonej sekcji z listy rozwijanej **Konto magazynu** wybierz nazwę konta magazynu utworzonego w kroku **Tworzenie konta magazynu**, a następnie kliknij przycisk **OK**.

   ![Wybieranie konta magazynu](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Ustaw suwak **Okres przechowywania (w dniach)** na wartość 30. Suwak umożliwia ustawienie liczby dni przechowywania danych monitorowania na koncie magazynu. Starsze dane są automatycznie usuwane przez usługę Azure Monitor. Wpisanie wartości zero powoduje, że dane są przechowywane przez nieograniczony czas.

6. Kliknij przycisk **Zapisz** i zamknij tę sekcję.

Dane monitorowania z subskrypcji będą teraz przepływać do konta magazynu.

## <a name="route-resource-data-to-the-storage-account"></a>Przekierowywanie danych zasobów do konta magazynu

Teraz ustawimy przekierowanie danych poziomu zasobów (metryk zasobów i dzienników diagnostycznych) do konta magazynu, konfigurując **ustawienia diagnostyczne zasobów**.

1. Kliknij przycisk **Monitoruj** na liście w okienku nawigacji po lewej stronie, a następnie wybierz pozycję **Ustawienia diagnostyczne**. Zostanie wyświetlona lista wszystkich zasobów w subskrypcji, które generują dane monitorowania za pośrednictwem usługi Azure Monitor. Jeśli nie widzisz żadnych zasobów na tej liście, możesz najpierw [utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Dzięki temu uzyskasz zasób, dla którego możesz skonfigurować ustawienie diagnostyczne.

2. Kliknij zasób na liście, a następnie kliknij pozycję **Włącz diagnostykę**.

   ![Włączanie diagnostyki](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Jeśli już skonfigurowano ustawienie, widać bieżące ustawienia i przycisk **Dodaj ustawienie diagnostyczne**. Kliknij go.

   Ustawienie diagnostyczne zasobu definiuje, *jakie* dane monitorowania powinny być przekierowywane z określonego zasobu i *gdzie* powinny one trafiać.

3. W wyświetlonej sekcji podaj **nazwę** ustawienia i zaznacz pole wyboru **Zarchiwizuj na koncie magazynu**.

   ![Sekcja ustawień diagnostycznych](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Kliknij przycisk **Konfiguruj** w obszarze **Zarchiwizuj na koncie magazynu** i wybierz konto magazynu utworzone w poprzedniej sekcji. Kliknij przycisk **OK**.

   ![Ustawienia diagnostyczne — konto magazynu](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Zaznacz wszystkie pola w obszarach **Dziennik** i **Metryka**. W zależności od typu zasobu może być widoczna tylko jedna z tych opcji. Za pomocą tych pól wyboru można wybrać kategorie danych dzienników i metryk, dostępne dla tego typu zasobu, które mają być wysyłane do wybranego miejsca docelowego — w tym przypadku konta magazynu.

   ![Kategorie ustawień diagnostycznych](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)

6. Ustaw suwak **Okres przechowywania (w dniach)** na wartość 30. Suwak umożliwia ustawienie liczby dni przechowywania danych monitorowania na koncie magazynu. Starsze dane są automatycznie usuwane przez usługę Azure Monitor. Wpisanie wartości zero powoduje, że dane są przechowywane przez nieograniczony czas.

7. Kliknij pozycję **Zapisz**.

Dane monitorowania z zasobu będą teraz przepływać do konta magazynu.

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Przekierowywanie danych maszyny wirtualnej (systemu operacyjnego gościa) do konta magazynu

1. Jeśli nie masz jeszcze maszyny wirtualnej w ramach subskrypcji, [utwórz maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).

2. Na liście w okienku nawigacji po lewej stronie kliknij pozycję **Maszyny wirtualne**.

3. Na liście maszyn wirtualnych kliknij utworzoną maszynę wirtualną.

4. W wyświetlonej sekcji kliknij pozycję **Ustawienia diagnostyczne** w okienku nawigacji po lewej stronie. W tej sekcji można skonfigurować gotowe do użycia rozszerzenie monitorowania usługi Azure Monitor na maszynie wirtualnej i przekierować dane generowane w systemie Windows lub Linux do konta magazynu.

   ![Przechodzenie do ustawień diagnostycznych](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. W wyświetlonej sekcji kliknij pozycję **Włącz monitorowanie na poziomie gościa**.

   ![Włączanie ustawień diagnostycznych](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. Po poprawnym zapisaniu ustawienia diagnostycznego na karcie **Przegląd** pojawi się lista zbieranych danych wraz z informacjami o miejscach ich magazynowana. Kliknij sekcję **Liczniki wydajności**, aby zapoznać się z zestawem zbieranych liczników wydajności systemu Windows.

   ![Ustawienia liczników wydajności](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)

7. Kliknij kartę **Dzienniki** i zaznacz pola wyboru dzienników poziomu **informacyjnego** w obszarach dzienników aplikacji i dzienników systemowych.

   ![Ustawienia dzienników](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Kliknij kartę **Agent**, a następnie w obszarze **Konto magazynu** kliknij nazwę wyświetlanego konta magazynu.

   ![Aktualizowanie konta magazynu](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. W wyświetlonej sekcji wybierz konto magazynu utworzone w kroku **Tworzenie konta magazynu**.

10. Kliknij pozycję **Zapisz**.

Dane monitorowania z maszyn wirtualnych będą teraz przepływać do konta magazynu.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Wyświetlanie danych monitorowania na koncie magazynu

Jeśli wykonano powyższe kroki, dane zaczęły już przepływać do konta magazynu.

1. W przypadku niektórych typów danych, na przykład dziennika aktywności, muszą być wykonywane jakieś działania, które generują zdarzenie na koncie magazynu. Aby wygenerować działania umieszczane w dzienniku aktywności, wykonaj [te instrukcje](./monitor-quick-audit-notify-action-in-subscription.md). Może upłynąć nawet pięć minut, zanim zdarzenie pojawi się na koncie magazynu.

2. W portalu przejdź do sekcji **Konta magazynu** widocznej na pasku nawigacyjnym po lewej stronie.

3. Znajdź konto magazynu utworzone w poprzedniej sekcji i kliknij je.

4. Kliknij pozycję**Obiekty blob**, kliknij kontener oznaczony etykietą **insights-operational-logs**, a następnie kliknij kontener oznaczony etykietą **name=default**. Jest to kontener zawierający dziennik aktywności. Dane monitorowania są grupowane w kontenerach według identyfikatora zasobu (tylko identyfikatora subskrypcji dla dziennika aktywności), a następnie według daty i godziny. Pełny format tych obiektów blob jest następujący:

   insights-operational-logs/name=default/id_zasobu=/SUBSCRIPTIONS/{identyfikator subskrypcji}/y={czterocyfrowy rok}/m={dwucyfrowy miesiąc}/d={dwucyfrowy dzień}/h={dwucyfrowa pełna godzina w formacie 24-godzinnym}/m=00/PT1H.json

5. Przejdź do pliku PT1H.json, klikając identyfikator zasobu, datę i godzinę w obrębie kontenerów. Kliknij plik PT1H.json, a następnie kliknij przycisk **Pobierz**. Każdy obiekt blob PT1H.json zawiera obiekt blob JSON ze zdarzeniami, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość odpowiadająca minutom(m = 00) zawsze wynosi 00, ponieważ zdarzenia dziennika są dzielone na poszczególne obiekty blob w odstępach jednogodzinnych.

   Możesz teraz wyświetlić zdarzenie JSON przechowywane na koncie magazynu. W przypadku dzienników diagnostycznych zasobów format obiektów blob jest następujący:

   insights-logs-{nazwa kategorii dziennika}/id_zasobu=/{identyfikator zasobu}/y={czterocyfrowy rok}/m={dwucyfrowy miesiąc}/d={dwucyfrowy dzień}/h={dwucyfrowa pełna godzina w formacie 24-godzinnym}/m=00/PT1H.json

6. Dane monitorowania systemu operacyjnego gościa są przechowywane w tabelach. Wróć do strony głównej konta magazynu, a następnie kliknij przycisk **Tabele**. Dostępne tabele zawierają dane metryk, liczników wydajności i dzienników zdarzeń.

Udało nam się skonfigurować archiwizowanie danych monitorowania na koncie magazynu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

1. Wróć do sekcji **Eksportuj dziennik aktywności** opisanej w kroku **Przekierowywanie dzienników subskrypcji do konta magazynu** i kliknij pozycję **Resetuj**.

2. Przejdź do sekcji **Ustawienia diagnostyczne**, kliknij zasób, dla którego utworzono ustawienie diagnostyczne w kroku **Przekierowywanie danych zasobów do konta magazynu**, znajdź utworzone ustawienie, a następnie kliknij kolejno przyciski **Edytuj ustawienie** i **Usuń**.

3. Przejdź do sekcji **Ustawienia diagnostyczne** na maszynie wirtualnej skonfigurowanej w kroku **Przekierowywanie danych maszyny wirtualnej (systemu operacyjnego gościa) do konta magazynu**, a następnie na karcie **Agent** kliknij pozycję **Usuń** (pod sekcją **Usuń agenta funkcji Diagnostyka Azure**).

4. Przejdź do konta magazynu utworzonego w kroku **Tworzenie konta magazynu** i kliknij pozycję **Usuń konto magazynu**. Wpisz nazwę konta magazynu, a następnie kliknij pozycję **Usuń**.

5. Jeśli w poprzednich krokach utworzono maszynę wirtualną lub aplikację logiki, również je usuń.

## <a name="next-steps"></a>Następne kroki

W tym samouczku omówiono archiwizowanie danych monitorowania ze środowiska platformy Azure (subskrypcji, zasobów i systemu operacyjnego gościa) na koncie magazynu.


> [!div class="checklist"]
> * Tworzenie konta magazynu do przechowywania danych monitorowania
> * Przekierowywanie dzienników subskrypcji do konta magazynu
> * Przekierowywanie danych zasobów do konta magazynu
> * Przekierowywanie danych maszyny wirtualnej (systemu operacyjnego gościa) do konta magazynu
> * Wyświetlanie danych monitorowania na koncie magazynu
> * Czyszczenie zasobów

Aby uzyskać szczegółowe informacje i wykorzystać swoje dane w większym stopniu, wyślij je również do usługi Log Analytics.

> [!div class="nextstepaction"]
> [Wprowadzenie do usługi Log Analytics](../log-analytics/log-analytics-get-started.md)
