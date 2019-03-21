---
title: Samouczek dotyczący indeksowania baz danych Azure SQL Database w portalu Azure Portal — Azure Search
description: W tym samouczku połączyć z bazą danych Azure SQL, wyodrębnianie danych z możliwością wyszukiwania i załadować je do indeksu usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4e94f4c1b5de47e36dd9a5be6b9e7f43d264de82
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201402"
---
# <a name="tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Samouczek: Przeszukiwanie bazy danych Azure SQL Database przy użyciu indeksatorów usługi Azure Search

Dowiedz się, jak skonfigurować indeksator do wyodrębniania danych z możliwością wyszukiwania z przykładowej bazy danych Azure SQL. [Indeksatory](search-indexer-overview.md) to składnik usługi Azure Search, który przeszukuje zewnętrzne źródła danych, wypełniając [indeks wyszukiwania](search-what-is-an-index.md) przy użyciu zawartości. Wszystkie indeksatory indeksator usługi Azure SQL Database jest najczęściej używana. 

Zaawansowanie w zakresie obsługi konfiguracji indeksatora jest pomocne, ponieważ upraszcza proces zapisu i obsługiwania kodu oraz zmniejsza jego ilość. Zamiast przygotowywać i wypychać zestaw danych JSON zgodny ze schematem, można dołączyć indeksator do źródła danych, poczekać, aż indeksator wyodrębni dane i wstawi je do indeksu, a następnie opcjonalnie uruchomić indeksator zgodnie z cyklicznym harmonogramem w celu zastosowania zmian w odpowiednim źródle.

