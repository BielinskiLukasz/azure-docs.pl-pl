---
title: Co to jest Azure Cosmos DB magazyn analityczny (wersja zapoznawcza)?
description: Dowiedz się więcej na temat Azure Cosmos DB transakcyjnych (opartych na wierszach) i analitycznych (opartych na kolumnach). Zalety magazynu analitycznego, wpływ na wydajność obciążeń o dużej skali oraz automatyczne synchronizowanie danych z magazynu transakcyjnego do magazynu analitycznego
author: SriChintala
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: a6f486f15fb5967dfb14508115e2340e4953be81
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85116030"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>Co to jest Azure Cosmos DB magazyn analityczny (wersja zapoznawcza)?

> [!IMPORTANT]
> Magazyn analityczny Azure Cosmos DB jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Magazyn analityczny Azure Cosmos DB to w pełni izolowany magazyn kolumn służący do włączania analiz dużej skali względem danych operacyjnych w Azure Cosmos DB, bez wpływu na obciążenia transakcyjne.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Wyzwania związane z analizą danych operacyjnych na dużą skalę

Dane operacyjne dla wielomodelowego kontenera Azure Cosmos DB są przechowywane wewnętrznie w indeksowanym wierszu "magazyn transakcyjny". Format magazynu wierszy został zaprojektowany tak, aby umożliwiał szybkie odczyty transakcyjne i zapisy w kolejności czasu odpowiedzi w milisekundach i zapytaniach operacyjnych. Jeśli zestaw danych powiększa duże, złożone zapytania analityczne mogą być kosztowne pod względem alokowanej przepływności danych przechowywanych w tym formacie. Duże użycie zainicjowanej przepływności ma wpływ na wydajność obciążeń transakcyjnych używanych przez aplikacje i usługi w czasie rzeczywistym.

Tradycyjnie, aby analizować duże ilości danych, dane operacyjne są wyodrębniane z magazynu transakcyjnego Azure Cosmos DB i przechowywane w oddzielnej warstwie danych. Na przykład dane są przechowywane w magazynie danych lub w usłudze Data Lake w odpowiednim formacie. Te dane są później używane do analizy na dużą skalę i analizowane przy użyciu aparatu obliczeniowego, takiego jak klastry Apache Spark. Takie rozdzielenie warstwowych magazynów analitycznych i obliczeniowych z danych operacyjnych skutkuje dodatkowym opóźnieniem, ponieważ potoki ETL (wyodrębnianie, przekształcanie, ładowanie) są uruchamiane rzadziej, aby zminimalizować potencjalny wpływ obciążeń transakcyjnych.

Potoki ETL są również złożone w przypadku obsługi aktualizacji danych operacyjnych w porównaniu z obsługą tylko nowo wprowadzonych danych operacyjnych. 

## <a name="column-oriented-analytical-store"></a>Magazyn analityczny zorientowany na kolumny

Azure Cosmos DB magazyn analityczny rozwiązuje problemy z złożonością i opóźnieniem występujące w tradycyjnych potokach ETL. Magazyn analityczny Azure Cosmos DB może automatycznie synchronizować dane operacyjne w oddzielnym magazynie kolumn. Format magazynu kolumn jest odpowiedni dla kwerend analitycznych o dużej skali, które mają być wykonywane w sposób zoptymalizowany, co poprawia opóźnienia takich zapytań.

Korzystając z linku Synapse platformy Azure, możesz teraz tworzyć rozwiązania No-ETL HTAP przez bezpośrednie łączenie Azure Cosmos DB się z magazynem analitycznym w usłudze Synapse Analytics. Umożliwia ona uruchamianie w czasie niemal rzeczywistym analiz na dużą skalę na danych operacyjnych.

## <a name="analytical-store-details"></a>Szczegóły magazynu analitycznego

