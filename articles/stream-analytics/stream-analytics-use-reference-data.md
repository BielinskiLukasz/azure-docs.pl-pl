---
title: Używanie danych referencyjnych do wyszukiwania w Azure Stream Analytics
description: W tym artykule opisano sposób używania danych referencyjnych do wyszukiwania lub skorelowania danych w projekcie zapytania Azure Stream Analytics zadania.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 8d094113107d8c49e34779cf8be62ecd71cb8cce
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937201"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Używanie danych referencyjnych do wyszukiwania w Stream Analytics

Dane referencyjne (nazywane również tabelami odnośników) to zbiór danych, który jest statyczny lub wolno zmieniany w charakterze, używany do przeszukiwania lub rozszerzania strumieni danych. Na przykład w scenariuszu IoT można przechowywać metadane dotyczące czujników (które często nie zmieniają się) w danych referencyjnych i przyłączać je do strumieni danych IoT w czasie rzeczywistym. Azure Stream Analytics ładuje dane referencyjne w pamięci, aby osiągnąć Przetwarzanie strumienia o małym opóźnieniu. Aby korzystać z danych referencyjnych w zadaniu Azure Stream Analytics, zazwyczaj użyjesz [sprzężenia danych referencyjnych](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) w zapytaniu. 

Stream Analytics obsługuje magazyn obiektów blob platformy Azure i Azure SQL Database jako warstwę magazynu dla danych referencyjnych. Możesz również przekształcić i/lub skopiować dane referencyjne do usługi BLOB Storage z Azure Data Factory, aby korzystać z [dowolnej liczby magazynów danych opartych na chmurze i lokalnych](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Magazyn obiektów blob platformy Azure

Dane referencyjne są modelowane jako sekwencja obiektów BLOB (zdefiniowanych w konfiguracji wejściowej) w kolejności rosnącej daty/godziny określonej w nazwie obiektu BLOB. Obsługuje on **tylko** Dodawanie do końca sekwencji przy użyciu daty/godziny **większej** niż określona przez ostatni obiekt BLOB w sekwencji.

### <a name="configure-blob-reference-data"></a>Skonfiguruj dane referencyjne obiektów BLOB

Aby skonfigurować dane referencyjne, musisz najpierw utworzyć dane wejściowe typu **dane referencyjne**. W poniższej tabeli objaśniono każdą właściwość, która będzie potrzebna podczas tworzenia danych referencyjnych, z opisem:

|**Nazwa właściwości**  |**Opis**  |
|---------|---------|
|Alias wejściowy   | Przyjazna nazwa, która zostanie użyta w zapytaniu zadania, aby odwołać się do tego danych wejściowych.   |
|Konto magazynu   | Nazwa konta magazynu, w którym znajdują się obiekty blob. Jeśli znajduje się w tej samej subskrypcji co zadanie Stream Analytics, możesz wybrać ją z listy rozwijanej.   |
|Klucz konta magazynu   | Klucz tajny skojarzony z kontem magazynu. Ta wartość zostanie wypełniona automatycznie, jeśli konto magazynu znajduje się w tej samej subskrypcji co zadanie Stream Analytics.   |
|Kontener magazynu   | Kontenery zapewniają logiczne grupowanie obiektów BLOB przechowywanych w Blob service Microsoft Azure. Po przekazaniu obiektu BLOB do Blob service należy określić kontener dla tego obiektu BLOB.   |
|Wzorzec ścieżki   | Ścieżka używana do lokalizowania obiektów BLOB w określonym kontenerze. W ścieżce możesz określić jedno lub więcej wystąpień następujących dwóch zmiennych:<BR>{Date}, {Time}<BR>Przykład 1: produkty/{Date}/{Time}/Product-List. csv<BR>Przykład 2: produkty/{Date}/Product-List. csv<BR>Przykład 3: Product-List. csv<BR><br> Jeśli obiekt BLOB nie istnieje w określonej ścieżce, zadanie Stream Analytics będzie oczekiwać, że obiekt BLOB stanie się nieokreślony.   |
|Format daty [opcjonalnie]   | Jeśli używasz {Date} w określonym wzorcu ścieżki, możesz wybrać format daty, w którym obiekty blob są zorganizowane z listy rozwijanej obsługiwanych formatów.<BR>Przykład: RRRR/MM/DD, MM/DD/RRRR itd.   |
|Format czasu [opcjonalnie]   | Jeśli użyto {Time} w określonym wzorcu ścieżki, można wybrać format czasu, w którym obiekty blob są zorganizowane z listy rozwijanej obsługiwanych formatów.<BR>Przykład: gg, HH/mm lub HH-mm.  |
|Format serializacji zdarzeń   | Aby upewnić się, że zapytania działają w oczekiwany sposób, Stream Analytics należy wiedzieć, który format serializacji jest używany w przypadku przychodzących strumieni danych. W przypadku danych referencyjnych obsługiwane formaty to CSV i JSON.  |
|Kody   | W tym momencie jedynym obsługiwanym formatem kodowania jest UTF-8.  |

### <a name="static-reference-data"></a>Statyczne dane referencyjne

Jeśli dane referencyjne nie powinny być zmieniane, obsługa statycznych danych referencyjnych jest włączana przez określenie ścieżki statycznej w konfiguracji wejściowej. Azure Stream Analytics pobiera obiekt BLOB z określonej ścieżki. tokeny podstawiania {date} i {Time} nie są wymagane. Ponieważ dane referencyjne są niezmienne w Stream Analytics, zastępowanie statycznego obiektu BLOB danych odwołania nie jest zalecane.

### <a name="generate-reference-data-on-a-schedule"></a>Generuj dane referencyjne według harmonogramu

Jeśli dane referencyjne są wolnie zmienianym zestawem danych, to obsługa odświeżania danych referencyjnych jest włączana przez określenie wzorca ścieżki w konfiguracji wejściowej przy użyciu tokenów podstawiania {date} i {Time}. Stream Analytics pobiera zaktualizowane definicje danych referencyjnych na podstawie tego wzorca ścieżki. Na przykład wzorzec `sample/{date}/{time}/products.csv` z formatem daty **"rrrr-mm-dd"** i formatem czasu **"gg-mm"** powoduje, że Stream Analytics do pobrania zaktualizowanego obiektu BLOB `sample/2015-04-16/17-30/products.csv` o godzinie 5:30 PM w 16 kwietnia 2015 strefie czasowej UTC.

Azure Stream Analytics automatycznie skanuje w poszukiwaniu odświeżonych obiektów BLOB danych referencyjnych w ciągu jednej minuty. Jeśli obiekt BLOB z sygnaturą czasową 10:30:00 jest przekazywany z niewielkim opóźnieniem (na przykład 10:30:30), zobaczysz małe opóźnienie w Stream Analytics zadania odwołującego się do tego obiektu BLOB. Aby uniknąć takich scenariuszy, zaleca się przekazanie obiektu BLOB wcześniej niż docelowy czas obowiązywania (10:30:00 w tym przykładzie), aby umożliwić Stream Analytics zadanie wystarczającą ilość czasu na odnalezienie i załadowanie go w pamięci oraz wykonanie operacji. 

> [!NOTE]
> Obecnie Stream Analytics zadania wyszukują odświeżanie obiektów BLOB tylko wtedy, gdy komputer przekroczy czas zakodowany w nazwie obiektu BLOB. Na przykład zadanie będzie wyglądać `sample/2015-04-16/17-30/products.csv` najszybciej, jak to możliwe, ale nie wcześniej niż 5:30 PM, 16 kwietnia 2015, strefa czasowa czasu UTC. *Nigdy nie* będzie szukać obiektu BLOB z zakodowanym czasem wcześniejszym niż ostatni wykryty.
> 
> Na przykład, gdy zadanie odnajdzie obiekt BLOB `sample/2015-04-16/17-30/products.csv` zignoruje wszystkie pliki z zakodowaną datą wcześniejszą niż 5:30 PM kwietnia, 2015, więc jeśli zostanie utworzony obiekt BLOB o późnej nabycie `sample/2015-04-16/17-25/products.csv` w tym samym kontenerze, zadanie nie będzie go używać.
> 
> Podobnie, jeśli wartość `sample/2015-04-16/17-30/products.csv` jest generowana tylko o godzinie 10:03 2015, 16 kwietnia, ale w kontenerze nie występuje obiekt BLOB z wcześniejszą datą, zadanie użyje tego pliku, rozpoczynając od 10:03 od 1 kwietnia 2015 16.
> 
> Wyjątkiem jest to, kiedy zadanie musi ponownie przetworzyć dane w czasie lub podczas pierwszego uruchomienia zadania. W czasie uruchamiania zadanie szuka najnowszego obiektu BLOB wygenerowanego przed określonym czasem rozpoczęcia zadania. W tym celu upewnij się, że podczas uruchamiania zadania istnieje zestaw danych referencyjnych, które **nie są puste** . Jeśli nie można go znaleźć, zadanie Wyświetla następującą diagnostykę: `Initializing input without a valid reference data blob for UTC time <start time>`.

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) może służyć do organizowania zadania tworzenia zaktualizowanych obiektów BLOB wymaganych przez Stream Analytics do aktualizowania definicji danych referencyjnych. Data Factory to oparta na chmurze usługa integracji danych, która organizuje i automatyzuje przenoszenie i transformację danych. Data Factory obsługuje [łączenie z dużą liczbą magazynów danych opartych na chmurze i lokalnymi](../data-factory/copy-activity-overview.md) oraz szybkie przeniesienie danych zgodnie z regularnym określonym harmonogramem. Aby uzyskać więcej informacji i wskazówki krok po kroku dotyczące konfigurowania potoku Data Factory w celu wygenerowania danych referencyjnych dla Stream Analytics, które odświeżają zgodnie ze wstępnie zdefiniowanym harmonogramem, zapoznaj się z tym [przykładem](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)w witrynie GitHub.

