---
title: "Zbieranie dzienników aktywności platformy Azure w usłudze Log Analytics w wielu subskrypcjach | Microsoft Docs"
description: "Użyj usługi Centra zdarzeń lub Logic Apps, aby zbierać dane z dziennika aktywności platformy Azure i wysyłać je do obszaru roboczego usługi Azure Log Analytics w innej dzierżawie."
services: log-analytics, logic-apps, event-hubs
documentationcenter: 
author: richrundmsft
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: richrund; bwren
ms.openlocfilehash: 89c62563b9772fa07d63a24b4aa20857b0143f85
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
---
# <a name="collect-azure-activity-logs-into-log-analytics-across-subscriptions"></a>Zbieranie dzienników aktywności platformy Azure w usłudze Log Analytics w wielu subskrypcjach

W tym artykule omówiono metodę zbierania dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics przy użyciu łącznika Azure Log Analytics Data Collector dla usługi Logic Apps. Użyj procesu opisanego w tym artykule, jeśli chcesz wysłać dzienniki do obszaru roboczego w innej usłudze Azure Active Directory. Jeśli na przykład jesteś dostawcą zarządzanej usługi, możesz zebrać dzienniki aktywności z subskrypcji klienta i przechować je w obszarze roboczym usługi Log Analytics we własnej subskrypcji.

Jeśli obszar roboczy usługi Log Analytics znajduje się w tej samej subskrypcji platformy Azure lub w innej subskrypcji, ale w tej samej usłudze Azure Active Directory, użyj kroków w [rozwiązaniu dziennika aktywności platformy Azure](../log-analytics/log-analytics-activity.md), aby zebrać dzienniki aktywności platformy Azure.

## <a name="overview"></a>Omówienie

Strategia używana w tym scenariuszu jest oparta na wysyłaniu zdarzeń przez dziennik aktywności platformy Azure do [centrum zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md), skąd [aplikacja logiki](../logic-apps/logic-apps-overview.md) wysyła je do obszaru roboczego usługi Log Analytics. 

![obraz przepływu danych z dziennika aktywności do usługi Log Analytics](media/log-analytics-activity-logs-subscriptions/data-flow-overview.png)

Zalety tego podejścia obejmują:
- Małe opóźnienia, ponieważ dziennik aktywności jest przesyłany strumieniowo do centrum zdarzeń.  Następnie zostaje wyzwolona aplikacja logiki, która przesyła dane do usługi Log Analytics. 
- Jest wymagana minimalna ilość kodu, a ponadto nie ma infrastruktury serwera do wdrożenia.

Ten artykuł opisuje:
1. Tworzenie centrum zdarzeń. 
2. Eksportowanie dzienników aktywności do centrum zdarzeń przy użyciu profilu eksportowania dzienników aktywności platformy Azure.
3. Tworzenie aplikacji logiki na potrzeby odczytywania zdarzeń z centrum zdarzeń i wysyłania ich do usługi Log Analytics.

## <a name="requirements"></a>Wymagania
Poniżej przedstawiono wymagania dla zasobów platformy Azure używanych w tym scenariuszu.

- Przestrzeń nazw centrum zdarzeń nie musi znajdować się w tej samej subskrypcji co emitowane dzienniki subskrypcji. Użytkownik, który konfiguruje ustawienie, musi mieć odpowiednie uprawnienia do obu subskrypcji. Jeśli masz wiele subskrypcji w tej samej usłudze Azure Active Directory, możesz wysłać dzienniki aktywności dla wszystkich subskrypcji do jednego centrum zdarzeń.
- Aplikacja logiki może znajdować się w innej subskrypcji niż centrum zdarzeń. Nie musi być również w tej samej subskrypcji co usługa Azure Active Directory. Aplikacja logiki odczytuje dane z centrum zdarzeń przy użyciu współdzielonego klucza dostępu centrum zdarzeń.
- Obszar roboczy usługi Log Analytics może znajdować się w innej subskrypcji, tak jak usługa Azure Active Directory i aplikacja logiki, ale dla uproszczenia działań zalecamy umieszczenie ich w tej samej subskrypcji. Aplikacja logiki wysyła dane do usługi Log Analytics przy użyciu identyfikatora i klucza obszaru roboczego usługi Log Analytics.



