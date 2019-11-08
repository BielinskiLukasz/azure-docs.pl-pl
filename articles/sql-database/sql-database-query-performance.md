---
title: Szczegółowe informacje o wydajności zapytań
description: Monitorowanie wydajności zapytań identyfikuje większość zapytań zużywających procesor CPU dla bazy danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/03/2019
ms.openlocfilehash: 56daca0aa817d03298bad971506402739d71482e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821252"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Szczegółowe informacje o wydajności zapytań Azure SQL Database

Zarządzanie i dostrajanie wydajności relacyjnych baz danych wymaga znajomości i czasu. Szczegółowe informacje o wydajności zapytań jest częścią Azure SQL Database liniowej wydajności produktu. Pomaga poświęcać mniej czasu na rozwiązywanie problemów z wydajnością bazy danych, zapewniając:

* Dokładniejsze informacje o zużyciu zasobów baz danych (DTU).
* Szczegóły dotyczące najważniejszych zapytań bazy danych według procesora CPU, czasu trwania i liczby wykonań (możliwe jest dostrojenie kandydatów na potrzeby ulepszeń wydajności).
* Możliwość przechodzenia do szczegółów zapytania, aby wyświetlić tekst zapytania i historię użycia zasobów.
* Adnotacje pokazujące zalecenia dotyczące wydajności [SQL Database Advisor](sql-database-advisor.md).

