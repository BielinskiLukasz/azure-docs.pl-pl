---
title: Usługa cognitive search do wyodrębnienia danych, przetwarzanie języka naturalnego sztucznej Inteligencji w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Wyodrębnianie zawartości, języka naturalnego (NLP) i obrazów, przetwarzania, aby tworzyć zawartość można wyszukiwać w usłudze Azure Search indeksowanie przy użyciu algorytmów sztucznej Inteligencji i umiejętności poznawcze
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: heidist
ms.openlocfilehash: 5d7f275be1f04658f9901aba9faca83375a9bbf5
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956197"
---
# <a name="what-is-cognitive-search"></a>Co to jest usługa cognitive search?

Usługa cognitive search tworzy informacje można wyszukiwać zawartość niemożliwych, dołączając algorytmów sztucznej Inteligencji do potoku indeksowania. Opiera się integracji sztucznej Inteligencji *umiejętności poznawcze*, wzbogaca źródła dokumentów w drodze do indeksu wyszukiwania. 

**Przetwarzanie języka naturalnego** obejmują umiejętności [rozpoznawanie jednostek](cognitive-search-skill-named-entity-recognition.md), wykrywanie języka [kluczowe frazy](cognitive-search-skill-keyphrases.md), manipulacja tekstem i wykrywania tonacji. Za pomocą tych umiejętności staje się strukturę tekstu bez struktury, mapowane na wyszukiwanie i filtrowanie pól w indeksie.

**Przetwarzanie obrazów** obejmuje [optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) i identyfikacji [funkcje visual](cognitive-search-skill-image-analysis.md), takich jak wykrywanie twarzy obrazu interpretacji, obrazów, rozpoznawania (sławne osoby i charakterystycznych elementów krajobrazu) lub atrybuty, takie jak kolory lub orientację obrazu. Można utworzyć tekstowej reprezentacji zawartości obrazu, którą można przeszukiwać za pomocą wszystkich możliwości zapytań usługi Azure Search.

![Usługa cognitive search diagram potoku](./media/cognitive-search-intro/cogsearch-architecture.png "Przegląd potoku wyszukiwania kognitywnego")

Umiejętności poznawcze w usłudze Azure Search są oparte na tych samych algorytmów sztucznej Inteligencji używane w interfejsy API usług Cognitive Services: [o nazwie jednostki interfejsu API rozpoznawania](cognitive-search-skill-named-entity-recognition.md), [interfejsu API usługi Key frazy wyodrębniania](cognitive-search-skill-keyphrases.md), i [API optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) są tylko kilka. 

Język naturalny i przetwarzanie obrazu jest stosowana w fazie wprowadzania danych z wynikami staje się częścią kompozycji dokument w indeksie wyszukiwania w usłudze Azure Search. Dane są źródło jako zestawu danych platformy Azure, a następnie przekazywane za pośrednictwem potoku indeksowania, przy użyciu zależności [wbudowanych umiejętności](cognitive-search-predefined-skills.md) potrzebujesz. Architektura jest rozszerzalny, więc jeśli wbudowane umiejętności nie są wystarczające, można tworzyć i dołączać [umiejętności niestandardowe](cognitive-search-create-custom-skill-example.md) niestandardowych integracji. Przykłady mogą być przeznaczone dla określonej domeny, takich jak finanse, publikacji naukowych lub medycyna klasyfikatora modułu lub dokumentu jednostkę niestandardową.

> [!NOTE]
> Wyszukiwanie poznawcze jest dostępne w publicznej wersji zapoznawczej. Możliwości wykonywania zestawów umiejętności oraz wyodrębniania i normalizacji obrazów są obecnie oferowane bezpłatnie. Cennik dla tych możliwości zostanie opublikowany w późniejszym czasie. 

## <a name="components-of-cognitive-search"></a>Składniki wyszukiwania kognitywnego

Usługa cognitive search jest w wersji zapoznawczej z [usługi Azure Search](search-what-is-azure-search.md)dostępny dla wszystkich warstw w południowo-środkowe stany USA i Europa Zachodnia. 

Potok usługa cognitive search jest oparty na [usługi Azure Search *indeksatory* ](search-indexer-overview.md) , przeszukiwać źródeł danych i zapewniają przetwarzania indeksu end-to-end. Umiejętności są teraz dołączone do indeksatorów, przechwytuje i wzbogacania dokumentów na podstawie zestawu umiejętności, należy zdefiniować. Po indeksowane, możesz uzyskać dostęp zawartości za pośrednictwem żądania wyszukiwania przez wszystkie [zapytania typy obsługiwanych przez usługę Azure Search](search-query-overview.md).  Jeśli jesteś nowym użytkownikiem indeksatorów, ta sekcja przeprowadzi Cię przez kroki.

### <a name="source-data-and-document-cracking-phase"></a>Źródło danych i faza łamania dokumentów