## <a name="step-1---create-an-event-hub"></a>Krok 1. Tworzenie centrum zdarzeń

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../event-hubs/event-hubs-create.md) to create your event hub. -->

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Usługa Centra zdarzeń**.

   ![Nowe centrum zdarzeń Marketplace](media/log-analytics-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. W obszarze **Utwórz przestrzeń nazw** wprowadź nową przestrzeń nazw lub wybierz istniejącą. System od razu sprawdza, czy nazwa jest dostępna.

   ![obraz okna dialogowego tworzenia centrum zdarzeń](media/log-analytics-activity-logs-subscriptions/create-event-hub1.png)

4. Wybierz warstwę cenową (Podstawowa lub Standardowa), subskrypcję platformy Azure, grupę zasobów oraz lokalizację nowego zasobu.  Kliknij pozycję **Utwórz**, aby utworzyć przestrzeń nazw. Może być konieczne odczekanie kilku minut, aby system przeprowadził pełną aprowizację zasobów.
6. Kliknij nowo utworzoną przestrzeń nazw na liście.
7. Wybierz opcję **Zasady dostępu współdzielonego**, a następnie kliknij pozycję **RootManageSharedAccessKey**.

   ![obraz zasad dostępu współdzielonego centrum zdarzeń](media/log-analytics-activity-logs-subscriptions/create-event-hub7.png)
   
8. Kliknij przycisk kopiowania, aby skopiować parametry połączenia **RootManageSharedAccessKey** do schowka. 

   ![obraz wspólnego klucza dostępu centrum zdarzeń](media/log-analytics-activity-logs-subscriptions/create-event-hub8.png)

9. W lokalizacji tymczasowej, np. w Notatniku, przechowaj kopię nazwy centrum zdarzeń oraz podstawowe lub pomocnicze parametry połączenia centrum zdarzeń. Aplikacja logiki wymaga tych wartości.  W przypadku parametrów połączenia centrum zdarzeń możesz użyć parametrów połączenia **RootManageSharedAccessKey** lub utworzyć oddzielne parametry.  Używane parametry połączenia muszą rozpoczynać się od `Endpoint=sb://` i muszą być przeznaczone dla zasad zawierających zasady dostępu **Zarządzanie**.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Krok 2. Eksportowanie dzienników aktywności do centrum zdarzeń

Aby włączyć strumieniowe przesyłanie dzienników aktywności, możesz wybrać przestrzeń nazw centrum zdarzeń oraz zasady dostępu współdzielonego dla tej przestrzeni nazw. Centrum zdarzeń jest tworzone w tej przestrzeni nazw, gdy wystąpi pierwsze nowe zdarzenie dziennika zdarzeń. 

Możesz użyć przestrzeni nazw centrum zdarzeń, która nie znajduje się w tej samej subskrypcji, co dzienniki emitowane subskrypcji, jednak subskrypcje muszą znajdować się w tej samej usłudze Azure Active Directory. Użytkownik, który konfiguruje ustawienie, musi mieć odpowiednią rolę RBAC, aby uzyskać dostęp do obu subskrypcji. 

1. W witrynie Azure Portal wybierz opcję **Monitor** > **Dziennik aktywności**.
3. Kliknij przycisk **Eksportuj** u góry strony.

   ![obraz monitora platformy Azure w nawigacji](media/log-analytics-activity-logs-subscriptions/activity-log-blade.png)

4. Wybierz opcję **Subskrypcja**, aby wyeksportować, a następnie wybierz opcję **Zaznacz wszystko** w menu rozwijanym **Regiony**, aby wybrać zdarzenia dla zasobów we wszystkich regionach. Kliknij pole wyboru **Eksportuj do centrum zdarzeń**.
7. Kliknij opcję **Przestrzeń nazw magistrali usług**, a następnie wybierz **Subskrypcję** z centrum zdarzeń, **przestrzeń nazw centrum zdarzeń** oraz **nazwę zasad centrum zdarzeń**.

    ![obraz eksportowania do strony centrum zdarzeń](media/log-analytics-activity-logs-subscriptions/export-activity-log2.png)

11. Kliknij przycisk **OK**, a następnie przycisk **Zapisz**, aby zapisać te ustawienia. Ustawienia są natychmiastowo stosowane do subskrypcji.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Krok 3. Tworzenie aplikacji logiki

Gdy dzienniki aktywności rozpoczną zapisywanie danych w centrum zdarzeń, należy utworzyć aplikację logiki, aby zbierać dzienniki z centrum zdarzeń i zapisywać je w usłudze Log Analytics.

Aplikacja logiki obejmuje następujące elementy:
- Wyzwalacz [łącznika centrum zdarzeń](https://docs.microsoft.com/connectors/eventhubs/) do odczytywania danych z centrum zdarzeń.
- [Akcja analizy danych JSON](../logic-apps/logic-apps-content-type.md) do wyodrębniania zdarzeń JSON.
- [Akcja redagowania](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) do konwertowania danych JSON na obiekty.
- [Łącznik wysyłania danych usługi Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) do wysyłania danych do usługi Log Analytics.

   ![obraz dodawania wyzwalacza centrum zdarzeń w usłudze Logic Apps](media/log-analytics-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Wymagania aplikacji logiki
Przed utworzeniem aplikacji logiki upewnij się, że masz następujące informacje z poprzednich kroków:
- Nazwa centrum zdarzeń
- Parametry połączenia centrum zdarzeń (podstawowe lub pomocnicze) dla przestrzeni nazw centrum zdarzeń.
- Identyfikator obszaru roboczego usługi Log Analytics
- Klucz wspólny usługi Log Analytics

Aby uzyskać nazwę centrum zdarzeń i parametry połączenia, wykonaj kroki opisane w [Sprawdzanie uprawnień przestrzeni nazw centrów zdarzeń oraz wyszukiwanie parametrów połączenia](../connectors/connectors-create-api-azure-event-hubs.md#check-event-hubs-namespace-permissions-and-find-the-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Tworzenie nowej pustej aplikacji logiki

1. W witrynie Azure Portal wybierz opcję **Utwórz zasób** > **Integracja dla przedsiębiorstw** > **Aplikacja logiki**.

    ![Nowa aplikacja logiki na platformie Marketplace](media/log-analytics-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Wprowadź ustawienia w poniższej tabeli.

    ![Tworzenie aplikacji logiki](media/log-analytics-activity-logs-subscriptions/create-logic-app.png)

   |Ustawienie | Opis  |
   |:---|:---|
   | Name (Nazwa)           | Unikatowa nazwa aplikacji logiki. |
   | Subskrypcja   | Wybierz subskrypcję platformy Azure, która będzie zawierać aplikację logiki. |
   | Grupa zasobów | Wybierz istniejącą grupę zasobów platformy Azure lub utwórz nową grupę dla aplikacji logiki. |
   | Lokalizacja       | Wybierz region centrum danych do wdrażania swojej aplikacji logiki. |
   | Log Analytics  | Wybierz, czy chcesz rejestrować stan każdego uruchomienia aplikacji logiki w usłudze Log Analytics.  |

    
3. Wybierz pozycję **Utwórz**. Gdy zostanie wyświetlone powiadomienie **Wdrażanie zakończyło się pomyślnie**, kliknij opcję **Przejdź do zasobu**, aby otworzyć aplikację logiki.

4. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**. 

Projektant aplikacji logiki wyświetla teraz dostępne łączniki i ich wyzwalacze, których używasz do uruchamiania przepływu pracy aplikacji logiki.

<!-- Learn [how to create a logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Dodawanie wyzwalacza centrum zdarzeń

1. W polu wyszukiwania projektanta aplikacji logiki wpisz *centra zdarzeń* jako filtr. Wybierz wyzwalacz **usługi Centra zdarzeń — gdy zdarzenia są dostępne w Centrum zdarzeń**.

   ![obraz dodawania wyzwalacza centrum zdarzeń w usłudze Logic Apps](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Jeśli zostanie wyświetlony monit o poświadczenia, połączenie z przestrzeni nazw usługi Centra zdarzeń. Wprowadź nazwę połączenia, a następnie skopiowane parametry połączenia.  Wybierz pozycję **Utwórz**.

   ![obraz dodawania połączenia centrum zdarzeń w usłudze Logic Apps](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Po utworzeniu połączenia edytuj ustawienia wyzwalacza. Zacznij od wybrania opcji **insights-operational-logs** z menu rozwijanego **nazwy centrum zdarzeń**.

   ![Okno dialogowe Jeśli zdarzenia są dostępne](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Rozwiń pozycję **Pokaż opcje zaawansowane** i zmień opcję **Typ zawartości** na *aplikacja/json*

   ![Okno dialogowe konfiguracji centrum zdarzeń](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Dodawanie akcji analizy danych JSON

Dane wyjściowe z centrum zdarzeń zawierają ładunek JSON z tablicą rekordów. Akcja [Przeanalizuj dane JSON](../logic-apps/logic-apps-content-type.md) jest używana do wyodrębniania samej tablicy rekordów do wysłania do usługi Log Analytics.

1. Kliknij opcję **Nowy krok** > **Dodaj akcję**
2. W polu wyszukiwania wpisz *przeanalizuj dane json* jako filtr. Wybierz akcję **Operacje danych — przeanalizuj dane JSON**.

   ![Dodawanie akcji analizy danych json w usłudze Logic Apps](media/log-analytics-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Kliknij pole **Zawartość**, a następnie wybierz opcję *Treść*.

4. Skopiuj i wklej następujący schemat w polu **Schemat**.  Ten schemat dopasowuje dane wyjściowe z akcji centrum zdarzeń.  

   ![Okno dialogowe Przeanalizuj dane JSON](media/log-analytics-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Możesz uzyskać przykładowy ładunek, klikając opcję **Uruchom** i sprawdzając **Nieprzetworzone dane wyjściowe** z centrum zdarzeń.  Następnie możesz użyć tych danych wyjściowych i opcji **Użyj przykładowego ładunku, aby wygenerować schemat** w aktywności **Przeanalizuj dane JSON**, aby wygenerować schemat.

### <a name="add-compose-action"></a>Dodawanie akcji redagowania
Akcja [Redaguj](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) pobiera dane wyjściowe JSON i tworzy obiekt, który może być użyty przez akcję usługi Log Analytics.

1. Kliknij opcję **Nowy krok** > **Dodaj akcję**
2. Wpisz *redaguj* jako filtr, a następnie wybierz akcję **Operacje danych — redaguj**.

    ![Dodawanie akcji redagowania](media/log-analytics-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Kliknij pole **Dane wejściowe** i wybierz opcję **Treść** w aktywności **Przeanalizuj dane JSON**.


### <a name="add-log-analytics-send-data-action"></a>Dodawanie akcji wysyłania danych usługi Log Analytics
Akcja [Moduł zbierający dane usługi Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) pobiera obiekt z akcji Redaguj i wysyła go do usługi Log Analytics.

1. Kliknij opcję **Nowy krok** > **Dodaj akcję**
2. Wpisz *log analytics* jako filtr, a następnie wybierz akcję **Moduł zbierający dane usługi Azure Log Analytics — wyślij dane**.

   ![Dodawanie akcji wysyłania danych usługi Log Analytics w usłudze Logic Apps](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Wprowadź nazwę połączenia i wklej **identyfikator obszaru roboczego** oraz **klucz obszaru roboczego** dla obszaru roboczego usługi Log Analytics.  Kliknij przycisk **Utwórz**.

   ![Dodawanie połączenia usługi Log Analytics w usłudze Logic Apps](media/log-analytics-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Po utworzeniu połączenia edytuj ustawienia w poniższej tabeli. 

    ![Konfigurowanie akcji wysyłania danych](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Ustawienie        | Wartość           | Opis  |
   |---------------|---------------------------|--------------|
   |Treść żądania JSON  | **Dane wyjściowe** z akcji **Redaguj** | Pobiera rekordy z treści akcji Redaguj. |
   | Nazwa dziennika niestandardowego | AzureActivity | Nazwij tabelę dziennika niestandardowego do utworzenia w usłudze Log Analytics na potrzeby przechowywania importowanych danych. |
   | Time-generated-field | time | Nie zaznaczaj pola JSON w pozycji **time** — zamiast tego wpisz słowo „time”. Jeśli wybierzesz pole JSON, projektant umieści akcję **Wyślij dane** w pętli *For Each*, czego nie chcemy. |




10. Kliknij przycisk **Zapisz**, aby zapisać zmiany wprowadzone w aplikacji logiki.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Krok 4. Testowanie i rozwiązywanie problemów z aplikacją logiki
Po ukończeniu przepływu pracy możesz przeprowadzić test w projektancie, aby sprawdzić, czy rozwiązanie działa bez błędów.

W projektancie aplikacji logiki kliknij przycisk **Uruchom**, aby przetestować aplikację logiki. Każdy krok w aplikacji logiki wyświetla ikonę stanu. Biały znacznik wyboru w zielonym kole informuje o powodzeniu.

   ![Testowanie aplikacji logiki](media/log-analytics-activity-logs-subscriptions/test-logic-app.png)

Aby wyświetlić szczegółowe informacje dotyczące każdego kroku, kliknij nazwę kroku, aby go rozwinąć. Kliknij opcje **Pokaż nieprzetworzone dane wejściowe** i **Pokaż nieprzetworzone dane wyjściowe**, aby zobaczyć więcej informacji o danych odbieranych i wysyłanych w każdym kroku.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Krok 5. Wyświetlenie dziennika aktywności platformy Azure w usłudze Log Analytics
Ostatnim krokiem jest sprawdzenie obszaru roboczego usługi Log Analytics, aby upewnić się, że dane są zbierane zgodnie z oczekiwaniami.

1. W witrynie Azure Portal wybierz opcję **Log Analytics**.
2. Wybierz obszar roboczy, a następnie kafelek **Przeszukiwanie dzienników**.
3. Na pasku wyszukiwania zapytania wpisz `AzureActivity_CL` i kliknij przycisk wyszukiwania. Jeśli dziennik niestandardowy nie został nazwany *AzureActivity*, wpisz wybraną nazwę i dołącz do niej `_CL`.

>[!NOTE]
> Kiedy nowy dziennik niestandardowy zostaje wysłany do usługi Log Analytics po raz pierwszy, możliwość jego wyszukania może być dostępna dopiero po godzinie.

>[!NOTE]
> Dzienniki aktywności są zapisywane w tabeli niestandardowej i nie są wyświetlane w [rozwiązaniu dziennika aktywności](./log-analytics-activity.md).


![Testowanie aplikacji logiki](media/log-analytics-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono aplikację logiki w celu odczytywania dzienników aktywności platformy Azure z centrum zdarzeń oraz wysyłania ich do usługi Log Analytics w celu przeprowadzenia analizy. Aby dowiedzieć się więcej o wizualizacji danych w usłudze Log Analytics, w tym o tworzeniu pulpitów nawigacyjnych, sprawdź samouczek dotyczący wizualizacji danych.

> [!div class="nextstepaction"]
> [Samouczek dotyczący wizualizacji danych przeszukiwania dzienników](./log-analytics-tutorial-dashboards.md)
