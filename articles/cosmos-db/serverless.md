---
title: Oferta bezserwerowa oparta na zużyciu w Azure Cosmos DB
description: Dowiedz się więcej o ofercie bezserwerowej opartej na użyciu Azure Cosmos DB.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: d99deea8271a3f9e630a7d2e8cd9f73bd41645a2
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608871"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB bezserwerowe (wersja zapoznawcza)

> [!IMPORTANT]
> Azure Cosmos DB bezserwerowy jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez Umowa dotycząca poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Azure Cosmos DB bezserwerowe umożliwia korzystanie z konta usługi Azure Cosmos w sposób, w którym opłata jest naliczana tylko za jednostki żądań używane przez operacje bazy danych i Magazyn używany przez dane. Użycie Azure Cosmos DB w trybie bezserwerowym nie wiąże się z opłatą minimalną.

> [!IMPORTANT] 
> Czy masz opinię na temat bezserwerowego? Chcemy słyszeć! Śmiało, aby usunąć komunikat do zespołu bezserwerowego Azure Cosmos DB: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

W przypadku korzystania z Azure Cosmos DB każda operacja bazy danych ma koszt wyrażony w [jednostkach żądania](request-units.md). Opłata za ten koszt jest zależna od typu konta usługi Azure Cosmos:

- W trybie [przepływności aprowizacji](set-throughput.md) należy zatwierdzić określoną ilość przepływności (wyrażoną w jednostkach żądań na sekundę), która jest obsługiwana w bazach danych i kontenerach. Koszt operacji wykonywanych w bazie danych jest następnie odejmowany od liczby jednostek żądania dostępnych w każdej sekundzie. Po upływie okresu rozliczeniowego opłata jest naliczana za zainicjowaną przepływność.
- W trybie bezserwerowym nie trzeba inicjować żadnej przepływności podczas tworzenia kontenerów na koncie usługi Azure Cosmos. Na koniec okresu rozliczeniowego opłaty są naliczane za liczbę jednostek żądania, które były zużywane przez operacje bazy danych.

## <a name="use-cases"></a>Przypadki użycia

Azure Cosmos DB najwygodniejsze dla serwera scenariusze, których oczekujesz:

- **Lekki ruch**: ponieważ pojemność aprowizacji w takich sytuacjach nie jest wymagana i może być kosztowna
- **Umiarkowana**możliwość naliczania: ponieważ kontenery bezserwerowe mogą dostarczyć do 5 000 jednostek żądań na sekundę
- **Średnia wydajność**: ponieważ kontenery bezserwerowe mają [określone charakterystyki wydajności](#performance)

Z tego względu należy rozważyć Azure Cosmos DB bezserwerowym dla następujących typów obciążeń:

- Opracowywanie zawartości
- Testowanie
- Tworzenie prototypów
- Weryfikacja koncepcji
- Niekrytyczna aplikacja z lekkim ruchem

Zapoznaj się z artykułem [jak wybrać między zainicjowaną przepływność i bezserwerowym](throughput-serverless.md) systemem, aby uzyskać więcej wskazówek dotyczących wybierania oferty najlepiej dopasowanej do Twojego przypadku użycia.

## <a name="using-serverless-resources"></a>Korzystanie z zasobów bezserwerowych

Bezserwerowy jest nowym typem konta usługi Azure Cosmos, co oznacza, że należy wybrać między **zainicjowaną przepływność** i **bezserwerową** podczas tworzenia nowego konta. Aby rozpocząć pracę z serwerem, należy utworzyć nowe konto bezserwerowe. W wersji zapoznawczej jedynym obsługiwanym sposobem tworzenia nowego konta bezserwerowego jest [użycie Azure Portal](create-cosmosdb-resources-portal.md). Migrowanie istniejących kont do/z trybu bezserwerowego nie jest obecnie obsługiwane.

> [!NOTE]
> Bezserwerowe jest obecnie obsługiwane tylko w interfejsie API Azure Cosmos DB Core (SQL).

Każdy kontener tworzony na koncie bezserwerowym jest kontenerem bezserwerowym. Kontenery bezserwerowe udostępniają te same funkcje co kontenery utworzone w trybie przepływności, dlatego należy czytać, zapisywać i wysyłać zapytania do danych dokładnie tak samo. Jednak bezserwerowe i kontenery mają również określone cechy:

> [!IMPORTANT]
> Niektóre z tych ograniczeń mogą być rozładowaniu lub usuwane, gdy bezserwerowy stał się ogólnie dostępny i **Twoje opinie** pomogą nam podjąć decyzję! Skontaktuj się z nami i przekaż nam więcej informacji na temat środowiska bezserwerowego: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Konto bezserwerowe można uruchomić tylko w jednym regionie świadczenia usługi Azure. Nie można dodać kolejnych regionów świadczenia usługi Azure do konta bezserwerowego po jego utworzeniu.
- Nie można włączyć [funkcji Synapse linku w wersji zapoznawczej](synapse-link.md) na koncie bezserwerowym.
- Przepływność aprowizacji nie jest wymagana w kontenerach bezserwerowych, dlatego mają zastosowanie następujące instrukcje:
    - Nie można przekazać żadnej przepływności podczas tworzenia kontenera bezserwerowego, co spowoduje zwrócenie błędu.
    - Nie można odczytać ani zaktualizować przepływności w kontenerze bezserwerowym, co spowoduje zwrócenie błędu.
    - Nie można utworzyć udostępnionej bazy danych przepływności na koncie bezserwerowym, co spowoduje zwrócenie błędu.
- Kontenery bezserwerowe mogą zapewnić maksymalną przepustowość 5 000 jednostek żądań na sekundę.
- Kontenery bezserwerowe mogą przechowywać maksymalnie 50 GB danych i indeksów.

## <a name="monitoring-your-consumption"></a>Monitorowanie użycia

Jeśli wcześniej korzystano z Azure Cosmos DB w trybie przepływności, okaże się, że bezserwerowy jest tańszy, gdy ruch nie uzasadnia zainicjowanej pojemności. W związku z tym, że Twoje koszty staną się mniej przewidywalne, opłaty są naliczane na podstawie liczby żądań przetworzonych przez bazę danych. W związku z tym ważne jest, aby zachować oczy na bieżącym zużyciu.

Podczas przeglądania okienka **metryk** konta zostanie wyszukany wykres o nazwie **jednostki żądania zużyty** na karcie **Przegląd** . Ten wykres pokazuje, ile jednostek żądania zostało użytych przez Twoje konto:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Wykres przedstawiający zużyte jednostki żądań" border="false":::

Ten sam wykres można znaleźć podczas korzystania z Azure Monitor, zgodnie z opisem w [tym miejscu](monitor-request-unit-usage.md). Należy pamiętać, że Azure Monitor umożliwia konfigurowanie [alertów](../azure-monitor/platform/alerts-metric-overview.md), które mogą być używane do powiadamiania, gdy użycie jednostki żądania przekazało określony próg.

## <a name="performance"></a><a id="performance"></a>Wydajność

Zasoby bezserwerowe mają szczególne cechy wydajności, które różnią się od obsługiwanych zasobów przepływności:

- **Dostępność**: gdy oferta bezserwerowa stanie się ogólnie dostępna, dostępność kontenerów bezserwerowych zostanie objęta umowa dotycząca poziomu usług (SLA) 99,9%, gdy strefy dostępności (nadmiarowość strefy) nie są używane. W przypadku używania Strefy dostępności umowa SLA wynosi 99,99%.
- **Opóźnienie**: po ogólnym udostępnieniu oferty bezserwerowej opóźnienie kontenerów bezserwerowych zostanie objęte przeznaczeniem poziomu usługi (SLO) wynoszącym 10 milisekund lub mniej w przypadku operacji odczytu i 30 milisekund. Operacje odczytu punktu obejmują Pobieranie pojedynczego elementu według jego identyfikatora i wartości klucza partycji.
- Możliwości **przenoszenia: gdy**oferta bezserwerowa będzie ogólnie dostępna, możliwości przenoszenia kontenerów bezserwerowych będą objęte celem poziomu usługi (SLO) wynoszącym 95%. Oznacza to, że maksymalne możliwości można osiągnąć przez co najmniej 95% czasu.

> [!NOTE]
> Jako wersja zapoznawcza platformy Azure usługa Azure Cosmos DB bezserwerowa jest wykluczona z umów dotyczących poziomu usług (SLA). Wymienione powyżej cechy wydajności są dostępne jako wersja zapoznawcza tego, co oferta będzie dostarczana, gdy jest ogólnie dostępna.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z następującymi artykułami:

- [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
- [Wybór między zainicjowaną przepływność a bezserwerowym](throughput-serverless.md)
- [Model cen w usłudze Azure Cosmos DB](how-pricing-works.md)
