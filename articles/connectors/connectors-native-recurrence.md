---
title: Planowanie cyklicznych zadań z wyzwalaczem cyklu — Azure Logic Apps
description: Zaplanuj i uruchom cykliczne zadania automatyczne i przepływy pracy z wyzwalaczem cyklu w Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0bd7262daf23f205552e46bc3ca2802cf35f85db
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914449"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Twórz, Zaplanuj i uruchamiaj zadania cykliczne i przepływy pracy z wyzwalaczem cyklu w Azure Logic Apps

Aby regularnie uruchamiać zadania, procesy lub zadania według określonego harmonogramu, można uruchomić przepływ pracy aplikacji logiki przy użyciu wbudowanego wyzwalacza **harmonogramu cyklu** . Można ustawić datę i godzinę oraz strefę czasową do uruchomienia przepływu pracy i cykl dla powtarzania tego przepływu pracy. Jeśli z jakiegoś powodu nie zostały pominięte cykle, ten wyzwalacz powtarza się cyklicznie w następnym zaplanowanym interwale. Aby uzyskać więcej informacji o wbudowanych wyzwalaczach i akcjach harmonogramu, zobacz temat [Planowanie i uruchamianie cyklicznych zautomatyzowanych, zadań i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Poniżej przedstawiono niektóre wzorce obsługiwane przez ten wyzwalacz oraz bardziej zaawansowane cykle i skomplikowane harmonogramy:

* Uruchom natychmiast i powtórz co *n* liczba sekund, minuty, godziny, dni, tygodnie lub miesiące.

* Zacznij od określonej daty i godziny, a następnie uruchamiaj i Powtarzaj co *n* liczba sekund, minuty, godziny, dni, tygodnie lub miesiące.

* Uruchamiaj i Powtarzaj co najmniej jeden raz dziennie, na przykład o 8:00 AM i 5:00 PM.

* Uruchamiaj i Powtarzaj co tydzień, ale tylko dla określonych dni, takich jak sobota i niedziela.

* Uruchamiaj i Powtarzaj co tydzień, ale tylko dla określonych dni i godzin, takich jak od poniedziałku do piątku o godzinie 8:00 i 5:00 PM.

W przypadku różnic między tym wyzwalaczem a oknem przesuwania lub aby uzyskać więcej informacji na temat planowania cyklicznych przepływów pracy, zobacz [Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Jeśli chcesz wyzwolić aplikację logiki i uruchamiać ją tylko raz w przyszłości, zobacz [Uruchamianie zadań tylko jeden raz](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [zarejestrować się w celu uzyskania bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [aplikacji logiki](../logic-apps/logic-apps-overview.md). Jeśli dopiero zaczynasz tworzyć aplikacje logiki, Dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Dodaj wyzwalacz cykliczny

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Gdy zostanie wyświetlony projektant aplikacji logiki, w polu wyszukiwania wprowadź ciąg "cykl" jako filtr. Z listy Wyzwalacze wybierz ten wyzwalacz jako pierwszy krok w przepływie pracy aplikacji logiki: **Cykl**

   ![Wybierz wyzwalacz "cykl"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw te właściwości, aby uruchamiać przepływy pracy co tydzień.

   ![Ustawianie interwału i częstotliwości](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Właściwość | Wymagane | Nazwa JSON | Type | Opis |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Tak | przedział | Integer | Dodatnia liczba całkowita, która opisuje, jak często przebiega przepływ pracy na podstawie częstotliwości. Poniżej znajdują się minimalne i maksymalne interwały: <p>Bieżącym 1-16 miesięcy </br>Dzień 1-500 dni </br>Wydajność 1 – 12 000 godzin </br>Minutę 1 – 72000 minut </br>Drugi 1 – 9999999 s<p>Jeśli na przykład interwał wynosi 6, a częstotliwość to "miesiąc", cykl jest co 6 miesięcy. |
   | **Częstotliwość** | Tak | frequency | String | Jednostka czasu dla cyklu: **Sekunda**, **minuta**, **godzina**, **dzień**, **tydzień**lub **miesiąc** |
   ||||||

   Aby uzyskać więcej opcji planowania, Otwórz listę **Dodaj nowy parametr** . 
   Wszystkie wybrane opcje są wyświetlane w wyzwalaczu po zaznaczeniu.

   ![Zaawansowane opcje planowania](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Właściwość | Wymagane | Nazwa JSON | Type | Opis |
   |----------|----------|-----------|------|-------------|
   | **Strefa czasowa** | Nie | timeZone | String | Ma zastosowanie tylko w przypadku określenia czasu rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, która ma zostać zastosowana. |
   | **Godzina rozpoczęcia** | Nie | startTime | String | Podaj datę i godzinę rozpoczęcia w tym formacie: <p>RRRR-MM-DDTgg: mm: SS w przypadku wybrania strefy czasowej <p>—lub— <p>RRRR-MM-DDTgg: mm: SSS, jeśli nie wybierzesz strefy czasowej <p>Na przykład jeśli chcesz, aby 18 września 2017 o 2:00 PM, określ wartość "2017-09-18T14:00:00" i wybierz strefę czasową, na przykład Pacyfik (czas standardowy). Lub określ wartość "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ta godzina rozpoczęcia ma maksymalnie 49 lat w przyszłości i musi być zgodna ze [specyfikacją ISO 8601 Data Time](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie czasu UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie wybierzesz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. Ten "Z" odnosi się do odpowiadającego [czasu morskich](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, a w przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany dłużej niż godzina rozpoczęcia. [*Jakie są sposoby używania daty i godziny rozpoczęcia?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **W tych dniach** | Nie | weekDays | Ciąg lub tablica ciągów | W przypadku wybrania opcji "tydzień" można wybrać co najmniej jeden dzień, w którym ma zostać uruchomiony przepływ pracy: **Poniedziałek**, **wtorek**, **środa**, **czwartek**, **piątek**, **Sobota**i **Niedziela** |
   | **W tych godzinach** | Nie | godz. | Tablica liczb całkowitych lub liczb całkowitych | W przypadku wybrania opcji "dzień" lub "tydzień" można wybrać co najmniej jedną liczbę całkowitą z przedziału od 0 do 23 jako godzinę, o której ma być uruchamiany przepływ pracy. <p><p>Na przykład, jeśli określisz wartość "10", "12" i "14", otrzymujesz 10 AM, 12 PM i 2 PM w godzinach dnia, ale minuty dnia są obliczane na podstawie momentu rozpoczęcia cyklu. Aby ustawić minuty dnia, określ wartość dla właściwości **at** . |
   | **W tych minutach** | Nie | minutes | Tablica liczb całkowitych lub liczb całkowitych | W przypadku wybrania opcji "dzień" lub "tydzień" można wybrać co najmniej jedną liczbę całkowitą z zakresu od 0 do 59 jako minuty godziny, Kiedy chcesz uruchomić przepływ pracy. <p>Na przykład można określić wartość "30" jako znak minuty i użyć poprzedniego przykładu dla godzin dnia, otrzymują 10:30 AM, 12:30 PM i 2:30 PM. |
   |||||

   Załóżmy na przykład, że dzisiaj jest poniedziałek, 4 września 2017. Następujący wyzwalacz cykliczny nie jest uruchamiany *wcześniej* niż data i godzina rozpoczęcia, czyli poniedziałek, 18 września, 2017 w dniu 8:00 am. Harmonogram cyklu jest jednak ustawiany dla 10:30 AM, 12:30 PM i 2:30 PM tylko w poniedziałek. Dlatego po pierwszym uruchomieniu wyzwalacza i utworzeniu wystąpienia przepływu pracy aplikacji logiki jest 10:30 AM. Aby dowiedzieć się więcej na temat czasu rozpoczęcia pracy, zobacz te [przykłady czasu rozpoczęcia](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Przyszłe uruchomienia są wykonywane o godzinie 12:30 PM i 2:30 PM w tym samym dniu. Każdy cykl tworzy własne wystąpienie przepływu pracy. Po wykonaniu tej czynności cały harmonogram zostanie powtórzony w ciągu następnych poniedziałku. [*Jakie są inne przykładowe wystąpienia?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Przykład planowania zaawansowanego](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Wyzwalacz pokazuje podgląd określonego cyklu tylko w przypadku wybrania częstotliwości "dzień" lub "tydzień".

1. Teraz Skompiluj pozostałe przepływy pracy z innymi akcjami. Aby uzyskać więcej akcji, które można dodać, zobacz [Łączniki dla Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definicja przepływu pracy — cykl

W źródłowej definicji przepływu pracy aplikacji logiki, która używa formatu JSON, można wyświetlić [definicję wyzwalacza cyklu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) z wybranymi opcjami. Aby wyświetlić tę definicję, na pasku narzędzi projektanta wybierz **Widok kod**. Aby powrócić do projektanta, wybierz na pasku narzędzi projektanta pozycję **Projektant**.

Ten przykład pokazuje, jak definicja wyzwalacza cyklicznego może wyglądać w źródłowej definicji przepływu pracy:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Wstrzymywanie przepływów pracy z opóźnionymi akcjami](../connectors/connectors-native-delay.md)
* [Łączniki dla Logic Apps](../connectors/apis-list.md)
