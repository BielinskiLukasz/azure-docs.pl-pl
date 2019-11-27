---
title: Azure Functions skalowanie i hosting
description: Dowiedz się, jak wybierać między planem zużycia Azure Functions a planem Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebb2fcf0f626a82bcb5e6439183ba98c39c58588
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322886"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions skalowanie i hosting

Podczas tworzenia aplikacji funkcji na platformie Azure musisz wybrać plan hostingu dla swojej aplikacji. Istnieją trzy plany hostingu dostępne dla Azure Functions: [Plan zużycia](#consumption-plan), [Plan Premium](#premium-plan)i [Plan App Service](#app-service-plan).

Wybrany plan hostingu wymusza następujące zachowania:

* Sposób skalowania aplikacji funkcji.
* Zasoby dostępne dla każdego wystąpienia aplikacji funkcji.
* Obsługa zaawansowanych funkcji, takich jak łączność z siecią wirtualną.

Plany zużycia i Premium automatycznie dodają moc obliczeniową, gdy kod jest uruchomiony. Aplikacja jest skalowana w poziomie, gdy jest wymagana do obsłużenia obciążenia i skalowana w dół, gdy kod przestanie działać. W przypadku planu zużycia nie trzeba również uiszczać opłat za bezczynne maszyny wirtualne lub zarezerwować pojemność.  

Plan Premium oferuje dodatkowe funkcje, takie jak wystąpienia obliczeniowe w warstwie Premium, możliwość utrzymywania nieokreślonych wystąpień i łączności między sieciami wirtualnymi.

Plan App Service umożliwia korzystanie z dedykowanej infrastruktury zarządzanej przez użytkownika. Aplikacja funkcji nie jest skalowana na podstawie zdarzeń, co oznacza, że nigdy nie skaluje się w dół do zera. (Wymaga, aby [zawsze](#always-on) włączony).

## <a name="hosting-plan-support"></a>Obsługa planu hostingu

Obsługa funkcji znajduje się w następujących dwóch kategoriach:

* _Ogólnie dostępna (ga)_ : w pełni obsługiwana i zatwierdzona do użycia w środowisku produkcyjnym.
* _Wersja zapoznawcza_: nie jest jeszcze w pełni obsługiwana i zatwierdzona do użycia w środowisku produkcyjnym.

Poniższa tabela przedstawia bieżący poziom wsparcia dla trzech planów hostingu w przypadku uruchamiania w systemie Windows lub Linux:

| | Plan Zużycie | Plan Premium | Plan dedykowany |
|-|:----------------:|:------------:|:----------------:|
| Windows | Ogólna dostępność | Ogólna dostępność | Ogólna dostępność |
| Linux | Ogólna dostępność | Ogólna dostępność | Ogólna dostępność |

## <a name="consumption-plan"></a>Plan Zużycie

Gdy używasz planu zużycia, wystąpienia hosta Azure Functions są dynamicznie dodawane i usuwane na podstawie liczby zdarzeń przychodzących. Ten plan bezserwerowy jest skalowany automatycznie i naliczana jest opłata za zasoby obliczeniowe tylko wtedy, gdy funkcje są uruchomione. W planie zużycia czas wykonywania funkcji jest przekroczenia przez konfigurowalny okres.

Opłaty są naliczane na podstawie liczby wykonań, czasu wykonania i używanej pamięci. Rozliczanie jest agregowane we wszystkich funkcjach w ramach aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Plan zużycia jest domyślnym planem hostingu i oferuje następujące korzyści:

* Płacisz tylko wtedy, gdy funkcje są uruchomione
* Automatyczne skalowanie w poziomie, nawet w okresach dużego obciążenia

Aplikacje funkcji w tym samym regionie mogą być przypisane do tego samego planu zużycia. Nie ma Minusem ani wpływu na wiele aplikacji uruchomionych w tym samym planie zużycia. Przypisywanie wielu aplikacji do tego samego planu zużycia nie ma wpływu na odporność, skalowalność ani niezawodność każdej aplikacji.

Aby dowiedzieć się więcej na temat szacowania kosztów podczas pracy w planie zużycia, zobacz [Opis kosztów planu zużycia](functions-consumption-costs.md).

## <a name="premium-plan"></a>Plan Premium

W przypadku korzystania z planu Premium wystąpienia hosta Azure Functions są dodawane i usuwane na podstawie liczby zdarzeń przychodzących, podobnie jak w przypadku planu zużycia.  Plan Premium obsługuje następujące funkcje:

* Bezterminowo podgrzewane wystąpienia, aby uniknąć dowolnego zimnego startu
* Łączność z siecią wirtualną
* Nieograniczony czas trwania wykonywania
* Rozmiary wystąpienia Premium (jeden rdzeń, dwa rdzenie i cztery podstawowe wystąpienia)
* Bardziej przewidywalny Cennik
* Alokacja aplikacji o wysokiej gęstości dla planów z wieloma aplikacjami funkcji

Informacje na temat sposobu konfigurowania tych opcji można znaleźć w [dokumencie Azure Functions planu Premium](functions-premium-plan.md).

Zamiast naliczania opłat za wykonanie i zużywaną pamięć rozliczenia dla planu Premium są oparte na liczbie podstawowych sekund i ilości pamięci używanej w ramach wymaganych i wstępnie rozgrzanych wystąpień. Co najmniej jedno wystąpienie musi być aktywne przez cały czas dla każdego planu. Oznacza to, że jest minimalny miesięczny koszt dla aktywnego planu, niezależnie od liczby wykonań. Należy pamiętać, że wszystkie aplikacje funkcji w planie Premium udostępniają wstępnie rozgrzane i aktywne wystąpienia.

Zapoznaj się z planem Azure Functions Premium w następujących sytuacjach:

* Aplikacje funkcji działają w sposób ciągły lub niemal nieustannie.
* Masz dużą liczbę niewielkich wykonań i masz rozliczenie o dużym obciążeniu, ale niski GB drugiego rachunku w planie zużycia.
* Potrzebujesz więcej opcji dotyczących procesora CPU lub pamięci niż to, co jest dostępne w ramach planu zużycia.
* Twój kod musi działać dłużej niż [Maksymalny dozwolony czas wykonywania](#timeout) w planie zużycia.
* Wymagane są funkcje, które są dostępne tylko w planie Premium, np. łączność sieci wirtualnej/VPN.

Podczas uruchamiania funkcji JavaScript w planie Premium należy wybrać wystąpienie, które ma mniej procesorów wirtualnych vCPU. Aby uzyskać więcej informacji, zapoznaj się z tematem [Wybieranie jednego podstawowego planu Premium](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Plan dedykowany (App Service)

Aplikacje funkcji można również uruchamiać na tych samych dedykowanych maszynach wirtualnych, co inne aplikacje App Service (wersje Basic, standard, Premium i izolowane).

Należy wziąć pod uwagę plan App Service w następujących sytuacjach:

* Istnieją już istniejące, nieużywane maszyny wirtualne, na których uruchomiono inne wystąpienia App Service.
* Chcesz udostępnić niestandardowy obraz, na którym będą uruchamiane funkcje.

Płacisz za aplikacje funkcji w planie App Service, tak jak w przypadku innych zasobów App Service, takich jak aplikacje sieci Web. Aby uzyskać szczegółowe informacje na temat działania planu App Service, zobacz [szczegółowe Omówienie planów Azure App Service](../app-service/overview-hosting-plans.md).

Plan App Service umożliwia ręczne skalowanie w poziomie przez dodanie większej liczby wystąpień maszyn wirtualnych. Możesz również włączyć automatyczne skalowanie. Aby uzyskać więcej informacji, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Możesz również skalować w górę, wybierając inny plan App Service. Aby uzyskać więcej informacji, zobacz [skalowanie w górę aplikacji na platformie Azure](../app-service/manage-scale-up.md). 

Podczas uruchamiania funkcji JavaScript w planie App Service należy wybrać plan, który ma mniej procesorów wirtualnych vCPU. Aby uzyskać więcej informacji, zobacz [Wybieranie planów App Service z pojedynczym rdzeniem](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a>Zawsze włączone

Jeśli uruchamiasz plan App Service, należy włączyć ustawienie **zawsze** włączone, aby aplikacja funkcji działała poprawnie. W planie App Service środowisko uruchomieniowe funkcji przechodzi w stan bezczynności po kilku minutach braku aktywności, więc tylko Wyzwalacze HTTP będą wznawiać działanie funkcji. Zawsze włączone jest dostępne tylko w planie App Service. Zgodnie z planem zużycia platforma automatycznie aktywuje aplikacje funkcji.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Nawet przy włączonej opcji zawsze włączone przekroczenie limitu czasu wykonywania poszczególnych funkcji jest kontrolowane przez ustawienie `functionTimeout` w pliku projektu [host. JSON](functions-host-json.md#functiontimeout) .

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Określanie planu hostingu istniejącej aplikacji

Aby określić plan hostingu używany przez aplikację funkcji, zobacz **App Service plan/warstwa cenowa** na karcie **Przegląd** dla aplikacji funkcji w [Azure Portal](https://portal.azure.com). W przypadku planów App Service określono również warstwę cenową.

![Wyświetlanie planu skalowania w portalu](./media/functions-scale/function-app-overview-portal.png)

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby określić plan w następujący sposób:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Gdy dane wyjściowe tego polecenia są `dynamic`, aplikacja funkcji jest w planie zużycia. Gdy dane wyjściowe tego polecenia są `ElasticPremium`, aplikacja funkcji jest w planie Premium. Wszystkie inne wartości wskazują różne warstwy planu App Service.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

W każdym planie aplikacja funkcji wymaga konta usługi Azure Storage, które obsługuje obiekty blob, kolejki, pliki i tabele usługi Azure Storage. Wynika to z faktu, że funkcje programu korzystają z usługi Azure Storage w przypadku operacji takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji, ale niektóre konta magazynu nie obsługują kolejek i tabel. Te konta, które obejmują konta magazynu tylko dla obiektów BLOB (w tym magazyn Premium Storage) i konta magazynu ogólnego przeznaczenia z replikacją magazynową strefowo nadmiarową, są odfiltrowane z istniejących ustawień **konta magazynu** podczas tworzenia Aplikacja funkcji.

To samo konto magazynu używane przez aplikację funkcji może być również używane przez wyzwalacze i powiązania do przechowywania danych aplikacji. Jednak w przypadku operacji intensywnie korzystających z magazynu należy użyć oddzielnego konta magazynu.   

<!-- JH: Does using a Premium Storage account improve perf? -->

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [wprowadzenie do usług Azure Storage](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Jak działają plany zużycia i Premium

W planach zużycia i Premium infrastruktura Azure Functions skaluje zasoby procesora i pamięci, dodając kolejne wystąpienia hosta funkcji na podstawie liczby zdarzeń wyzwalanych przez jej funkcje. Każde wystąpienie hosta funkcji w planie zużycia jest ograniczone do 1,5 GB pamięci i jednego procesora CPU.  Wystąpieniem hosta jest cała aplikacja funkcji, co oznacza, że wszystkie funkcje w ramach zasobu funkcji udział aplikacji w ramach wystąpienia i skalowania w tym samym czasie. Aplikacje funkcji, które współużytkują ten sam plan zużycia, są skalowane niezależnie.  W planie Premium rozmiar planu określi dostępną pamięć i procesor dla wszystkich aplikacji w tym wystąpieniu.  

Pliki kodu funkcji są przechowywane w udziałach Azure Files na głównym koncie magazynu funkcji. Po usunięciu głównego konta magazynu aplikacji funkcji pliki kodu funkcji zostaną usunięte i nie będzie można go odzyskać.

### <a name="runtime-scaling"></a>Skalowanie środowiska uruchomieniowego

Azure Functions używa składnika zwanego *kontrolerem skalowania* , aby monitorować częstotliwość zdarzeń i określać, czy skalować w poziomie, czy skalować w poziomie. Kontroler skalowania używa algorytmów heurystycznych dla każdego typu wyzwalacza. Na przykład podczas korzystania z wyzwalacza usługi Azure queue storage skaluje się w zależności od długości kolejki i wieku najstarszej wiadomości w kolejce.

Jednostką skalowania Azure Functions jest aplikacja funkcji. Gdy aplikacja funkcji jest skalowana w poziomie, dodatkowe zasoby są przydzieleni do uruchamiania wielu wystąpień hosta Azure Functions. Z drugiej strony, ponieważ zapotrzebowanie obliczeniowe jest ograniczone, kontroler skalowania usuwa wystąpienia hosta funkcji. Liczba wystąpień jest ostatecznie skalowana w dół do zera, gdy żadne funkcje nie działają w ramach aplikacji funkcji.

![Skalowanie zdarzeń monitorowania kontrolera i Tworzenie wystąpień](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Zrozumienie zachowań skalowania

Skalowanie może się różnić w zależności od liczby czynników i skalować w różny sposób w zależności od wybranego wyzwalacza i języka. Istnieje kilka złożonego zachowań do skalowania:

* Pojedynczą aplikację funkcji można skalować w górę do maksymalnie 200 wystąpień. Pojedyncze wystąpienie może przetwarzać więcej niż jeden komunikat lub żądanie w tym samym czasie, więc nie ma ustawionego limitu liczby współbieżnych wykonań.
* W przypadku wyzwalaczy HTTP nowe wystąpienia będą przyliczane tylko co 1 sekundę.
* W przypadku wyzwalaczy innych niż HTTP nowe wystąpienia będą przyliczane tylko co 30 sekund.

Różne wyzwalacze mogą również mieć różne limity skalowania, a także opisane poniżej:

* [Centrum zdarzeń](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Najlepsze rozwiązania i wzorce dotyczące skalowalnych aplikacji

Istnieje wiele aspektów aplikacji funkcji, która będzie miała wpływ na wydajność skalowania, w tym konfigurację hosta, rozmiar środowiska uruchomieniowego i efektywność zasobów.  Aby uzyskać więcej informacji, zobacz [sekcję skalowalność artykułu zagadnienia dotyczące wydajności](functions-best-practices.md#scalability-best-practices). Należy również wiedzieć, jak połączenia działają w miarę skalowania aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [jak zarządzać połączeniami w Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Model rozliczania

Rozliczenia dla różnych planów są szczegółowo opisane na [stronie cennika Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Użycie jest agregowane na poziomie aplikacji funkcji i zlicza tylko czas wykonywania kodu funkcji. Następujące jednostki dotyczą rozliczeń:

* **Użycie zasobów w gigabajtach sekund (GB-s)** . Obliczany jako kombinacja rozmiaru pamięci i czasu wykonywania dla wszystkich funkcji w aplikacji funkcji. 
* **Wykonania**. Zliczane za każdym razem, gdy funkcja jest wykonywana w odpowiedzi na wyzwalacz zdarzenia.

Przydatne zapytania i informacje dotyczące sposobu zrozumienia rachunku zużycia można znaleźć [na stronie często zadawanych pytań dotyczących rozliczeń](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limity usługi

W poniższej tabeli przedstawiono limity dotyczące aplikacji funkcji w przypadku uruchamiania w różnych planach hostingu:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
