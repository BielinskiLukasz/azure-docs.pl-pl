---
title: Indeksowanie dużych zestawów danych za pomocą wbudowanych indeksatorów — Azure Search
description: Poznaj strategie umożliwiające indeksowanie dużych ilości danych lub indeksowanie intensywnie korzystające z obliczeń przy użyciu trybu wsadowego, odzyskania i technik dla zaplanowanych, równoległych i rozproszonych indeksowania.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a98d716562f53488e9adb5d485a1dbf7fafc3102
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648171"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Jak indeksować duże zestawy danych w Azure Search

W miarę wzrostu lub przetwarzania woluminów danych, może się okazać, że domyślne strategie indeksowania nie są już praktyczne. W przypadku Azure Search istnieje kilka podejścia do dodawania większych zestawów danych, od sposobu tworzenia struktury żądania przekazywania danych przy użyciu indeksatora specyficznego dla zasobów dla obciążeń zaplanowanych i rozproszonych.

Te same techniki dla dużych ilości danych mają zastosowanie również do długotrwałych procesów. W szczególności kroki opisane w temacie [Parallel Indexing](#parallel-indexing) są przydatne w przypadku obliczeń intensywnie wykorzystujących indeksowanie, takie jak analiza obrazu lub przetwarzanie języka naturalnego w [potokach wyszukiwania poznawczego](cognitive-search-concept-intro.md).

## <a name="batch-indexing"></a>Indeksowanie wsadowe

Jednym z najprostszych mechanizmów indeksowania większego zestawu danych jest przesyłanie wielu dokumentów lub rekordów w jednym żądaniu. Tak długo, jak cały ładunek przekracza 16 MB, żądanie może obsłużyć do 1000 dokumentów w operacji ładowania zbiorczego. Przy założeniu, że [interfejs API REST Dodaj lub zaktualizuj dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), w treści żądania należy spakować 1000 dokumentów.

Indeksowanie wsadowe jest implementowane dla indywidualnych żądań przy użyciu interfejsu REST lub platformy .NET lub za pośrednictwem indeksatorów. Kilka indeksatorów działa pod różnymi limitami. W tym celu funkcja indeksowania obiektów blob platformy Azure ustawia rozmiar wsadu w 10 dokumentach w celu rozpoznania większego średniego rozmiaru dokumentu. W przypadku indeksatorów opartych na [interfejsie API Rest tworzenia indeksatora](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer )można ustawić `BatchSize` argument, aby dostosować to ustawienie, aby lepiej odpowiadało charakterystyce danych. 

> [!NOTE]
> Aby zachować rozmiar dokumentu w dół, pamiętaj, aby wykluczyć z żądania dane inne niż queryable. Obrazy i inne dane binarne nie są bezpośrednio przeszukiwane i nie powinny być przechowywane w indeksie. Aby zintegrować dane niequeryablene z wynikami wyszukiwania, należy zdefiniować pole, które nie jest możliwe do przeszukania, które przechowuje odwołanie do tego zasobu.

## <a name="add-resources"></a>Dodaj zasoby

Usługi, które są udostępniane w jednej ze [standardowych warstw cenowych](search-sku-tier.md) , często mają bezproblemową wydajność zarówno dla magazynu, jak i obciążeń (zapytania lub indeksowanie), co sprawia, że [zwiększenie partycji i repliki](search-capacity-planning.md) to oczywiste rozwiązanie dla Obsługa większych zestawów danych. Aby uzyskać najlepsze wyniki, potrzebne są oba zasoby: partycje magazynu i repliki dla pracy pozyskiwania danych.

Rosnące repliki i partycje są płatnymi zdarzeniami, które zwiększają koszt, ale o ile nie są stale indeksowane w ramach maksymalnego obciążenia, można dodać skalę na czas trwania procesu indeksowania, a następnie dopasować poziomy zasobów w dół po indeksie zakończeniu.

## <a name="use-indexers"></a>Używanie indeksatorów

[Indeksatory](search-indexer-overview.md) służą do przeszukiwania zewnętrznych źródeł danych w celu przeszukania zawartości. Chociaż nie jest to przeznaczone do indeksowania na dużą skalę, kilka funkcji indeksatora jest szczególnie przydatne do obsługi większych zestawów danych:

+ Harmonogramy umożliwiają dedystrybuowanie indeksowania w regularnych odstępach czasu, aby można było je rozłożyć w miarę upływu czasu.
+ Zaplanowane indeksowanie można wznowić w ostatnim znanym punkcie zatrzymywania. Jeśli źródło danych nie jest w pełni przeszukiwane w oknie 24-godzinnym, indeksator wznowi indeksowanie dziennie dwa w miejscu, w którym jest pozostawione.
+ Partycjonowanie danych w mniejszych pojedynczych źródłach danych umożliwia przetwarzanie równoległe. Można podzielić duże ilości danych na mniejsze zestawy danych, a następnie utworzyć wiele definicji źródła danych, które mogą być indeksowane równolegle.

> [!NOTE]
> Indeksatory są specyficzne dla źródła danych, więc użycie metody indeksatora jest możliwe tylko dla wybranych źródeł danych na platformie Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [BLOB Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

## <a name="scheduled-indexing"></a>Zaplanowane indeksowanie

Planowanie indeksatora to ważny mechanizm przetwarzania dużych zestawów danych, a także długotrwałe procesy, takie jak analiza obrazu w potoku wyszukiwania poznawczego. Przetwarzanie indeksatora działa w oknie 24-godzinnym. Jeśli przetwarzanie zakończy się niepowodzeniem w ciągu 24 godzin, zachowania funkcji indeksatora dotyczące planowania mogą współdziałać z zaletą. 

Zaplanowana funkcja indeksowania jest uruchamiana w określonych odstępach czasu, a zadanie zwykle kończy się przed wznowieniem przy następnym zaplanowanym interwale. Jeśli jednak przetwarzanie nie zakończy się w przedziale czasu, indeksator zostanie zatrzymany (ponieważ został uruchomiony poza czasem). Podczas następnego interwału przetwarzanie zostanie wznowione w miejscu, w którym zostało przerwane, a system śledzi miejsce wystąpienia. 

W praktyce w przypadku obciążeń indeksu obejmujących kilka dni można umieścić indeksator w harmonogramie 24-godzinnym. Gdy indeksowanie zostanie wznowione dla następnego 24-godzinnego cyklu, zostanie ono ponownie uruchomione od ostatniego znanego dobrego dokumentu. Dzięki temu indeksator może współdziałać w sposób za pośrednictwem zaległości dokumentu w ciągu kilku dni do czasu przetworzenia wszystkich nieprzetworzonych dokumentów. Aby uzyskać więcej informacji na temat tego podejścia, zobacz [indeksowanie dużych zestawów danych w usłudze Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Aby uzyskać więcej informacji na temat ogólnych ustawień harmonogramów, zobacz [Tworzenie interfejsu API REST indeksatora](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax) lub zapoznaj [się z tematem planowanie indeksatorów dla Azure Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indeksowanie równoległe

Strategia indeksowań równoległych opiera się na indeksowaniu wielu źródeł danych w artykułem, gdzie każda definicja źródła danych określa podzestaw danych. 

W przypadku nierutynowych, intensywnie korzystających z obliczeń wymagań dotyczących indeksowania — takich jak OCR na skanowanych dokumentach w potoku wyszukiwania poznawczego, analiza obrazu lub przetwarzanie języka naturalnego — strategia indeksowana równoległa jest często właściwym podejściem do ukończenia długotrwały proces w najkrótszym czasie. Jeśli możesz wyeliminować lub zmniejszyć żądania zapytań, indeksowanie równoległe w usłudze, która nie jest równocześnie obsługiwana, jest najlepszą strategią do pracy w dużej części zawartości z niską szybkością przetwarzania. 

Przetwarzanie równoległe obejmuje następujące elementy:

+ Poddzielenie danych źródłowych między wieloma kontenerami lub wieloma folderami wirtualnymi w tym samym kontenerze. 
+ Zamapuj każdy zestaw danych minipasek na jego własne [Źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source), sparowany z jego własnym [indeksatorem](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ W przypadku wyszukiwania poznawczego odwołują się do tego samego [zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) w każdej definicji indeksatora.
+ Zapisz w tym samym docelowym indeksie wyszukiwania. 
+ Zaplanuj, aby wszystkie indeksatory były uruchamiane w tym samym czasie.

> [!NOTE]
> Azure Search nie obsługuje dedykowanych replik ani partycji do określonych obciążeń. Ryzyko dużego współbieżnego indeksowania polega na przeniesieniu nadmiernego obciążenia systemu do poziomu szkody związanej z wydajnością zapytań. Jeśli masz środowisko testowe, najpierw Zaimplementuj indeksowanie równoległe, aby zrozumieć kompromisy.

### <a name="how-to-configure-parallel-indexing"></a>Jak skonfigurować indeksowanie równoległe

W przypadku indeksatorów pojemność przetwarzania jest luźno oparta na jednym podsystemie indeksatora dla każdej jednostki usługi (SU) używanej przez usługę wyszukiwania. Istnieje wiele współbieżnych indeksatorów dla usług Azure Search obsługiwanych w warstwach Podstawowa lub standardowa mających co najmniej dwie repliki. 

1. Na [Azure Portal](https://portal.azure.com)na stronie **Przegląd** pulpitu nawigacyjnego usługi wyszukiwania Sprawdź **warstwę cenową** , aby potwierdzić, że może ona obsługiwać równoległe indeksowanie. Warstwy Podstawowa i Standardowa oferują wiele replik.

2. W obszarze **Ustawienia** > Skala [Zwiększ liczbę replik](search-capacity-planning.md) do przetwarzania równoległego: jedną dodatkową replikę dla każdego obciążenia indeksatora. Pozostaw wystarczającą liczbę dla istniejącego woluminu zapytania. Obniżanie obciążenia zapytań dla indeksowania nie jest dobrym kompromisem.

3. Dystrybuuj dane do wielu kontenerów na poziomie, który może dotrzeć do Azure Search indeksatorów. Może to być wiele tabel w Azure SQL Database, wiele kontenerów w usłudze Azure Blob Storage lub wiele kolekcji. Zdefiniuj jeden obiekt źródła danych dla każdej tabeli lub kontenera.

4. Utwórz i Zaplanuj uruchamianie wielu indeksatorów równolegle:

   + Załóżmy, że usługa ma sześć replik. Skonfiguruj sześć indeksatorów, z których każdy jest mapowany do źródła danych zawierającego jeden szósty zestaw danych dla 6-kierunkowego podziału całego zestawu danych. 

   + Wskaż każdy indeksator do tego samego indeksu. W przypadku obciążeń związanych z wyszukiwaniem poznawczym należy wskazać każdy indeksator do tego samego zestawu umiejętności.

   + W ramach każdej definicji indeksatora Zaplanuj ten sam wzorzec wykonywania w czasie wykonywania. Na przykład program `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` tworzy harmonogram na 2018-05-15 dla wszystkich indeksatorów uruchomionych w ciągu ośmiu godzin.

W zaplanowanym czasie wszystkie indeksatory rozpoczynają wykonywanie, ładowania danych, stosowania wzbogacania (Jeśli skonfigurowano potok wyszukiwania poznawczego) i zapisu w indeksie. Azure Search nie blokuje indeksów dla aktualizacji. Współbieżne zapisy są zarządzane, a następnie ponów próbę, jeśli określony zapis nie powiedzie się przy pierwszej próbie.

> [!Note]
> Podczas zwiększania replik należy rozważyć zwiększenie liczby partycji, jeśli rozmiar indeksu jest rzutowany, aby znacząco zwiększyć. Partycje przechowują wycinki indeksowanej zawartości; im więcej partycji, tym mniejsza jest możliwość przechowywania wycinka każdego z nich.

## <a name="see-also"></a>Zobacz także

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Azure SQL Database indeksator](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w Azure Search](search-security-overview.md)