### <a name="tips-on-refreshing-blob-reference-data"></a>Porady dotyczące odświeżania danych referencyjnych obiektów BLOB

1. Nie należy zastępować obiektów BLOB danych referencyjnych, ponieważ są one niezmienne.
2. Zalecanym sposobem odświeżania danych referencyjnych jest:
    * Użyj {Date}/{Time} we wzorcu ścieżki
    * Dodaj nowy obiekt BLOB przy użyciu tego samego kontenera i wzorca ścieżki zdefiniowanego w danych wejściowych zadania
    * Użyj daty/godziny **większej** niż wartość określona przez ostatni obiekt BLOB w sekwencji.
3. Obiekty BLOB danych referencyjnych **nie** są uporządkowane według czasu "Ostatnia modyfikacja" obiektu BLOB, ale tylko według czasu i daty określonej w nazwie obiektu BLOB przy użyciu podstawiania {date} i {Time}.
3. Aby uniknąć konieczności wyświetlania listy dużej liczby obiektów blob, należy rozważyć usunięcie bardzo starych obiektów blob, dla których przetwarzanie nie zostanie już wykonane. Należy pamiętać, że w przypadku programu ASA może wystąpić konieczność ponownego przetworzenia niewielkiej ilości w niektórych scenariuszach, takich jak ponowne uruchomienie.

