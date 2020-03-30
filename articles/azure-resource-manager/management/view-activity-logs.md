---
title: Wyświetlanie dzienników aktywności platformy Azure w celu monitorowania zasobów
description: Dzienniki aktywności służy do przeglądania akcji użytkownika i błędów. Pokazuje platformę Azure portal PowerShell, platformę Azure CLI i REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 9511090099894fad6708843f106570ed029f0c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478138"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Wyświetlanie dzienników aktywności w celu monitorowania akcji zasobów

Za pomocą dzienników aktywności można określić:

* jakie operacje zostały podjęte na zasoby w ramach subskrypcji
* który rozpoczął operację
* kiedy operacja miała miejsce
* status operacji
* wartości innych właściwości, które mogą pomóc w badaniu operacji

Dziennik aktywności zawiera wszystkie operacje zapisu (PUT, POST, DELETE) dla zasobów. Nie obejmuje on operacji odczytu (GET). Aby uzyskać listę akcji zasobów, zobacz [Operacje dostawcy zasobów usługi Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki zasób został zmodyfikowany przez użytkownika w organizacji.

Dzienniki aktywności są przechowywane przez 90 dni. Możesz wykonać zapytanie dotyczące dowolnego zakresu dat, jednak data początkowa nie może być wcześniejsza od daty bieżącej o więcej niż 90 dni.

Informacje można pobierać z dzienników aktywności za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API spoczynku aplikacji Insights lub [biblioteki .NET aplikacji Insights.](https://www.nuget.org/packages/Microsoft.Azure.Insights/)

## <a name="azure-portal"></a>Portal Azure

Aby wyświetlić dzienniki aktywności za pośrednictwem portalu, wykonaj następujące kroki:

1. W menu portalu Azure wybierz pozycję **Monitor**lub wyszukaj i wybierz **pozycję Monitor** z dowolnej strony.

    ![Wybierz monitor](./media/view-activity-logs/select-monitor-from-menu.png)

1. Wybierz **dziennik aktywności**.

    ![Wybierz dziennik aktywności](./media/view-activity-logs/select-activity-log.png)

1. Pojawi się podsumowanie ostatnich operacji. Domyślny zestaw filtrów jest stosowany do operacji. Zwróć uwagę na informacje na podsumowanie obejmuje, kto rozpoczął akcję i kiedy to się stało.

    ![Wyświetlanie podsumowania ostatnich operacji](./media/view-activity-logs/audit-summary.png)

1. Aby szybko uruchomić wstępnie zdefiniowany zestaw filtrów, wybierz opcję **Szybkie spostrzeżenia .**

    ![Wybieranie szybkich statystyk](./media/view-activity-logs/select-quick-insights.png)

1. Wybierz jedną z opcji. Na przykład wybierz **nieudane wdrożenia,** aby wyświetlić błędy z wdrożeń.

    ![Wybieranie nieudanych wdrożeń](./media/view-activity-logs/select-failed-deployments.png)

1. Zwróć uwagę, że filtry zostały zmienione, aby skupić się na błędach wdrażania w ciągu ostatnich 24 godzin. Wyświetlane są tylko operacje zgodne z filtrami.

    ![Filtry widoku](./media/view-activity-logs/view-filters.png)

1. Aby skupić się na określonych operacjach, zmień filtry lub zastosuj nowe. Na przykład na poniższej ilustracji przedstawiono nową wartość dla **timespan** i **typ zasobu** jest ustawiona na konta magazynu.

    ![Ustawianie opcji filtru](./media/view-activity-logs/set-filter.png)

1. Jeśli chcesz uruchomić kwerendę ponownie później, wybierz **pozycję Przypin bieżące filtry**.

    ![Filtry pinów](./media/view-activity-logs/pin-filters.png)

1. Nadaj filtrowi nazwę.

    ![Filtry nazw](./media/view-activity-logs/name-filters.png)

1. Filtr jest dostępny na pulpicie nawigacyjnym. W menu witryny Azure Portal wybierz pozycję **Pulpit nawigacyjny**.

    ![Pokaż filtr na pulpicie nawigacyjnym](./media/view-activity-logs/activity-log-on-dashboard.png)

1. W portalu można wyświetlać zmiany w zasobie. Wróć do widoku domyślnego w monitorze i wybierz operację, która wiązała się ze zmianą zasobu.

    ![Wybierz operację](./media/view-activity-logs/select-operation.png)

1. Wybierz **pozycję Zmień historię (Podgląd)** i wybierz jedną z dostępnych operacji.

    ![Wybieranie historii zmian](./media/view-activity-logs/select-change-history.png)

1. Zostaną wyświetlone zmiany w zasobie.

    ![Pokaż zmiany](./media/view-activity-logs/show-changes.png)

Aby dowiedzieć się więcej o historii zmian, zobacz [Get changes resource changes](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby pobrać wpisy dziennika, uruchom polecenie **Get-AzLog.** Należy podać dodatkowe parametry do filtrowania listy wpisów. Jeśli nie określisz czasu rozpoczęcia i zakończenia, zostaną zwrócone wpisy z ostatnich siedmiu dni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

W poniższym przykładzie pokazano, jak używać dziennika działań do operacji badawczych wykonanych w określonym czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Można też użyć funkcji daty, aby określić zakres dat, na przykład ostatnie 14 dni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Można wyszukać akcje podjęte przez określonego użytkownika.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Można filtrować operacje nie powiodły się.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Można skupić się na jeden błąd, patrząc na komunikat o stanie dla tego wpisu.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Można wybrać określone wartości, aby ograniczyć zwracane dane.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

W zależności od określonej czasu rozpoczęcia poprzednie polecenia mogą zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki pod kątem tego, czego szukasz, podając kryteria wyszukiwania. Na przykład można filtrować według typu operacji.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Za pomocą wykresu zasobów można wyświetlić historię zmian dla zasobu. Aby uzyskać więcej informacji, zobacz [Get changes resource changes](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby pobrać wpisy dziennika, uruchom polecenie [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) z przesunięciem, aby wskazać przedział czasu.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

W poniższym przykładzie pokazano, jak używać dziennika działań do operacji badawczych wykonanych w określonym czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Można wyszukać akcje podjęte przez określonego użytkownika, nawet dla grupy zasobów, która już nie istnieje.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Można filtrować operacje nie powiodły się.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Można skupić się na jeden błąd, patrząc na komunikat o stanie dla tego wpisu.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Można wybrać określone wartości, aby ograniczyć zwracane dane.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

W zależności od określonej czasu rozpoczęcia poprzednie polecenia mogą zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki pod kątem tego, czego szukasz, podając kryteria wyszukiwania. Na przykład można filtrować według typu operacji.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Za pomocą wykresu zasobów można wyświetlić historię zmian dla zasobu. Aby uzyskać więcej informacji, zobacz [Get changes resource changes](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>Interfejs API REST

Operacje REST do pracy z dziennikiem aktywności są częścią [interfejsu API rest aplikacji Insights](/rest/api/monitor/). Aby pobrać zdarzenia dziennika aktywności, zobacz [Lista zdarzeń zarządzania w ramach subskrypcji](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Następne kroki

* Dzienniki aktywności platformy Azure mogą być używane z usługą Power BI, aby uzyskać lepszy wgląd w akcje w ramach subskrypcji. Zobacz [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Aby dowiedzieć się więcej o ustawianiu zasad zabezpieczeń, zobacz [Kontrola dostępu oparta na rolach platformy Azure](../../role-based-access-control/role-assignments-portal.md).
* Aby wyświetlić więcej szczegółów dotyczących zmian w aplikacjach z warstwy infrastruktury aż do wdrożenia aplikacji, zobacz [Korzystanie z analizy zmian aplikacji w usłudze Azure Monitor](../../azure-monitor/app/change-analysis.md).
* Aby dowiedzieć się więcej o poleceniach służących do wyświetlania operacji wdrażania, zobacz [Wyświetlanie operacji wdrażania](../templates/deployment-history.md).
* Aby dowiedzieć się, jak zapobiegać usuwaniu zasobów dla wszystkich użytkowników, zobacz [Blokowanie zasobów za pomocą usługi Azure Resource Manager](lock-resources.md).
* Aby wyświetlić listę operacji dostępnych dla każdego dostawcy usługi Microsoft Azure Resource Manager, zobacz [Operacje dostawcy zasobów usługi Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md)
