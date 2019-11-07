---
title: Importowanie danych treningowych
titleSuffix: ML Studio (classic) Azure
description: Jak zaimportować dane do Azure Machine Learning Studio (klasycznego) z różnych źródeł danych. Dowiedz się, jakie typy danych i formaty danych są obsługiwane.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 453efc1129ad9ef2a8b68a571c98e3f5620a671f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684791"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importowanie danych szkoleniowych do Azure Machine Learning Studio (klasyczne) z różnych źródeł danych

Aby użyć własnych danych w Machine Learning Studio (klasyczny) do opracowania i uczenia rozwiązania do analizy predykcyjnej, możesz użyć danych z: 

* **Lokalny plik** — Załaduj dane lokalne wcześniej z dysku twardego, aby utworzyć moduł DataSet w Twoim obszarze roboczym
* **Źródła danych w trybie online** — za pomocą modułu [Importuj dane][import-data] można uzyskiwać dostęp do danych z jednego z kilku źródeł online, gdy działa eksperyment
* **Machine Learning Studio (klasyczny) eksperyment** — Użyj danych zapisanych jako zestaw danych w klasycznej wersji Machine Learning Studio
* [**Lokalna baza danych SQL Server**](use-data-from-an-on-premises-sql-server.md) — używanie danych z lokalnej SQL Server bazy danych bez konieczności ręcznego kopiowania danych

> [!NOTE]
> Istnieje wiele przykładowych zestawów danych dostępnych w klasycznej wersji Machine Learning Studio, których można użyć do uczenia się. Aby uzyskać informacje na ten temat, zobacz [Korzystanie z przykładowych zestawów danych w Azure Machine Learning Studio (klasyczne)](use-sample-datasets.md).

## <a name="prepare-data"></a>Przygotowywanie danych

Klasyczna wersja Machine Learning Studio została zaprojektowana tak, aby działała z danymi prostokątnymi lub tabelarycznymi, takimi jak dane tekstowe, które są rozdzielane lub dane strukturalne z bazy danych, chociaż w niektórych okolicznościach mogą być używane dane nieprostokątne.

Najlepiej, jeśli dane są stosunkowo czyste przed zaimportowaniem ich do klasycznej wersji programu Studio. Na przykład warto zadbać o problemy, takie jak ciągi nieujęte w cudzysłów.

Dostępne są jednak moduły w klasycznej wersji programu Studio, które umożliwiają manipulowanie danymi w ramach eksperymentu po zaimportowaniu danych. W zależności od algorytmów uczenia maszynowego, które będą używane, może zajść konieczność podjęcia decyzji o tym, jak będą obsługiwane problemy strukturalne danych, takie jak brakujące wartości i dane rozrzedzone, oraz istnieją moduły, które mogą być w nim pomocne. Zapoznaj się z sekcją **Przekształcanie danych** w palecie modułów dla modułów, które wykonują te funkcje.

W dowolnym momencie eksperymentu możesz wyświetlić lub pobrać dane, które są wytwarzane przez moduł, klikając port wyjściowy. W zależności od modułu mogą być dostępne różne opcje pobierania lub można wizualizować dane w przeglądarce internetowej w klasycznej wersji programu Studio.

## <a name="supported-data-formats-and-data-types"></a>Obsługiwane formaty danych i typy danych

Możesz zaimportować wiele typów danych do eksperymentu, w zależności od tego, jaki mechanizm służy do importowania danych i skąd pochodzą z:

