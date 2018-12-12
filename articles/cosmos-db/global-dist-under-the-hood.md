---
title: Usługa Azure Cosmos DB globalną dystrybucję - kulisy
description: Ten artykuł zawiera szczegółowe informacje techniczne dotyczące dystrybucji globalnej usługi Azure Cosmos DB
services: cosmos-db
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 54511505841f170180bce0fccd8bd289ba24de2b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53073362"
---
# <a name="azure-cosmos-db-global-distribution---under-the-hood"></a>Usługa Azure Cosmos DB globalną dystrybucję - kulisy

Usługa Azure Cosmos DB to podstawowe usługi platformy Azure, dzięki czemu jest wdrażana we wszystkich regionach platformy Azure na całym świecie, w tym publiczny, suwerennych, Departament Obrony (DoD) i chmury dla instytucji rządowych. W ramach centrum danych, firma Microsoft wdrażanie i zarządzanie Azure Cosmos DB na ogromną sygnaturą czasową maszyny, każdy z dedykowanych dla magazynu lokalnego. W centrum danych usługi Azure Cosmos DB jest wdrażana w wielu klastrach, każdy potencjalnie uruchamianie wielu generacji sprzętu. Maszyny w ramach klastra są rozmieszczone na 10-20 domen błędów. Na poniższej ilustracji przedstawiono topologię systemu dystrybucji globalnej usługi Cosmos DB:

![Topologia systemu](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Dystrybucja globalna w usłudze Azure Cosmos DB jest kompleksowa:** w dowolnym momencie za pomocą kilku kliknięć lub programowo przy użyciu jednego wywołania interfejsu API można dodawać i usuwać regionów geograficznych, skojarzonych z ich bazy danych Cosmos. Bazy danych Cosmos z kolei zawiera zestaw kontenerów Cosmos. W usłudze Cosmos DB kontenery służyć jako jednostki logiczne dystrybucji i skalowalności. Kolekcje, tabele i wykresy, które możesz utworzyć to (wewnętrznie) po prostu Cosmos kontenery. Kontenery są całkowicie niezależne od schematu i podaj zakres dla zapytania. Dane w kontenerze Cosmos są automatycznie indeksowane po pozyskiwania. Automatyczne indeksowanie umożliwia użytkownikom do wykonywania zapytań o dane bez konieczności schematu lub problemów z zarządzaniem indeksem, szczególnie w konfiguracji globalnej dystrybucji.  

- W danym regionie dane znajdujące się w kontenerze są przesyłane przy użyciu klucza partycji, które zapewniają oraz sposób niewidoczny dla użytkownika jest zarządzana przez podstawowy partycje fizyczne (Dystrybucja lokalna).  

- Każda partycja zasobów jest również replikowana w regionach geograficznych (dystrybucja globalna). 

Gdy aplikacji za pomocą usługi Cosmos DB elastycznie skaluje przepustowość (lub zużywa więcej pamięci masowej) na kontenerze Cosmos usługi Cosmos DB obsługuje przezroczysty operacje zarządzania partycjami (podziału, klonowanie, usuwanie) we wszystkich regionach. Niezależnie od skali, dystrybucji czy awarii usługa Cosmos DB stale zapewnia pojedynczy obraz systemu danych w kontenerach, które są globalnie rozproszone w dowolnej liczbie regionów.  

Jak pokazano na poniższej ilustracji, dane znajdujące się w kontenerze są rozmieszczane w dwóch wymiarach:  

![Partycje fizyczne](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Fizyczną partycję jest implementowany przez grupę repliki, nazywany zestawu replik. Każda maszyna obsługuje setki replik, które odnoszą się do różnych partycji fizycznych, w obrębie ustalony zestaw elementów procesów, jak pokazano na poprzedniej ilustracji. Repliki, odpowiadające na partycje fizyczne dynamicznie są umieszczane i zrównoważonym między komputerami w ramach klastrów i centrów danych w obrębie regionu.  

Replikę należy jednoznacznie do dzierżawy usługi Azure Cosmos DB. Każda replika znajduje się wystąpienie usługi Cosmos DB [aparatu bazy danych](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), która zarządza zasobami, a także indeksy. Aparat bazy danych Cosmos DB działa w systemie na podstawie typu atom rekord sekwencja (ARS). Aparat jest niezależny od koncepcji schematu i rozmywając granic między wartościami struktury i wystąpienia rekordów. Usługa cosmos DB realizuje agnosticism pełnego schematu przez automatyczne indeksowanie wszystkich elementów, od pozyskiwania w sposób efektywny, które umożliwia użytkownikom do wykonywania zapytań ich danych rozproszonych globalnie, bez konieczności zarządzania schematami lub indeksami.

Aparat bazy danych Cosmos DB składa się z implementacji kilka podstawowych koordynacji, środowisk uruchomieniowych języków, procesor zapytań przechowywania i indeksowania podsystemów odpowiedzialny za transakcji magazynu i indeksowanie danych, w tym składniki odpowiednio. Zapewnienie trwałości i wysokiej dostępności, aparatu bazy danych będzie nadal występować, jego danych i indeksu na dyskach SSD i replikuje ją między wystąpieniami aparatu bazy danych w ramach repliki — określiła odpowiednio. Większe dzierżaw odpowiadają większej skali przepływności i pamięci i większy lub większej liczby replik i / lub. Każdy składnik system jest w pełni asynchroniczne — nigdy nie blokuje żadnych wątków, a każdy wątek działa krótkotrwałe bez ponoszenia żadnych przełączników niepotrzebne wątku. Ograniczanie szybkości i ciśnienia wstecznego są przyłączone do instalacji w całym stosie z formantu czasowej na wszystkie ścieżki we/wy. Naszego aparatu bazy danych została zaprojektowana wykorzystać szczegółowych współbieżności i dostarczanie wysokiej przepływności podczas pracy w ramach frugal ilość zasobów systemowych.

Dystrybucji globalnej usługi cosmos DB opiera się na dwa kluczowe elementy abstrakcji — zestawów replik i zestawach partycji. Zestawu replik to moduły blok Lego koordynacji i zestaw partycji jest dynamiczne nakładkę z jedną lub więcej partycji fizycznych geograficznie rozproszonych. Aby zrozumieć, jak globalnej dystrybucji działa, należy zrozumieć te dwa elementy abstrakcji klucza. 

## <a name="replica-sets"></a>Zestawów replik

Partycja zasobu jest zmaterializowany jako grupa samozarządzanej i dynamicznego równoważenia obciążenia repliki rozkładają się na wielu domenach błędów, o nazwie zestawu replik. Ten zestaw zbiorczo implementuje protokół maszyny replikowanych stanie sprawić, że dane w partycji zasobów wysoko dostępne, niezawodne i spójne. Dynamiczne członkostwo zestawu replik N — ją zachowuje informujących o NMin i nmaks na podstawie błędy, operacje administracyjne i czas dla replik nie powiodło się ponowne generowanie/odzyskiwanie. Na podstawie członkostwa zmian, replikacja protokołu również Rekonfiguruj rozmiar odczytu i zapisu kworum. Aby równomiernie rozłożyć przepływność, która jest przypisana do partycji danego zasobu, stosujemy dwóch pomysłów: po pierwsze, kosztów przetwarzania żądań zapisu na lidera jest wyższy niż koszt zastosowania aktualizacji w poniżej. Odpowiednio lidera jest w budżecie więcej zasobów systemowych niż obserwatorów. Po drugie w miarę możliwości odczytu kworum dla poziomu spójności danego składa się wyłącznie z replikami poniżej. Unikaj firma Microsoft, kontaktując się z liderem do obsługi operacji odczytu, chyba że wymagane. Stosujemy szereg pomysły z badań na relacje [obciążenia i wydajności](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) w systemach kworum dla modeli spójności pięć Cosmos DB obsługuje.  

## <a name="partition-sets"></a>Zestawy partycji

Grupa fizyczne partycje, jeden z każdym z regionów bazy danych Cosmos, skonfigurowaną składa się do zarządzania ten sam zestaw kluczy replikowane we wszystkich regionach skonfigurowany. Ten wyższe typ pierwotny koordynacji nosi nazwę partycji zestaw - geograficznie rozproszonych nakładki dynamicznych w partycji fizycznych, zarządzanie danego zestawu kluczy. Gdy partycji danego zasobu (repliki zestaw) jest zakresem w ramach klastra, zestaw partycji mogą znajdować się na klastrach, centrów danych i regionów geograficznych, jak pokazano na poniższej ilustracji:  

![Zestawy partycji](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Zestaw partycji można traktować jako geograficznie "super repliki zestaw", który składa się z wielu zestawów replik będącej właścicielem tego samego zestawu kluczy. Podobnie jak zestawu replik, partycji set członkostwo jest również dynamiczne — podlegał fluktuacjom zależnie od operacje zarządzania partycjami niejawne zasobu do listy dodawania/usuwania nowych partycji do/z danego zestawu partycji (na przykład gdy użytkownik skalowania w poziomie przepływności kontener, dodawać i usuwać w regionie z bazą danych Cosmos lub gdy występują błędy) ze względu na to, o każdej partycji (zestaw partycji) zarządzać członkostwie zestawu partycji w ramach własnego zestawu replik, członkostwo jest w pełni zdecentralizowane i wysoce jest dostępna. Podczas ponownej konfiguracji zestawu partycji również zostanie nawiązane topologii nakładki między partycje fizyczne. Topologia wybrano dynamicznie na podstawie poziomu spójności, położenia geograficznego i dostępną przepustowość sieci między źródłem i partycjami fizycznymi docelowego.  

Usługa pozwala na skonfigurowanie baz danych Cosmos z regionu zapisu w jednym lub wielu regionach zapisu, a w zależności od wyboru, zestawach partycji są skonfigurowane do akceptowania zapisów w dokładnie jednego lub wszystkich regionach. System używającego protokołu consensus dwupoziomowej, zagnieżdżone — jeden poziom działa w ramach repliki zestawu replik partycji zasobów akceptuje zapisu i innych działa na poziomie zestawu partycji pełną zagwarantować sortowania dla wszystkich zatwierdzone operacje zapisu w zestawie partycji. Ten consensus wielowarstwowych, zagnieżdżone jest krytyczne dla realizacji umowy SLA rygorystyczne w celu zapewnienia wysokiej dostępności, a także wdrożenia modeli spójności, które Cosmos DB oferuje klientom.  

## <a name="conflict-resolution"></a>Rozwiązywanie konfliktów

Nasze projektowanie pod kątem propagacji aktualizacji, rozwiązywanie konfliktów i przyczynowości śledzenia są INSPIROWANE od wcześniejszego pracy na [epidemii algorytmy](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) i [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) systemu. Jądra pomysły przetrwały i zapewniają wygodny układem odniesienia do komunikowania się projekt systemu usługi Cosmos DB, również przeszły znaczące przekształcenia, jak firma Microsoft stosowane do systemu usługi Cosmos DB. Było to wymagane, ponieważ wcześniejsze systemy zostały zaprojektowane, ani o zarządzanie zasobami przy użyciu skali, w którym usługi Cosmos DB musi działać ani możliwości (na przykład spójności powiązana nieaktualność) i rygorystyczne oraz kompleksową Umowy SLA, które usługi Cosmos DB zapewnia swoim klientom.  

Pamiętaj, że zestaw partycji jest rozpowszechniana w wielu regionach i korzysta z protokołu replikacji bazy danych Cosmos (Multi-master) do replikacji danych między partycjami fizycznymi wchodzących w skład danego zestawu partycji. Każda partycja zasobów (z zestaw partycji) akceptuje zapisów i zazwyczaj służy odczytów do klientów, którzy są lokalne w danym regionie. Zaakceptowane przez partycję zasobu w obrębie regionu zapisu są trwale zatwierdzone i wprowadzone o wysokiej dostępności w ramach partycji zasobów przed wysłane do klienta. Te są wstępne operacje zapisu i są propagowane do innych fizycznych partycji w obrębie zestawu partycji przy użyciu kanału zapobieganie entropii. Klienci mogą żądać zapisów wstępnie zaakceptowane lub zatwierdzone przez przekazanie nagłówek żądania. Propagacja zapobieganie entropii (w tym częstotliwość propagacji) jest dynamiczny, na podstawie topologii zestawu partycji i regionalnym bliskość partycje fizyczne i spójności, poziom skonfigurowany. W ramach zestawu partycji Cosmos DB jest zgodny schemat podstawowy zatwierdzenia z partycją dynamicznie wybrane kryterium. Wybór kryterium jest dynamiczne i jest integralną częścią ponownej konfiguracji zestawu partycji na podstawie topologii nakładki. Zatwierdzone operacje zapisu (w tym wielu-row/partii aktualizacje) jest gwarantowana do zamówienia. 

Stosujemy zegary zakodowany wektora (zawierający identyfikator regionu i zegary logiczne odpowiadający każdy poziom zgody na zestawu replik i zestawu partycji odpowiednio) przyczynowości, śledzenia i wersji, mogą wystąpić konflikty aktualizacji wektorów do wykrywania i rozwiązywania. Topologii oraz algorytm Wybór elementu równorzędnego jest zapewnienie stałych i minimalne magazynu i sieci minimalnej nakładów pracy związanych z wektorów wersji. Algorytm gwarantuje właściwość zbieżności strict.  

W przypadku baz danych Cosmos skonfigurowany z wieloma regionami zapisu systemu udostępnia szereg elastyczny automatycznych konflikt zasad rozpoznawania dla deweloperów do wyboru, w tym: 

- Ostatni zapis usługi Wins (LWW), która domyślnie używa właściwości zdefiniowane przez system sygnatura czasowa (która jest oparta na protokole zegara synchronizacji czasu). Usługa cosmos DB umożliwia również określenie innych niestandardowych właściwości wartości liczbowych służący do rozwiązywania konfliktów.  
- Zdefiniowane przez aplikację niestandardowe zasady rozwiązywania konfliktów (wyrażona za pomocą procedury scalania) przeznaczony dla zdefiniowanych przez aplikację semantyki uzgadniania konfliktów. Te procedury Pobierz wywoływane po wykryciu konflikty zapisów pod nadzorem transakcji bazy danych po stronie serwera. System zawiera dokładnie raz gwarantuje do wykonania procedury scalania w ramach protokołu zobowiązania. Są dostępne do zabawy kilka przykładów.  

## <a name="consistency-models"></a>Modeli spójności

Czy bazy danych Cosmos jest skonfigurować przy użyciu jednej lub wielu regionach zapisu, można wybierać spośród pięciu dobrze zdefiniowanych modeli spójności. Przy użyciu nowo dodano obsługę włączania wielu regionów zapisu poniżej przedstawiono kilka istotnych aspektów poziomów spójności:  

Jak wcześniej, spójności powiązana nieaktualność gwarantuje, że wszystkie operacje odczytu będzie w obrębie k prefiksów lub t sekund od najnowszych zapisu we wszystkich regionach. Ponadto odczyty spójności powiązana nieaktualność zapewniona jest monotoniczny i gwarancje, spójny prefiks. Protokół zapobieganie entropii działa w sposób ograniczony szybkość i gwarantuje, że prefiksy nie są gromadzone i wsteczne zapisu nie ma zastosowanie. Podobnie jak wcześniej, sesji monotoniczny gwarancje spójności odczytu i zapisu monotoniczny odczytywać własne zapisy, zapis obserwowanych odczycie i spójny prefiks gwarantuje na całym świecie. W przypadku baz danych skonfigurowane za pomocą silnej spójności, korzyści z multi opanowanie (opóźnienie zapisu niski, pisanie wysokiej dostępności) nie stosuje się z powodu synchroniczną replikację między regionami.

Semantyka pięcioma modelami spójności w usłudze Cosmos DB są opisane [tutaj](consistency-levels.md) i ze sobą matematycznie wyświetlane przy użyciu wysokiego poziomu specyfikacji TLA + [tutaj](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Kolejne kroki

Następnie Dowiedz się, jak skonfigurować dystrybucję globalną, korzystając z następującymi artykułami:

* [Jak skonfigurować klientów dla wielu](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Dodawanie/usuwanie regionów z Twojego konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak utworzyć zasady rozpoznawania konfliktu niestandardowego konta interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