W tym samouczku [biblioteki klienta .NET usługi Azure Search](https://aka.ms/search-sdk) i aplikacji konsoli .NET Core do wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie informacji o usłudze wyszukiwania do ustawień aplikacji
> * Przygotowywanie zewnętrznego zestawu danych w bazie danych Azure SQL Database 
> * Przeglądanie definicji indeksu i indeksatora w przykładowym kodzie
> * Uruchamianie kodu indeksatora w celu zaimportowania danych
> * Przeszukiwanie indeksu
> * Wyświetlanie konfiguracji indeksatora w portalu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku.

* [Usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/) dostarczanie zewnętrzne źródło danych używane w indeksatorze. Przykładowe rozwiązanie udostępnia plik danych SQL w celu utworzenia tabeli.

* + [Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), w każdej wersji. Przykładowy kod i instrukcje zostały przetestowane na bezpłatna wersja Community.

> [!Note]
> Jeśli używasz bezpłatnej usługi Azure Search, możesz korzystać tylko z trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych zasobów.

### <a name="download-the-solution"></a>Pobieranie rozwiązania

Rozwiązanie indeksatora używane w tym samouczku pochodzi z kolekcji przykładów usługi Azure Search dostarczanych w postaci jednego głównego pliku do pobrania. Rozwiązanie używane w tym samouczku to *DotNetHowToIndexers*.

1. Przejdź do pozycji [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started) w repozytorium serwisu GitHub zawierającym przykłady związane z platformą Azure.

2. Kliknij pozycję **Klonuj lub pobierz** > **Pobierz plik ZIP**. Domyślnie plik jest przenoszony do folderu plików do pobrania.

3. W obszarze **Eksplorator plików** > **Pliki do pobrania** kliknij prawym przyciskiem myszy plik i wybierz pozycję **Wyodrębnij wszystko**.

4. Wyłącz uprawnienia tylko do odczytu. Kliknij prawym przyciskiem myszy nazwę folderu > **Właściwości** > **Ogólne**, a następnie wyczyść atrybut **Tylko do odczytu** dla bieżącego folderu, podfolderów i plików.

5. W programie **Visual Studio 2017** otwórz rozwiązanie *DotNetHowToIndexers.sln*.

6. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy górny węzeł nadrzędny Rozwiązanie > **Przywróć pakiety Nuget**.

### <a name="set-up-connections"></a>Konfigurowanie połączeń
Informacje o połączeniu z wymaganymi usługami są określane w pliku **appsettings.json** w rozwiązaniu. 

W Eksploratorze rozwiązań otwórz plik **appsettings.json**, aby można było wypełnić poszczególne ustawienia przy użyciu instrukcji podanych w tym samouczku.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Pobieranie nazwy usługi wyszukiwania i klucza api-key administratora

Klucz i punkt końcowy usługi wyszukiwania można znaleźć w portalu. Klucz zapewnia dostęp do operacji usługi. Klucze administratora oferują prawa do zapisu niezbędne do tworzenia i usuwania obiektów, takich jak indeksy i indeksatory w usłudze.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i znajdź [usługi wyszukiwania powiązane z Twoją subskrypcją](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Otwórz stronę usługi.

3. W górnej części strony głównej znajdź nazwę usługi. Na poniższym zrzucie ekranu jest to nazwa *azs-tutorial*.

   ![Nazwa usługi](./media/search-indexer-tutorial/service-name.png)

4. Skopiuj i wklej ją jako pierwszy wpis do pliku **appsettings.json** w programie Visual Studio.

   > [!Note]
   > Nazwa usługi jest częścią punktu końcowego, który obejmuje element search.windows.net. Jeśli Cię to interesuje, możesz zobaczyć pełen adres URL w obszarze **Podstawy** na stronie przeglądu. Adres URL wygląda podobnie do następującego przykładu: https://your-service-name.search.windows.net

5. Po lewej stronie w obszarze **Ustawienia** > **Klucze** skopiuj jeden z kluczy administratora i wklej go jako drugi wpis w pliku **appsettings.json**. Klucze to ciągi znaków alfanumerycznych, które są generowane podczas aprowizowania usługi i są wymagane do uzyskiwania autoryzowanego dostępu do operacji usługi. 

   Po dodaniu obu ustawień plik powinien wyglądać podobnie do poniższego przykładu:

   ```json
   {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
   }
   ```

## <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych

W tym kroku zostanie utworzone zewnętrzne źródło danych, które indeksator może przeszukiwać. Plik danych w tym samouczku to *hotels.sql* dostępny w folderze rozwiązania \DotNetHowToIndexers. 

### <a name="azure-sql-database"></a>Azure SQL Database

Przy użyciu witryny Azure Portal i pliku *hotels.sql* z przykładu można utworzyć zestaw danych w bazie danych Azure SQL Database. Usługa Azure Search używa spłaszczonych zestawów wierszy, takich jak zestaw generowany w oparciu o widok lub zapytanie. Plik SQL w przykładowym rozwiązaniu umożliwia utworzenie i wypełnienie pojedynczej tabeli.

W poniższym ćwiczeniu założono, że nie ma istniejących serwerów ani baz danych — obydwa te elementy zostaną utworzone w kroku 2. Jeśli zasób istnieje można również dodać do niego tabelę hotels, zaczynając pracę od kroku 4.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). 

2. Kliknij pozycję **Utwórz zasób** > **SQL Database**, aby utworzyć bazę danych, serwer i grupę zasobów. Możesz użyć wartości domyślnych i warstwy cenowej najniższego poziomu. Jedną z zalet tworzenia serwera jest możliwość określenia nazwy i hasła użytkownika administratora. Są one niezbędne do utworzenia i załadowania tabel w późniejszym kroku.

   ![Strona nowej bazy danych](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Kliknij pozycję **Utwórz**, aby wdrożyć nowy serwer i bazę danych. Poczekaj na wdrożenie serwera i bazy danych.

4. Otwórz stronę bazy danych SQL Database dla nowej bazy danych, jeśli nie jest została wcześniej otwarta. Nazwą zasobu powinna być *SQL Database*, a nie *SQL Server*.

   ![Strona bazy danych SQL Database](./media/search-indexer-tutorial/hotels-db.png)

4. Na pasku poleceń kliknij kolejno pozycje **Narzędzia** > **Edytor zapytań**.

5. Kliknij pozycję **Zaloguj się**, a następnie wprowadź nazwę użytkownika i hasło administratora serwera.

6. Kliknij pozycję **Otwórz zapytanie** i przejdź do lokalizacji pliku *hotels.sql*. 

7. Wybierz plik, a następnie kliknij pozycję **Otwórz**. Skrypt powinien być podobny do tego na poniższym zrzucie ekranu:

   ![Skrypt SQL](./media/search-indexer-tutorial/sql-script.png)

8. Kliknij przycisk **Uruchom**, aby wykonać zapytanie. W okienku wyników powinien zostać wyświetlony komunikat o pomyślnym ukończeniu wykonywania zapytania dla 3 wierszy.

9. Aby zwrócić zestaw wierszy z tej tabeli, w ramach kroku weryfikacyjnego możesz wykonać następujące zapytanie:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    Prototypowe zapytanie, `SELECT * FROM Hotels`, nie działa w Edytorze zapytań. Przykładowe dane obejmują współrzędne geograficzne w polu lokalizacji. Nie są one obecnie obsługiwane w edytorze. Aby wyświetlić listę innych kolumn, dla których można wykonać zapytanie, możesz wykonać tę instrukcję: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Teraz masz już zewnętrzny zestaw danych. Skopiuj parametry połączenia ADO.NET dla bazy danych. Na stronie swojej bazy danych SQL Database przejdź do obszaru **Ustawienia** > **Parametry połączenia** i skopiuj parametry połączenia ADO.NET.
 
    Parametry połączenia ADO.NET wyglądają jak w poniższym przykładzie. Zostały one zmodyfikowane w celu użycia prawidłowej nazwy bazy danych, nazwy użytkownika i hasła.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Wklej parametry połączenia w obszarze „AzureSqlConnectionString” jako trzeci wpis w pliku **appsettings.json** w programie Visual Studio.

    ```json
    {
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Opis kodu indeksatora i indeksu

Kod jest teraz gotowy do skompilowania i uruchomienia. Przed wykonaniem tych czynności poświęć chwilę na zapoznanie się z definicjami indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

  + **hotel.cs**, który zawiera schemat definiujący indeks
  + **Program.cs**, który zawiera funkcje służące do tworzenia struktur i zarządzania nimi w usłudze

### <a name="in-hotelcs"></a>W pliku hotel.cs

Schemat indeksu definiuje kolekcję pól, w tym atrybuty określające dozwolone operacje, takie jak wyszukiwanie pełnotekstowe, filtrowanie lub sortowanie w polu, jak pokazano w poniższej definicji pola HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Schemat może również obejmować inne elementy, w tym profile oceniania na potrzeby poprawiania wyniku wyszukiwania, niestandardowe analizatory i inne konstrukcje. Jednak na nasze potrzeby zdefiniowaliśmy bardzo prosty schemat, który zawiera tylko pola znalezione w przykładowych bazach danych.

W tym samouczku indeksator ściąga dane z jednego źródła danych. W praktyce można dołączyć wiele indeksatorów do tego samego indeksu, tworząc w ten sposób skonsolidowany indeks z możliwością przeszukiwania na podstawie wielu źródeł danych i indeksatorów. Możesz użyć tej samej pary indeks-indeksator różniącej się tylko źródłami danych lub jednego indeksu z różnymi kombinacjami indeksatora i źródła danych, w zależności od tego, w jakim obszarze chcesz zachować elastyczność.

### <a name="in-programcs"></a>W pliku Program.cs

Główny program obejmuje funkcje przeznaczone dla wszystkich trzech reprezentatywnych źródeł danych. Jeśli skoncentrujesz się tylko na usłudze Azure SQL Database, wyróżniają się następujące obiekty:

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

W usłudze Azure Search obiekty, które można wyświetlać, konfigurować lub usuwać niezależnie, obejmują indeksy, indeksatory i źródła danych (odpowiednio *hotels*, *azure-sql-indexer*, *azure-sql*). 

Kolumna *AzureSqlHighWaterMarkColumnName* wymaga szczególnej uwagi, ponieważ udostępna informacje dotyczące wykrywania zmian, których indeksator używa w celu określenia, czy wiersz został zmieniony od czasu ostatniego obciążenia indeksowania. [Zasady wykrywania zmian](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) są obsługiwane tylko w indeksatorach i różnią się w zależności od źródła danych. W usłudze Azure SQL Database możesz wybrać jedną z dwóch zasad, w zależności od wymagań bazy danych.

Poniższy kod przedstawia metody w pliku Program.cs używanym do tworzenia źródła danych i indeksatora. Kod sprawdza i usuwa istniejące zasoby o tej samej nazwie, przy założeniu, że ten program może być uruchamiany wiele razy.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Zauważ, że wywołania interfejsu API indeksatora są niezależne od platformy, z wyjątkiem elementu [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), która określa typ przeszukiwarki do wywołania.

## <a name="run-the-indexer"></a>Uruchamianie indeksatora

W tym kroku program zostanie skompilowany i uruchomiony. 

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **DotNetHowToIndexers** i wybierz pozycję **Kompiluj**.
2. Ponownie kliknij prawym przyciskiem myszy pozycję **DotNetHowToIndexers**, a następnie pozycje **Debuguj** > **Uruchom nowe wystąpienie**.

Program będzie działać w trybie debugowania. Stan każdej operacji będzie zgłaszany w oknie konsoli.

  ![Skrypt SQL](./media/search-indexer-tutorial/console-output.png)

Kod jest uruchamiany lokalnie w programie Visual Studio. Łączy się on z usługą wyszukiwania na platformie Azure, która z kolei używa parametrów połączenia do łączenia z bazą danych Azure SQL Database i pobierania zestawu danych. Przy tak dużej liczbie operacji istnieje kilka punktów, których potencjalnie mogą wystąpić awarie, ale jeśli pojawi się błąd, sprawdź najpierw następujące warunki:

+ Podane informacje o połączeniu usługi wyszukiwania zostały w tym samouczku ograniczone do nazwy usługi. Jeśli wprowadzono pełny adres URL, operacje zatrzymują się na etapie tworzenia indeksu i występuje błąd nawiązywania połączenia.

+ Informacje o połączeniu z bazą danych w pliku **appsettings.json**. Powinny to być parametry połączenia ADO.NET uzyskane z portalu i zmodyfikowane w celu uwzględnienia nazwy użytkownika i hasła, które obowiązują w przypadku Twojej bazy danych. Konto użytkownika musi mieć uprawnienia do pobierania danych.

+ Limity zasobów. Pamiętaj, że usługa udostępniona (bezpłatna) ma limit 3 indeksów, indeksatorów i źródeł danych. W usłudze obsługującej maksymalny limit nie można tworzyć nowych obiektów.

## <a name="search-the-index"></a>Przeszukiwanie indeksu 

W witrynie Azure Portal na stronie przeglądu usługi wyszukiwania kliknij pozycję **Eksplorator wyszukiwania** u góry, aby przesłać kilka zapytań dotyczących nowego indeksu.

1. Kliknij pozycję **Zmień indeks** u góry, aby wybrać indeks *hotels*.

2. Kliknij przycisk **Wyszukaj**, aby utworzyć puste wyszukiwanie. 

   Trzy wpisy w indeksie zostaną zwrócone jako dokumenty JSON. Eksplorator wyszukiwania zwraca dokumenty w formacie JSON, tak, aby można było przeglądać całą strukturę.

3. Następnie wprowadź wyszukiwany ciąg: `search=river&$count=true`. 

   To zapytanie powoduje wywołanie wyszukiwania pełnotekstowego terminu `river`, a wynik obejmuje również liczbę pasujących dokumentów. Zwracanie liczby pasujących dokumentów jest przydatne w przypadku testowania scenariuszy, jeśli masz duży indeks z tysiącami lub milionami dokumentów. W takim przypadku tylko jeden dokument pasuje do zapytania.

4. Na koniec wprowadź wyszukiwany ciąg, który ogranicza dane wyjściowe JSON do odpowiednich pól: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Odpowiedź na zapytanie jest redukowana do wybranych pól, co zapewnia bardziej zwięzły widok danych wyjściowych.

## <a name="view-indexer-configuration"></a>Wyświetlanie konfiguracji indeksatora

W portalu są wyświetlane wszystkie indeksatory, w tym właśnie utworzony przy użyciu programu. Możesz otworzyć definicję indeksatora i wyświetlić źródło danych lub skonfigurować harmonogram odświeżania w celu zidentyfikowania nowych i zmienionych wierszy.

1. Otwórz stronę przeglądu usługi dla usługi Azure Search.
2. Przewiń w dół w celu wyszukania kafelków **Indeksatory** i **Źródła danych**.
3. Kliknij kafelek, aby otworzyć listę każdego zasobu. Możesz wybrać poszczególne indeksatory lub źródła danych w celu wyświetlenia lub zmodyfikowania ustawień konfiguracji.

   ![Kafelki Indeksatory i Źródła danych](./media/search-indexer-tutorial/tiles-portal.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najszybszym sposobem wyczyszczenia środowiska po ukończeniu samouczka jest usunięcie grupy zasobów zawierającej usługę Azure Search. Możesz teraz usunąć tę grupę zasobów, aby trwale usunąć całą jej zawartość. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd usługi Azure Search.

## <a name="next-steps"></a>Kolejne kroki

Do potoku indeksatora możesz dołączyć algorytmy oparte na sztucznej inteligencji. Aby wykonać następny krok, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Indexing Documents in Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie dokumentów w usłudze Azure Blob Storage)