* Zwykły tekst (. txt)
* Wartości rozdzielane przecinkami (CSV) z nagłówkiem (CSV) lub bez (. NH. csv)
* Wartości rozdzielane tabulatorami (TSV) z nagłówkiem (. tsv) lub bez (. NH. tsv)
* Plik programu Excel
* Tabela platformy Azure
* Tabela programu Hive
* Tabela bazy danych SQL
* Wartości OData
* SVMLight Data (. SVMLight) (zobacz [definicję SVMLight](http://svmlight.joachims.org/) , aby uzyskać informacje o formacie)
* Plik relacji atrybutu (ARFF) — dane (. ARFF) (zobacz [definicję ARFF](https://weka.wikispaces.com/ARFF) , aby uzyskać informacje o formacie)
* Plik zip (zip)
* Obiekt R lub plik obszaru roboczego (. RData

W przypadku zaimportowania danych w formacie takim jak ARFF, który zawiera metadane, w klasycznej wersji programu Studio są używane te metadane do definiowania nagłówka i typu danych każdej kolumny.

W przypadku zaimportowania danych, takich jak TSV lub format CSV, które nie zawierają tych metadanych, klasyczna wersja programu Studio wnioskuje typ danych dla każdej kolumny przez próbkowanie danych. Jeśli dane również nie mają nagłówków kolumn, klasyczna wersja programu Studio zawiera nazwy domyślne.

Można jawnie określić lub zmienić nagłówki i typy danych dla kolumn za pomocą modułu [Edytowanie metadanych][edit-metadata] .

Następujące typy danych są rozpoznawane przez klasyczną wersję programu Studio:

* Ciąg
* Liczba całkowita
* Double
* Wartość logiczna
* DateTime
* Czasu

Studio używa wewnętrznego typu danych o nazwie ***tabela danych*** do przekazywania danych między modułami. Dane można jawnie przekonwertować na format tabeli danych przy użyciu modułu [Konwertuj na zestaw][convert-to-dataset] danych.

Każdy moduł, który akceptuje formaty inne niż tabela danych, przekonwertuje dane do tabeli danych dyskretnie przed przekazaniem ich do następnego modułu.

W razie potrzeby można przekonwertować Format tabeli danych z powrotem do formatu CSV, TSV, ARFF lub SVMLight przy użyciu innych modułów konwersji.
Zapoznaj się z sekcją **konwersje formatu danych** w palecie modułów dla modułów, które wykonują te funkcje.

## <a name="data-capacities"></a>Pojemności danych

Moduły w klasycznej wersji Machine Learning Studio obsługują zestawy danych o pojemności do 10 GB w przypadku wspólnych przypadków użycia. Jeśli moduł przyjmuje więcej niż jedną operację wprowadzania danych wejściowych, wówczas 10 GB to łączny rozmiar wszystkich danych wejściowych. Większe zestawy danych można próbkować za pomocą zapytań z gałęzi Hive lub Azure SQL Database lub można użyć funkcji uczenie przez zliczanie wstępne przed zaimportowaniem danych.  

Podczas normalizacji funkcji następujące typy danych mogą ulegać rozszerzaniu do większych zestawów danych. Takie dane muszą być mniejsze niż 10 GB:

* Rozrzedzone
* Podzielone na kategorie
* Ciągi
* Dane binarne

W przypadku następujących modułów obowiązuje ograniczenie do zestawów danych mniejszych niż 10 GB:

* Moduły polecania
* Moduł Synthetic Minority Oversampling Technique (SMOTE)
* Moduły skryptów: R, Python, SQL
* Moduły, w których rozmiar danych wyjściowych może być większy niż rozmiar danych wejściowych, na przykład Przyłączenie lub Tworzenie skrótu funkcji
* Krzyżowa weryfikacja, Hiperparametry modelu strojenia, Regresja porządkowa oraz Multiklasa Jedna kontra wszystkie, gdy liczba iteracji jest bardzo duża

W przypadku zestawów danych o rozmiarach większych niż kilka GB należy przekazać dane do usługi Azure Storage lub Azure SQL Database lub użyć usługi Azure HDInsight zamiast przekazywania ich bezpośrednio z pliku lokalnego.

Informacje o danych obrazu można znaleźć w dokumentacji modułu [Import images](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) .

## <a name="import-from-a-local-file"></a>Importuj z pliku lokalnego

Można przekazać plik danych z dysku twardego, aby używać go jako danych szkoleniowych w klasycznej wersji programu Studio. Podczas importowania pliku danych należy utworzyć moduł DataSet gotowy do użycia w eksperymentach w obszarze roboczym.

Aby zaimportować dane z lokalnego dysku twardego, wykonaj następujące czynności:

1. Kliknij pozycję **+ Nowy** u dołu okna Studio (klasyczne).
2. Wybierz pozycję **zestaw danych** i **z pliku lokalnego**.
3. W oknie dialogowym **Przekaż nowy zestaw danych** przejdź do pliku, który chcesz przekazać.
4. Wprowadź nazwę, zidentyfikuj typ danych i opcjonalnie wprowadź opis. Zalecany opis — umożliwia rejestrowanie wszelkich cech dotyczących danych, które mają być zapamiętywane podczas korzystania z danych w przyszłości.
5. Pole wyboru **jest to nowa wersja istniejącego zestawu danych** umożliwia zaktualizowanie istniejącego zestawu danych przy użyciu nowych danych. Aby to zrobić, kliknij to pole wyboru, a następnie wprowadź nazwę istniejącego zestawu danych.

![Przekaż nowy zestaw danych](./media/import-data/upload-dataset-from-local-file.png)

Czas przekazywania zależy od rozmiaru danych i szybkości połączenia z usługą. Jeśli wiesz, że plik zajmie dużo czasu, możesz wykonać inne czynności w klasycznej wersji programu Studio, gdy zaczekasz. Jednak zamknięcie przeglądarki przed ukończeniem przekazywania danych spowoduje niepowodzenie przekazywania.

Gdy dane zostaną przekazane, są przechowywane w module DataSet i dostępne dla każdego eksperymentu w obszarze roboczym.

Podczas edytowania eksperymentu można znaleźć zestawy danych, które zostały przekazane na liście **moje zbiory danych** na liście **zapisane zestawy danych** w palecie modułów. Możesz przeciągać i upuszczać zestaw danych na kanwę eksperymentu, gdy chcesz użyć zestawu danych do dalszej analizy i uczenia maszynowego.

## <a name="import-from-online-data-sources"></a>Importuj ze źródeł danych online

Korzystając z modułu [Importuj dane][import-data] , eksperyment może importować dane z różnych źródeł danych online, gdy działa eksperyment.

> [!NOTE]
> Ten artykuł zawiera ogólne informacje dotyczące modułu [Import danych][import-data] . Aby uzyskać bardziej szczegółowe informacje na temat typów danych, do których można uzyskać dostęp, formatów, parametrów i odpowiedzi na często zadawane pytania, zobacz temat odwołanie do modułu dla modułu [Importuj dane][import-data] .

Korzystając z modułu [Importuj dane][import-data] , można uzyskać dostęp do danych z jednego z kilku źródeł danych online, gdy działa eksperyment:

* Adres URL sieci Web przy użyciu protokołu HTTP
* Usługa Hadoop przy użyciu HiveQL
* Azure Blob Storage
* Tabela platformy Azure
* Usługa Azure SQL Database lub SQL Server na maszynie wirtualnej platformy Azure
* Lokalna baza danych SQL Server
* Dostawca strumieniowego źródła danych, obecnie
* Azure Cosmos DB

Ponieważ te dane szkoleniowe są dostępne, gdy eksperyment jest uruchomiony, jest dostępny tylko w tym doświadczeniu. Dzięki porównaniu dane przechowywane w module DataSet są dostępne dla każdego eksperymentu w obszarze roboczym.

Aby uzyskać dostęp do źródeł danych online w doświadczeniu w programie Studio (klasycznym), Dodaj moduł [Import danych][import-data] do eksperymentu. Następnie wybierz pozycję **Uruchom Kreatora importowania danych** w obszarze **Właściwości** instrukcji krok po kroku, aby wybrać i skonfigurować źródło danych. Alternatywnie można ręcznie wybrać **Źródło danych** w obszarze **Właściwości** i podać parametry potrzebne do uzyskania dostępu do danych.

Obsługiwane źródła danych w trybie online są wyszczególnione w poniższej tabeli. Ta tabela zawiera również podsumowanie obsługiwanych formatów plików i parametrów, które są używane do uzyskiwania dostępu do danych.

> [!IMPORTANT]
> Obecnie moduły [Import][import-data] danych i [Eksportowanie danych][export-data] mogą odczytywać i zapisywać dane tylko z usługi Azure Storage utworzonej przy użyciu klasycznego modelu wdrażania. Innymi słowy, nowy typ konta usługi Azure Blob Storage, który oferuje warstwę dostępu do magazynu gorącego lub warstwę dostępu do magazynu chłodnego, nie jest jeszcze obsługiwany.
>
> Ogólnie rzecz biorąc, wszystkie konta usługi Azure Storage, które mogły zostać utworzone przed udostępnieniem tej opcji usługi, nie powinny mieć żadnych zmian.
> Jeśli musisz utworzyć nowe konto, wybierz opcję **klasyczne** dla modelu wdrożenia lub Użyj usługi Resource Manager i wybierz pozycję **ogólnego przeznaczenia** zamiast **magazynu obiektów BLOB** dla danego **rodzaju konta**.
>
> Aby uzyskać więcej informacji, zobacz [Azure Blob Storage: warstwy magazynowania gorąca i chłodna](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Obsługiwane źródła danych online
Klasyczna wersja modułu Azure Machine Learning Studio **Importowanie danych** obsługuje następujące źródła danych:

| Źródło danych | Opis | Parametry |
| --- | --- | --- |
| Adres URL sieci Web za pośrednictwem protokołu HTTP |Odczytuje dane w formacie wartości rozdzielanych przecinkami (CSV), wartości rozdzielane znakami tabulacji (TSV), format pliku relacji atrybutu (ARFF) i obsługują formaty maszyny wektorowe (SVM-Light) z dowolnego adresu URL sieci Web korzystającego z protokołu HTTP |<b>URL</b>: Określa pełną nazwę pliku, w tym adres URL witryny i nazwę pliku, z dowolnym rozszerzeniem. <br/><br/><b>Format danych</b>: określa jeden z obsługiwanych formatów danych: CSV, TSV, ARFF lub SVM-jasna. Jeśli dane zawierają wiersz nagłówka, jest on używany do przypisywania nazw kolumn. |
| Hadoop/HDFS |Odczytuje dane z magazynu rozproszonego w usłudze Hadoop. Należy określić dane, które mają być używane w HiveQL, języku zapytań przypominającym język SQL. HiveQL może również służyć do agregowania danych i wykonywania filtrowania danych przed dodaniem danych do klasycznej wersji programu Studio. |<b>Zapytanie bazy danych Hive</b>: określa zapytanie programu Hive używane do generowania danych.<br/><br/><b>Identyfikator URI serwera HCatalog</b> : Określ nazwę klastra przy użyciu formatu *&lt;nazwę klastra&gt;. azurehdinsight.NET.*<br/><br/><b>Nazwa konta użytkownika usługi Hadoop</b>: Określa nazwę konta użytkownika usługi Hadoop służącą do aprowizacji klastra.<br/><br/><b>Hasło konta użytkownika usługi Hadoop</b> : określa poświadczenia używane podczas aprowizacji klastra. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów Hadoop w usłudze HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Lokalizacja danych wyjściowych</b>: określa, czy dane są przechowywane w rozproszonym systemie plików HADOOP (HDFS), czy na platformie Azure. <br/><ul>Jeśli dane wyjściowe są przechowywane w systemie plików HDFS, określ identyfikator URI serwera HDFS. (Pamiętaj, aby użyć nazwy klastra usługi HDInsight bez prefiksu HTTPS://). <br/><br/>Jeśli dane wyjściowe są przechowywane na platformie Azure, należy określić nazwę konta usługi Azure Storage, klucz dostępu do magazynu i nazwę kontenera magazynu.</ul> |
| Baza danych SQL |Odczytuje dane przechowywane w bazie danych Azure SQL lub w bazie danych SQL Server działającej na maszynie wirtualnej platformy Azure. |<b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym jest uruchomiona baza danych programu.<br/><ul>W przypadku Azure SQL Database wprowadź wygenerowaną nazwę serwera. Zazwyczaj ma on postać *&lt;generated_identifier&gt;. Database.Windows.NET.* <br/><br/>W przypadku programu SQL Server hostowanego na maszynie wirtualnej platformy Azure wprowadź wartość *TCP:&lt;nazwę DNS maszyny wirtualnej&gt;, 1433*</ul><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika dla konta, które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Hasło konta użytkownika serwera</b>: Określa hasło dla konta użytkownika.<br/><br/><b>Zapytanie bazy danych</b>: wprowadź instrukcję SQL opisującą dane, które mają zostać odczytane. |
| Lokalna baza danych SQL |Odczytuje dane przechowywane w lokalnej bazie danych SQL. |<b>Brama danych</b>: Określa nazwę bramy zarządzanie danymi zainstalowanej na komputerze, na którym może uzyskać dostęp do bazy danych SQL Server. Informacje o konfigurowaniu bramy znajdują się w temacie [wykonywanie zaawansowanej analizy za pomocą klasycznej wersji Azure Machine Learning Studio przy użyciu danych z lokalnego programu SQL Server](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nazwa serwera bazy danych</b>: Określa nazwę serwera, na którym jest uruchomiona baza danych programu.<br/><br/><b>Nazwa bazy danych </b>: Określa nazwę bazy danych na serwerze. <br/><br/><b>Nazwa konta użytkownika serwera</b>: Określa nazwę użytkownika dla konta, które ma uprawnienia dostępu do bazy danych. <br/><br/><b>Nazwa użytkownika i hasło</b>: kliknij przycisk <b>wprowadź wartości</b> , aby wprowadzić poświadczenia bazy danych. W zależności od konfiguracji lokalnego SQL Server, można użyć uwierzytelniania zintegrowanego systemu Windows lub uwierzytelniania SQL Server.<br/><br/><b>Zapytanie bazy danych</b>: wprowadź instrukcję SQL opisującą dane, które mają zostać odczytane. |
| Tabela platformy Azure |Odczytuje dane z Table service w usłudze Azure Storage.<br/><br/>Jeśli często odczytujesz duże ilości danych, Użyj usługi Azure Table Service. Zapewnia elastyczne, nierelacyjne (NoSQLe), wysoce skalowalne i niedrogie rozwiązanie magazynu o wysokiej dostępności. |Opcje w obszarze **Importuj dane** zmieniają się w zależności od tego, czy uzyskujesz dostęp do informacji publicznych lub prywatnego konta magazynu wymagającego poświadczeń logowania. Jest to określane przez <b>Typ uwierzytelniania</b> , który może mieć wartość "PublicOrSAS" lub "Account", z których każdy ma własny zestaw parametrów. <br/><br/><b>Identyfikator URI sygnatury dostępu współdzielonego (SAS)</b>: parametry są następujące:<br/><br/><ul><b>Identyfikator URI tabeli</b>: określa publiczny lub adres URL sygnatury dostępu współdzielonego dla tabeli.<br/><br/><b>Określa wiersze do skanowania w poszukiwaniu nazw właściwości</b>: wartości są <i>TopN</i> do skanowania określonej liczby wierszy lub <i>ScanAll</i> , aby pobrać wszystkie wiersze z tabeli. <br/><br/>Jeśli dane są jednorodne i przewidywalne, zaleca się wybranie opcji *TopN* i wprowadzenie numeru N. W przypadku dużych tabel może to spowodować szybsze odczytywanie czasu.<br/><br/>Jeśli dane są strukturalne z zestawami właściwości, które różnią się w zależności od głębokości i położenia tabeli, wybierz opcję *ScanAll* , aby skanować wszystkie wiersze. Pozwala to zagwarantować integralność powstających właściwości i konwersji metadanych.<br/><br/></ul><b>Prywatne konto magazynu</b>: parametry są następujące: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta, które zawiera tabelę do odczytania.<br/><br/><b>Klucz konta</b>: określa klucz magazynu skojarzony z kontem.<br/><br/><b>Nazwa tabeli</b> : Określa nazwę tabeli zawierającej dane do odczytania.<br/><br/><b>Wiersze do skanowania nazw właściwości</b>: wartości są <i>TopN</i> do skanowania określonej liczby wierszy lub <i>ScanAll</i> , aby pobrać wszystkie wiersze z tabeli.<br/><br/>Jeśli dane są jednorodne i przewidywalne, zalecamy wybranie opcji *TopN* i wprowadzenie numeru N. W przypadku dużych tabel może to spowodować szybsze odczytywanie czasu.<br/><br/>Jeśli dane są strukturalne z zestawami właściwości, które różnią się w zależności od głębokości i położenia tabeli, wybierz opcję *ScanAll* , aby skanować wszystkie wiersze. Pozwala to zagwarantować integralność powstających właściwości i konwersji metadanych.<br/><br/> |
| Azure Blob Storage |Odczytuje dane przechowywane w Blob service w usłudze Azure Storage, w tym obrazy, tekst bez struktury lub dane binarne.<br/><br/>Blob service można użyć do publicznego ujawnienia danych lub do prywatnego przechowywania danych aplikacji. Dostęp do danych można uzyskać z dowolnego miejsca przy użyciu połączeń HTTP lub HTTPS. |Opcje w module **Importuj dane** zmieniają się w zależności od tego, czy uzyskujesz dostęp do informacji publicznych lub prywatnego konta magazynu wymagającego poświadczeń logowania. Jest to określane przez <b>Typ uwierzytelniania</b> , który może mieć wartość "PublicOrSAS" lub "Account".<br/><br/><b>Identyfikator URI sygnatury dostępu współdzielonego (SAS)</b>: parametry są następujące:<br/><br/><ul><b>URI</b>: określa publiczny lub adres URL sygnatury dostępu współdzielonego dla obiektu blob magazynu.<br/><br/><b>Format pliku</b>: określa format danych w BLOB Service. Obsługiwane formaty to CSV, TSV i ARFF.<br/><br/></ul><b>Prywatne konto magazynu</b>: parametry są następujące: <br/><br/><ul><b>Nazwa konta</b>: Określa nazwę konta, które zawiera obiekt BLOB, który ma zostać odczytany.<br/><br/><b>Klucz konta</b>: określa klucz magazynu skojarzony z kontem.<br/><br/><b>Ścieżka do kontenera, katalogu lub obiektu BLOB</b> : Określa nazwę obiektu BLOB, który zawiera dane do odczytu.<br/><br/><b>Format pliku BLOB</b>: określa format danych w usłudze BLOB. Obsługiwane formaty danych to CSV, TSV, ARFF, CSV z określonym kodowaniem i program Excel. <br/><br/><ul>Jeśli format jest CSV lub TSV, należy się upewnić, czy plik zawiera wiersz nagłówka.<br/><br/>Możesz użyć opcji programu Excel, aby odczytać dane ze skoroszytów programu Excel. W opcji <i>Format danych programu Excel</i> wskaż, czy dane mieszczą się w zakresie arkusza programu Excel, czy w tabeli programu Excel. W <i>arkuszu programu Excel lub osadzonej tabeli </i>Określ nazwę arkusza lub tabeli, z której chcesz się odczytywać.</ul><br/> |
| Dostawca strumieniowych źródeł danych |Odczytuje dane z obsługiwanego dostawcy źródła strumieniowego. Obecnie obsługiwany jest tylko format Open Data Protocol (OData). |<b>Typ zawartości danych</b>: określa format OData.<br/><br/><b>Źródłowy adres URL</b>: określa pełny adres URL strumieniowego źródła danych. <br/>Na przykład następujący adres URL odczytuje z przykładowej bazy danych Northwind: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importuj z innego eksperymentu

Jeśli chcesz wykonać pośredni wynik z jednego eksperymentu i użyć go jako części innego eksperymentu, będziesz mieć dużo czasu. W tym celu należy zapisać moduł jako zestaw danych:

1. Kliknij dane wyjściowe modułu, który chcesz zapisać jako zestaw danych.
2. Kliknij przycisk **Zapisz jako zestaw danych**.
3. Po wyświetleniu monitu wprowadź nazwę i opis, które pozwolą na łatwą identyfikację zestawu danych.
4. Kliknij przycisk **OK** .

Po zakończeniu zapisywania zestaw danych będzie dostępny do użycia w ramach dowolnego eksperymentu w obszarze roboczym. Można go znaleźć na liście **zapisane zestawy danych** w palecie modułów.

## <a name="next-steps"></a>Następne kroki

[Wdrażanie usług sieci Web Azure Machine Learning Studio korzystających z modułów import danych i eksportowanie danych](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