Na początku tego potoku, masz tekstu bez struktury lub zawartości bez tekstu (na przykład obraz i pliki JPEG skanowanego dokumentu). Dane muszą istnieć w to usługa magazynu danych na platformie Azure, która może zostać oceniony przez indeksator. Indeksatory można "złamanie" dokumentów źródłowych do wyodrębniania tekstu z danymi źródłowymi.

![Faza łamania dokumentów](./media/cognitive-search-intro/document-cracking-phase-blowup.png "łamania dokumentów")

 Obsługiwane źródła obejmują usługi Azure blob storage, usługa Azure table storage, Azure SQL Database i Azure Cosmos DB. Można wyodrębnić zawartość tekstu z następujących typów plików: pliki PDF, Word, PowerPoint, pliki CSV. Aby uzyskać pełną listę, zobacz [obsługiwane formaty](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Umiejętności poznawcze i wzbogacania fazy

Wzbogacanie odbywa się za pośrednictwem *umiejętności poznawcze* wykonywania niepodzielnych operacji. Na przykład po utworzeniu treści tekstu z plików PDF, możesz zastosować, wykrywanie języka rozpoznawania jednostek i wyodrębnianie kluczowych fraz w celu utworzenia nowych pól w indeksie, które nie są dostępne natywnie w źródle. Cała nosi nazwę kolekcji umiejętności używane w potoku *zestawu umiejętności*.  

![Faza wzbogacania](./media/cognitive-search-intro/enrichment-phase-blowup.png "wzbogacania fazy")

Zestawu umiejętności jest oparty na [wstępnie zdefiniowane umiejętności poznawcze](cognitive-search-predefined-skills.md) lub [umiejętności niestandardowe](cognitive-search-create-custom-skill-example.md) Podaj i nawiązać połączenie z zestawu umiejętności. Zestawu umiejętności może być minimalny lub bardzo złożone i określa nie tylko typ przetwarzania, ale kolejność operacji. Zestawu umiejętności oraz zdefiniowany jako część indeksatora pełni określa wzbogacony potok mapowania pól. Aby uzyskać więcej informacji na temat połączenie wszystkie te elementy ze sobą, zobacz [Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md).

Wewnętrznie potok generuje kolekcję wzbogaconego dokumentów. Można zdecydować, które części dokumentów wzbogaconego powinno zostać zamapowane można indeksować pola w indeksie wyszukiwania. Na przykład jeśli zastosowano wyodrębnianie kluczowych fraz i umiejętności rozpoznawania jednostek, następnie te nowe pola stanie się częścią dokumentu wzbogaconego i mogą być mapowane do pól w indeksie. Zobacz [adnotacje](cognitive-search-concept-annotations-syntax.md) dowiedzieć się więcej o formacji wejścia/wyjścia.

### <a name="search-index-and-query-based-access"></a>Indeks wyszukiwania i dostępu na podstawie zapytania

Po zakończeniu przetwarzania będziesz mieć korpus wyszukiwania, składający się z wzbogaconego dokumentów, w pełni tekst wyszukiwania w usłudze Azure Search. [Wykonywanie zapytania dotyczącego indeksu](search-query-overview.md) jest jak deweloperów i użytkowników dostępu do zawartości wzbogaconego generowane przez potok. 

![Indeks o ikonę wyszukiwania](./media/cognitive-search-intro/search-phase-blowup.png "indeksu za pomocą ikony wyszukiwania")

Indeks przypomina innych można utworzyć dla usługi Azure Search: możesz uzupełnić z analizatory niestandardowe, wywołania zapytania wyszukiwania rozmytego, Dodaj wyszukiwanie filtrowane lub eksperymentować, profile, aby przekształcić wyniki wyszukiwania oceniania.

Indeksy są generowane na podstawie schematu indeksu, który definiuje pola atrybutów i innych konstrukcji dołączonych do określonego indeksu, takie jak profile oceniania i mapy synonimów. Gdy indeks jest zdefiniowany i wypełnione, umożliwia indeksowanie przyrostowo do pobrania źródła nowych i zaktualizowanych dokumentów. Pewne zmiany wymagają ponownej pełnej kompilacji. Należy używać małym zestawem danych, do czasu projektowania schematu jest stabilna. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Kluczowe funkcje i pojęcia

| Pojęcie | Opis| Linki |
|---------|------------|-------|
| Zestawu umiejętności | Najwyższego poziomu o nazwie zasobu zawierającego zbiór umiejętności. Zestawu umiejętności jest wzbogacony potok. Jest wywoływana podczas indeksowania w indeksatorze. | [Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md) |
| Umiejętności cognitive | Niepodzielne przekształcenie w wzbogacony potok. Często jest składnikiem, który wyodrębnia lub wnioskuje struktury i w związku z tym rozszerzają zrozumienie danych wejściowych. Prawie zawsze dane wyjściowe są oparte na tekście, a przetwarzanie jest przetwarzanie języka naturalnego i przetwarzanie obrazu, który wyodrębnia lub generuje tekstu z obrazów w danych wejściowych. Dane wyjściowe z umiejętności mogą być zamapowane do pola w indeksie lub używane jako dane wejściowe wzbogacania podrzędnego. Wstępnie zdefiniowane i udostępniane przez firmę Microsoft lub niestandardowe umiejętności: utworzonych i wdrożonych przez użytkownika. | [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) |
| Wyodrębnianie danych | Obejmuje szerokiej gamy przetwarzanie, jednak odnoszących się do wyszukiwania kognitywnego umiejętności rozpoznawanie podmiotów jest najczęściej używana do wyodrębniania danych (jednostki) ze źródła, która nie zapewnia natywnej tych informacji. | [Umiejętności rozpoznawania nazwanych jednostek](cognitive-search-skill-named-entity-recognition.md)| 
| Przetwarzanie obrazów | Wnioskuje tekstu z obrazu, takich jak możliwości rozpoznawania charakterystycznych elementów krajobrazu lub umożliwia wyodrębnianie tekstu z obrazu. Typowe przykłady obejmują optyczne rozpoznawanie znaków do podnoszenia znaków z pliku skanowanego dokumentu (JPEG), lub rozpoznawanie Nazwa ulicy na zdjęciu zawierających znak ulicy. | [Obraz umiejętności analizy](cognitive-search-skill-image-analysis.md) lub [umiejętności optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md)
| Przetwarzanie języka naturalnego | Tekst przetwarzania szczegółowych informacji i informacje o tekstu w danych wejściowych. Wykrywanie języka, analizę tonacji i wyodrębnianie kluczowych fraz są umiejętności, które są objęte przetwarzania języka naturalnego.  | [Kluczowe frazy wyodrębniania umiejętności](cognitive-search-skill-keyphrases.md), [umiejętności wykrywania języka](cognitive-search-skill-language-detection.md), [umiejętności analizy tonacji](cognitive-search-skill-sentiment.md) |
| Łamania dokumentów | Proces wyodrębniania lub tworzenia zawartości tekstowej ze źródeł innych niż tekst podczas indeksowania. Optyczne rozpoznawanie znaków (OCR) znajduje się przykład, ale zazwyczaj odwołuje się do podstawowych funkcji indeksator zgodnie z indeksatora wyodrębnia zawartość z plików aplikacji. Źródło danych, podając lokalizację pliku źródłowego i definicja indeksatora, zapewniając mapowania pól są oba kluczowe czynniki łamania dokumentów. | Zobacz [indeksatorów](search-indexer-overview.md) |
| Kształtowanie | Konsolidowanie fragmentów tekstu w większej struktury lub z drugiej strony podziału większe fragmenty tekstu do rozmiarów do dalszego przetwarzania transmisji dla klientów. | [Umiejętności shaper](cognitive-search-skill-shaper.md), [umiejętności fuzji tekstu](cognitive-search-skill-textmerger.md), [tekst podziału umiejętności](cognitive-search-skill-textsplit.md) |
| Wzbogaconego dokumentów | Przejściowe wewnętrznej struktury, nie są bezpośrednio dostępne w kodzie. Wzbogaconego dokumenty są generowane podczas przetwarzania, ale tylko ostateczne dane wyjściowe są zachowywane w indeksie wyszukiwania. Mapowania pól określają, elementy danych, które są dodawane do indeksu. | Zobacz [uzyskiwaniu dostępu do dokumentów wzbogaconego](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indeksator |  Przeszukiwarką, która wyodrębnia dane z możliwością wyszukiwania i metadanych z zewnętrznego źródła danych i wypełnienie indeksu oparte na mapowania pól do pól między indeksem a źródłem danych dla łamania dokumentów. W przypadku wzbogacenia wyszukiwania kognitywnego indeksatora wywołuje zestawu umiejętności i zawiera mapowania pól kojarzenie Wzbogacanie danych wyjściowych pól docelowych. w indeksie. Definicja indeksatora znajdują się instrukcje i odwołania dla operacji potoku, a potok jest wywoływana po uruchomieniu indeksatora. | [Indexers](search-indexer-overview.md) (Indeksatory) |
| Źródło danych  | Obiekt, który używane w indeksatorze, aby nawiązać połączenie z zewnętrznym źródłem danych z obsługiwanych typów na platformie Azure. | Zobacz [indeksatorów](search-indexer-overview.md) |
| Indeks | Korpus utrwalonych wyszukiwania w usłudze Azure Search, utworzony na podstawie schematu indeksu, który definiuje pola struktury i użycia. | [Indeksy w usłudze Azure Search](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>Od czego zacząć?

**Krok 1: Tworzenie usługi wyszukiwania w regionie, zapewniając interfejsów API** 

+ Środkowo-zachodnie stany USA
+ Środkowo-południowe stany USA
+ Wschodnie stany USA
+ Wschodnie stany USA 2
+ Zachodnie stany USA 2
+ Kanada Środkowa
+ Europa Zachodnia
+ Południowe Zjednoczone Królestwo
+ Europa Północna
+ Brazylia Południowa
+ Azja Południowo-Wschodnia
+ Indie Środkowe
+ Australia Wschodnia

**Krok 2: Praktyczne doświadczenie w pracy opanuj korzystanie z przepływu pracy**

+ [Przewodnik Szybki Start (portal)](cognitive-search-quickstart-blob.md)
+ [Samouczek (żądania HTTP)](cognitive-search-tutorial-blob.md)
+ [Przykład umiejętności niestandardowe (C#)](cognitive-search-create-custom-skill-example.md)

**Krok 3: Przeglądanie interfejsu API (REST tylko)**

Obecnie tylko interfejsy API REST są dostarczane. Użyj `api-version=2017-11-11-Preview` we wszystkich żądaniach. Kompiluj rozwiązanie wyszukiwania kognitywnego przy użyciu następujących interfejsów API. Tylko dwa interfejsy API są dodawane lub rozszerzony na użytek usłudze wyszukiwania poznawczego. Inne interfejsy API mieć tej samej składni jako ogólnie dostępnej wersji.

| Interfejs API REST | Opis |
|-----|-------------|
| [Utwórz źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Identyfikowanie zewnętrzne źródło danych dostarczania źródła danych używany do tworzenia dokumentów wzbogaconego zasób.  |
| [Tworzenie zestawu umiejętności (wersja api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasobu, koordynowania użytkowania [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) i [niestandardowe umiejętności poznawcze](cognitive-search-custom-skill-interface.md) używane w wzbogacony potok podczas indeksowania. |
| [Tworzenie indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schemat może przedstawiać indeksu usługi Azure Search. Pola w indeksie mapowania pól w danych źródłowych lub pola wyprodukowany w fazie wzbogacania (na przykład pole nazwy organizacji utworzone przez rozpoznawanie jednostek). |
| [Tworzenie indeksatora (wersja api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasób Definiowanie składniki używane podczas indeksowania: w tym źródłem danych, zestawu umiejętności, skojarzeń pól ze źródła i struktury są dane pośrednie indeksu docelowego i sam indeks. Uruchamianie indeksatora jest wyzwalacz dla pozyskiwania danych i wzbogacanie. Dane wyjściowe są korpus wyszukiwania, na podstawie schematu indeks, wypełniony danych źródłowych, wzbogacone za pośrednictwem zestawu umiejętności.  |

**Lista kontrolna: Typowy przepływ pracy**

1. Podzbiór danych źródłowych platformy Azure do reprezentatywnej próbki. Indeksowania wymaga czasu więc rozpoczynać małe, reprezentatywny zestaw danych, a następnie tworząc go stopniowo jak dojrzewa rozwiązania.

1. Tworzenie [obiektu źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) w usłudze Azure Search, aby zapewnić ciąg połączenia dla pobierania danych.

1. Tworzenie [zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) wzbogacania czynności.

1. Zdefiniuj [schemat indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index). *Pola* kolekcja zawiera pola ze źródła danych. Należy również namiastki, się dodatkowe pola, aby przechowywać wartości wygenerowany dla zawartości tworzone podczas wzbogacania.

1. Zdefiniuj [indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-skillset) odwołujące się do źródła danych, zestawu umiejętności i indeksu.

1. W ramach indeksatora, Dodaj *outputFieldMappings*. W tej sekcji mapuje dane wyjściowe z zestawu umiejętności (w kroku 3) do pola danych wejściowych w schemacie indeksu (w kroku 4).

1. Wyślij *tworzenie indeksatora* żądania właśnie utworzony (żądania POST z definicję indeksatora w treści żądania) do wyrażenia indeksatora w usłudze Azure Search. Ten krok jest, działanie indeksatora, wywoływania potoku.

1. Uruchamianie zapytań do oceny wyników i zmodyfikować kod, aby dokładniejsze aktualizacji, schematu lub konfiguracji indeksatora.

1. [Resetuj indeksator](search-howto-reindex.md) przed odbudowywania potoku.

Aby uzyskać więcej informacji na temat określonego pytania lub problemy, zobacz [wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Kolejne kroki

+ [Dokumentacja wyszukiwania kognitywnego](cognitive-search-resources-documentation.md)
+ [Szybki Start: Wypróbuj wyszukiwanie kognitywne w Przewodnik po portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Dowiedz się, usługa cognitive search interfejsów API](cognitive-search-tutorial-blob.md)
