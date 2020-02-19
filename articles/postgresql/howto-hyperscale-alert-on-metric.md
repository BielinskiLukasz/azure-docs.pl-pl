---
title: Konfigurowanie alertów — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do alertów metryk dla Azure Database for PostgreSQL-ze skalowaniem (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 0e2eb4ab13319779ae209e58253c6a5f2ccb75da
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462432"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Użyj Azure Portal, aby skonfigurować alerty dotyczące metryk dla Azure Database for PostgreSQL-Citus

W tym artykule opisano sposób konfigurowania alertów Azure Database for PostgreSQL przy użyciu Azure Portal. Możesz otrzymywać alerty na podstawie [metryk monitorowania](concepts-hyperscale-monitoring.md) dla usług platformy Azure.

Skonfigurujemy alert, który zostanie wyzwolony, gdy wartość określonej metryki przekroczy próg. Alert jest wyzwalany po pierwszym spełnieniu warunku, a następnie jest wyzwalany ponownie.

Można skonfigurować alert, aby wykonał następujące akcje po jego wyzwoleniu:
* Wyślij powiadomienia e-mail do administratora usługi i współadministratorów.
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail, które określisz.
* Wywoływanie elementu webhook.

Można skonfigurować i uzyskać informacje o regułach alertów przy użyciu:
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfejs wiersza polecenia platformy Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Interfejs API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertu na podstawie metryki z Azure Portal
1. W [Azure Portal](https://portal.azure.com/)wybierz serwer Azure Database for PostgreSQL, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **alerty** , jak pokazano:

   ![Wybierz reguły alertów](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Wybierz pozycję **Nowa reguła alertu** (ikona +).

4. Zostanie otwarta strona **Tworzenie reguły** , jak pokazano poniżej. Wprowadź wymagane informacje:

   ![Formularz dodawania alertu metryki](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. W sekcji **warunek** wybierz pozycję **Dodaj**.

6. Wybierz metrykę z listy sygnałów, dla których chcesz otrzymywać alerty. W tym przykładzie wybierz pozycję "procent magazynu".
   
   ![Wybierz metrykę](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Skonfiguruj logikę alertów:

    * **Operator** (np. "Większe niż")
    * **Wartość progowa** (np. 85 procent)
    * **Stopień szczegółowości agregacji** czasu, przez jaki reguła metryki musi być spełniona przed wyzwalaczem alertu (np. "W ciągu ostatnich 30 minut")
    * **częstotliwość oceny** (np. "1 minuta")
   
   Wybierz pozycję **gotowe** po zakończeniu.

   ![Wybierz metrykę](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. W sekcji **grupy akcji** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę, która będzie otrzymywać powiadomienia dotyczące alertu.

9. Wypełnij formularz "Dodawanie grupy akcji" z nazwą, krótką nazwą, subskrypcją i grupą zasobów.

    ![Grupa akcji](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Skonfiguruj typ akcji **poczty e-mail/SMS/wypychania/głosu** .
    
    Wybierz pozycję "Azure Resource Manager rolę E-mail", aby wysyłać powiadomienia do właścicieli subskrypcji, współautorów i czytelników.
   
    Po zakończeniu wybierz **przycisk OK** .

    ![Grupa akcji](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Określ nazwę, opis i ważność reguły alertu.

    ![Grupa akcji](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert.

    W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.

### <a name="managing-alerts"></a>Zarządzanie alertami

Po utworzeniu alertu możesz go zaznaczyć i wykonać następujące czynności:

* Wyświetl wykres pokazujący próg metryki i rzeczywiste wartości z poprzedniego dnia odpowiednie dla tego alertu.
* **Edytuj** lub **Usuń** regułę alertów.
* **Wyłączenie** lub **włączenie** alertu, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień.

## <a name="suggested-alerts"></a>Sugerowane alerty

### <a name="disk-space"></a>Miejsce na dysku

Monitorowanie i alerty są ważne dla każdej grupy serwerów Citus. Bazowa baza danych PostgreSQL wymaga poprawnego działania wolnego miejsca na dysku. Jeśli dysk zostanie zapełniony, węzeł serwera bazy danych przejdzie w tryb offline i nie będzie można go uruchomić do momentu udostępnienia miejsca. W tym momencie wymaga żądania pomocy technicznej firmy Microsoft w celu rozwiązania problemu.

Zalecamy ustawienie alertów dotyczących miejsca na dysku dla każdego węzła w każdej grupie serwerów, nawet w przypadku nieprodukcyjnego użycia. Alerty dotyczące użycia miejsca na dysku zapewniają ostrzeżenie z wyprzedzeniem, które jest konieczne do interwencji i utrzymywania węzłów w dobrej kondycji. Aby uzyskać najlepsze wyniki, wypróbuj serię alertów na 75%, 85% i 95% użycia. Wartości procentowe do wybrania zależą od szybkości pozyskiwania danych, ponieważ szybkie pozyskiwanie danych powoduje szybsze wypełnianie dysku.

Ponieważ dysk zbliża się do limitu miejsca, wypróbuj te metody, aby uzyskać więcej wolnego miejsca:

* Przejrzyj zasady przechowywania danych. Jeśli to możliwe, Przenieś starsze dane do magazynu chłodnego.
* Rozważ [dodanie węzłów](howto-hyperscale-scaling.md#add-worker-nodes) do grupy serwerów i ponowne zrównoważenie fragmentów. Ponowne równoważenie dystrybuuje dane na większej liczbie komputerów.
* Rozważ zwiększenie [pojemności](howto-hyperscale-scaling.md#increase-vcores) węzłów procesu roboczego. Każdy proces roboczy może mieć do 2 TiB magazynu. Należy jednak próbować dodać węzły przed zmianą rozmiarów węzłów, ponieważ Dodawanie węzłów kończy się szybciej.

### <a name="cpu-usage"></a>Użycie procesora CPU

Monitorowanie użycia procesora CPU jest przydatne do ustanowienia podstawy wydajności. Na przykład można zauważyć, że użycie procesora CPU jest zwykle około 40-60%. Jeśli użycie procesora nagle rozpocznie się w pobliżu 95%, można rozpoznać anomalię. Użycie procesora CPU może odzwierciedlać wzrost organiczny, ale może również ujawniać nieujawnione zapytanie. Podczas tworzenia alertu dotyczącego procesora CPU Ustaw długi stopień szczegółowości agregacji, aby przechwycić długotrwałe wzrosty i zignorować liczby chwilowe.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
* Zapoznaj się z [omówieniem kolekcji metryk](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , aby upewnić się, że usługa jest dostępna i odpowiada.