Po włączeniu magazynu analitycznego w kontenerze Azure Cosmos DB nowy magazyn kolumn jest tworzony wewnętrznie na podstawie danych operacyjnych w kontenerze. Ten magazyn kolumn jest utrwalany niezależnie od magazynu transakcyjnego zorientowanego na wiersze dla tego kontenera. Operacje wstawiania, aktualizacji i usuwania danych operacyjnych są automatycznie synchronizowane z magazynem analitycznym. Nie potrzebujesz kanału informacyjnego zmiany ani ETL do synchronizowania danych.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Magazyn kolumn dla obciążeń analitycznych dotyczących danych operacyjnych

Obciążenia analityczne zwykle obejmują agregacje i sekwencyjne skanowanie wybranych pól. Przechowywanie danych w kolejności najważniejszych kolumn — magazyn analityczny pozwala na Serializowanie grupy wartości dla każdego pola. Ten format zmniejsza liczbę operacji we/wy wymaganą do skanowania lub obliczania statystyk dla określonych pól. Znacznie skraca czas odpowiedzi na zapytania w przypadku skanowania w dużych zestawach danych. 

Na przykład jeśli tabele operacyjne mają następujący format:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Przykładowa tabela operacyjna" border="false":::

Magazyn wierszy zachowuje powyższe dane w serializowanym formacie, na jeden wiersz na dysku. Ten format umożliwia szybsze operacje odczytu, zapisu i działania, takie jak "Zwróć informacje o Product1". Jednak w miarę wzrostu zestawu danych i, jeśli chcesz uruchomić złożone zapytania analityczne dotyczące danych, może to być kosztowne. Jeśli na przykład chcesz uzyskać "trendy sprzedaży dla produktu w kategorii o nazwie" sprzęt "w różnych jednostkach i miesiącach", musisz uruchomić złożone zapytanie. Duże skany w tym zestawie danych mogą być kosztowne w sensie zainicjowanej przepływności i mogą również wpływać na wydajność obciążeń transakcyjnych, które umożliwiają działanie aplikacji i usług w czasie rzeczywistym.

Magazyn analityczny, który jest magazynem kolumn, jest lepiej dostosowany do takich zapytań, ponieważ deserializacji podobne pola danych i zmniejsza liczbę operacji we/wy dysku.

Na poniższej ilustracji przedstawiono magazyn wierszy transakcyjnych i analitycznego magazynu kolumn w Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Magazyn wierszy transakcyjnych i magazyn kolumn analitycznych w Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Oddzielona wydajność obciążeń analitycznych

Nie ma to wpływu na wydajność obciążeń transakcyjnych ze względu na zapytania analityczne, ponieważ magazyn analityczny jest oddzielony od magazynu transakcyjnego.  Magazyn analityczny nie potrzebuje oddzielnych jednostek żądania (jednostek ru) do przydzielenia.

### <a name="auto-sync"></a>Autosynchronizacja

Automatyczna synchronizacja dotyczy w pełni zarządzanej funkcji Azure Cosmos DB, w której operacje wstawiania, aktualizacji i usuwania danych operacyjnych są automatycznie synchronizowane z magazynu transakcyjnego do magazynu analitycznego niemal w czasie rzeczywistym w ciągu 5 minut.

Funkcja autosynchronizacji wraz z magazynem analitycznym zapewnia następujące korzyści:

#### <a name="scalability--elasticity"></a>Elastyczność & skalowalności

Korzystając z partycjonowania poziomego, Azure Cosmos DB magazyn transakcyjny może elastycznie skalować magazyn i przepływność bez przestojów. Partycjonowanie poziome w magazynie transakcyjnym zapewnia skalowalność & elastyczność w ramach autosynchronizacji, aby upewnić się, że dane są synchronizowane z magazynem analitycznym niemal w czasie rzeczywistym. Synchronizacja danych odbywa się niezależnie od transakcyjnej przepływności ruchu, niezależnie od tego, czy jest to 1000 operacji/s czy 1 000 000 operacji/s, i nie ma wpływu na przepływność administracyjną w magazynie transakcyjnym. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatycznie Obsługuj aktualizacje schematu

