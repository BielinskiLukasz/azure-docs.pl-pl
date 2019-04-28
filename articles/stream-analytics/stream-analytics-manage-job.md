---
title: 'Samouczek: Tworzenie zadania usługi Stream Analytics i zarządzanie nim przy użyciu witryny Azure Portal'
description: Ten samouczek zawiera kompleksowy opis sposobu używania usługi Azure Stream Analytics do analizowania fałszywych połączeń w strumieniu połączeń telefonicznych.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/07/2018
ms.openlocfilehash: 261b55f722fdc3c1e8f4b45debc664f49db3f898
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61480485"
---
# <a name="analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Analizowanie danych połączeń telefonicznych za pomocą usługi Stream Analytics i wizualizowanie wyników na pulpicie nawigacyjnym usługi Power BI

W tym samouczku pokazano, jak analizować dane połączeń telefonicznych przy użyciu usługi Azure Stream Analytics. Dane połączeń telefonicznych wygenerowane przez aplikację kliencką zawierają pewne fałszywe połączenia, które zostaną wyfiltrowane za pomocą zadania usługi Stream Analytics.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Generowanie przykładowych danych połączeń telefonicznych i wysyłanie ich do usługi Azure Event Hubs
> * Tworzenie zadania usługi Stream Analytics
> * Konfigurowanie danych wejściowych i wyjściowych zadania
> * Definiowanie zapytania w celu filtrowania fałszywych połączeń
> * Testowanie i uruchamianie zadania
> * Wizualizowanie wyników w usłudze Power BI

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
* Pobierz aplikację generatora zdarzeń połączeń telefonicznych [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z Centrum pobierania Microsoft lub uzyskaj kod źródłowy z witryny [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Musisz mieć konto usługi Power BI.

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub

Zanim usługa Stream Analytics będzie mogła przeanalizować strumień danych fałszywych połączeń, należy wysłać dane do platformy Azure. W tym samouczku dane zostaną wysłane do platformy Azure przy użyciu usługi [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs).

Utwórz centrum zdarzeń usługi Event Hub i wyślij do niego dane połączeń, wykonując poniższe czynności:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Event Hubs**.

   ![Tworzenie centrum Azure Event Hub w portalu](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Wypełnij okienko **Tworzenie przestrzeni nazw** następującymi wartościami:

   |**Ustawienie**  |**Sugerowana wartość** |**Opis**  |
   |---------|---------|---------|
   |Name (Nazwa)     | myEventHubsNS        |  Unikatowa nazwa identyfikująca przestrzeń nazw centrum zdarzeń.       |
   |Subskrypcja     |   \<Twoja subskrypcja\>      |   Wybierz subskrypcję platformy Azure, w której chcesz utworzyć centrum zdarzeń.      |
   |Grupa zasobów     |   MyASADemoRG      |  Wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów dla swojego konta.       |
   |Lokalizacja     |   Zachodnie stany USA 2      |    Lokalizacja, w której można wdrożyć przestrzeń nazw centrum zdarzeń.     |

4. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Utwórz**.

   ![Tworzenie przestrzeni nazw centrum zdarzeń w witrynie Azure Portal](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Po zakończeniu wdrażania przestrzeni nazw wybierz pozycję **Wszystkie zasoby** i znajdź zasób *myEventHubNS* na liście zasobów platformy Azure. Wybierz zasób *myEventHubsNS*, aby go otworzyć.
6. Następnie wybierz pozycję **+ Centrum zdarzeń** i w polu **Nazwa** wprowadź nazwę *MyEventHub* lub inną, wybraną przez Ciebie. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Utwórz**. Następnie poczekaj na pomyślne zakończenie wdrożenia.

   ![Konfiguracja centrum zdarzeń w witrynie Azure Portal](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udzielanie dostępu do centrum zdarzeń i pobieranie parametrów połączenia

Aby aplikacja mogła wysyłać dane do usługi Azure Event Hubs, centrum zdarzeń musi mieć zasady, które zezwalają na odpowiedni dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1. Przejdź do centrum zdarzeń utworzonego w poprzednim kroku: *MyEventHub*. Wybierz pozycję **Zasady dostępu współużytkowanego** w obszarze **Ustawienia**, a następnie wybierz pozycję **+ Dodaj**.

2. Nadaj zasadom nazwę **MyPolicy** i upewnij się, że zaznaczono opcję **Zarządzaj**. Następnie wybierz przycisk **Utwórz**.

   ![Tworzenie zasad dostępu współdzielonego centrum zdarzeń](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Po utworzeniu zasad wybierz je, aby je otworzyć, i znajdź pozycję **Parametry połączenia — klucz podstawowy**. Wybierz niebieski przycisk **kopiuj** obok parametrów połączenia.

   ![Zapisz parametry połączenia zasad dostępu współużytkowanego](media/stream-analytics-manage-job/save-connection-string.png)

4. Wklej parametry połączenia do edytora tekstu. Te parametry połączenia będą potrzebne w następnej sekcji.

   Parametry połączenia wyglądają następująco:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Zauważ, że parametry połączenia zawierają wiele par klucz-wartość oddzielonych średnikami: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** i **EntityPath**.

## <a name="start-the-event-generator-application"></a>Uruchamianie aplikacji generatora zdarzeń

Przed uruchomieniem aplikacji TelcoGenerator należy ją skonfigurować do przesyłania danych do usługi Azure Event Hubs utworzonej wcześniej.

1. Wyodrębnij zawartość pliku [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Otwórz plik `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` w wybranym edytorze tekstów (istnieje więcej niż jeden plik config, dlatego upewnij się, że otwierasz właściwy).

3. Zaktualizuj element `<appSettings>` w pliku config przy użyciu następujących szczegółów:

   * Ustaw wartość klucza *EventHubName* na wartość EntityPath w parametrach połączenia.
   * Ustaw wartość klucza *Microsoft.ServiceBus.ConnectionString* na wartość parametrów połączenia bez wartości EntityPath.

4. Zapisz plik.
5. Następnie otwórz okno polecenia i przejdź do folderu, w którym znajduje się wyodrębniona aplikacja TelcoGenerator. Wprowadź następujące polecenie:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   To polecenie przyjmuje następujące parametry:
   * Liczba rekordów danych połączeń na godzinę.
   * Procentowe prawdopodobieństwo oszustwa, czyli jak często aplikacja powinna symulować fałszywe połączenia. Wartość 0,2 oznacza, że około 20% rekordów połączeń będzie wyglądać na fałszywe.
   * Czas trwania w godzinach, czyli liczba godzin, przez jaką aplikacja ma być uruchomiona. Aplikację możesz również zatrzymać w dowolnym momencie przez zakończenie procesu (**Ctrl+C**) w wierszu polecenia.

   Po kilku sekundach aplikacja rozpocznie wyświetlanie rekordów połączeń telefonicznych na ekranie w miarę wysyłania ich do centrum zdarzeń. Dane połączenia telefonicznego zawierają następujące pola:

   |**Rekord**  |**Definicja**  |
   |---------|---------|
   |CallrecTime    |  Znacznik czasu godziny rozpoczęcia połączenia.       |
   |SwitchNum     |  Centrala telefoniczna używana do wykonania połączenia. W tym przykładzie centrale są ciągami reprezentującymi kraj pochodzenia (USA, Chiny, Zjednoczone Królestwo, Niemcy lub Australia).       |
   |CallingNum     |  Numer telefonu dzwoniącego.       |
   |CallingIMSI     |  Numer IMSI (International Mobile Subscriber Identity). Unikatowy identyfikator dzwoniącego.       |
   |CalledNum     |   Numer telefonu odbiorcy połączenia.      |
   |CalledIMSI     |  Numer IMSI (International Mobile Subscriber Identity). Unikatowy identyfikator odbiorcy połączenia.       |

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Teraz, gdy masz strumień zdarzeń połączeń, możesz utworzyć zadanie usługi Stream Analytics, które odczytuje dane z centrum zdarzeń.

1. Aby utworzyć zadanie usługi Stream Analytics, przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.

3. Wypełnij okienko **Zadanie usługi Stream Analytics** przy użyciu następujących wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Nazwa zadania     |  ASATutorial       |   Unikatowa nazwa identyfikująca przestrzeń nazw centrum zdarzeń.      |
   |Subskrypcja    |  \<Twoja subskrypcja\>   |   Wybierz subskrypcję platformy Azure, w której chcesz utworzyć zadanie.       |
   |Grupa zasobów   |   MyASADemoRG      |   Wybierz pozycję **Użyj istniejącej**, a następnie wprowadź nazwę nowej grupy zasobów dla swojego konta.      |
   |Lokalizacja   |    Zachodnie stany USA 2     |      Lokalizacja, w której można wdrożyć zadanie. Zaleca się umieszczenie zadania i centrum zdarzeń w tym samym regionie, aby uzyskać najlepszą wydajność i nie płacić za transfer danych między regionami.      |
   |Środowisko hostingu    | Chmura        |     Zadania usługi Stream Analytics można wdrożyć w chmurze lub na urządzeniu brzegowym. Wartość Chmura umożliwia wdrożenie w usłudze Azure Cloud, a Urządzenie brzegowe umożliwia wdrożenie na urządzeniu usługi IoT Edge.    |
   |Jednostki przesyłania strumieniowego     |    1       |      Jednostki przesyłania strumieniowego reprezentują zasoby obliczeniowe, które są wymagane do wykonania zadania. Domyślnie to ustawienie ma wartość 1. Aby dowiedzieć się więcej na temat skalowania jednostek przesyłania strumieniowego, zobacz artykuł [Understanding and adjusting streaming units (Opis i dostosowywanie jednostek przesyłania strumieniowego)](stream-analytics-streaming-unit-consumption.md).      |

4. W pozostałych ustawieniach użyj opcji domyślnych, a następnie wybierz przycisk **Utwórz** i poczekaj na pomyślne ukończenie wdrożenia.

   ![Tworzenie zadania usługi Azure Stream Analytics](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

Następnym krokiem jest określenie źródła danych wejściowych dla zadania w celu odczytywania danych za pomocą centrum zdarzeń utworzonego w poprzedniej sekcji.

1. W witrynie Azure Portal otwórz okienko **Wszystkie zasoby** i znajdź zadanie usługi Stream Analytics o nazwie *ASATutorial*.

2. W sekcji **Topologia zadania** okienka zadania usługi Stream Analytics wybierz opcję **Dane wejściowe**.

3. Wybierz pozycje **+ Dodaj wejście strumienia** i **Centrum zdarzeń**. Wypełnij okienko przy użyciu następujących wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wejściowych     |  CallStream       |  Podaj przyjazną nazwę identyfikującą dane wejściowe. Alias danych wejściowych może zawierać tylko znaki alfanumeryczne, łączniki i podkreślenia oraz musi składać się z od 3 do 63 znaków.       |
   |Subskrypcja    |   \<Twoja subskrypcja\>      |   Wybierz subskrypcję platformy Azure, w której zostało utworzone centrum zdarzeń. Centrum zdarzeń może znajdować się w tej samej subskrypcji co zadanie usługi Stream Analytics lub w innej.       |
   |Przestrzeń nazw centrum zdarzeń    |  myEventHubsNS       |  Wybierz przestrzeń nazw centrum zdarzeń utworzoną w poprzedniej sekcji. Wszystkie przestrzenie nazw centrum zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |
   |Nazwa centrum zdarzeń    |   MyEventHub      |  Wybierz centrum zdarzeń utworzone w poprzedniej sekcji. Wszystkie centra zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |
   |Nazwa zasad centrum zdarzeń   |  Mypolicy       |  Wybierz zasady dostępu współdzielonego centrum zdarzeń utworzone w poprzedniej sekcji. Wszystkie zasady centrów zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |

4. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Zapisz**.

   ![Konfigurowanie danych wejściowych usługi Azure Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania

Ostatnim krokiem jest określenie ujścia danych wyjściowych zadania, w którym może ono zapisać przekształcone dane. W tym samouczku dane wyjściowe są wizualizowane przy użyciu usługi Power BI.

1. W witrynie Azure Portal otwórz okienko **Wszystkie zasoby** i zadanie usługi Stream Analytics o nazwie *ASATutorial*.

2. W sekcji **Topologia zadania** okienka zadania usługi Stream Analytics wybierz opcję **Dane wyjściowe**.

3. Wybierz pozycję **+ Dodaj** > **Power BI**. Następnie wypełnij formularz przy użyciu poniższych wartości i wybierz pozycję **Autoryzuj**:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Alias danych wyjściowych  |  MyPBIoutput  |
   |Nazwa zestawu danych  |   ASAdataset  |
   |Nazwa tabeli |  ASATable  |

   ![Konfigurowanie danych wyjściowych usługi Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. Po wybraniu pozycji **Autoryzuj** zostanie otwarte okno podręczne i zostanie wyświetlona prośba o podanie poświadczeń w celu uwierzytelnienia na koncie usługi Power BI. Kiedy autoryzacja zakończy się pomyślnie, **zapisz** ustawienia.

## <a name="define-a-query-to-analyze-input-data"></a>Definiowanie zapytania w celu analizowania danych wejściowych

Następnym krokiem jest utworzenie przekształcenia, które analizuje dane w czasie rzeczywistym. Zapytanie przekształcenia należy zdefiniować przy użyciu [języka zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). Zapytanie używane w tym samouczku wykrywa fałszywe połączenia w danych telefonicznych.

W tym przykładzie fałszywe połączenia są wykonywane od tego samego użytkownika w przeciągu pięciu sekund, ale w różnych lokalizacjach. Na przykład ten sam użytkownik nie może rzeczywiście wykonywać w tym samym czasie połączeń ze Stanów Zjednoczonych i Australii. Aby zdefiniować zapytanie przekształcenia dla zadania usługi Stream Analytics:

1. W witrynie Azure Portal otwórz okienko **Wszystkie zasoby** i przejdź do utworzonego wcześniej zadania usługi Stream Analytics **ASATutorial**.

2. W sekcji **Topologia zadania** okienka zadania usługi Stream Analytics wybierz opcję **Zapytanie**. Okno zapytania zawiera wejścia i wyjścia skonfigurowane dla zadania i umożliwia utworzenie zapytania w celu przekształcania strumienia wejściowego.

3. Zastąp istniejące zapytanie w edytorze poniższym zapytaniem, które wykonuje samosprzężenie danych połączeń z 5-sekundowego interwału:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Aby sprawdzić fałszywe połączenia, możesz utworzyć samosprzężenie danych strumieniowych na podstawie wartości `CallRecTime`. Następnie można wyszukać rekordy połączeń, w których wartość `CallingIMSI` (numer źródłowy) jest taka sama, ale wartość `SwitchNum` (kraj źródłowy) różni się. W przypadku używania operacji JOIN na danych przesyłanych strumieniowo sprzężenie musi udostępniać pewne ograniczenia określające maksymalną odległość czasową między dwoma pasującymi wierszami. Ponieważ dane przesyłane strumieniowo są nieskończone, granice czasowe dla relacji są określone w klauzuli **ON** sprzężenia, przy użyciu funkcji [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics).

   To zapytanie jest takie samo jak normalne sprzężenie SQL, z wyjątkiem funkcji **DATEDIFF**. Funkcja **DATEDIFF** używana w tym zapytaniu jest specyficzna dla usługi Stream Analytics i musi znajdować się w klauzuli `ON...BETWEEN`.

4. **Zapisz** zapytanie.

   ![Definiowanie zapytania usługi Stream Analytics w portalu](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>Testowanie zapytania

Zapytanie można przetestować z poziomu edytora zapytań przy użyciu przykładowych danych. Uruchom następujące kroki, aby przetestować zapytanie:

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona i tworzy rekordy połączeń telefonicznych.

2. W okienku **Zapytanie** wybierz kropki obok wejścia *CallStream*, a następnie wybierz pozycję **Przykładowe dane z danych wejściowych**.

3. Ustaw opcję **Minuty** na wartość 3 i wybierz przycisk **OK**. Ze strumienia wejściowego zostanie pobrana próbka danych odpowiadająca trzem minutom. Kiedy przykładowe dane będą gotowe, zostanie wyświetlone powiadomienie. Stan próbkowania można zobaczyć na pasku powiadomień.

   Przykładowe dane są przechowywane tymczasowo i są dostępne, kiedy okno zapytania jest otwarte. Jeśli zamkniesz okno zapytania, przykładowe dane zostaną usunięte i w celu przeprowadzenia testu konieczne będzie utworzenie nowego zestawu przykładowych danych. Alternatywnie możesz użyć pliku JSON zawierającego przykładowe dane z serwisu [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), a następnie przekazać ten plik JSON do użycia jako przykładowe dane dla wejścia *CallStream*.

   ![Wizualizowanie sposobu próbkowania danych wejściowych dla usługi Stream Analytics](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. Wybierz pozycję **Testuj**, aby przetestować zapytanie. Powinny zostać wyświetlone następujące wyniki:

   ![Dane wyjściowe z testu zapytania usługi Stream Analytics](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>Uruchamianie zadania i wizualizacja danych wyjściowych

1. Aby uruchomić zadanie, przejdź do okienka **Przegląd** zadania, a następnie wybierz pozycję **Uruchom**.

2. Wybierz wartość **Teraz** jako godzinę rozpoczęcia generowania danych wyjściowych zadania, a następnie wybierz pozycję **Uruchom**. Stan zadania możesz wyświetlić na pasku powiadomień.

3. Po pomyślnym zakończeniu zadania przejdź do usługi [Power BI](https://powerbi.com/) i zaloguj się przy użyciu konta służbowego. Jeśli zapytanie zadania usługi Stream Analytics generuje wyniki, utworzony zestaw danych *ASAdataset* znajduje się na karcie **Zestawy danych**.

4. W obszarze roboczym usługi Power BI wybierz pozycję **+ Utwórz**, aby utworzyć nowy pulpit nawigacyjny o nazwie *Fraudulent Calls* (Fałszywe połączenia).

5. W górnej części okna wybierz pozycję **Dodaj kafelek**. Następnie wybierz pozycje **Niestandardowe dane przesyłane strumieniowo** i **Dalej**. Wybierz pozycję **ASAdataset** w obszarze **Twoje zestawy danych**. Wybierz pozycję **Karta** na liście rozwijanej **Typ wizualizacji** i dodaj pozycję **fraudulentcalls** do obszaru **Pola**. Wybierz pozycję **Dalej**, aby wprowadzić nazwę kafelka, a następnie wybierz pozycję **Zastosuj**, aby utworzyć kafelek.

   ![Tworzenie kafelków pulpitu nawigacyjnego usługi Power BI](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Wykonaj ponownie krok 5, wybierając następujące opcje:
   * Gdy pojawi się opcja Typ wizualizacji, wybierz wartość Wykres liniowy.
   * Dodaj oś i wybierz pozycję **windowend**.
   * Dodaj wartość i wybierz pozycję **fraudulentcalls**.
   * W ustawieniu **Okno czasowe do wyświetlenia** wybierz ostatnie 10 minut.

7. Po dodaniu obydwu kafelków pulpit nawigacyjny powinien wyglądać jak na poniższym przykładzie. Zauważ, że jeśli aplikacja nadawcy centrum zdarzeń i aplikacja usługi Stream Analytics są uruchomione, pulpit nawigacyjny usługi Power BI jest okresowo aktualizowany po nadejściu nowych danych.

   ![Wyświetlanie wyników na pulpicie nawigacyjnym usługi Power BI](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Osadzanie pulpitu nawigacyjnego usługi Power BI w aplikacji internetowej

W tej części samouczka użyjesz przykładowej aplikacji internetowej [ASP.NET](https://asp.net/) utworzonej przez zespół usługi Power BI, aby osadzić pulpit nawigacyjny. Aby uzyskać więcej informacji na temat osadzania pulpitów nawigacyjnych, zobacz artykuł [Osadzanie za pomocą usługi Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

Aby skonfigurować aplikację, przejdź do repozytorium GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) i postępuj zgodnie z instrukcjami w sekcji **User Owns Data** (Dane należą do użytkownika) — użyj adresów URL przekierowania i strony głównej z podsekcji **integrate-dashboard-web-app**. Ponieważ korzystamy z przykładu pulpitu nawigacyjnego, użyj przykładowego kodu **integrate-dashboard-web-app** znajdującego się w [repozytorium GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app).
Po uruchomieniu aplikacji w przeglądarce wykonaj następujące kroki, aby osadzić utworzony wcześniej pulpit nawigacyjny na stronie internetowej:

1. Wybierz opcję **Sign in to Power BI (Zaloguj się do usługi Power BI)**, aby przyznać aplikacji dostęp do pulpitów nawigacyjnych na Twoim koncie usługi Power BI.

2. Wybierz przycisk **Get Dashboards (Pobierz pulpity nawigacyjne)**, aby wyświetlić pulpity nawigacyjne Twojego konta w tabeli. Znajdź nazwę pulpitu nawigacyjnego utworzonego wcześniej, **powerbi-embedded-dashboard**, i skopiuj odpowiedni element **EmbedUrl**.

3. Na koniec wklej wartość **EmbedUrl** w odpowiednim polu tekstowym i wybierz pozycję **Embed Dashboard (Osadź pulpit nawigacyjny)**. Możesz teraz wyświetlić ten sam pulpit nawigacyjny osadzony w aplikacji internetowej.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzono proste zadanie usługi Stream Analytics, przeanalizowano dane przychodzące i przedstawiono wyniki na pulpicie nawigacyjnym usługi Power BI. Aby dowiedzieć się więcej na temat zadań usługi Stream Analytics, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Uruchamianie funkcji Azure Functions w ramach zadań usługi Stream Analytics](stream-analytics-with-azure-functions.md)
