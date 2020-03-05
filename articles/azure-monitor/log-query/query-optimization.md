---
title: Optymalizowanie zapytań dzienników w Azure Monitor
description: Najlepsze rozwiązania dotyczące optymalizowania zapytań dzienników w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/28/2019
ms.openlocfilehash: e5c3da94cf2440b30dc59fe20bc51a34095f7d5f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269061"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optymalizowanie zapytań dzienników w Azure Monitor
Dzienniki Azure Monitor korzystają z [usługi Azure Eksplorator danych (ADX)](/azure/data-explorer/) do przechowywania danych dziennika i uruchamiania zapytań w celu analizowania tych danych. Tworzy, zarządza i obsługuje klastry ADX oraz optymalizuje je do obciążeń analizy dzienników. Po uruchomieniu zapytania jest on zoptymalizowany i kierowany do odpowiedniego klastra ADX, który przechowuje dane obszaru roboczego. Zarówno dzienniki Azure Monitor, jak i Azure Eksplorator danych korzystają z wielu automatycznych mechanizmów optymalizacji zapytań. Chociaż Optymalizacja automatyczna zapewnia znaczący wzrost, w niektórych przypadkach można znacznie poprawić wydajność zapytań. W tym artykule opisano zagadnienia dotyczące wydajności i kilka technik rozwiązywania tych problemów.

Większość technik jest wspólna dla zapytań, które są uruchamiane bezpośrednio na platformie Azure Eksplorator danych i w dziennikach Azure Monitor, chociaż kilka unikatowych zagadnień dotyczących dzienników Azure Monitor, które zostały omówione w tym miejscu. Aby uzyskać więcej porad dotyczących optymalizacji Eksplorator danych platformy Azure, zobacz [najlepsze rozwiązania dotyczące zapytań](/azure/kusto/query/best-practices).

Zoptymalizowane zapytania będą:

- Uruchamiaj szybciej, skracaj łączny czas wykonywania zapytania.
- Mniejsza szansa na ograniczenie lub odrzucanie.

Należy zwrócić szczególną uwagę na zapytania, które są używane do przekroczenia bieżącego i użycia na rozerwanie, takich jak pulpity nawigacyjne, alerty, Logic Apps i Power BI. W takich przypadkach wpływ nieskutecznego zapytania jest znaczny.

## <a name="query-performance-pane"></a>Okienko wydajności zapytań
Po uruchomieniu zapytania w Log Analytics kliknij strzałkę w dół powyżej wyników zapytania, aby wyświetlić okienko wydajności zapytania, które pokazuje wyniki kilku wskaźników wydajności dla zapytania. Te wskaźniki wydajności są opisane w następnej sekcji.

