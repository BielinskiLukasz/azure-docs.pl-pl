---
title: 'Samouczek: Zarządzanie obliczeniami przy użyciu Azure Functions'
description: Jak używać usługi Azure Functions do zarządzania obliczeniami puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9d680283250cc323c833f388f6b20d7fe6fa132d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211055"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Używanie Azure Functions do zarządzania zasobami obliczeniowymi w puli SQL usługi Azure Synapse Analytics

Ten samouczek używa Azure Functions do zarządzania zasobami obliczeniowymi dla puli SQL w usłudze Azure Synapse Analytics.

Aby można było korzystać z usługi Azure aplikacja funkcji z pulą SQL, należy utworzyć [konto głównej usługi](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) z dostępem współautora w ramach tej samej subskrypcji, w której znajduje się wystąpienie puli SQL.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Wdrażanie skalowania opartego na czasomierzu przy użyciu szablonu Azure Resource Manager

Aby wdrożyć szablon, potrzebne są następujące informacje:

- Nazwa grupy zasobów, w której znajduje się wystąpienie puli SQL
- Nazwa serwera, na którym znajduje się wystąpienie puli SQL
- Nazwa wystąpienia puli SQL
- Identyfikator dzierżawy (identyfikator katalogu ) usługi Azure Active Directory
- Identyfikator subskrypcji
- Identyfikator aplikacji nazwy głównej usługi
- Klucz tajny usługi nazwy głównej usługi

Po uzyskaniu powyższych informacji Wdróż ten szablon:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Po wdrożeniu szablonu należy znaleźć trzy nowe zasoby: bezpłatny plan Azure App Service, plan aplikacja funkcji na podstawie zużycia oraz konto magazynu obsługujące rejestrowanie i kolejkę operacji. Kontynuuj czytanie pozostałych sekcji, aby dowiedzieć się, jak zmodyfikować wdrożone funkcje i dostosować je do swoich potrzeb.

## <a name="change-the-compute-level"></a>Zmień poziom obliczeń

1. Przejdź do usługi aplikacji funkcji. Jeśli szablon został wdrożony przy użyciu wartości domyślnych, usługa ta powinna mieć nazwę *DWOperations*. Po otwarciu aplikacji funkcji powinno być widocznych pięć funkcji wdrożonych w usłudze aplikacji funkcji.

   ![Funkcje wdrażane przy użyciu szablonu](./media/manage-compute-with-azure-functions/five-functions.png)

