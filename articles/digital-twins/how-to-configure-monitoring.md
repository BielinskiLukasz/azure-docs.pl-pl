---
title: Jak skonfigurować monitorowanie w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować monitorowanie w reprezentacji urządzeń cyfrowych platformy Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: adgera
ms.openlocfilehash: 1c8f1931a29ae9769f7d8ad57a184e3240105a1a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945825"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Jak skonfigurować monitorowanie w reprezentacji urządzeń cyfrowych platformy Azure

Twins cyfrowych platformy Azure obsługuje niezawodne rejestrowanie, monitorowanie i analizy. Rozwiązania deweloperzy mogą używać usługi Azure Log Analytics, dzienniki diagnostyczne, rejestrowanie aktywności i innych usług, do obsługi złożonych potrzeb monitorowania aplikacji IoT. Opcje rejestrowania można łączyć z zapytania lub wyświetla rekordy w kilku usługach oraz zapewniają szczegółowe rejestrowanie pokrycie dla wielu usług.

Ten artykuł zawiera podsumowanie, rejestrowania i monitorowania opcje i sposób łączenia ich w sposoby określonych Twins cyfrowych platformy Azure.

## <a name="review-activity-logs"></a>Przejrzyj dzienniki aktywności

Azure [dzienników aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają szybki wgląd w poziom subskrypcji zdarzeń i operacji historii dla każdego wystąpienia usług platformy Azure.

Zdarzenia na poziomie subskrypcji, obejmują:

* Tworzenie zasobów
* Usuwanie zasobu
* Tworzenie kluczy tajnych aplikacji
* Integrowanie z innymi usługami

Rejestrowanie aktywności bliźniaki cyfrowych platformy Azure jest domyślnie włączona i można znaleźć w witrynie Azure portal przez:

1. Wybieranie wystąpienia Twins cyfrowych platformy Azure.
1. Wybieranie **dziennika aktywności** Aby wyświetlić ekran w panelu:

    ![Dziennik aktywności][1]

Aby uzyskać Zaawansowane rejestrowanie aktywności:

1. Wybierz **dzienniki** opcję, aby wyświetlić **przegląd działań usługi Log Analytics**:

    ![Wybór][2]

1. **Przegląd działań usługi Log Analytics** podsumowuje dane dzienników aktywności podstawowe:

    ![Przegląd działań usługi log analytics][3]

>[!TIP]
>Użyj **dzienników aktywności** dla szybki wgląd w zdarzenia na poziomie subskrypcji.

## <a name="enable-customer-diagnostic-logs"></a>Włączanie dzienników diagnostycznych klienta

Azure [ustawień diagnostycznych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) można ustawić dla każdego wystąpienia platformy Azure uzupełnić rejestrowania aktywności. Gdy dzienniki aktywności odnoszą się do poziomu subskrypcji zdarzeń, rejestrowanie diagnostyczne zapewnia wgląd w historię operacyjnej samych zasobów.

Przykłady rejestrowania diagnostycznego:

* Czas wykonywania dla funkcji zdefiniowanej przez użytkownika
* Kod stanu odpowiedzi pomyślne żądania interfejsu API
* Pobieranie klucza aplikacji z magazynu

Aby włączyć dzienniki diagnostyczne na potrzeby wystąpienie:

1. Przenieś zasób w witrynie Azure portal.
1. Kliknij przycisk **ustawień diagnostycznych**:

    ![Ustawienia diagnostyczne jeden][4]

1. Kliknij przycisk **Włącz diagnostykę** do zbierania danych (jeśli wcześniej nie jest włączona).
1. Wprowadź żądane pola i wybierz, jak i, w którym zostaną zapisane dane:

    ![Ustawienia diagnostyczne dwóch][5]

    Dzienniki diagnostyczne często są zapisywane przy użyciu [usługi Azure File Storage](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide) i udostępniane [usługi Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal). Można wybrać obu opcji.

>[!TIP]
>Użyj **dzienniki diagnostyczne** uzyskać wgląd w operacje zasobów.

## <a name="azure-monitor-and-log-analytics"></a>Monitorowanie i rejestrowanie funkcja analizy usługi Azure

Aplikacje IoT łączenia różnych zasobów, urządzenia, lokalizacji i dane w jedną. Szczegółowe rejestrowanie udostępnia szczegółowe informacje na temat każdej konkretne, usługi lub składnik architektury cała aplikacja, ale Przegląd ujednoliconego często jest wymagany do obsługi i debugowania.

Usługa Azure Monitor obejmuje zaawansowane usługi Log Analytics umożliwia rejestrowanie źródeł, aby wyświetlać i analizować w jednej lokalizacji. Usługa Azure Monitor, dlatego jest bardzo przydatna w celu analizowania dzienników w zaawansowanych aplikacji IoT.

Przykłady użycia:

* Zapytania wielu historie dziennik diagnostyczny
* Wyświetlanie dzienników dla kilku funkcji zdefiniowanych przez użytkownika
* Wyświetlanie dzienników dla dwóch lub więcej usług w określonym przedziale czasu

Pełny dziennik zapytań jest oferowana w ramach [usługi Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Aby skonfigurować te zaawansowane funkcje:

1. Wyszukaj **usługi Log Analytics** w witrynie Azure portal.
1. Zostanie wyświetlony dostępnych **usługi Log Analytics** wystąpień. Wybierz jedną, a następnie wybierz pozycję **dzienniki** zapytania:

    ![Log Analytics][6]

1. Jeśli nie masz jeszcze **usługi Log Analytics** wystąpienia, możesz utworzyć obszar roboczy, klikając **Dodaj** przycisku:

    ![Tworzenie pakietu OMS][7]

Raz swoje **usługi Log Analytics** aprowizowano wystąpienie, możesz użyć zaawansowanych zapytań, aby odnaleźć wpisów w dziennikach wielokrotności lub wyszukiwanie przy użyciu kryteriów określonych za pomocą **Zarządzanie dziennikami**:

   ![Zarządzanie dziennikami][8]

Aby uzyskać więcej informacji o operacjach zaawansowanych zapytań, zobacz [wprowadzenie do zapytań](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

> [!NOTE]
> Może wystąpić opóźnienie 5 minut, podczas wysyłania zdarzeń do **usługi Log Analytics** po raz pierwszy.

Usługa Azure Log Analytics udostępnia również zaawansowane błąd i usług powiadomień o alertach, które można wyświetlić, klikając **diagnozowanie i rozwiązywanie problemów**:

   ![Alert i błąd powiadomienia][9]

>[!TIP]
>Użyj **usługi Log Analytics** do historii dziennika zapytań dla wielu aplikacji funkcji, subskrypcji lub usług.

## <a name="other-options"></a>Inne opcje

Twins cyfrowych platformy Azure obsługuje również specyficzne dla aplikacji, rejestrowanie i przeprowadzanie inspekcji bezpieczeństwa. Aby uzyskać szczegółowe omówienie do Twojego wystąpienia usługi Azure cyfrowego bliźniaczych reprezentacji wszystkie opcje rejestrowania platformy Azure, zobacz [inspekcji usługi Azure log](https://docs.microsoft.com/azure/security/azure-log-audit) artykułu.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usłudze Azure [dzienników aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

Dowiedz się więcej na konfiguracji ustawień diagnostyki platformy Azure, czytając [Przegląd dzienników diagnostycznych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

Przeczytaj więcej na temat [usługi Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal).

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png