![Okienko wydajności zapytań](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Wskaźniki wydajności zapytań

Następujące wskaźniki wydajności zapytań są dostępne dla każdego wykonywanego zapytania:

- [Łączny czas CPU](#total-cpu): ogólne obliczenia używane do przetwarzania zapytania we wszystkich węzłach obliczeniowych. Reprezentuje czas używany do przetwarzania, analizowania i pobierania danych. 

- [Dane używane do przetworzenia zapytania](#data-used-for-processed-query): dane ogólne, do których uzyskano dostęp do przetwarzania zapytania. Wpływ na rozmiar tabeli docelowej, użyty zakres czasu, zastosowane filtry i liczbę kolumn, do których istnieją odwołania.

- [Przedział czasu przetworzonego zapytania](#time-span-of-the-processed-query): różnica między najnowszymi i najstarszymi danymi, do których uzyskano dostęp do przetwarzania zapytania. Wpływ na jawny zakres czasu określony dla zapytania.

- [Wiek przetworzonych danych](#age-of-processed-data): przerwy między nimi i najstarszych danych, do których uzyskano dostęp do przetwarzania zapytania. Ma wysoce wpływ na wydajność pobierania danych.

- [Liczba obszarów roboczych](#number-of-workspaces): ile obszarów roboczych zostało uzyskanych podczas przetwarzania zapytania z powodu niejawnego lub jawnego wyboru.

- [Liczba regionów](#number-of-regions): ile regionów było dostępnych podczas przetwarzania zapytania na podstawie niejawnego lub jawnego wyboru obszarów roboczych. Zapytania w wielu regionach są znacznie mniej wydajne i wskaźniki wydajności stanowią część pokrycia.

- [Równoległość](#parallelism): wskazuje, ile systemu było w stanie wykonać to zapytanie w wielu węzłach. Dotyczy tylko zapytań, które mają duże użycie procesora CPU. Wpływ na użycie określonych funkcji i operatorów.


## <a name="total-cpu"></a>Łączny czas procesora
Rzeczywisty procesor obliczeniowy, który został zainwestowany w celu przetworzenia tego zapytania we wszystkich węzłach przetwarzania zapytań. Ponieważ większość zapytań jest wykonywanych na dużej liczbie węzłów, zwykle będzie znacznie większa niż czas trwania wykonywania zapytania. 

Czas przetwarzania zapytania jest poświęcany na:
- Pobieranie danych — pobieranie starych danych zajmie więcej czasu niż pobranie ostatnich danych.
- Przetwarzanie danych — logika i ocena danych. 

Oprócz czasu spędzonego w węzłach przetwarzania zapytań istnieje dodatkowy czas, który jest poświęcany przez Azure Monitor dzienników: uwierzytelniaj użytkownika i sprawdź, czy mogą oni uzyskać dostęp do tych danych, zlokalizować magazyn danych, przeanalizować zapytanie i przydzielić przetwarzanie zapytania nich. Ten czas nie jest uwzględniany w łącznym czasie procesora CPU.

Niektóre polecenia i funkcje zapytania są intensywnie używane do użycia procesora CPU. Jest to szczególnie prawdziwe w przypadku poleceń, które analizują dane JSON i XML lub wyodrębniają złożone wyrażenia regularne. Takie analizowanie może być wykonywane jawnie za pośrednictwem funkcji [parse_json ()](/azure/kusto/query/parsejsonfunction) lub [parse_xml ()](/azure/kusto/query/parse-xmlfunction) lub niejawnie w przypadku odwoływania się do kolumn dynamicznych.

Te funkcje zużywają procesor proporcjonalnie do liczby wierszy, które są przetwarzane. Najbardziej wydajna Optymalizacja polega na dodaniu tam przypadków, w których warunki w zapytaniu mogą odfiltrować dowolną liczbę rekordów, zanim funkcja intensywnie korzysta z procesora CPU.

Na przykład następujące zapytania generują dokładnie ten sam wynik, ale drugi jest najbardziej wydajny jako warunek [WHERE](/azure/kusto/query/whereoperator) przed analizą wyklucza wiele rekordów:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
```

Zapytania zawierające klauzule [WHERE](/azure/kusto/query/whereoperator) w ocenianej kolumnie, a nie w kolumnach, które są fizycznie obecne w zestawie danych tracą wydajność. Filtrowanie dla ocenionych kolumn uniemożliwia pewne optymalizacje systemu, gdy są obsługiwane duże zestawy danych.
Na przykład następujące zapytania dają dokładnie ten sam wynik, ale druga jest bardziej wydajna, gdy warunek [WHERE](/azure/kusto/query/whereoperator) odwołuje się do kolumny wbudowanej

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

Chociaż niektóre polecenia agregujące, takie jak [Max ()](/azure/kusto/query/max-aggfunction), [sum ()](/azure/kusto/query/sum-aggfunction), [Count ()](/azure/kusto/query/count-aggfunction)i [AVG ()](/azure/kusto/query/avg-aggfunction) mają niski wpływ na procesor CPU ze względu na ich logikę, inne są bardziej skomplikowane i obejmują algorytmy heurystyczne i oszacowania, które umożliwiają ich wydajne wykonywanie. Na przykład, [DCount ()](/azure/kusto/query/dcount-aggfunction) używa algorytmu HyperLogLog, aby zapewnić ścisłe oszacowanie do odrębnej liczby dużych zestawów danych bez faktycznego zliczania każdej wartości; funkcje percentylu są podobne do przybliżania przy użyciu najbliższego algorytmu percentylu rangi. Kilka poleceń zawiera opcjonalne parametry w celu zmniejszenia ich wpływu. Na przykład funkcja [MakeSet ()](/azure/kusto/query/makeset-aggfunction) ma opcjonalny parametr definiujący maksymalny rozmiar zestawu, który znacząco wpływa na procesor i pamięć.

Polecenia [Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) i [podsumowujące](/azure/kusto/query/summarizeoperator) mogą spowodować wysokie wykorzystanie procesora CPU podczas przetwarzania dużego zestawu danych. Ich złożoność jest bezpośrednio związana z liczbą możliwych wartości, które są określane jako *Kardynalność*kolumn, które są używane jako `by` podsumowujące lub jako atrybuty sprzężenia. Aby uzyskać wyjaśnienie i optymalizację przyłączania i podsumowywania, zobacz artykuły z dokumentacją i porady dotyczące optymalizacji.

Na przykład następujące zapytania dają dokładnie ten sam wynik, ponieważ **CounterPath** jest zawsze jeden do jednego zmapowany do **CounterName** i **ObjectName**. Druga z nich jest bardziej wydajna, ponieważ wymiar agregacji jest mniejszy:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

Użycie procesora CPU może również mieć wpływ na warunki lub kolumny rozszerzone, które wymagają intensywnego przetwarzania danych. Wszystkie proste porównania ciągów, takie jak [równości = =](/azure/kusto/query/datatypes-string-operators) i [StartsWith](/azure/kusto/query/datatypes-string-operators) , mają mniej więcej na ten sam wpływ na procesor, podczas gdy zaawansowane dopasowania tekstu mają większy wpływ. [W odróżnieniu od operatora](/azure/kusto/query/datatypes-string-operators) [Contains](/azure/kusto/query/datatypes-string-operators) jest bardziej wydajne. Ze względu na techniki obsługi ciągów bardziej wydajne jest wyszukiwanie ciągów, które są dłuższe niż cztery znaki niż krótkie ciągi.

Na przykład następujące zapytania dają podobne wyniki, w zależności od zasad nazewnictwa komputerów, ale druga z nich jest bardziej wydajna:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Ten wskaźnik przedstawia tylko procesor CPU z klastra bezpośredniego. W zapytaniu obejmującym wiele regionów reprezentuje tylko jeden z regionów. W zapytaniu obejmującym wiele obszarów roboczych może nie zawierać wszystkich obszarów roboczych.


## <a name="data-used-for-processed-query"></a>Dane używane do przetworzenia zapytania

Krytycznym czynnikiem w przetwarzaniu zapytania jest ilość danych, które są skanowane i używane do przetwarzania zapytań. Usługa Azure Eksplorator danych korzysta z agresywnych optymalizacji, które znacząco zmniejszają ilość danych w porównaniu z innymi platformami danych. Nadal istnieją kluczowe czynniki w zapytaniu, które mogą mieć wpływ na używany wolumin danych.
W dziennikach Azure Monitor kolumna **TimeGenerated** służy jako sposób indeksowania danych. Ograniczanie wartości **TimeGenerated** do tak wąskiego zakresu, jak to możliwe, spowoduje znaczne zwiększenie wydajności zapytań przez znaczne ograniczenie ilości danych, które należy przetworzyć.

Innym czynnikiem, który zwiększa dane procesu, jest użycie dużej liczby tabel. Dzieje się tak zazwyczaj, gdy `search *` i `union *` polecenia są używane. Te polecenia wymuszają, aby system obliczał i skanował dane ze wszystkich tabel w obszarze roboczym. W niektórych przypadkach w obszarze roboczym mogą znajdować się setki tabel. Spróbuj uniknąć możliwie największej ilości miejsca przy użyciu funkcji wyszukiwania * lub dowolnego wyszukiwania bez określania zakresu dla konkretnej tabeli.

Na przykład następujące zapytania dają dokładnie ten sam wynik, ale ostatni z nich jest najbardziej wydajny:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

Inną metodą zredukowania ilości danych jest w [przypadku](/azure/kusto/query/whereoperator) wczesnych warunków w zapytaniu. Platforma Azure Eksplorator danych obejmuje pamięć podręczną, która pozwala na określenie, które partycje zawierają dane istotne dla określonego warunku WHERE. Na przykład, jeśli zapytanie zawiera `where EventID == 4624`, będzie ono dystrybuowane tylko do węzłów, które obsługują partycje ze zgodnymi zdarzeniami.

Poniższe przykładowe zapytania dają dokładnie ten sam wynik, ale druga jest bardziej wydajna:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

Ponieważ Eksplorator danych platformy Azure to kolumnowy magazyn danych, pobieranie każdej kolumny jest niezależne od innych. Liczba kolumn, które są pobierane bezpośrednio wpływa na cały rozmiar danych. Należy uwzględnić tylko kolumny w danych wyjściowych, które są potrzebne, [Podsumowując](/azure/kusto/query/summarizeoperator) wyniki lub [projekcję](/azure/kusto/query/projectoperator) określonych kolumn. Usługa Azure Eksplorator danych ma kilka optymalizacji, aby zmniejszyć liczbę pobranych kolumn. Jeśli określa, że kolumna nie jest wymagana, na przykład jeśli nie jest przywoływana w poleceniu [podsumowywania](/azure/kusto/query/summarizeoperator) , nie zostanie ona pobrana.

Na przykład drugie zapytanie może przetwarzać trzy razy więcej danych, ponieważ nie musi pobrać jednej kolumny, ale trzy:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Przedział czasu przetworzonego zapytania

Wszystkie dzienniki dzienników Azure Monitor są partycjonowane według kolumny **TimeGenerated** . Liczba dostępnych partycji jest bezpośrednio związana z przedziałem czasu. Skrócenie zakresu czasu jest najbardziej wydajnym sposobem zapewnienia wykonywania zapytania o monit.

Zakres czasu można ustawić za pomocą selektora zakresu czasu na ekranie Log Analytics, zgodnie z opisem w temacie [zakres zapytania dziennika i zakres czasu w Log Analytics Azure monitor](scope.md#time-range). Jest to zalecana metoda, ponieważ wybrany zakres czasu jest przesyłany do zaplecza przy użyciu metadanych zapytania. 

Alternatywną metodą jest jawne uwzględnienie warunku [WHERE](/azure/kusto/query/whereoperator) w **TimeGenerated** w zapytaniu. Tej metody należy użyć, ponieważ gwarantuje to, że przedział czasu jest stały, nawet gdy zapytanie jest używane z innego interfejsu.
Należy upewnić się, że wszystkie części zapytania mają filtry **TimeGenerated** . Gdy zapytanie zawiera podzapytania pobierające dane z różnych tabel lub tej samej tabeli, każda musi zawierać własny warunek [WHERE](/azure/kusto/query/whereoperator) .

Na przykład w poniższym zapytaniu, gdy tabela **wydajności** będzie skanowana tylko przez ostatni dzień, tabela **pulsu** zostanie przeskanowana w celu uzyskania całej historii, co może być maksymalnie dwa lata:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Typowy przypadek, w którym występuje błąd jest używany do znajdowania ostatniego wystąpienia [arg_max ()](/azure/kusto/query/arg-max-aggfunction) . Na przykład:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Można to łatwo naprawić, dodając filtr czasu w wewnętrznej kwerendzie:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Innym przykładem tego błędu jest wykonywanie filtrowania zakresu czasu zaraz po [Unii](/azure/kusto/query/unionoperator?pivots=azuremonitor) w kilku tabelach. Podczas wykonywania Unii każde podzapytanie powinno być objęte zakresem. Można użyć instrukcji [Let](/azure/kusto/query/letstatement) , aby zapewnić spójność zakresu.

Na przykład następujące zapytanie przeskanuje wszystkie dane w tabelach *pulsu* i *wydajności* , a nie tylko na ostatni 1 dzień:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

To zapytanie powinno być ustalone w następujący sposób:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

Pomiar jest zawsze większy niż określony czas rzeczywisty. Na przykład jeśli filtr zapytania wynosi 7 dni, system może skanować 7,5 lub 8,1 dni. Wynika to z faktu, że system dzieli dane na fragmenty w zmiennym rozmiarze. Aby upewnić się, że wszystkie odpowiednie rekordy są skanowane, skanuje całą partycję, która może obejmować kilka godzin, a nawet kilka dni.

Istnieje kilka przypadków, w których system nie może zapewnić dokładnego pomiaru zakresu czasu. Zdarza się to w większości przypadków, gdy zapytanie obejmuje mniej niż dzień lub zapytania obejmujące wiele obszarów roboczych.


> [!IMPORTANT]
> Ten wskaźnik przedstawia tylko dane przetworzone w bezpośrednim klastrze. W zapytaniu obejmującym wiele regionów reprezentuje tylko jeden z regionów. W zapytaniu obejmującym wiele obszarów roboczych może nie zawierać wszystkich obszarów roboczych.

## <a name="age-of-processed-data"></a>Wiek przetworzonych danych
Usługa Azure Eksplorator danych korzysta z kilku warstw magazynowania: lokalnych dysków SSD i znacznie wolniejszych obiektów blob platformy Azure. Im nowsze dane, tym wyższy jest szansa, że jest ona przechowywana w bardziej wydajnej warstwie z mniejszym opóźnieniem, co skraca czas trwania zapytania i procesor CPU. Oprócz samych danych system ma również pamięć podręczną dla metadanych. Im starsze dane, tym mniej szansa, że metadane będą znajdować się w pamięci podręcznej.

Niektóre zapytania wymagają użycia starych danych, ale zdarzają się sytuacje, w których stare dane są używane przez pomyłkę. Dzieje się tak, gdy zapytania są wykonywane bez podawania zakresu czasu w metadanych, a nie wszystkie odwołania do tabeli obejmują filtr w kolumnie **TimeGenerated** . W takich przypadkach system przeskanuje wszystkie dane, które są przechowywane w tej tabeli. Gdy przechowywanie danych jest długie, może obejmować wiele przedziałów czasu, a tym samym dane, które są tak stare jak okres przechowywania danych.

Takie przypadki mogą być na przykład następujące:

- Nie ustawiono zakresu czasu w Log Analytics z podzapytaniem, które nie jest ograniczone. Zobacz przykład powyżej.
- Korzystanie z interfejsu API bez parametrów opcjonalnych zakresu czasu.
- Korzystanie z klienta, który nie wymusza przedziału czasu, takiego jak łącznik Power BI.

Zapoznaj się z przykładami i uwagami w sekcji poprzedniej, ponieważ są one również odpowiednie w tym przypadku.

## <a name="number-of-regions"></a>Liczba regionów
Istnieje kilka sytuacji, w których pojedyncze zapytanie może być wykonywane w różnych regionach:

- Gdy kilka obszarów roboczych jest jawnie wymienionych i znajdują się w różnych regionach.
- Gdy zapytanie o zakres zasobów pobiera dane, a dane są przechowywane w wielu obszarach roboczych, które znajdują się w różnych regionach.

Wykonywanie zapytań między regionami wymaga, aby system mógł serializować i przesłać do dużych fragmentów danych pośrednich, które są zwykle znacznie większe niż końcowe wyniki zapytania. Pozwala również ograniczyć możliwość wykonywania optymalizacji, algorytmów heurystycznych i wykorzystania pamięci podręcznych przez system.
Jeśli nie ma żadnej prawdziwej przyczyny skanowania wszystkich tych regionów, należy dostosować zakres tak, aby obejmował mniejszą liczbę regionów. Jeśli zakres zasobów jest zminimalizowany, ale nadal są używane wiele regionów, może się to zdarzyć z powodu błędu konfiguracji. Na przykład dzienniki inspekcji i ustawienia diagnostyczne są wysyłane do różnych obszarów roboczych w różnych regionach lub wiele konfiguracji ustawień diagnostycznych. 

> [!IMPORTANT]
> Gdy zapytanie jest uruchamiane w kilku regionach, pomiary procesora i danych nie będą dokładne i będą przedstawiać pomiar tylko w jednym z regionów.

## <a name="number-of-workspaces"></a>Liczba obszarów roboczych
Obszary robocze są kontenerami logicznymi, które są używane do segregowania i administrowania danymi dzienników. Zaplecze optymalizuje umieszczanie obszarów roboczych w klastrach fizycznych w wybranym regionie.

Użycie wielu obszarów roboczych może wynikać z: 

- Gdzie kilka obszarów roboczych jest jawnie wymienionych.
- Gdy zapytanie o zakres zasobów pobiera dane, a dane są przechowywane w wielu obszarach roboczych.
 
Wykonywanie zapytań między regionami i między klastrami wymaga, aby system mógł serializować i przesłać do dużych fragmentów danych pośrednich, które są zwykle znacznie większe niż końcowe wyniki zapytania. Pozwala również ograniczyć możliwość wykonywania optymalizacji, algorytmów heurystycznych i wykorzystania pamięci podręcznych przez system.

> [!IMPORTANT]
> W niektórych scenariuszach obejmujących wiele obszarów roboczych pomiary procesora i danych nie będą dokładne i będą reprezentować tylko niektóre obszary robocze.

## <a name="parallelism"></a>Równoległości
Dzienniki Azure Monitor korzystają z dużych klastrów usługi Azure Eksplorator danych do uruchamiania zapytań. te klastry różnią się w zależności od liczby węzłów obliczeniowych. System automatycznie skaluje klastry zgodnie z logiką i pojemnością umieszczenia obszaru roboczego.

Aby efektywnie wykonać zapytanie, jest ono partycjonowane i dystrybuowane do węzłów obliczeniowych na podstawie danych, które są wymagane do przetwarzania. Istnieją sytuacje, w których system nie może wykonać tej czynności efektywnie. Może to prowadzić do długiego czasu trwania zapytania. 

Zachowania zapytań, które mogą obniżyć równoległość, obejmują:

- Użycie funkcji serializacji i okna, takich jak [operator serializacji](/azure/kusto/query/serializeoperator), [Next ()](/azure/kusto/query/nextfunction), [poprzedni ()](/azure/kusto/query/prevfunction)i funkcje [wiersza](/azure/kusto/query/rowcumsumfunction) . W niektórych z tych przypadków można używać szeregów czasowych i funkcji analitycznych użytkownika. Nieefektywna Serializacja może również wystąpić, jeśli następujące operatory nie znajdują się na końcu zapytania: [zakres](/azure/kusto/query/rangeoperator), [Sortowanie](/azure/kusto/query/sortoperator), [kolejność](/azure/kusto/query/orderoperator), [Top](/azure/kusto/query/topoperator), [Top-hitters](/azure/kusto/query/tophittersoperator), [GetSchema](/azure/kusto/query/getschemaoperator).
-   Użycie funkcji agregacji [DCount ()](/azure/kusto/query/dcount-aggfunction) wymusza, aby system miał centralną kopię różnych wartości. Gdy skala danych jest wysoka, rozważ użycie opcjonalnych parametrów funkcji DCount do zredukowania dokładności.
-   W wielu przypadkach operator [Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) obniżyć ogólną wartość równoległości. Badaj rozłączenie losowe jako alternatywę w przypadku problemów z wydajnością.
-   W zapytaniach dotyczących zakresu zasobów, kontrole kontroli RBAC przedwykonawcy mogą być pokutujące w sytuacjach, gdy istnieje bardzo duża liczba przypisań RBAC. Może to prowadzić do dłuższego sprawdzenia, które mogłoby spowodować zmniejszenie równoległości. Na przykład zapytanie jest wykonywane w ramach subskrypcji, w której istnieją tysiące zasobów, a każdy zasób ma wiele przypisań roli na poziomie zasobu, a nie w ramach subskrypcji lub grupy zasobów.
-   Jeśli zapytanie przetwarza małe fragmenty danych, jego równoległość będzie niska, ponieważ system nie rozwiąże go w wielu węzłach obliczeniowych.



## <a name="next-steps"></a>Następne kroki

- [Dokumentacja języka zapytań Kusto](/azure/kusto/query/).