Magazyn transakcyjny Azure Cosmos DB to Schema-niezależny od i umożliwia iteracyjne wykonywanie iteracji w aplikacjach transakcyjnych bez konieczności rozwiązywania problemów ze schematem lub zarządzaniem indeksem. W przeciwieństwie do tego Azure Cosmos DB magazyn analityczny jest schematized do optymalizacji pod kątem wydajności zapytań analitycznych. Dzięki funkcji autosynchronizacji Program Azure Cosmos DB zarządza wnioskami o schemacie w porównaniu z najnowszymi aktualizacjami ze sklepu transakcyjnego.  Zarządza również reprezentacją schematu w magazynie analitycznym, który obejmuje obsługę zagnieżdżonych typów danych.

W przypadku ewolucji schematu, gdy nowe właściwości są dodawane z upływem czasu, magazyn analityczny automatycznie przedstawia schemat Union dla wszystkich schematów historycznych w magazynie transakcyjnym.

Jeśli wszystkie dane operacyjne w Azure Cosmos DB są zgodne z dobrze zdefiniowanym schematem analitycznym, schemat zostanie automatycznie wywnioskowany i reprezentowany prawidłowo w magazynie analitycznym. Jeśli dobrze zdefiniowany schemat analityczny, zgodnie z definicją poniżej, został naruszony przez pewne elementy, nie zostaną one uwzględnione w magazynie analitycznym. Jeśli masz zablokowane scenariusze ze względu na dobrze zdefiniowany schemat dla definicji analitycznej, Wyślij wiadomość e-mail do [zespołu Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

Dobrze zdefiniowany schemat dla analiz został zdefiniowany z następującymi kwestiami:

* Właściwość zawsze ma ten sam typ w wielu elementach

  * Na przykład nie `{"a":123} {"a": "str"}` ma dobrze zdefiniowanego schematu, ponieważ `"a"` jest czasami ciągiem i czasami liczbą. 
  
    W takim przypadku magazyn analityczny rejestruje typ danych `“a”` jako typ danych `“a”` w pierwszym elemencie w okresie istnienia kontenera. Elementy, w których typ danych `“a”` różni się nie zostaną uwzględnione w magazynie analitycznym.
  
    Ten warunek nie ma zastosowania do właściwości o wartości null. Na przykład, `{"a":123} {"a":null}` jest nadal zdefiniowane.

* Typy tablic muszą zawierać pojedynczy powtórzony typ

  * Na przykład, `{"a": ["str",12]}` nie jest dobrze zdefiniowanym schematem, ponieważ tablica zawiera kombinację liczb całkowitych i typów ciągów

* Istnieje maksymalnie 200 właściwości na dowolnym poziomie zagnieżdżenia schematu i Maksymalna głębokość zagnieżdżenia wynosząca 5

  * Element o właściwościach 201 na najwyższym poziomie nie ma dobrze zdefiniowanego schematu.

  * Element o więcej niż pięciu zagnieżdżonych poziomach w schemacie również nie ma dobrze zdefiniowanego schematu. Na przykład: `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* Nazwy właściwości są unikatowe w porównaniu z wielkością liter

  * Na przykład następujące elementy nie mają dobrze zdefiniowanego schematu `{"Name": "fred"} {"name": "john"}` — `"Name"` i `"name"` są takie same w porównaniu z wielkością liter

### <a name="cost-effective-archival-of-historical-data"></a>Ekonomiczne archiwizowanie danych historycznych

Obsługa warstw danych dotyczy rozdzielania danych między infrastrukturami magazynu zoptymalizowanymi pod kątem różnych scenariuszy. W efekcie zwiększenie ogólnej wydajności i efektywności kosztu kompleksowego stosu danych. W przypadku magazynu analitycznego Azure Cosmos DB obsługuje automatyczne warstwy danych ze sklepu transakcyjnego do magazynu analitycznego z różnymi układami danych. Dzięki magazynowi analitycznemu zoptymalizowanemu pod względem kosztów magazynu w porównaniu z magazynem transakcyjnym można zachować znacznie dłuższy Horizons danych operacyjnych na potrzeby analizy historycznej.

Po włączeniu magazynu analitycznego, w zależności od potrzeb przechowywania danych w ramach obciążeń transakcyjnych, można skonfigurować właściwość "czas magazynu transakcyjnego na żywo" (transakcyjna wartość czasu wygaśnięcia), aby rekordy były automatycznie usuwane ze sklepu transakcyjnego po upływie określonego czasu. Analogicznie "czas trwania magazynu analitycznego (analityczne TTL)" pozwala zarządzać cyklem życia danych przechowywanych w magazynie analitycznym niezależnym od magazynu transakcyjnego. Włączając magazyn analityczny i konfigurując właściwości czasu wygaśnięcia, można bezproblemowo warstwować i definiować okres przechowywania danych dla dwóch magazynów.

### <a name="global-distribution"></a>Dystrybucja globalna

Jeśli masz konto Azure Cosmos DB dystrybuowane globalnie, po włączeniu magazynu analitycznego dla kontenera będzie ono dostępne we wszystkich regionach tego konta.  Wszelkie zmiany danych operacyjnych są replikowane globalnie we wszystkich regionach. Możesz efektywnie uruchamiać zapytania analityczne w stosunku do najbliższej kopii regionalnej danych w Azure Cosmos DB.

### <a name="security"></a>Zabezpieczenia

Uwierzytelnianie za pomocą magazynu analitycznego jest takie samo jak w przypadku magazynu transakcyjnego dla danej bazy danych. Do uwierzytelniania można użyć kluczy głównych lub tylko do odczytu. Możesz użyć połączonej usługi w programie Synapse Studio, aby zapobiec wklejaniu kluczy Azure Cosmos DB w notesach platformy Spark. Dostęp do tej połączonej usługi jest dostępny dla każdego, kto ma dostęp do obszaru roboczego.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Obsługa wielu środowisk uruchomieniowych usługi Azure Synapse Analytics

Magazyn analityczny jest zoptymalizowany pod kątem zapewnienia skalowalności, elastyczności i wydajności obciążeń analitycznych bez żadnej zależności od czasu wykonywania obliczeń. Technologia magazynowania jest samozarządzana w celu optymalizacji obciążeń związanych z analizą bez ręcznych działań.

Poprzez oddzielenie systemu magazynu analitycznego od systemu obliczeń analitycznych dane w Azure Cosmos DB magazyn analityczny mogą być wysyłane jednocześnie z różnych środowisk uruchomieniowych analizy obsługiwanych przez usługę Azure Synapse Analytics. Obecnie program Synapse Analytics obsługuje Apache Spark i program SQL Server z Azure Cosmos DB magazynem analitycznym.

> [!NOTE]
> Można odczytywać tylko z magazynu analitycznego, używając czasu wykonywania analizy Synapse. Dane można zapisać z powrotem do magazynu transakcyjnego jako obsługujący warstwę.

## <a name="pricing"></a><a id="analytical-store-pricing"></a>Wpisaną

Magazyn analityczny jest zgodny z modelem cen opartym na zużyciu, w którym opłata jest naliczana za:

* Magazyn: ilość danych przechowywanych w magazynie analitycznym co miesiąc, łącznie z danymi historycznymi zdefiniowanymi przez analityczny czas wygaśnięcia.

* Operacje zapisu analitycznego: w pełni zarządzana synchronizacja aktualizacji danych operacyjnych z magazynem analitycznym ze sklepu transakcyjnego (Autosynchronizacja)

* Operacje odczytu analitycznego: operacje odczytu wykonywane względem magazynu analitycznego z Synapse Analytics Spark i programu SQL Server.

> [!NOTE]
> Magazyn analityczny Azure Cosmos DB jest dostępny w publicznej wersji zapoznawczej bezpłatnie do 30 sierpnia 2020.

Cennik sklepu analitycznego jest oddzielony od modelu cen magazynu transakcji. W magazynie analitycznym nie ma koncepcji jednostek rud. Aby uzyskać szczegółowe informacje o modelu cen dla magazynu analitycznego, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

Aby uzyskać szacunkowy koszt wysokiego poziomu, aby umożliwić magazyn analityczny w kontenerze Azure Cosmos DB, można użyć [terminarza wydajności Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) i uzyskać szacunkowy koszt magazynu analitycznego i operacji zapisu. Koszty operacji odczytu analitycznego są zależne od cech obciążenia analizy, ale w przypadku oszacowania wysokiego poziomu skanowanie 1 TB danych w magazynie analitycznym zwykle skutkuje wynikiem operacji odczytu analitycznego 130 000, a wynikiem jest koszt $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a>Czas trwania analizy (TTL)

Analityczny czas wygaśnięcia wskazuje, jak długo dane mają być przechowywane w magazynie analitycznym dla kontenera. 

Operacje wstawiania, aktualizacji, usuwania do danych operacyjnych są automatycznie synchronizowane z magazynu transakcyjnego do magazynu analitycznego, niezależnie od transakcyjnej konfiguracji czasu wygaśnięcia (TTL). Przechowywanie danych operacyjnych w magazynie analitycznym może być kontrolowane przez analityczną wartość czasu wygaśnięcia na poziomie kontenera, jak określono poniżej:

Analityczny czas wygaśnięcia dla kontenera jest ustawiany przy użyciu `AnalyticalStoreTimeToLiveInSeconds` Właściwości:

* Jeśli wartość jest równa "0", brak (lub ma wartość null): Magazyn analityczny jest wyłączony i żadne dane nie są replikowane z magazynu transakcyjnego do magazynu analitycznego

* Jeśli jest obecny i wartość jest równa "-1": Magazyn analityczny zachowuje wszystkie dane historyczne, niezależnie od przechowywania danych w magazynie transakcyjnym. To ustawienie wskazuje, że magazyn analityczny ma nieskończone przechowywanie danych operacyjnych

* Jeśli jest obecny, a wartość jest równa liczbie dodatniej "n": elementy wygaśnie z magazynu analitycznego "n" s po ostatniej modyfikacji w magazynie transakcyjnym. Tego ustawienia można użyć, jeśli chcesz przechowywać dane operacyjne przez ograniczony czas w magazynie analitycznym, niezależnie od przechowywania danych w magazynie transakcyjnym.

Oto niektóre ważne kwestie:
*   Gdy magazyn analityczny jest włączony z wartością analityczną TTL, można go później zaktualizować do innej prawidłowej wartości. 
*   Gdy transakcyjny czas wygaśnięcia można ustawić na poziomie kontenera lub elementu, analityczny czas TTL można ustawić tylko na poziomie kontenera.
*   Możesz uzyskać więcej przechowywania danych operacyjnych w magazynie analitycznym, ustawiając wartości w polu analityczny czas wygaśnięcia >= transakcyjna wartość czasu wygaśnięcia na poziomie kontenera.
*   Magazyn analityczny może być tworzony w celu dublowania magazynu transakcyjnego przez ustawienie wartości czas wygaśnięcia (TTL).

Aby dowiedzieć się więcej, zobacz [jak skonfigurować analityczny czas wygaśnięcia dla kontenera](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz następujące dokumenty:

* [Link Synapse platformy Azure dla Azure Cosmos DB](synapse-link.md)

* [Rozpoczynanie pracy z usługą Azure Synapse Link dla usługi Azure Cosmos DB](configure-synapse-link.md)

* [Często zadawane pytania dotyczące usługi Synapse Link dla usługi Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Przypadki użycia usługi Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link-use-cases.md)