2. Wybierz pozycję *DWScaleDownTrigger* lub *DWScaleUpTrigger* w zależności od tego, czy chcesz zmienić czas skalowania w górę, czy czas skalowania w dół. Z menu rozwijanego wybierz pozycję Integruj.

   ![Wybieranie pozycji Integruj dla funkcji](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Obecnie powinna być wyświetlana wartość *%ScaleDownTime%* lub *%ScaleUpTime%*. Te wartości wskazują, że harmonogram jest oparty na wartościach określonych w [ustawieniach aplikacji](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Na razie można zignorować tę wartość i zmienić harmonogram na preferowany czas na podstawie następnych kroków.

4. W obszarze harmonogramu dodaj godzinę, o której wyrażenie CRON ma odzwierciedlać, jak często usługa SQL Data Warehouse ma być skalowana w górę.

   ![Zmienianie harmonogramu funkcji](./media/manage-compute-with-azure-functions/change-schedule.png)

   Wartość `schedule` jest [wyrażeniem CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) obejmującym sześć pól:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Na przykład *"0 30 9 * * 1-5"* będzie odzwierciedlał wyzwalacz każdego dnia tygodnia o godzinie 9:10:30. Aby uzyskać więcej informacji, zapoznaj się z [przykładami harmonogramów](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) usługi Azure Functions.

## <a name="change-the-time-of-the-scale-operation"></a>Zmień godzinę operacji skalowania

1. Przejdź do usługi aplikacji funkcji. Jeśli szablon został wdrożony przy użyciu wartości domyślnych, usługa ta powinna mieć nazwę *DWOperations*. Po otwarciu aplikacji funkcji powinno być widocznych pięć funkcji wdrożonych w usłudze aplikacji funkcji.

2. Wybierz pozycję *DWScaleDownTrigger* lub *DWScaleUpTrigger* w zależności od tego, czy chcesz zmienić wartość obliczeniową skalowania w górę, czy wartość obliczeniową skalowania w dół. Po wybraniu funkcji w okienku powinien zostać wyświetlony plik *index.js*.

   ![Zmienianie poziomu obliczeniowego wyzwalacza funkcji](././media/manage-compute-with-azure-functions/index-js.png)

3. Zmień wartość elementu *ServiceLevelObjective* na odpowiedni poziom i wybierz pozycję Zapisz. Ta wartość jest poziomem obliczeń, na który będzie skalowane wystąpienie magazynu danych, na podstawie harmonogramu zdefiniowanego w sekcji Integruj.

## <a name="use-pause-or-resume-instead-of-scale"></a>Używanie wstrzymywania lub wznawiania zamiast skalowania

Obecnie funkcje włączone domyślnie to *DWScaleDownTrigger* i *DWScaleUpTrigger*. Jeśli zamiast tego chcesz korzystać z funkcji wstrzymywania i wznawiania, możesz włączyć funkcje *DWPauseTrigger* lub *DWResumeTrigger*.

1. Przejdź do okienka Funkcje.

   ![Okienko Funkcje](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Kliknij przełącznik obok wyzwalaczy, które chcesz włączyć.

3. Przejdź do kart *Integracja* odpowiednich wyzwalaczy, aby zmienić ich harmonogram.

   > [!NOTE]
   > Różnica funkcjonalna między wyzwalaczami skalowania i wyzwalaczami wstrzymywania/wznawiania jest komunikatem wysyłanym do kolejki. Aby uzyskać więcej informacji, zobacz [Dodawanie nowej funkcji wyzwalacza](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Dodawanie nowej funkcji wyzwalacza

Obecnie szablon zawiera tylko dwie funkcje skalowania. Korzystając z tych funkcji, w ciągu dnia można skalować w dół tylko raz i jeden raz. Aby uzyskać bardziej szczegółową kontrolę, na przykład skalowanie w dół wiele razy dziennie lub mieć inne zachowanie skalowania w weekendy, należy dodać kolejny wyzwalacz.

1. Utwórz nową pustą funkcję. Wybierz *+* przycisk obok pozycji funkcje, aby wyświetlić okienko szablonu funkcji.

   ![Tworzenie nowej funkcji](./media/manage-compute-with-azure-functions/create-new-function.png)

2. W obszarze Język wybierz pozycję *JavaScript*, a następnie wybierz pozycję *TimerTrigger*.

   ![Tworzenie nowej funkcji](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nazwij funkcję i ustaw harmonogram. Ilustracja przedstawia, jak można ustawić wyzwalanie funkcji w każdą sobotę o północy (późny wieczór w piątek).

   ![Skalowanie w dół w sobotę](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Skopiuj zawartość pliku *index.js* z jednej z funkcji wyzwalacza.

   ![Kopiowanie pliku index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Ustaw dla zmiennej operacji odpowiednie zachowanie w następujący sposób:

   ```JavaScript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```

## <a name="complex-scheduling"></a>Sporządzanie złożonego harmonogramu

W tej sekcji przedstawiono krótkie informacje o tym, co jest niezbędne do uzyskania bardziej złożonego harmonogramu funkcji wstrzymywania, wznawiania i skalowania.

### <a name="example-1"></a>Przykład 1

Codzienne skalowanie w górę o godz. 8:00 do wartości DW600 i skalowanie w dół o godz. 20:00 do wartości DW200.

| Funkcja  | Zaplanuj     | Operacja                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Przykład 2

Codzienne skalowanie w górę o 8:00 do wartości DW1000, skalowanie w dół do wartości DW600 w 16:00 i skalowanie w dół o 10pm do wartości DW200.

| Funkcja  | Zaplanuj     | Operacja                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Przykład 3

Skalowanie w górę o godz. 8:00 do wartości DW1000, jednokrotne skalowanie w dół do wartości DW600 o godz. 16:00 w dni robocze. Wstrzymanie w piątek o godz. 23:00, wznowienie w poniedziałek rano o godz. 7:00.

| Funkcja  | Zaplanuj       | Operacja                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o funkcjach platformy Azure z [wyzwalaczem czasomierza](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Wyewidencjonowywanie [repozytorium przykładów](https://github.com/Microsoft/sql-data-warehouse-samples)puli SQL.