## <a name="azure-sql-database"></a>Azure SQL Database

Dane referencyjne Azure SQL Database są pobierane przez zadanie Stream Analytics i są przechowywane jako migawka w pamięci do przetwarzania. Migawka danych referencyjnych jest również przechowywana w kontenerze na koncie magazynu określonym w ustawieniach konfiguracji. Kontener jest tworzony przy użyciu autostartu, gdy zadanie zostanie uruchomione. Jeśli zadanie zostanie zatrzymane lub przejdzie do stanu niepowodzenia, tworzone przez siebie kontenery zostaną usunięte po ponownym uruchomieniu zadania.  

Jeśli dane referencyjne są wolnie zmieniającymi się zestawami danych, należy okresowo odświeżyć migawkę używaną w danym zadaniu. Stream Analytics pozwala ustawić częstotliwość odświeżania podczas konfigurowania połączenia danych wejściowych Azure SQL Database. Środowisko uruchomieniowe Stream Analytics będzie wysyłać zapytania do Azure SQL Database w interwale określonym przez częstotliwość odświeżania. Częstotliwość odświeżania jest obsługiwana raz na minutę. Dla każdego odświeżenia Stream Analytics przechowuje nową migawkę w podanym koncie magazynu.

Stream Analytics dostępne są dwie opcje wykonywania zapytań dotyczących Azure SQL Database. Zapytanie migawki jest wymagane i musi zostać dołączone do każdego zadania. Stream Analytics uruchamia zapytanie migawek okresowo na podstawie interwału odświeżania i używa wyniku zapytania (migawka) jako zestawu danych referencyjnych. Zapytanie migawki powinno być zgodne z większością scenariuszy, ale w przypadku problemów z wydajnością z dużymi zestawami danych i szybkością szybkiego odświeżania można użyć opcji zapytania Delta. W przypadku zapytań, które mają więcej niż 60 sekund, aby zwrócić zestaw danych referencyjnych, spowoduje to przekroczenie limitu czasu.

