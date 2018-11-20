---
title: Co to jest usługa Azure Search | Microsoft Docs
description: Azure Search jest w pełni zarządzaną, hostowaną usługą wyszukiwania w chmurze. Ten artykuł zawiera więcej informacji na temat tej funkcji.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 11/09/2018
ms.author: heidist
ms.openlocfilehash: bf616dda602edff6add5070ad1f1ab85b6a21380
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568975"
---
# <a name="what-is-azure-search"></a>Co to jest usługa Azure Search?
Azure Search jest opartym na chmurze rozwiązaniem typu „wyszukiwanie jako usługa”, które udostępnia deweloperom interfejsy API oraz narzędzia umożliwiające dodawanie zaawansowanych funkcji wyszukiwania do prywatnej, heterogenicznej zawartości w aplikacjach internetowych, mobilnych i firmowych. Wykonywanie zapytań odbywa się na podstawie indeksu zdefiniowanego przez użytkownika.

+ Twórz korpus wyszukiwania zawierający tylko Twoje dane o różnych typach zawartości, pochodzące z wielu platform. 
+ Korzystaj z indeksowania opartego na sztucznej inteligencji, aby wyodrębnić tekst i elementy z plików obrazów lub wydzielić jednostki i frazy kluczowe z tekstu nieprzetworzonego.
+ Twórz intuicyjne środowiska wyszukiwania z nawigacją tworzenia aspektów, filtrami, synonimami, automatycznym uzupełnianiem i analizą tekstu dla automatycznie poprawionych terminów wyszukiwanych funkcji „Czy chodziło Ci o”.
+ Dodaj wyszukiwanie geograficzne do funkcji „Znajdź w pobliżu”, analizatory języka do wyszukiwania pełnotekstowego w językach innych niż angielski oraz logikę oceniania do pozycji wyszukiwania.

Funkcje są uwidaczniane za pośrednictwem prostego [interfejsu API REST](/rest/api/searchservice/) lub [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md), które pozwalają zamaskować złożoność związaną z używaniem pobierania informacji. Oprócz interfejsów API witryna Azure Portal udostępnia funkcje administrowania i zarządzania zawartością oraz narzędzia służące do tworzenia prototypów i wykonywania zapytań względem indeksów. Ponieważ usługa ta działa w chmurze, zarządzaniem infrastrukturą i dostępnością zajmuje się firma Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Podsumowanie funkcji

