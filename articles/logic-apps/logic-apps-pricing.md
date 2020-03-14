---
title: Cennik & model rozliczeń
description: Omówienie sposobu działania modelu cen i rozliczeń dla Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270461"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Model cen dla Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) pomaga tworzyć i uruchamiać zautomatyzowane przepływy pracy integracji, które można skalować w chmurze. W tym artykule opisano sposób działania rozliczeń i cen dla Azure Logic Apps. Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Model cen zużycia

W przypadku nowych aplikacji logiki, które są uruchamiane w publicznej lub globalnej usłudze Azure Logic Apps, płacisz tylko za to, czego używasz. Te aplikacje logiki korzystają z planu i modelu cen opartego na zużyciu. W aplikacji logiki każdy krok jest akcją, a Azure Logic Apps Gazomierze wszystkie akcje, które są uruchamiane w aplikacji logiki.

Na przykład akcje obejmują:

* Wyzwalacze, które są specjalnymi akcjami. Wszystkie aplikacje logiki wymagają wyzwalacza jako pierwszy krok.
* ["Wbudowane" lub natywne akcje](../connectors/apis-list.md#built-in) , takie jak http, wywołania do Azure Functions i API Management itd.
* Wywołania [łączników zarządzanych](../connectors/apis-list.md#managed-connectors) , takich jak Outlook 365, Dropbox itd.
* Kroki przepływu sterowania, takie jak pętle, instrukcje warunkowe i tak dalej

[Standardowe łączniki](../connectors/apis-list.md#managed-connectors) są rozliczane według [standardowej ceny łącznika](https://azure.microsoft.com/pricing/details/logic-apps). Ogólnie dostępne [Łączniki Enterprise](../connectors/apis-list.md#managed-connectors) są obciążane [kosztami łącznika przedsiębiorstwa](https://azure.microsoft.com/pricing/details/logic-apps), podczas gdy w publicznej wersji zapoznawczej Łączniki przedsiębiorstwa są obciążani opłatami za [Standardowy łącznik](https://azure.microsoft.com/pricing/details/logic-apps).

Dowiedz się więcej o tym, jak rozliczenia działają dla [wyzwalaczy](#triggers) i [akcji](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Stały model cen

[ *Środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zapewnia izolowaną metodę tworzenia i uruchamiania aplikacji logiki, które mogą uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure. W przypadku nowych aplikacji logiki, które są uruchamiane w ramach usługi ISE, płacisz [stałą cenę miesięczną](https://azure.microsoft.com/pricing/details/logic-apps) za następujące możliwości:

* [Wbudowane](../connectors/apis-list.md#built-in) wyzwalacze i akcje

  W ramach ISE wbudowane wyzwalacze i akcje wyświetlają **podstawową** etykietę i działają w tym samym ISE, co Aplikacje logiki.

* Łączniki [standardowe](../connectors/apis-list.md#managed-connectors) i łączniki [Enterprise](../connectors/apis-list.md#enterprise-connectors) (tak wiele połączeń w przedsiębiorstwie, ile potrzebujesz)

   Łączniki standardowe i Enterprise, które wyświetlają etykietę **ISE** , działają w tym samym ISE, jak aplikacje logiki. Łączniki, które nie wyświetlają etykiety ISE w globalnej usłudze Logic Apps. Stałe ceny miesięczne dotyczą również łączników, które są uruchamiane w usłudze globalnej, gdy są używane z aplikacjami logiki, które działają w ISE.

* Użycie [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) bez dodatkowych kosztów na podstawie [jednostki SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **Jednostka SKU Premium**: pojedyncze konto integracji [warstwy standardowej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Jednostka SKU dla deweloperów**: pojedyncze konto integracji w [warstwie Bezpłatna](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Każda jednostka SKU ISE jest ograniczona do 5 łącznych kont integracji. Aby uzyskać dodatkowe koszty, możesz mieć więcej kont integracji opartych na ISE SKU:

  * **Jednostka SKU Premium**: do czterech bardziej standardowych kont. Brak kont bezpłatnych lub podstawowych.

  * **Jednostka SKU dla deweloperów**: maksymalnie 4 więcej kont standardowych lub maksymalnie 5 standardowych kont Standard. Brak kont podstawowych.

  Aby uzyskać więcej informacji na temat limitów kont integracji, zobacz [Logic Apps limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Więcej informacji na temat [warstw kont integracji i ich modelu cen](#integration-accounts) można znaleźć w dalszej części tego tematu.

W przypadku wybrania jednostki SKU Premium ISE jednostka podstawowa ma stałą pojemność. Jeśli potrzebujesz większej przepływności, możesz [dodać więcej jednostek skalowania](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)podczas tworzenia lub później. Jednostka SKU ISE dewelopera nie ma możliwości dodawania większej liczby jednostek skalowania. Aplikacje logiki, które działają w ISE, nie wiążą się z kosztami przechowywania danych.

Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Łączniki

Łączniki Azure Logic Apps pomagają aplikacji logiki uzyskiwać dostęp do aplikacji, usług i systemów w chmurze lub lokalnie, dostarczając [wyzwalacze](#triggers), [Akcje](#actions)lub obie te funkcje. Łączniki są klasyfikowane jako standardowe lub Enterprise. Aby zapoznać się z omówieniem tych łączników, zobacz [Łączniki dla Azure Logic Apps](../connectors/apis-list.md). Jeśli nie są dostępne wstępnie skompilowane łączniki dla interfejsów API REST, które mają być używane w aplikacjach logiki, można utworzyć [Łączniki niestandardowe](https://docs.microsoft.com/connectors/custom-connectors), które są tylko otokami otaczającymi te interfejsy API REST. Łączniki niestandardowe są rozliczane jako łączniki standardowe. Poniższe sekcje zawierają więcej informacji na temat sposobu działania rozliczeń dla wyzwalaczy i akcji.

<a name="triggers"></a>

## <a name="triggers"></a>Wyzwalacze

Wyzwalacze to specjalne akcje, które tworzą wystąpienie aplikacji logiki, gdy wystąpi określone zdarzenie. Wyzwalacze działają na różne sposoby, które mają wpływ na sposób mierzenia aplikacji logiki. Oto różne rodzaje wyzwalaczy, które istnieją w Azure Logic Apps:

* **Wyzwalacz sondowania**: ten wyzwalacz stale sprawdza punkt końcowy dla komunikatów, które spełniają kryteria tworzenia wystąpienia aplikacji logiki i uruchamiania przepływu pracy. Nawet wtedy, gdy nie zostanie utworzone żadne wystąpienie aplikacji logiki, Logic Apps gazomierzy każde żądanie sondowania jako wykonanie. Aby określić interwał sondowania, skonfiguruj wyzwalacz za pomocą projektanta aplikacji logiki.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Wyzwalacz elementu webhook**: ten wyzwalacz czeka na wysłanie żądania do określonego punktu końcowego przez klienta. Każde żądanie wysyłane do punktu końcowego elementu webhook jest traktowane jako wykonanie akcji. Na przykład żądanie i wyzwalacz elementu webhook protokołu HTTP to wyzwalacze elementu webhook.

* **Wyzwalacz cyklu**: ten wyzwalacz tworzy wystąpienie aplikacji logiki na podstawie interwału cyklu, który został skonfigurowany w wyzwalaczu. Na przykład można skonfigurować wyzwalacz cykliczny, który jest uruchamiany co trzy dni lub bardziej skomplikowany harmonogram.

<a name="actions"></a>

## <a name="actions"></a>Akcje

Azure Logic Apps mierników "wbudowanych", takich jak HTTP, jako natywnych akcji. Na przykład akcje wbudowane obejmują wywołania HTTP, wywołania z Azure Functions lub API Management i kroki przepływu sterowania, takie jak warunki, pętle i instrukcje Switch. Każda akcja ma własny typ akcji. Na przykład akcje wywołujące [Łączniki](https://docs.microsoft.com/connectors) mają typ "ApiConnection". Te łączniki są klasyfikowane jako łączniki standardowe lub Enterprise, które są mierzone w oparciu o ich odpowiednie [ceny](https://azure.microsoft.com/pricing/details/logic-apps). Łączniki korporacyjne w *wersji zapoznawczej* są rozliczone jako łączniki standardowe.

Azure Logic Apps Gazomierze wszystkie pomyślne i niepomyślne akcje jako wykonania. Niemniej jednak Logic Apps nie mierzą następujących działań:

* Akcje, które zostaną pominięte ze względu na warunki niewypełnienia
* Akcje, które nie są uruchamiane, ponieważ aplikacja logiki została zatrzymana przed zakończeniem

W przypadku akcji uruchamianych wewnątrz pętli Azure Logic Apps zlicza każdą akcję dla każdego cyklu w pętli. Załóżmy na przykład, że masz pętlę "for each", która przetwarza listę. Logic Apps gazomierzy w tej pętli przez pomnożenie liczby elementów listy z liczbą akcji w pętli i dodanie akcji rozpoczynającej pętlę. Dlatego obliczenia dla listy 10 elementów są (10 * 1) + 1, co skutkuje 11 wykonaniami akcji.

## <a name="disabled-logic-apps"></a>Wyłączone Aplikacje logiki

Wyłączone Aplikacje logiki nie są rozliczone, ponieważ nie mogą tworzyć nowych wystąpień, gdy są one wyłączone. Po wyłączeniu aplikacji logiki wszystkie aktualnie uruchomione wystąpienia mogą upłynąć trochę czasu, zanim zostaną całkowicie zatrzymane.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Konta integracji

[Stały model cen](https://azure.microsoft.com/pricing/details/logic-apps) ma zastosowanie do [kont integracji](logic-apps-enterprise-integration-create-integration-account.md) , w których można eksplorować, opracowywać i testować funkcje [B2B i EDI](logic-apps-enterprise-integration-b2b.md) i [przetwarzania XML](logic-apps-enterprise-integration-xml.md) w Azure Logic Apps bez dodatkowych kosztów. Każda subskrypcja platformy Azure może mieć do [określonego limitu kont integracji](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Każde konto integracji może przechowywać do określonego [limitu artefaktów, takich](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)jak partnerzy handlowi, umowy, mapy, schematy, zestawy, certyfikaty, konfiguracje wsadowe i tak dalej.

Azure Logic Apps oferuje bezpłatne, podstawowe i standardowe konta integracji. Warstwy Basic i standard są obsługiwane przez Logic Apps umowy dotyczącej poziomu usług (SLA), natomiast warstwa Bezpłatna nie jest obsługiwana przez umowę SLA i ma limity dotyczące przepływności i użycia. Z wyjątkiem kont integracji w warstwie Bezpłatna możesz mieć więcej niż jedno konto integracji w każdym regionie świadczenia usługi Azure. Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/).

Jeśli masz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)— [Premium lub Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), Twoje ISE może mieć 5 łącznych kont integracji. Aby dowiedzieć się, jak działa stały model cen dla ISE, zobacz sekcję poprzedni [model cen stałych](#fixed-pricing) w tym temacie. Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps).

Aby wybrać jedną z kont integracji bezpłatnej, podstawowej lub standardowej, zapoznaj się z następującymi opisami przypadków użycia:

* **Bezpłatnie**: w przypadku, gdy chcesz wypróbować scenariusze poznawcze, a nie w scenariuszach produkcyjnych

* **Podstawowa**: w przypadku, gdy ma być obsługiwana obsługa komunikatów lub działać jako mały partner biznesowy, który ma relację partnera handlowego z większą jednostką biznesową

* **Standard**: w przypadku bardziej złożonych relacji B2B i większej liczby jednostek, które muszą być zarządzane

<a name="data-retention"></a>

## <a name="data-retention"></a>Przechowywanie danych

Z wyjątkiem aplikacji logiki, które są uruchamiane w środowisku usługi integracji (ISE), wszystkie dane wejściowe i wyjściowe, które są przechowywane w historii uruchamiania aplikacji logiki, są rozliczane na podstawie [okresu przechowywania uruchomienia](logic-apps-limits-and-config.md#run-duration-retention-limits)aplikacji logiki. Aplikacje logiki, które działają w ISE, nie wiążą się z kosztami przechowywania danych. Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps).

Aby ułatwić monitorowanie użycia magazynu aplikacji logiki, możesz:

* Wyświetl liczbę jednostek magazynowych w GB, które są używane przez aplikację logiki miesięcznie.
* Wyświetl rozmiary danych wejściowych i wyjściowych określonej akcji w historii uruchamiania aplikacji logiki.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Wyświetlanie użycia magazynu aplikacji logiki

1. W Azure Portal Znajdź i Otwórz aplikację logiki.

1. Z menu aplikacji logiki w obszarze **monitorowanie**wybierz pozycję **metryki**.

1. W okienku po prawej stronie w obszarze **tytuł wykresu**z listy **Metryka** wybierz pozycję **użycie rozliczenia dla wykonań zużycia magazynu**.

   Ta Metryka zapewnia liczbę jednostek zużycia magazynu w GB miesięcznie, w których są naliczane opłaty.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Wyświetlanie rozmiarów danych wejściowych i wyjściowych akcji

1. W Azure Portal Znajdź i Otwórz aplikację logiki.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W okienku po prawej stronie w obszarze **historia uruchamiania**wybierz przebieg zawierający dane wejściowe i wyjściowe, które chcesz sprawdzić.

1. W obszarze **Uruchom aplikację logiki**wybierz pozycję **Uruchom szczegóły**.

1. W okienku **szczegółów przebiegu aplikacji logiki** w tabeli Actions (akcje), która zawiera listę stanów i czas trwania akcji, należy wybrać akcję, która ma zostać wyświetlona.

1. W okienku **Akcja aplikacji logiki** Znajdź rozmiary danych wejściowych tej akcji, a dane wyjściowe są wyświetlane odpowiednio w obszarze **link do danych wejściowych** i **wyjściowych**.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o Azure Logic Apps](logic-apps-overview.md)
* [Tworzenie pierwszej aplikacji logiki](quickstart-create-first-logic-app-workflow.md)