Przy użyciu opcji zapytania Delta Stream Analytics początkowo uruchamia zapytanie migawki w celu pobrania zestawu danych referencyjnych punktu odniesienia. Po uruchomieniu Stream Analytics okresowo wykonywać zapytania Delta na podstawie interwału odświeżania, aby pobrać zmiany przyrostowe. Te zmiany przyrostowe są ciągle stosowane do zestawu danych referencyjnych, aby zachować jego aktualizację. Użycie zapytania różnicowego może pomóc w zmniejszeniu kosztów magazynu i operacji we/wy sieci.

### <a name="configure-sql-database-reference"></a>Skonfiguruj odwołanie SQL Database

Aby skonfigurować dane referencyjne SQL Database, musisz najpierw utworzyć dane **referencyjne** . W poniższej tabeli objaśniono każdą właściwość, która będzie potrzebna podczas tworzenia danych referencyjnych, z opisem. Aby uzyskać więcej informacji, zobacz [Korzystanie z danych referencyjnych z SQL Database dla zadania Azure Stream Analytics](sql-reference-data.md).

Można użyć [Azure SQL Database wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) jako dane wejściowe danych referencyjnych. Należy [skonfigurować publiczny punkt końcowy w Azure SQL Database wystąpieniu zarządzanym](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) , a następnie ręcznie skonfigurować poniższe ustawienia w programie Azure Stream Analytics. Maszyna wirtualna platformy Azure z systemem SQL Server z dołączoną bazą danych jest również obsługiwana przez ręczne skonfigurowanie ustawień poniżej.

|**Nazwa właściwości**|**Opis**  |
|---------|---------|
|Alias wejściowy|Przyjazna nazwa, która zostanie użyta w zapytaniu zadania, aby odwołać się do tego danych wejściowych.|
|Ramach|Wybierz subskrypcję|
|Baza danych|Azure SQL Database, który zawiera dane referencyjne. Dla Azure SQL Database wystąpienia zarządzanego wymagane jest określenie portu 3342. Na przykład *sampleserver. Public. Database. Windows. NET, 3342*|
|Nazwa użytkownika|Nazwa użytkownika skojarzona z Azure SQL Database.|
|Hasło|Hasło skojarzone z Twoim Azure SQL Database.|
|Odświeżaj okresowo|Ta opcja pozwala wybrać częstotliwość odświeżania. Wybranie opcji "włączone" pozwoli określić częstotliwość odświeżania w DD: HH: MM.|
|Zapytanie migawki|Jest to domyślna opcja zapytania, która pobiera dane referencyjne z SQL Database.|
|Zapytanie różnicowe|W przypadku zaawansowanych scenariuszy z dużymi zestawami danych i krótką częstotliwością odświeżania wybierz opcję dodania zapytania różnicowego.|

## <a name="size-limitation"></a>Ograniczenie rozmiaru

Stream Analytics obsługuje dane referencyjne o **maksymalnym rozmiarze 300 MB**. Limit 300 MB maksymalnego rozmiaru danych referencyjnych jest osiągalny tylko w przypadku prostych zapytań. W miarę wzrostu złożoności zapytania w celu uwzględnienia przetwarzania stanowego, takiego jak agregacje okienkowe, sprzężenia czasowe i funkcje analityczne czasowe, oczekuje się, że maksymalny obsługiwany rozmiar danych referencyjnych jest zmniejszany. Jeśli Azure Stream Analytics nie może załadować danych referencyjnych i wykonać złożonych operacji, zadanie wygaśnie za mało pamięci i zakończy się niepowodzeniem. W takich przypadkach Metryka użycia SU% osiągnie 100%.    

|**Liczba jednostek przesyłania strumieniowego**  |**Przybliżony obsługiwany rozmiar (w MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 i więcej   |300   |

Zwiększenie liczby jednostek przesyłania strumieniowego zadania poza 6 nie zwiększa maksymalnego obsługiwanego rozmiaru danych referencyjnych.

Obsługa kompresji nie jest dostępna dla danych referencyjnych. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