| Kategoria | Funkcje |
|----------|----------|
|Wyszukiwanie pełnotekstowe i analiza tekstu | [Wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) to główny przypadek użycia w większości aplikacji opartych na funkcjach wyszukiwania. Zapytania można formułować za pomocą obsługiwanej składni. <br/><br/>[**Prosta składnia zapytań**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) zawiera operatory logiczne, operatory wyszukiwania fraz, operatory sufiksów oraz operatory pierwszeństwa.<br/><br/>[**Składnia zapytań Lucene**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) obejmuje wszystkie operacje prostej składni rozszerzone o wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, promowanie terminów i wyrażenia regularne.|
|Wyszukiwanie poznawcze (wersja zapoznawcza) | [Algorytmy oparte na AI](cognitive-search-concept-intro.md) służące do analizowania obrazów i tekstu można stosować do potoku indeksowania, aby wyodrębnić informacje tekstowe z nieprzetworzonej zawartości. Kilka przykładów [wbudowanych możliwości](cognitive-search-predefined-skills.md) to optyczne rozpoznawanie znaków (które pozwala na przeszukiwanie plików JPEG), rozpoznawanie jednostek (identyfikowanie organizacji, nazwy lub lokalizacji) oraz rozpoznawanie kluczowych fraz. W celu dołączenia do potoku można również użyć [możliwości kodu niestandardowego](cognitive-search-create-custom-skill-example.md). |
| Integracja danych | Indeksy usługi Azure Search akceptują dane z dowolnego źródła, o ile mają one strukturę danych JSON. <br/><br/> W przypadku źródeł danych obsługiwanych przez platformę Azure można opcjonalnie używać [**indeksatorów**](search-indexer-overview.md) automatycznie przeszukujących usługę [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) lub [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) dla zawartości, którą można przeszukiwać w podstawowych magazynach danych. Indeksatory obiektów blob platformy Azure mogą przeprowadzać *„łamanie zabezpieczeń dokumentów”* w celu [wyodrębniania tekstu z głównych formatów plików](search-howto-indexing-azure-blob-storage.md), na przykład plików PDF i HTML oraz dokumentów pakietu Microsoft Office. |
| Analiza lingwistyczna | Analizatory to składniki używane do przetwarzania tekstu podczas operacji indeksowania i wyszukiwania. Są dwa typy analizatorów. <br/><br/>[**Niestandardowe analizatory leksykalne**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) są używane w złożonych zapytaniach wyszukiwania korzystających z dopasowania fonetycznego i wyrażeń regularnych. <br/><br/>[**Analizatory języków**](https://docs.microsoft.com/rest/api/searchservice/language-support), opracowane przez firmę Lucene lub Microsoft, są używane do inteligentnej obsługi struktur lingwistycznych, m.in. czasów gramatycznych, rodzajów i rzeczowników z nieregularną liczbą mnogą (na przykład „mouse” i „mice” w języku angielskim), a także rozkładania i dzielenia wyrazów (w przypadku języków, w których nie używa się odstępów). |
| Wyszukiwanie geograficzne | Usługa Azure Search umożliwia przetwarzanie, filtrowanie i wyświetlanie lokalizacji geograficznych. Pozwala ona użytkownikom eksplorować dane na podstawie zbliżenia wyniku wyszukiwania do lokalizacji fizycznej. Aby dowiedzieć się więcej, [obejrzyj ten film](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) lub [zapoznaj się z przykładem](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Funkcje środowiska użytkownika | [**Automatyczne uzupełnianie (wersja zapoznawcza)**](search-autocomplete-tutorial.md) można włączyć, aby ułatwić wpisywanie zapytań na pasku wyszukiwania. <br/><br/>[**Sugestie dotyczące wyszukiwania**](https://docs.microsoft.com/rest/api/searchservice/suggesters) działają również na podstawie tekstu częściowego wpisanego w pasku wyszukiwania, ale wyniki są faktycznymi dokumentami w indeksie, a nie terminami zapytania. <br/><br/>[**Synonimy**](search-synonyms.md) umożliwiają kojarzenie równoważnych terminów, co niejawnie rozszerza zakres zapytania — bez potrzeby podawania terminów alternatywnych przez użytkownika. <br/><br/>[**Nawigacja aspektowa**](search-faceted-navigation.md) jest włączana za pomocą jednego parametru zapytania. Usługa Azure Search zwraca strukturę nawigacji aspektowej, której można użyć jako kodu dla listy kategorii podczas samodzielnego filtrowania (na przykład filtrowania elementów katalogu według ceny i zakresu lub marki). <br/><br/> [**Filtry**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) umożliwiają integrowanie nawigacji aspektowej z interfejsem użytkownika aplikacji, rozbudowywanie zapytań oraz filtrowanie na podstawie kryteriów określonych przez użytkownika lub dewelopera. Do tworzenie filtrów służy składnia OData.<br/><br/> [**Wyróżnianie trafień**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) umożliwia zastosowanie formatowania tekstu do pasującego słowa kluczowego w wynikach wyszukiwania. Można wybrać pola, które zwracają wyróżnione fragmenty.<br/><br/>[**Sortowanie**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) jest dostępne dla wielu pól za pośrednictwem schematu indeksu. Można je przełączać w czasie wykonywania zapytania za pomocą pojedynczego parametru wyszukiwania.<br/><br/> [**Stronicowanie**](search-pagination-page-layout.md) i ograniczanie wyników wyszukiwania jest proste dzięki precyzyjnej kontroli nad wynikami wyszukiwania udostępnianej przez usługę Azure Search.  
| Trafność | [**Proste ocenianie**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) to kluczowa zaleta korzystania z usługi Azure Search. Profile oceniania służą do modelowania trafności jako funkcji wartości w samych dokumentach. Na przykład nowsze produkty lub produkty o obniżonej cenie mogą być wyświetlane na początku wyników wyszukiwania. Do tworzenia profilów oceniania można również używać tagów spersonalizowanej oceny opartych na preferencjach klientów, śledzonych i przechowywanych oddzielnie. |
| Monitorowanie i raportowanie | [**Analiza ruchu wyszukiwania**](search-traffic-analytics.md) jest przeprowadzana w celu uzyskania wglądu w dane wpisywane przez użytkowników w polu wyszukiwania. <br/><br/>Metryki dotyczące liczby zapytań na sekundę, opóźnienia i ograniczania są przechwytywane i udostępniane na stronach portalu bez konieczności konfigurowania dodatkowych ustawień. Można też łatwo monitorować liczbę indeksów i dokumentów, aby w razie potrzeby dostosować pojemność. Aby uzyskać więcej informacji, zobacz [Service administration (Administrowanie usługami)](search-manage.md). |
| Narzędzia służące do tworzenia prototypów i przeprowadzania inspekcji | Portal udostępnia [**kreatora importowania danych**](search-import-data-portal.md) umożliwiającego konfigurowanie indeksatorów, projektanta indeksów służącego do wdrożenia indeksu oraz [**eksploratora wyszukiwania**](search-explorer.md), który pozwala testować zapytania i dostosowywać profile oceniania. Można również otworzyć dowolny indeks, aby wyświetlić jego schemat. |
| Infrastruktura | **Platforma o wysokiej dostępności** zapewnia niezawodne działanie usługi wyszukiwania. W przypadku prawidłowego skalowania [usługa Azure Search gwarantuje dostępność na poziomie 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> Kompleksowe rozwiązanie Azure Search jest **w pełni zarządzane i skalowalne** — nie wymaga żadnych czynności w zakresie zarządzania infrastrukturą. Skalowanie w dwóch wymiarach pozwala dostosować usługę do swoich potrzeb, na przykład zwiększyć miejsce do magazynowania dokumentów czy zapewnić obsługę większych obciążeń zapytaniami.

## <a name="how-to-use-azure-search"></a>Jak używać usługi Azure Search
### <a name="step-1-provision-service"></a>Krok 1. Aprowizowanie usługi
Usługę Azure Search możesz aprowizować w witrynie [Azure Portal](https://portal.azure.com/) lub za pomocą [interfejsu API usługi Azure Resource Management](/rest/api/searchmanagement/). Możesz wybrać wersję bezpłatną, współużytkowaną z innymi subskrybentami, lub [warstwę płatną](https://azure.microsoft.com/pricing/details/search/), która pozwala rezerwować zasoby używane przez konkretną usługę. W warstwach płatnych dostępne są dwa wymiary skalowania usługi: 

- Dodawanie replik w celu zwiększenia pojemności umożliwiającej obsługę dużych obciążeń zapytaniami.   
- Dodawanie partycji w celu zwiększenia magazynu dla dokumentów. 

Dzięki oddzielnej obsłudze magazynu dokumentów i przepływności zapytań możesz kalibrować zasoby na podstawie wymagań środowiska produkcyjnego.

### <a name="step-2-create-index"></a>Krok 2. Tworzenie indeksu
Zanim zaczniesz przekazywać zawartość do przeszukiwania, musisz zdefiniować indeks usługi Azure Search. Indeks przypomina tabelę bazy danych, która przechowuje dane i może akceptować zapytania wyszukiwania. Musisz zdefiniować schemat indeksu odzwierciedlający strukturę dokumentów, które chcesz przeszukiwać, podobnie jak w przypadku pól w bazie danych.

Schemat możesz utworzyć w witrynie Azure Portal lub programowo przy użyciu [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md) lub [interfejsu API REST](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Krok 3. Ładowanie danych
Po zdefiniowaniu indeksu możesz zacząć przekazywać zawartość. Możesz użyć modelu wypychania lub modelu ściągania.

W modelu ściągania dane są pobierane z zewnętrznych źródeł danych. Jest on obsługiwany przez *indeksatory*, które usprawniają i automatyzują aspekty pozyskiwania danych, takie jak łączenie się z danymi oraz ich odczytywanie i serializowanie. [Indeksatory](/rest/api/searchservice/Indexer-operations) są dostępne dla usług Azure Cosmos DB, Azure SQL Database, Azure Blob Storage oraz programu SQL Server hostowanego na maszynie wirtualnej platformy Azure. Indeksator możesz skonfigurować na potrzeby zaplanowanego odświeżania danych lub odświeżania danych na żądanie.

Model wypychania jest oparty na zestawie SDK lub interfejsach API REST i umożliwia wysyłanie zaktualizowanych dokumentów do indeksu. Dane możesz wypychać praktycznie z każdego zestawu danych w formacie JSON. Aby uzyskać wskazówki dotyczące ładowania danych, zobacz [Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents) lub [Jak używać zestawu SDK dla platformy .NET](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Krok 4. Wyszukiwanie
Po wypełnieniu indeksu możesz [wysyłać zapytania wyszukiwania](/rest/api/searchservice/Search-Documents) do punktu końcowego usługi za pomocą prostych żądań HTTP, korzystających z interfejsu API REST lub zestawu SDK dla platformy .NET.

## <a name="how-it-compares"></a>Porównanie z innymi rozwiązaniami

Klienci często pytają, jak usługa Azure Search wypada na tle innych rozwiązań wyszukiwania. Poniższa tabela zawiera podsumowanie podstawowych różnic.

| W porównaniu do | Podstawowe różnice |
|-------------|-----------------|
|Bing | [Interfejs API wyszukiwania w sieci Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) wyszukuje przesłane pasujące terminy w indeksach witryny Bing.com. Indeksy są tworzone na podstawie plików HTML i XML oraz innej zawartości internetowej pochodzącej z publicznych witryn. Na takiej samej zasadzie [Wyszukiwanie niestandardowe Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) udostępnia tę samą przeszukiwarkę dla typów zawartości internetowej, ograniczoną do poszczególnych witryn.<br/><br/>Usługa Azure Search przeszukuje zdefiniowany indeks, wypełniony Twoimi danymi i dokumentami, pochodzącymi często z różnych źródeł. W przypadku niektórych źródeł danych funkcje przeszukiwarki usługi Azure Search korzystają z [indeksatorów](search-indexer-overview.md), ale możesz wypchnąć dowolny dokument JSON zgodny ze schematem indeksu do pojedynczego, skonsolidowanego zasobu, w którym można wyszukiwać. |
|Wyszukiwanie w bazie danych | Wiele platform baz danych udostępnia wbudowane środowisko wyszukiwania. Platforma SQL Server oferuje [wyszukiwanie pełnotekstowe](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Usługa Cosmos DB i podobne technologie mają indeksy odpytywalne. W przypadku oceny produktów, które łączą funkcje wyszukiwania i magazynu, może być trudno wybrać najlepsze rozwiązanie. W przypadku wielu rozwiązań używa się równocześnie: systemu zarządzania bazami danych (DBMS) dla magazynu oraz usługi Azure Search w przypadku specjalnych funkcji wyszukiwania.<br/><br/>W porównaniu z wyszukiwaniem DBMS usługa Azure Search przechowuje zawartość ze źródeł heterogenicznych i oferuje specjalne funkcje przetwarzania tekstu, np. językowe przetwarzanie tekstu (analizę słowotwórczą, lematyzację, formy wyrazów) w [56 językach](https://docs.microsoft.com/rest/api/searchservice/language-support). Obsługuje ona również autokorektę błędnie napisanych wyrazów, [synonimy](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions), [formanty oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [aspekty](https://docs.microsoft.com/azure/search/search-filters-facets)i [tokenizację niestandardową](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). [Aparat wyszukiwania pełnotekstowego](search-lucene-query-architecture.md) usługi Azure Search jest oparty na technologii Apache Lucene — branżowym standardzie pobierania informacji. Gdy usługa Azure Search utrwala dane w formie indeksu odwróconego, rzadko ma to na celu zastąpienie prawdziwego magazynu danych. Więcej informacji znajduje się we [wpisie na forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Użycie zasobów to kolejny etap w tej kategorii. Indeksowanie i niektóre operacje zapytań są często intensywne z obliczeniowego punktu widzenia. Przeniesienie wyszukiwania z DBMS do dedykowanego rozwiązania w chmurze pozwala zachować zasoby systemowe dla przetwarzania transakcyjnego. Dzięki temu można łatwo dostosować skalę do liczby zapytań.|
|Dedykowane rozwiązanie wyszukiwania | Jeśli przyjmiemy, że decydujesz się na dedykowane wyszukiwanie z pełnego zakresu funkcjonalności, ostateczne porównanie w oparciu o kategorie odbywa się miedzy rozwiązaniami lokalnymi a usługą w chmurze. Wiele technologii wyszukiwania umożliwia sterowanie indeksowaniem i potokami zapytań oraz dostęp do zaawansowanej składni zapytań i filtrów. Pozwalają one sterować rangą i trafnością, a także udostępniają funkcje samodzielnego i inteligentnego wyszukiwania. <br/><br/>Usługę w chmurze warto wybrać, jeśli potrzebne jest [gotowe rozwiązanie z regulowaną skalą, które prawie nie wymaga konserwacji i nakładu pracy](#cloud-service-advantage). <br/><br/>W ramach modelu chmury kilku dostawców oferuje porównywalne funkcje bazowe, obejmujące wyszukiwanie pełnotekstowe, wyszukiwanie geograficzne oraz obsługę określonego poziomu niejednoznaczności w danych wejściowych wyszukiwania. Zwykle korzysta się z [wyspecjalizowanej funkcji](#feature-drilldown) lub wybiera się łatwość użytkowania i ogólną prostotę interfejsów API, narzędzi i funkcji zarządzania. |

Usługa Azure Search to najlepszy dostawca chmury pod względem obsługi obciążeń wyszukiwania pełnotekstowego w magazynach zawartości i bazach danych na platformie Azure oraz w aspekcie aplikacji, które korzystają głównie z wyszukiwania zarówno do pobierania informacji, jak i nawigowania po zawartości. 

Oto najważniejsze zalety:

+ Integracja danych platformy Azure (przeszukiwarki) w warstwie indeksowania
+ Witryna Azure Portal umożliwiająca centralne zarządzanie
+ Skalowalność i niezawodność platformy Azure oraz dostępność światowej klasy
+ Analiza językowa i niestandardowa z analizatorami umożliwiającymi spójne wyszukiwanie pełnotekstowe w 56 językach
+ [Podstawowe funkcje, często używane w aplikacjach służących do wyszukiwania](#feature-drilldown): ocenianie, kategoryzowanie, sugestie, synonimy, wyszukiwanie geograficzne i inne.

> [!Note]
> Źródła danych spoza platformy Azure są w pełni obsługiwane, ale nie korzystają z indeksatorów, tylko metodologii wypychania wymagającej użycia większej ilości kodu. Za pomocą interfejsów API można przekazać potok z dowolną kolekcją dokumentów JSON do indeksu usługi Azure Search.

Zastosowania, które pozwalają wykorzystać największą liczbę funkcji usługi Azure Search, obejmują katalogi online, programy biznesowe i aplikacje odnajdywania dokumentów.

## <a name="rest-api--net-sdk"></a>Interfejs API REST | Zestaw SDK platformy .Net

Wiele zadań można wykonać w portalu, natomiast usługa Azure Search jest przeznaczona dla deweloperów, którzy chcą zintegrować funkcję wyszukiwania z istniejącymi aplikacjami. Dostępne są następujące interfejsy programowania.

|Platforma |Opis |
|-----|------------|
|[REST](/rest/api/searchservice/) | Polecenia HTTP obsługiwane przez dowolną platformę i język programowania, w tym Xamarin, Java i JavaScript|
|[Zestaw SDK platformy .NET](search-howto-dotnet-sdk.md) | Otoka .NET dla interfejsu API REST umożliwia efektywne kodowanie w języku C# oraz w innych językach z kodem zarządzanym, przeznaczonych dla platformy .NET |

## <a name="free-trial"></a>Bezpłatna wersja próbna
Subskrybenci platformy Azure mogą [aprowizować usługę w warstwie Bezpłatna](search-create-service-portal.md).

Jeśli nie masz subskrypcji, możesz [otworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Otrzymasz środki umożliwiające wypróbowanie płatnych usług platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.

Możesz też [aktywować korzyści subskrybenta MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) — w ramach subskrypcji MSDN co miesiąc otrzymasz kredyt, który można przeznaczyć na płatne usługi platformy Azure. 

## <a name="how-to-get-started"></a>Jak zacząć

1. Utwórz [bezpłatną usługę](search-create-service-portal.md). Wszystkie samouczki i przewodniki Szybki Start można ukończyć, korzystając z bezpłatnej usługi.

2. Wykonaj kroki opisane w [samouczku dotyczącym korzystania z wbudowanych narzędzi do indeksowania i tworzenia zapytań](search-get-started-portal.md). Pozwoli ci to zapoznać się z ważnymi pojęciami oraz informacjami udostępnianymi w portalu.

3. Rozpocznij pracę z kodem, korzystając z platformy .NET lub interfejsu API REST:

  + [Jak używać zestawu SDK dla platformy .NET](search-howto-dotnet-sdk.md) — przedstawia podstawowy przepływ pracy w kodzie zarządzanym.  
  + [Wprowadzenie do interfejsu API REST](https://github.com/Azure-Samples/search-rest-api-getting-started) — zawiera te same kroki, wykonywane za pomocą interfejsu API REST. Podczas pracy z tym przewodnikiem Szybki Start interfejsy API REST można również wywoływać przy użyciu narzędzia Postman lub Fiddler — zobacz [Odkrywanie interfejsów API REST usługi Azure Search](search-fiddler.md).

## <a name="watch-this-video"></a>Obejrzyj ten film wideo

Wyszukiwarki to podstawowe instrumenty do pobierania informacji w internecie, aplikacjach mobilnych i firmowych magazynach danych. Usługa Azure Search udostępnia narzędzia umożliwiające utworzenie środowiska wyszukiwania o podobnej funkcjonalności co w dużych, komercyjnych witrynach internetowych.

W tym 9-minutowym filmie wideo menedżer programu Liam Cavanagh przedstawia korzyści związane z integracją wyszukiwarki z aplikacją. Film zawiera krótkie pokazy najważniejszych funkcji usługi Azure Search oraz przedstawia typowy przepływ pracy. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0–3 minuta — omówienie kluczowych funkcji i przypadków użycia.
+ 3–4 minuta — omówienie aprowizowania usługi. 
+ 4–6 minuta — omówienie kreatora importowania danych używanego do utworzenia indeksu na podstawie wbudowanego zestawu danych dotyczących nieruchomości.
+ 6–9 minuta — omówienie eksploratora wyszukiwania i różnych zapytań.