![Szczegółowe informacje o wydajności zapytań](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> W przypadku podstawowego monitorowania wydajności przy użyciu Azure SQL Database zalecamy Szczegółowe informacje o wydajności zapytań. Zanotuj ograniczenia dotyczące produktu opublikowane w tym artykule. Aby uzyskać zaawansowane monitorowanie wydajności bazy danych na dużą skalę, zalecamy [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md). Ma wbudowaną analizę, która umożliwia automatyczne rozwiązywanie problemów z wydajnością. Aby automatycznie dostosowywać niektóre najczęstsze problemy z wydajnością bazy danych, zalecamy [dostrajanie automatyczne](sql-database-automatic-tuning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Szczegółowe informacje o wydajności zapytań wymaga, aby [Magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx) był aktywny w Twojej bazie danych. Domyślnie jest automatycznie włączona dla wszystkich baz danych usługi Azure SQL. Jeśli magazyn zapytań nie jest uruchomiony, Azure Portal wyświetli monit o jego włączenie.

> [!NOTE]
> Jeśli w portalu zostanie wyświetlony komunikat "magazyn zapytań jest niepoprawnie skonfigurowany w tej bazie danych", zobacz [Optymalizacja konfiguracji magazynu zapytań](#optimize-the-query-store-configuration-for-query-performance-insight).
>

## <a name="permissions"></a>Uprawnienia

Do korzystania z Szczegółowe informacje o wydajności zapytań są wymagane następujące uprawnienia [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) :

* Aby wyświetlić najpopularniejsze zapytania i wykresy, wymagane są uprawnienia **czytelnika**, **właściciel**, **współautor**, współautor **bazy danych SQL**lub **SQL Server współautor** .
* Do wyświetlania tekstu zapytania wymagane są uprawnienia **właściciel**, **współautor**, **współautor bazy danych SQL**lub współautor **SQL Server** .

## <a name="use-query-performance-insight"></a>Korzystanie ze szczegółowych informacji o wydajności zapytań

Szczegółowe informacje o wydajności zapytań jest łatwe w użyciu:

1. Otwórz [Azure Portal](https://portal.azure.com/) i Znajdź bazę danych, którą chcesz sprawdzić.
2. W menu po lewej stronie Otwórz pozycję **Intelligent performance** **szczegółowe informacje o wydajności zapytań** > .
  
   ![Szczegółowe informacje o wydajności zapytań menu](./media/sql-database-query-performance/tile.png)

3. Na pierwszej karcie Przejrzyj listę najważniejszych zapytań zużywających zasoby.
4. Wybierz pojedyncze zapytanie, aby wyświetlić jego szczegóły.
5. Otwórz > **zalecenia dotyczące wydajności** i sprawdź, czy są dostępne jakieś zalecenia **dotyczące wydajności.** Aby uzyskać więcej informacji na temat wbudowanych zaleceń dotyczących wydajności, zobacz [SQL Database Advisor](sql-database-advisor.md).
6. Użyj suwaków lub powiększ ikony, aby zmienić obserwowany interwał.

   ![Pulpit nawigacyjny wydajności](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Aby SQL Database renderować informacje w Szczegółowe informacje o wydajności zapytań, magazyn zapytań musi przechwycić kilka godzin. Jeśli baza danych nie ma działania lub magazyn zapytań był nieaktywny w określonym czasie, wykresy będą puste, gdy Szczegółowe informacje o wydajności zapytań wyświetla ten zakres czasu. Magazyn zapytań można włączyć w dowolnym momencie, jeśli nie jest uruchomiony. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania z magazynem zapytań](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).

## <a name="review-top-cpu-consuming-queries"></a>Przeglądanie najważniejszych zapytań zużywających procesor CPU

Domyślnie Szczegółowe informacje o wydajności zapytań pokazuje pięć najważniejszych zapytań zużywających procesor przy pierwszym otwarciu.

1. Zaznacz lub wyczyść pojedyncze zapytania, aby dołączyć lub wykluczyć je z wykresu przy użyciu pól wyboru.

    Górna linia pokazuje całkowity procent jednostek DTU dla bazy danych. Na paskach są wyświetlane wartości procentowe procesora, które zostały zużyte przez wybrane zapytania w wybranym interwale. Na przykład jeśli wybrano **ubiegły tydzień** , każdy pasek reprezentuje pojedynczy dzień.

    ![Najpopularniejsze zapytania](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > Pokazana linia DTU jest agregowana do wartości maksymalnego zużycia w okresach jednogodzinnych. Jest to przeznaczone do porównania wysokiego poziomu tylko z statystykami wykonywania zapytań. W niektórych przypadkach użycie jednostek DTU może wydawać się zbyt wysokie w porównaniu do wykonywanych zapytań, ale może to nie być przypadek.
   >
   > Na przykład, jeśli zapytanie maxed zewnętrzne do 100% przez zaledwie kilka minut, wiersz jednostek DTU w Szczegółowe informacje o wydajności zapytań będzie wyświetlać całą godzinę zużycia jako 100% (konsekwencję maksymalnej wartości zagregowanej).
   >
   > Aby zapoznać się z dokładniejszym porównaniem (do jednej minuty), należy rozważyć utworzenie niestandardowego wykresu wykorzystania jednostek DTU:
   >
   > 1. W Azure Portal wybierz pozycję **Azure SQL Database** **monitorowanie** > .
   > 2. Wybierz pozycję **Metryki**.
   > 3. Wybierz pozycję **+ Dodaj wykres**.
   > 4. Na wykresie wybierz wartość procentową jednostek DTU.
   > 5. Ponadto wybierz pozycję **ostatnie 24 godziny** w menu u góry lewej i zmień ją na jedną minutę.
   >
   > Użyj niestandardowego wykresu DTU o bardziej szczegółowym poziomie, aby porównać z wykresem wykonywania zapytania.

   Dolna siatka przedstawia zagregowane informacje dla widocznych zapytań:

   * Identyfikator zapytania, który jest unikatowym identyfikatorem zapytania w bazie danych.
   * Procesor CPU na zapytanie w przedziale zauważalnym, który zależy od funkcji agregującej.
   * Czas trwania każdego zapytania, który również zależy od funkcji agregującej.
   * Łączna Liczba wykonań dla konkretnego zapytania.

2. Jeśli dane staną się nieodświeżone, wybierz przycisk **Odśwież** .

3. Użyj suwaków i przycisków powiększenia, aby zmienić interwał obserwacji i zbadać skoki zużycia:

   ![Suwaki i przyciski powiększania w celu zmiany interwału](./media/sql-database-query-performance/zoom.png)

4. Opcjonalnie możesz wybrać kartę **niestandardową** , aby dostosować widok:

   * Metryka (procesor, czas trwania, Liczba wykonań).
   * Przedział czasu (ostatnie 24 godziny, ubiegły tydzień lub ostatni miesiąc).
   * Liczba zapytań.
   * Funkcja agregacji.
  
   ![Karta niestandardowa](./media/sql-database-query-performance/custom-tab.png)
  
5. Wybierz przycisk **przejdź >** , aby wyświetlić dostosowany widok.

   > [!IMPORTANT]
   > Szczegółowe informacje o wydajności zapytań jest ograniczona do wyświetlania pierwszych 5-20 zapytań zużywających, w zależności od wyboru. Baza danych może uruchamiać wiele innych zapytań poza górnymi wyświetlanymi, a te zapytania nie zostaną uwzględnione na wykresie.
   >
   > Może istnieć typ obciążenia bazy danych, w którym dużo mniejszych zapytań wykraczających poza pierwsze widoczne, uruchamiane często i używają większości jednostek DTU. Te zapytania nie są wyświetlane na wykresie wydajności.
   >
   > Na przykład zapytanie mogło zużywać znaczną ilość jednostek DTU przez pewien czas, mimo że całkowite zużycie w obserwowanym okresie jest mniejsze niż inne zapytania zużywające największe wartości. W takim przypadku użycie zasobów tego zapytania nie będzie widoczne na wykresie.
   >
   > Jeśli musisz zrozumieć najczęstsze wykonania zapytania poza ograniczeniami Szczegółowe informacje o wydajności zapytań, rozważ użycie [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) do zaawansowanego monitorowania wydajności bazy danych i rozwiązywania problemów.
   >

## <a name="view-individual-query-details"></a>Wyświetl szczegóły poszczególnych zapytań

Aby wyświetlić szczegóły zapytania:

1. Wybierz dowolne zapytanie na liście najważniejszych zapytań.

    ![Lista najważniejszych zapytań](./media/sql-database-query-performance/details.png)

   Zostanie otwarty widok szczegółowy. Pokazuje użycie procesora CPU, czas trwania i liczbę wykonań w czasie.

2. Wybierz funkcje wykresu, aby uzyskać szczegółowe informacje.

   * Na górnym wykresie jest wyświetlany wiersz z ogólną wartością procentową jednostek DTU bazy danych. Słupki to procent procesora CPU użyty przez wybrane zapytanie.
   * Drugi wykres pokazuje łączny czas trwania wybranego zapytania.
   * Dolny wykres pokazuje łączną liczbę wykonań przez wybrane zapytanie.

   ![Szczegóły zapytania](./media/sql-database-query-performance/query-details.png)

3. Opcjonalnie użyj suwaków, użyj przycisków powiększenia lub wybierz pozycję **Ustawienia** , aby dostosować sposób wyświetlania danych zapytania lub wybrać inny zakres czasu.

   > [!IMPORTANT]
   > Szczegółowe informacje o wydajności zapytań nie przechwytuje żadnych zapytań DDL. W niektórych przypadkach może nie przechwycić wszystkich zapytań ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Przegląd najważniejszych zapytań na czas trwania

Dwie metryki w Szczegółowe informacje o wydajności zapytań mogą pomóc znaleźć potencjalne wąskie gardła: czas trwania i liczbę wykonań.

Długotrwałe zapytania mają największy potencjał do blokowania zasobów, blokowania innych użytkowników i ograniczania skalowalności. Są one również najlepszymi kandydatami do optymalizacji.

Aby zidentyfikować długotrwałe zapytania:

1. Otwórz kartę **niestandardową** w szczegółowe informacje o wydajności zapytań dla wybranej bazy danych.
2. Zmień metryki na **czas trwania**.
3. Wybierz liczbę zapytań i interwał obserwacji.
4. Wybierz funkcję agregacji:

   * **Sum** dodaje wszystkie czas wykonywania zapytania dla całego interwału obserwacji.
   * **Wartość maksymalna** umożliwia znalezienie zapytań, w których czas wykonywania był maksymalny dla całego interwału obserwacji.
   * Wartość **średnia** powoduje znalezienie średniego czasu wykonywania wszystkich wykonań zapytań i wyświetlenie najważniejszych wartości dla tych średnich.

   ![Czas trwania zapytania](./media/sql-database-query-performance/top-duration.png)

5. Wybierz przycisk **przejdź >** , aby wyświetlić dostosowany widok.

   > [!IMPORTANT]
   > Dostosowywanie widoku zapytania nie aktualizuje linii DTU. Wiersz DTU zawsze pokazuje wartość maksymalnego zużycia dla interwału.
   >
   > Aby zrozumieć użycie jednostek DTU bazy danych z większą szczegółowością (do 1 minuty), rozważ utworzenie wykresu niestandardowego w Azure Portal:
   >
   > 1. Wybierz pozycję **monitorowanie** > **Azure SQL Database** .
   > 2. Wybierz pozycję **Metryki**.
   > 3. Wybierz pozycję **+ Dodaj wykres**.
   > 4. Na wykresie wybierz wartość procentową jednostek DTU.
   > 5. Ponadto wybierz pozycję **ostatnie 24 godziny** w menu u góry lewej i zmień ją na jedną minutę.
   >
   > Zalecamy użycie niestandardowego wykresu jednostek DTU do porównania z wykresem wydajności zapytań.
   >

## <a name="review-top-queries-per-execution-count"></a>Przejrzyj najpopularniejsze zapytania na liczbę wykonań

Aplikacja użytkownika korzystająca z bazy danych może działać powoli, nawet jeśli duża liczba wykonań może nie wpływać na samą bazę danych, a użycie zasobów jest niskie.

W niektórych przypadkach duża liczba wykonań może prowadzić do większej liczby operacji w sieci. Okrągłe podróże mają wpływ na wydajność. Są one uzależnione od opóźnienia sieci oraz opóźnienia serwera podrzędnego.

Na przykład wiele witryn sieci Web opartych na danych intensywnie uzyskuje dostęp do bazy danych dla każdego żądania użytkownika. Mimo że pule połączeń pomagają, zwiększony ruch sieciowy i obciążenie przetwarzaniem na serwerze bazy danych mogą spowalniać wydajność. Ogólnie rzecz biorąc, Przechowaj do minimum.

Aby zidentyfikować często wykonywane zapytania ("rozmawianie"):

1. Otwórz kartę **niestandardową** w szczegółowe informacje o wydajności zapytań dla wybranej bazy danych.
2. Zmień metryki na **liczbę wykonań**.
3. Wybierz liczbę zapytań i interwał obserwacji.
4. Wybierz przycisk **przejdź >** , aby wyświetlić dostosowany widok.

   ![Liczba wykonań zapytania](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Omówienie adnotacji dostrajania wydajności

Podczas eksplorowania obciążenia w Szczegółowe informacje o wydajności zapytań mogą być widoczne ikony z pionową linią w górnej części wykresu.

Te ikony są adnotacjami. Przedstawiają one zalecenia dotyczące wydajności [SQL Database Advisor](sql-database-advisor.md). Umieszczenie wskaźnika myszy nad adnotacją pozwala uzyskać podsumowanie informacji na temat zaleceń dotyczących wydajności.

   ![Adnotacja zapytania](./media/sql-database-query-performance/annotation.png)

Jeśli chcesz zrozumieć więcej lub zastosować zalecenie doradcy, wybierz ikonę, aby otworzyć Szczegóły zalecanej akcji. Jeśli jest to aktywne zalecenie, możesz od razu zastosować ją od portalu.

   ![Szczegóły adnotacji zapytania](./media/sql-database-query-performance/annotation-details.png)

W niektórych przypadkach, ze względu na poziom powiększenia, istnieje możliwość, że adnotacje blisko siebie są zwijane do pojedynczej adnotacji. Szczegółowe informacje o wydajności zapytań reprezentuje jako ikonę adnotacji grupy. Wybranie ikony grupy adnotacji powoduje otwarcie nowego bloku zawierającego adnotacje.

Skorelowane kwerendy i akcje dostrajania wydajności mogą pomóc w lepszym zrozumieniu obciążenia.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>Optymalizuj konfigurację magazynu zapytań dla Szczegółowe informacje o wydajności zapytań

Podczas korzystania z Szczegółowe informacje o wydajności zapytań mogą pojawić się następujące komunikaty o błędach magazynu zapytań:

* "Magazyn zapytań nie jest prawidłowo skonfigurowany w tej bazie danych. Kliknij tutaj, aby dowiedzieć się więcej ".
* "Magazyn zapytań nie jest prawidłowo skonfigurowany w tej bazie danych. Kliknij tutaj, aby zmienić ustawienia. "

Te komunikaty są zwykle wyświetlane, gdy magazyn zapytań nie może zbierać nowych danych.

Pierwszy przypadek ma miejsce, gdy magazyn zapytań jest w stanie tylko do odczytu, a parametry są ustawiane optymalnie. Można to naprawić przez zwiększenie rozmiaru magazynu danych lub przez wyczyszczenie magazynu zapytań. (Jeśli wyczyścisz magazyn zapytań, wszystkie wcześniej zebrane dane telemetryczne zostaną utracone).

   ![Szczegóły magazynu zapytań](./media/sql-database-query-performance/qds-off.png)

Drugi przypadek występuje, gdy magazyn zapytań nie jest włączony lub parametry nie są ustawione optymalnie. Można zmienić zasady przechowywania i przechwytywania, a także włączyć magazyn zapytań, uruchamiając następujące polecenia dostarczone z [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub Azure Portal.

### <a name="recommended-retention-and-capture-policy"></a>Zalecane zasady przechowywania i przechwytywania

Istnieją dwa typy zasad przechowywania:

* **Na podstawie rozmiaru**: Jeśli ta zasada jest ustawiona na **Automatyczne**, automatycznie czyści dane po osiągnięciu maksymalnego rozmiaru.
* **Czas na podstawie**: Domyślnie te zasady są ustawione na 30 dni. W przypadku braku miejsca w magazynie zapytań zostaną usunięte informacje o zapytaniu starsze niż 30 dni.

Zasady przechwytywania można ustawić na:

* **Wszystko**: Magazyn zapytań przechwytuje wszystkie zapytania.
* **Auto: Magazyn**zapytań ignoruje rzadko występujące zapytania i zapytania z nieznacznym czasem trwania kompilacji i wykonania. Progi liczby wykonań, czas trwania kompilacji i czas trwania środowiska uruchomieniowego są określane wewnętrznie. Jest to opcja domyślna.
* **Brak**: Magazyn zapytań przerywa przechwytywanie nowych zapytań, ale statystyki środowiska uruchomieniowego dla już przechwyconych zapytań są nadal zbierane.

Zalecamy ustawienie dla wszystkich zasad opcji **autoi czyszczenia na 30** dni, wykonując następujące polecenia w programie [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub Azure Portal. (Zastąp `YourDB` nazwą bazy danych).

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Zwiększ rozmiar magazynu zapytań, łącząc się z bazą danych za pomocą programu [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub Azure Portal i uruchamiając następujące zapytanie. (Zastąp `YourDB` nazwą bazy danych).

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Zastosowanie tych ustawień spowoduje, że magazyn zapytań zbiera dane telemetryczne dla nowych zapytań. Jeśli potrzebujesz od razu działania magazynu zapytań, możesz wybrać opcję czyszczenia magazynu zapytań, uruchamiając następujące zapytanie za pomocą programu SSMS lub Azure Portal. (Zastąp `YourDB` nazwą bazy danych).

> [!NOTE]
> Uruchomienie następującego zapytania spowoduje usunięcie wszystkich poprzednio zebranych danych telemetrycznych w magazynie zapytań.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>Podsumowanie

Szczegółowe informacje o wydajności zapytań pomaga zrozumieć wpływ obciążeń związanych z zapytaniami i odnoszących się do użycia zasobów bazy danych. Korzystając z tej funkcji, dowiesz się więcej na temat najważniejszych zapytań w bazie danych i znajdziesz zapytania do optymalizacji, zanim staną się one problemem.

## <a name="next-steps"></a>Następne kroki

* W przypadku zaleceń dotyczących wydajności bazy danych wybierz pozycję [zalecenia](sql-database-advisor.md) w bloku nawigacji szczegółowe informacje o wydajności zapytań.

    ![Karta zalecenia](./media/sql-database-query-performance/ia.png)

* Rozważ włączenie [dostrajania automatycznego](sql-database-automatic-tuning.md) dla typowych problemów z wydajnością bazy danych.
* Dowiedz się, jak [Intelligent Insights](sql-database-intelligent-insights.md) może pomóc w automatycznym rozwiązywaniu problemów z wydajnością bazy danych.
* Należy rozważyć użycie [Azure SQL Analytics]( ../azure-monitor/insights/azure-sql.md) do zaawansowanego monitorowania wydajności dużej floty baz danych SQL, pul elastycznych i wystąpień zarządzanych z wbudowaną inteligencją.
