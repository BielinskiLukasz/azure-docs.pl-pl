---
title: Zbieranie & analizowanie dzienników zasobów
description: Rejestruj i Analizuj zdarzenia dziennika zasobów dla Azure Container Registry takich jak uwierzytelnianie, wypychanie obrazów i ściąganie obrazów.
ms.topic: article
ms.date: 10/30/2019
ms.openlocfilehash: ada8502724c1779b9bdab2e8ac7e8ea61c256e44
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456433"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki

W tym artykule wyjaśniono, jak zbierać dane dziennika dla usługi Azure Container Registry przy użyciu funkcji programu [Azure monitor](../azure-monitor/overview.md). Azure Monitor zbiera [dzienniki zasobów](../azure-monitor/platform/resource-logs-overview.md) (wcześniej nazywane *dziennikami diagnostycznymi*) dla zdarzeń sterowanych przez użytkownika w rejestrze. Zbieraj dane i korzystaj z nich w celu zaspokajania potrzeb, takich jak:

* Inspekcja zdarzeń uwierzytelniania rejestru w celu zapewnienia bezpieczeństwa i zgodności 

* Zapewnij kompletną aktywność dla artefaktów rejestru, takich jak zdarzenia ściągania i ściągania, dzięki czemu można zdiagnozować problemy operacyjne z rejestrem 

Zbieranie danych dzienników zasobów przy użyciu Azure Monitor może pociągnąć za sobą dodatkowe koszty. Zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). 


> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia](#preview-limitations) mają zastosowanie. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Rejestrowanie zdarzeń na poziomie repozytorium nie obejmuje obecnie zdarzeń Delete ani UNTAG. Rejestrowane są tylko następujące zdarzenia repozytorium:
* **Zdarzenia wypychania** dla obrazów i innych artefaktów
* **Zdarzenia ściągania** dla obrazów i innych artefaktów

## <a name="registry-resource-logs"></a>Dzienniki zasobów rejestru

Dzienniki zasobów zawierają informacje wyemitowane przez zasoby platformy Azure opisujące ich wewnętrzną operację. W przypadku usługi Azure Container Registry dzienniki zawierają zdarzenia uwierzytelniania i na poziomie repozytorium przechowywane w poniższych tabelach. 

* **ContainerRegistryLoginEvents** — zdarzenia i stan uwierzytelniania rejestru, w tym przychodzącą tożsamość i adres IP
* **ContainerRegistryRepositoryEvents** — operacje, takie jak wypychanie i ściąganie dla obrazów i innych artefaktów w repozytoriach rejestru
* **AzureMetrics** - [metryki rejestru kontenerów](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) , takie jak agregowane liczby wypychania i ściągania.

W przypadku operacji dane dziennika obejmują:
  * Stan powodzenia lub niepowodzenia
  * Sygnatury czasowe rozpoczęcia i zakończenia

Oprócz dzienników zasobów platforma Azure udostępnia [Dziennik aktywności](../azure-monitor/platform/activity-logs-overview.md), pojedynczy rekord poziomu subskrypcji zdarzeń zarządzania platformy Azure, taki jak tworzenie lub usuwanie rejestru kontenerów.

## <a name="enable-collection-of-resource-logs"></a>Włącz zbieranie dzienników zasobów

Kolekcja dzienników zasobów dla rejestru kontenerów nie jest domyślnie włączona. Jawnie Włącz ustawienia diagnostyczne dla każdego rejestru, który ma być monitorowany. Aby uzyskać opcje włączania ustawień diagnostycznych, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników platformy i metryk na platformie Azure](../azure-monitor/platform/diagnostic-settings.md).

Aby na przykład wyświetlić dzienniki i metryki dla rejestru kontenerów niemal w czasie rzeczywistym w Azure Monitor, Zbierz dzienniki zasobów w Log Analytics obszarze roboczym. Aby włączyć to ustawienie diagnostyczne przy użyciu Azure Portal:

1. Jeśli nie masz jeszcze obszaru roboczego, Utwórz obszar roboczy przy użyciu [Azure Portal](../azure-monitor/learn/quick-create-workspace.md). Aby zminimalizować opóźnienie w zbieraniu danych, upewnij się, że obszar roboczy znajduje się w tym **samym regionie** co rejestr kontenerów.
1. W portalu wybierz rejestr, a następnie wybierz pozycję **monitorowanie > Ustawienia diagnostyczne > Dodaj ustawienie diagnostyczne**.
1. Wprowadź nazwę ustawienia, a następnie wybierz pozycję **Wyślij do log Analytics**.
1. Wybierz obszar roboczy dzienników diagnostycznych rejestru.
1. Wybierz dane dziennika, które chcesz zebrać, a następnie kliknij przycisk **Zapisz**.

Na poniższej ilustracji przedstawiono tworzenie ustawień diagnostycznych dla rejestru przy użyciu portalu.

![Włączanie ustawień diagnostycznych](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Zbierz tylko potrzebne dane, Zrównoważ koszt i wymagania dotyczące monitorowania. Na przykład, jeśli konieczne jest tylko Inspekcja zdarzeń uwierzytelniania, należy wybrać tylko dziennik **ContainerRegistryLoginEvents** . 

## <a name="view-data-in-azure-monitor"></a>Wyświetlanie danych w Azure Monitor

Po włączeniu zbierania dzienników diagnostycznych w Log Analytics może upłynąć kilka minut, zanim dane będą widoczne w Azure Monitor. Aby wyświetlić dane w portalu, wybierz rejestr, a następnie wybierz pozycję **monitorowanie > dzienników**. Wybierz jedną z tabel zawierających dane dla rejestru. 

Uruchom zapytania, aby wyświetlić dane. Dostępne są kilka przykładowych zapytań lub własne. Na przykład następujące zapytanie pobiera ostatnie 24 godziny danych z tabeli **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Na poniższej ilustracji przedstawiono przykładowe dane wyjściowe:

![Dane dziennika zapytań](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Aby zapoznać się z samouczkiem dotyczącym używania Log Analytics w Azure Portal, zobacz [wprowadzenie do Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)lub wypróbuj [środowisko demonstracyjne](https://portal.loganalytics.io/demo)log Analytics. 

Aby uzyskać więcej informacji na temat zapytań dzienników, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Dodatkowe przykłady zapytań

#### <a name="100-most-recent-registry-events"></a>100 ostatnich zdarzeń rejestru

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Dodatkowe miejsca docelowe dzienników

Oprócz wysyłania dzienników do Log Analytics lub jako alternatywy typowym scenariuszem jest wybranie konta usługi Azure Storage jako miejsca docelowego dziennika. Aby zarchiwizować dzienniki w usłudze Azure Storage, należy utworzyć konto magazynu przed włączeniem archiwizowania za pomocą ustawień diagnostycznych.

Zdarzenia dzienników diagnostycznych można przesyłać strumieniowo do [centrum zdarzeń platformy Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Usługa Event Hubs pozyskiwać miliony zdarzeń na sekundę, które można przetworzyć i zapisać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat używania [log Analytics](../azure-monitor/log-query/get-started-portal.md) i tworzenia [zapytań dzienników](../azure-monitor/log-query/get-started-queries.md).
* Zobacz [Omówienie dzienników platformy Azure](../azure-monitor/platform/platform-logs-overview.md) , aby dowiedzieć się więcej o dziennikach platformy, które są dostępne na różnych warstwach platformy Azure.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
