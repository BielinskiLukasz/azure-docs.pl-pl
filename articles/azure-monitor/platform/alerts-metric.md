---
title: Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure Monitor
description: Dowiedz się, jak używać Azure Portal lub interfejsu wiersza polecenia do tworzenia i wyświetlania reguł alertów metryk oraz zarządzania nimi.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 02/16/2020
ms.subservice: alerts
ms.openlocfilehash: 5452f67a9da9a71a72c9387bc3e62d6735929984
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363437"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure Monitor

Alerty metryk w Azure Monitor umożliwiają otrzymywanie powiadomień, gdy jedna z metryk przekroczy próg. Alerty dotyczące metryk działają na wielu metrykach wielowymiarowych platform, metrykach niestandardowych i standardowych i niestandardowych metrykach usługi Application Insights. W tym artykule opisano sposób tworzenia i wyświetlania reguł alertów dotyczących metryk oraz zarządzania nimi za pomocą Azure Portal i interfejsu wiersza polecenia platformy Azure. Reguły alertów dotyczących metryk można również tworzyć przy użyciu szablonów Azure Resource Manager, które są opisane w [osobnym artykule](alerts-metric-create-templates.md).

Aby dowiedzieć się więcej o tym, jak alerty metryk działają na podstawie [alertów dotyczących metryk](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Utwórz za pomocą Azure Portal

Poniższa procedura opisuje sposób tworzenia reguły alertu dotyczącego metryki w Azure Portal:

1. W [Azure Portal](https://portal.azure.com)kliknij pozycję **Monitoruj**. Blok monitor konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

2. Kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów zawiera również **alerty** w menu zasobów w obszarze **monitorowanie**, ale można również utworzyć alerty.

3. Kliknij pozycję **Wybierz element docelowy**, w okienku kontekstu, który ładuje, wybierz zasób docelowy, na którym chcesz utworzyć alert. Użyj listy rozwijanej Typ **subskrypcji** i **zasobu** , aby znaleźć zasób, który chcesz monitorować. Możesz również użyć paska wyszukiwania, aby znaleźć zasób.

4. Jeśli wybrany zasób ma metryki, na których można tworzyć alerty, **dostępne sygnały** z prawej strony będą zawierać metryki. W tym [artykule](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)można wyświetlić pełną listę typów zasobów, które są obsługiwane w przypadku alertów dotyczących metryk.

5. Po wybraniu zasobu docelowego kliknij pozycję **Dodaj warunek**.

6. Zostanie wyświetlona lista sygnałów obsługiwanych dla zasobu, wybierz metrykę, dla której chcesz utworzyć alert.

7. Zostanie wyświetlony wykres metryki dla ostatnich sześciu godzin. Za pomocą listy rozwijanej **Okres wykresu** wybierz opcję, aby wyświetlić dłuższą historię dla metryki.

8. Jeśli Metryka ma wymiary, zostanie wyświetlona tabela wymiarów. Wybierz co najmniej jedną wartość na wymiar.
    - Wyświetlone wartości wymiarów są oparte na danych metryk z ostatnich trzech dni.
    - Jeśli wartość wymiaru, którego szukasz, nie jest wyświetlana, kliknij przycisk "+", aby dodać wartość niestandardową.
    - Możesz również **wybrać \*** dla dowolnego z wymiarów. **Wybierz \*** będzie dynamicznie skalować zaznaczenie do wszystkich bieżących i przyszłych wartości wymiaru.

    Reguła alertu metryki będzie szacować warunek dla wszystkich wybranych kombinacji wartości. [Dowiedz się więcej o tym, jak działa alert dotyczący metryk wielowymiarowych](alerts-metric-overview.md).

9. Wybierz typ **progu** , **operator**i **typ agregacji**. Spowoduje to określenie logiki, która będzie Szacowana przez regułę alertu metryki.
    - Jeśli używasz progu **statycznego** , Kontynuuj definiowanie **wartości progowej**. Wykres metryk może pomóc w ustaleniu, co może być rozsądnym progiem.
    - Jeśli używasz progu **dynamicznego** , Kontynuuj definiowanie **czułości progowej**. Na wykresie metryki zostaną wyświetlone obliczone progi na podstawie ostatnich danych. [Dowiedz się więcej o opcjach typu i czułości wartości progów dynamicznych](alerts-dynamic-thresholds.md).

10. Opcjonalnie można uściślić warunek przez dostosowanie **stopnia szczegółowości agregacji** i **częstotliwości obliczania**. 

11. Kliknij przycisk **Gotowe**.

12. Opcjonalnie dodaj kolejne kryteria, jeśli chcesz monitorować złożoną regułę alertów. Obecnie użytkownicy mogą mieć reguły alertów z kryteriami progów dynamicznych jako pojedyncze kryterium.

13. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis**i **ważność** **reguły alertu**.

14. Dodaj grupę akcji do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

15. Kliknij przycisk **gotowe** , aby zapisać regułę alertu metryki.

> [!NOTE]
> Reguły alertów metryk utworzonych za pomocą portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

## <a name="view-and-manage-with-azure-portal"></a>Wyświetl Azure Portal i zarządzaj nimi

Reguły alertów dotyczących metryk można wyświetlać i zarządzać nimi za pomocą bloku zarządzanie regułami w obszarze alerty. W poniższej procedurze przedstawiono sposób wyświetlania reguł alertów dotyczących metryk i edytowania jednego z nich.

1. W Azure Portal przejdź do **monitorowania**

2. Kliknij pozycję **alerty** i **Zarządzaj regułami**

3. W bloku **Zarządzanie regułami** można wyświetlić wszystkie reguły alertów w różnych subskrypcjach. Można dodatkowo filtrować reguły przy użyciu **grupy zasobów**, **typu zasobu**i **zasobu**. Jeśli chcesz zobaczyć tylko alerty metryk, wybierz pozycję **Typ sygnału** jako metryki.

    > [!TIP]
    > W bloku **Zarządzanie regułami** możesz wybrać wiele reguł alertów i włączyć je/wyłączyć. Może to być przydatne w przypadku, gdy pewne zasoby docelowe muszą zostać umieszczone w trybie konserwacji

4. Kliknij nazwę reguły alertu metryki, którą chcesz edytować.

5. W obszarze Edytuj regułę kliknij **kryteria alertu** , które chcesz edytować. W razie potrzeby można zmienić metrykę, warunek progowy i inne pola

    > [!NOTE]
    > Po utworzeniu alertu dotyczącego metryki nie można edytować **zasobu docelowego** i **nazwy reguły alertu** .

6. Kliknij przycisk **gotowe** , aby zapisać zmiany.

## <a name="with-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Poprzednie sekcje opisują sposób tworzenia i wyświetlania reguł alertów dotyczących metryk oraz zarządzania nimi przy użyciu Azure Portal. W tej sekcji opisano, jak wykonać to samo przy użyciu wieloplatformowego [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Najszybszym sposobem na rozpoczęcie korzystania z interfejsu wiersza polecenia platformy Azure jest [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). W tym artykule będziemy używać Cloud Shell.

1. Przejdź do Azure Portal, kliknij pozycję **Cloud Shell**.

2. W wierszu polecenia można użyć poleceń z opcją ``--help``, aby dowiedzieć się więcej o poleceniu i sposobach ich użycia. Na przykład następujące polecenie pokazuje listę poleceń dostępnych do tworzenia i wyświetlania alertów metryk oraz zarządzania nimi

    ```azurecli
    az monitor metrics alert --help
    ```

3. Można utworzyć prostą regułę alertu metryki, która monitoruje, czy średni procent procesora CPU na maszynie wirtualnej jest większy niż 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Wszystkie alerty metryk w grupie zasobów można wyświetlić za pomocą następującego polecenia

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Szczegóły konkretnej reguły alertu metryki można zobaczyć przy użyciu nazwy lub identyfikatora zasobu reguły.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Regułę alertu metryki można wyłączyć za pomocą następującego polecenia.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Regułę alertu metryki można usunąć za pomocą następującego polecenia.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Następne kroki

- [Tworzenie alertów metryk przy użyciu szablonów Azure Resource Manager](../../azure-monitor/platform/alerts-enable-template.md).
- [Informacje o działaniu alertów dotyczących metryk](alerts-metric-overview.md).
- [Informacje o tym, jak działają Alerty metryczne z warunkiem progów dynamicznych](alerts-dynamic-thresholds.md).
- [Informacje o schemacie elementu webhook dla alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

