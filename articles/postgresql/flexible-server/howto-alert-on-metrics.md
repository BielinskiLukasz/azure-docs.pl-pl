---
title: Konfigurowanie alertów — Azure Portal-Azure Database for PostgreSQL — elastyczny serwer
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do alertów metryk dla Azure Database for PostgreSQL-elastyczny serwer z Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ac252c3898eb014885bf9a6bf6bdedb7db74fb62
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545841"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---flexible-server"></a>Użyj Azure Portal, aby skonfigurować alerty dotyczące metryk dla serwera Azure Database for PostgreSQL-elastyczny

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym artykule opisano sposób konfigurowania alertów Azure Database for PostgreSQL przy użyciu Azure Portal. Możesz otrzymywać alerty na podstawie metryk monitorowania dla usług platformy Azure.

Alert jest wyzwalany, gdy wartość określonej metryki przekroczy przypisany próg. Alert jest wyzwalany podczas pierwszego spełnienia warunku, a następnie gdy ten warunek nie jest już spełniany.

Można skonfigurować alert, aby wykonał następujące akcje po jego wyzwoleniu:

* Wyślij powiadomienia e-mail do administratora usługi i współadministratorów.
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail, które określisz.
* Wywoływanie elementu webhook.

Można skonfigurować i uzyskać informacje o regułach alertów przy użyciu:

* [Witryna Azure Portal](../../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfejs wiersza polecenia platformy Azure](../../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Interfejs API REST usługi Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertu na podstawie metryki z Azure Portal

1. W [Azure Portal](https://portal.azure.com/)wybierz serwer Azure Database for PostgreSQL, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **alerty** , jak pokazano:

   :::image type="content" source="./media/howto-alert-on-metrics/2-alert-rules.png" alt-text="Wybierz reguły alertów":::

3. Wybierz pozycję **Dodaj alert dotyczący metryki** (+ ikona).

4. Zostanie otwarta strona **Tworzenie reguły** , jak pokazano poniżej. Wypełnij wymagane informacje:

   :::image type="content" source="./media/howto-alert-on-metrics/4-add-rule-form.png" alt-text="Wybierz reguły alertów":::

5. W sekcji **warunek** wybierz pozycję **Dodaj warunek** .

6. Wybierz metrykę z listy sygnałów, dla których chcesz otrzymywać alerty. W tym przykładzie wybierz pozycję "procent magazynu".

   :::image type="content" source="./media/howto-alert-on-metrics/6-configure-signal-logic.png" alt-text="Wybierz reguły alertów":::

7. Skonfiguruj logikę alertów, łącznie z **warunkiem** (np. "Większe niż"), **próg** (np. 85 procent), **agregacja czasu** , **okres** czasu reguły metryki musi być spełniony przed wyzwalaczami alertu (np. "W ciągu ostatnich 30 minut") i **częstotliwość** .

   Wybierz pozycję **gotowe** po zakończeniu.

   :::image type="content" source="./media/howto-alert-on-metrics/7-set-threshold-time.png" alt-text="Wybierz reguły alertów" z nazwą, krótką nazwą, subskrypcją i grupą zasobów.

10. Skonfiguruj typ akcji **poczty e-mail/SMS/wypychania/głosu** .

    1. Wybierz pozycję "Azure Resource Manager rolę E-mail", aby wybrać właścicieli subskrypcji, współautorów i czytelników, którzy mają otrzymywać powiadomienia.

    2. Opcjonalnie Podaj prawidłowy identyfikator URI w polu **elementu webhook** , jeśli ma on być wywoływany po uruchomieniu alertu.

    3. Po zakończeniu wybierz **przycisk OK** .

    :::image type="content" source="./media/howto-alert-on-metrics/10-action-group-type.png" alt-text="Wybierz reguły alertów":::

11. Określ nazwę, opis i ważność reguły alertu.

    :::image type="content" source="./media/howto-alert-on-metrics/11-name-description-severity.png" alt-text="Wybierz reguły alertów"::: 

12. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert.

    W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.

## <a name="manage-your-alerts"></a>Zarządzanie alertami

Po utworzeniu alertu możesz go zaznaczyć i wykonać następujące czynności:

* Wyświetl wykres pokazujący próg metryki i rzeczywiste wartości z poprzedniego dnia odpowiednie dla tego alertu.
* **Edytuj** lub **Usuń** regułę alertów.
* **Wyłączenie** lub **włączenie** alertu, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../../azure-monitor/platform/alerts-webhooks.md).
* Zapoznaj się z [omówieniem kolekcji metryk](../../azure-monitor/platform/data-platform.md) , aby upewnić się, że usługa jest dostępna i odpowiada.