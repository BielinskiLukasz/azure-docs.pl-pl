---
title: Najlepsze rozwiązania dotyczące środowiska Node. js i rozwiązywanie problemów
description: Poznaj najlepsze rozwiązania i kroki rozwiązywania problemów z aplikacjami Node. js uruchomionymi w programie Azure App Service.
author: ranjithr
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: bwren
ms.custom: seodec18
ms.openlocfilehash: 75195bd7ad228bb66dfd21d2c65997cc8c02680e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672048"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Najlepsze rozwiązania i wskazówki dotyczące rozwiązywania problemów z aplikacjami węzłów w systemie Azure App Service Windows

W tym artykule przedstawiono najlepsze rozwiązania i kroki rozwiązywania problemów z [aplikacjami węzłów](app-service-web-get-started-nodejs.md) uruchomionymi na Azure App Service (z [programu iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Należy zachować ostrożność podczas korzystania z kroków rozwiązywania problemów w witrynie produkcyjnej. Zalecenie polega na rozwiązywaniu problemów z aplikacją w konfiguracji nieprodukcyjnej, na przykład w miejscu przejściowym i po rozwiązaniu problemu, zamiany miejsca przejściowego z miejscem produkcyjnym.
>

## <a name="iisnode-configuration"></a>Konfiguracja programu IISNODE

Ten [plik schematu](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) przedstawia wszystkie ustawienia, które można skonfigurować dla programu iisnode. Niektóre ustawienia, które są przydatne dla aplikacji:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

To ustawienie określa liczbę procesów węzłów, które są uruchamiane dla aplikacji IIS. Wartość domyślna to 1. Jako liczbę vCPU maszyny wirtualnej można uruchomić dowolną liczbę Node. exe, zmieniając wartość na 0. Zalecana wartość to 0 dla większości aplikacji, dzięki czemu można używać wszystkich procesorów wirtualnych vCPU na maszynie. Node. exe jest jednowątkowy, więc jeden Node. exe zużywa maksymalnie 1 vCPU. Aby uzyskać maksymalną wydajność aplikacji węzła, należy użyć wszystkich procesorów wirtualnych vCPU.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

To ustawienie steruje ścieżką do pliku Node. exe. Możesz ustawić tę wartość, aby wskazywała wersję pliku Node. exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

To ustawienie określa maksymalną liczbę równoczesnych żądań wysyłanych przez programu iisnode do każdego pliku Node. exe. Na Azure App Service wartość domyślna to nieskończoność. Można skonfigurować wartość w zależności od liczby żądań odbieranych przez aplikację oraz szybkości przetwarzania poszczególnych żądań przez aplikację.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

To ustawienie określa maksymalną liczbę przypadków programu iisnode ponownych prób nawiązania połączenia w nazwanym potoku w celu wysłania żądań do pliku Node. exe. To ustawienie w połączeniu z namedPipeConnectionRetryDelay określa łączny limit czasu dla każdego żądania w ramach programu iisnode. Wartość domyślna to 200 w Azure App Service. Łączny limit czasu w sekundach = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

To ustawienie Steruje ilością czasu (w MS) programu iisnode czeka między kolejnymi próbami wysłania żądania do pliku Node. exe przez nazwany potok. Wartość domyślna to 250 MS.
Łączny limit czasu w sekundach = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

Domyślnie łączny limit czasu w programu iisnode na Azure App Service to 200 \* 250 MS = 50 sekund.

### <a name="logdirectory"></a>logDirectory

To ustawienie określa katalog, w którym programu iisnode rejestruje stdout/stderr. Wartość domyślna to programu iisnode, która jest względna w stosunku do głównego katalogu skryptów (znajdującego się w katalogu głównym serwera. js)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

To ustawienie określa, która wersja inspektora Node-programu iisnode używa podczas debugowania aplikacji węzła. Obecnie tylko dwa prawidłowe wartości dla tego ustawienia to iisnode-Inspector-0.7.3. dll i iisnode-Inspector. dll. Wartość domyślna to iisnode-Inspector-0.7.3. dll. Wersja iisnode-Inspector-0.7.3. dll używa programu Node-Inspector-0.7.3 i używa gniazd sieci Web. Włącz gniazda sieci Web w usłudze Azure webapp, aby korzystać z tej wersji. Aby uzyskać więcej informacji na temat konfigurowania programu iisnode do korzystania z nowego kontrolera węzłów, zobacz <https://ranjithblogs.azurewebsites.net/?p=98>.

### <a name="flushresponse"></a>flushResponse

Domyślne zachowanie usług IIS polega na tym, że buforuje dane odpowiedzi do 4 MB przed ich opróżnianiem lub do końca odpowiedzi, w zależności od tego, co nastąpi wcześniej. programu iisnode oferuje ustawienie konfiguracji do przesłonięcia tego zachowania: Aby opróżnić fragment treści jednostki odpowiedzi, gdy tylko programu iisnode odbierze go z pliku Node. exe, należy ustawić atrybut iisnode/@flushResponse w pliku Web. config na wartość "true":

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Włączenie opróżniania każdego fragmentu treści jednostki odpowiedzi powoduje narzutowanie wydajności, co zmniejsza przepływność systemu przez ~ 5% (od v 0.1.13). Najlepsze do zakresu tego ustawienia tylko dla punktów końcowych, które wymagają przesyłania strumieniowego odpowiedzi (na przykład przy użyciu elementu `<location>` w pliku Web. config)

Oprócz tego w przypadku aplikacji przesyłanych strumieniowo należy również ustawić responseBufferLimit obsługi programu iisnode na 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Rozdzielana średnikami lista plików, które są oglądane pod kątem zmian. Wszelkie zmiany w pliku powodują odzyskanie aplikacji. Każdy wpis składa się z opcjonalnej nazwy katalogu, a także wymaganej nazwy pliku, odnoszącej się do katalogu, w którym znajduje się główny punkt wejścia aplikacji. Symbole wieloznaczne są dozwolone tylko w części nazwy pliku. Wartość domyślna to `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Wartość domyślna to false. Jeśli ta funkcja jest włączona, aplikacja węzła może nawiązać połączenie z nazwanym potokiem (zmienna środowiskowa programu IISNODE\_KONTROLka\_) i wysłać komunikat "Odtwórz ponownie". Powoduje to, że w3wp jest bezpiecznie odtwarzany.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Wartość domyślna to 0, co oznacza, że ta funkcja jest wyłączona. Po ustawieniu na wartość większą niż 0, programu iisnode będzie stronicować wszystkie procesy podrzędne w każdym z elementów podrzędnych w milisekundach. Zapoznaj się z [dokumentacją](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) , aby dowiedzieć się, co oznacza strona. To ustawienie jest przydatne w przypadku aplikacji, które zużywają duże ilości pamięci i chcą stronicować pamięć na dysk sporadycznie, aby zwolnić pamięć RAM.

> [!WARNING]
> Podczas włączania następujących ustawień konfiguracji w aplikacjach produkcyjnych należy zachować ostrożność. Zaleca się, aby nie włączać ich na aktywnych aplikacjach produkcyjnych.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Wartość domyślna to false. W przypadku ustawienia wartości true programu iisnode dodaje nagłówek odpowiedzi HTTP `iisnode-debug` do każdej odpowiedzi HTTP, która wyśle `iisnode-debug` wartość nagłówka jest adresem URL. Poszczególne fragmenty informacji diagnostycznych można uzyskać, przeglądając fragment adresu URL, jednak Wizualizacja jest dostępna, otwierając adres URL w przeglądarce.

### <a name="loggingenabled"></a>loggingEnabled

To ustawienie steruje rejestrowaniem stdout i stderr przez programu iisnode. Programu iisnode przechwytuje stdout/stderr z procesów węzłów, które są uruchamiane i zapisuje w katalogu określonym w ustawieniu "logDirectory". Gdy ta funkcja jest włączona, aplikacja zapisuje dzienniki w systemie plików i w zależności od liczby dzienników wykonywanej przez aplikację może mieć wpływ na wydajność.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Wartość domyślna to false. Po ustawieniu na wartość true programu iisnode wyświetla kod stanu HTTP i kod błędu Win32 w przeglądarce. Kod Win32 jest przydatny w debugowaniu niektórych typów problemów.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (nie włączaj w witrynie produkcyjnej na żywo)

To ustawienie steruje funkcją debugowania. Programu iisnode jest zintegrowany z inspektorem węzłów. Włączenie tego ustawienia umożliwia debugowanie aplikacji węzła. Po włączeniu tego ustawienia programu iisnode tworzy pliki inspektora Node w katalogu "debuggerVirtualDir" podczas pierwszego żądania debugowania do aplikacji węzła. Możesz załadować Inspektor węzła, wysyłając żądanie do `http://yoursite/server.js/debug`. Można kontrolować segment debugowania adresu URL za pomocą ustawienia "debuggerPathSegment". Domyślnie debuggerPathSegment = "debug". Można ustawić `debuggerPathSegment` na identyfikator GUID, na przykład, aby trudniejsze było odnajdywanie przez inne osoby.

Przeczytaj artykuł [debugowanie aplikacji node. js w systemie Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) , aby uzyskać więcej informacji na temat debugowania.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenariusze i zalecenia/Rozwiązywanie problemów

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Aplikacja My Node udostępnia zbyt duże wywołania wychodzące

Wiele aplikacji chce nawiązać połączenia wychodzące w ramach zwykłych operacji. Na przykład w przypadku zgłoszenia żądania w aplikacji węzła warto skontaktować się z interfejsem API REST w innym miejscu i uzyskać informacje dotyczące przetwarzania żądania. Podczas wykonywania wywołań http lub https warto używać agenta Keep Alive. Podczas wykonywania tych wywołań wychodzących można użyć modułu agentkeepalive jako agenta Keep Alive.

Moduł agentkeepalive gwarantuje, że gniazda są ponownie używane na maszynie wirtualnej usługi Azure WEBAPP. Utworzenie nowego gniazda dla każdego żądania wychodzącego powoduje dodanie narzutu do aplikacji. Gdy aplikacja ponownie używa gniazd do żądań wychodzących, zapewnia, że aplikacja nie przekroczy maxSockets przyznanych na maszynę wirtualną. Zalecenie dotyczące Azure App Service polega na ustawieniu wartości maxSockets agentKeepAlive na łączną liczbę wystąpień programu Node. exe \* 40 maxSockets/Instance) 160 gniazd na maszynę wirtualną.

Przykładowa konfiguracja [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) :

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> W tym przykładzie przyjęto założenie, że na maszynie wirtualnej jest uruchomiony 4 Node. exe. Jeśli na maszynie wirtualnej jest uruchomiona inna liczba węzłów Node. exe, należy odpowiednio zmodyfikować ustawienie maxSockets.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Aplikacja My Node zużywa zbyt dużo czasu procesora

Użytkownik może otrzymać zalecenie dotyczące Azure App Service w portalu dotyczące wysokiego użycia procesora CPU. Możesz również skonfigurować monitory do oglądania określonych [metryk](web-sites-monitor.md). Podczas sprawdzania użycia procesora na [pulpicie nawigacyjnym witryny Azure Portal](../azure-monitor/app/web-monitor-performance.md)Sprawdź wartości maksymalne dla procesora, aby nie trafiać wartości szczytu.
Jeśli uważasz, że aplikacja zużywa zbyt dużo czasu procesora i nie możesz wyjaśnić, dlaczego można profilować aplikację węzła, aby się dowiedzieć.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilowanie aplikacji node na Azure App Service za pomocą narzędzia V8-Profiler

Załóżmy na przykład, że masz aplikację Hello World, którą chcesz profilować w następujący sposób:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Przejdź do lokacji konsoli debugowania `https://yoursite.scm.azurewebsites.net/DebugConsole`

Przejdź do katalogu site/wwwroot. Zobaczysz wiersz polecenia, jak pokazano w następującym przykładzie:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Uruchom polecenie `npm install v8-profiler`.

To polecenie instaluje V8-Profiler w węźle Node\_modules i wszystkie jego zależności.
Teraz edytuj serwer. js, aby profilować aplikację.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Poprzedni kod profiluje funkcję WriteConsoleLog, a następnie zapisuje dane wyjściowe profilu do pliku "Profile. cpuprofile" w lokalizacji wwwroot. Wyślij żądanie do aplikacji. Zostanie wyświetlony plik "profil. cpuprofile" utworzony w witrynie wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Pobierz ten plik i otwórz go za pomocą narzędzi Chrome F12 Tools. Naciśnij klawisz F12 w przeglądarce Chrome, a następnie wybierz kartę **Profile** . Wybierz przycisk **Załaduj** . Wybierz pobrany plik profile. cpuprofile. Kliknij właśnie załadowany profil.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Można zobaczyć, że przez funkcję WriteConsoleLog wykorzystano 95% czasu. W danych wyjściowych znajdują się również dokładne numery wierszy i pliki źródłowe, które spowodowały problem.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Aplikacja mojego węzła zużywa zbyt dużo pamięci

Jeśli aplikacja zużywa zbyt dużo pamięci, zobaczysz powiadomienie z Azure App Service w portalu dotyczące wysokiego użycia pamięci. Można skonfigurować monitory do oglądania określonych [metryk](web-sites-monitor.md). Sprawdzając użycie pamięci na [pulpicie nawigacyjnym witryny Azure Portal](../azure-monitor/app/web-monitor-performance.md), pamiętaj o sprawdzeniu maksymalnej wartości pamięci, aby nie trafiać wartości szczytu.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Wykrywanie przecieków i różnica sterty dla środowiska Node. js

Aby ułatwić identyfikację przecieków pamięci, można użyć [węzła Node-memwatch](https://github.com/lloyd/node-memwatch) .
Możesz zainstalować `memwatch`, podobnie jak V8-Profiler i edytować swój kod, aby przechwytywać i różnicować sterty w celu zidentyfikowania przecieków pamięci w aplikacji.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Moje węzły Node. exe są ciągle zabijane

Istnieje kilka powodów, dla których plik Node. exe jest wyłączany losowo:

1. Aplikacja zgłasza nieprzechwycone wyjątki — Sprawdź d:\\Home\\LogFiles\\Application\\Logging-Errors. txt, aby uzyskać szczegółowe informacje na temat zgłoszonego wyjątku. Ten plik ma ślad stosu, który ułatwia debugowanie i poprawianie aplikacji.
2. Aplikacja zużywa zbyt dużo pamięci, co wpływa na inne procesy od rozpoczęcia. Jeśli całkowita ilość pamięci maszyny wirtualnej jest bliska 100%, może to spowodować przerwanie przez Menedżera procesów pliku Node. exe. Menedżer procesów kasuje niektóre procesy, aby umożliwić innym procesom uzyskanie pewnych zadań. Aby rozwiązać ten problem, należy profilować aplikację pod kątem przecieków pamięci. Jeśli aplikacja wymaga dużej ilości pamięci, można skalować w górę do większej maszyny wirtualnej (co zwiększa ilość pamięci RAM dostępnej dla maszyny wirtualnej).

### <a name="my-node-application-does-not-start"></a>Nie uruchomiono aplikacji My Node

Jeśli aplikacja zwróci błędy 500 podczas uruchamiania, może istnieć kilka przyczyn:

1. W poprawnej lokalizacji nie ma pliku Node. exe. Sprawdź ustawienie nodeProcessCommandLine.
2. Główny plik skryptu nie znajduje się w prawidłowej lokalizacji. Sprawdź plik Web. config i upewnij się, że nazwa głównego pliku skryptu w sekcji obsługi jest zgodna z głównym plikiem skryptu.
3. Konfiguracja pliku Web. config jest niepoprawna — Sprawdź nazwy ustawień/wartości.
4. Zimna — uruchamianie aplikacji trwa zbyt długo. Jeśli aplikacja zajmuje więcej niż (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000 sekund, programu iisnode zwraca błąd 500. Zwiększ wartości tych ustawień, aby dopasować czas rozpoczęcia aplikacji, aby zapobiec przekroczeniu limitu czasu programu iisnode i zwróceniem błędu 500.

### <a name="my-node-application-crashed"></a>Aplikacja mojego węzła uległa awarii

Aplikacja zgłasza nieprzechwycone wyjątki — Sprawdź `d:\\home\\LogFiles\\Application\\logging-errors.txt` plik, aby uzyskać szczegółowe informacje na temat zgłoszonego wyjątku. Ten plik ma ślad stosu, który pomaga zdiagnozować i naprawić aplikację.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Uruchamianie aplikacji w węźle trwa zbyt wiele czasu (zimny start)

Typową przyczyną długich czasów uruchamiania aplikacji jest duża liczba plików w węźle\_modułów. Aplikacja próbuje załadować większość z tych plików podczas uruchamiania. Domyślnie, ponieważ pliki są przechowywane w udziale sieciowym na Azure App Service, ładowanie wielu plików może zająć dużo czasu.
Niektóre rozwiązania, które umożliwiają szybsze wykonywanie tego procesu:

1. Upewnij się, że masz płaską strukturę zależności i nie ma zduplikowanych zależności przy użyciu npm3 do zainstalowania modułów.
2. Spróbuj ponownie załadować węzeł\_modułów i nie ładować wszystkich modułów podczas uruchamiania aplikacji. Do modułów ładowania z opóźnieniem, wywołanie wymagane ("module") powinno zostać wykonane, gdy w rzeczywistości wymaga modułu w funkcji przed pierwszym wykonaniem kodu modułu.
3. Azure App Service oferuje funkcję o nazwie lokalna pamięć podręczna. Ta funkcja kopiuje zawartość z udziału sieciowego na dysk lokalny na maszynie wirtualnej. Ponieważ pliki są lokalne, czas ładowania węzła\_modułów jest znacznie szybszy.

## <a name="iisnode-http-status-and-substatus"></a>PROGRAMU IISNODE stanu HTTP i podstanu

[Plik źródłowy](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) `cnodeconstants` zawiera listę wszystkich możliwych kombinacji stanu/podstanu programu iisnode może zwrócić z powodu błędu.

Włącz FREB dla aplikacji, aby zobaczyć kod błędu Win32 (Upewnij się, że FREB jest włączona tylko w witrynach nieprodukcyjnych ze względu na wydajność).

| Stan http | Podstan http | Możliwe przyczyny? |
| --- | --- | --- |
| 500 |1000 |Wystąpił problem podczas wysyłania żądania do programu IISNODE — Sprawdź, czy uruchomiono plik Node. exe. Program Node. exe mógł ulec awarii podczas uruchamiania. Sprawdź, czy konfiguracja pliku Web. config ma błędy. |
| 500 |1001 |-Win32Error 0x2-aplikacja nie odpowiada na adres URL. Sprawdź reguły ponownego zapisywania adresów URL lub sprawdź, czy aplikacja Express ma zdefiniowane poprawne trasy. -Win32Error 0x6d — nazwany potok jest zajęty — Node. exe nie akceptuje żądań, ponieważ potok jest zajęty. Sprawdź duże użycie procesora CPU. -Inne błędy — Sprawdź, czy awaria pliku Node. exe. |
| 500 |1002 |Plik Node. exe uległ awarii — Sprawdź d:\\Home\\LogFiles\\Logging-Errors. txt na potrzeby śledzenia stosu. |
| 500 |1003 |Problem z konfiguracją potoku — konfiguracja nazwanego potoku jest niepoprawna. |
| 500 |1004-1018 |Wystąpił błąd podczas wysyłania żądania lub przetwarzania odpowiedzi do/z pliku Node. exe. Sprawdź, czy w pliku Node. exe wystąpiła awaria. klauzula Check d:\\Home\\LogFiles\\Logging-Errors. txt na potrzeby śledzenia stosu. |
| 503 |1000 |Za mało pamięci, aby przydzielić więcej połączeń nazwanych potoków. Sprawdź, Dlaczego aplikacja zużywa tyle pamięci. Sprawdź wartość ustawienia maxConcurrentRequestsPerProcess. Jeśli nie jest to nieskończoność i masz wiele żądań, zwiększ tę wartość, aby uniknąć tego błędu. |
| 503 |1001 |Nie można wysłać żądania do pliku Node. exe, ponieważ trwa odtwarzanie aplikacji. Po odtworzeniu aplikacji należy normalnie obsłużyć żądania. |
| 503 |1002 |Sprawdź kod błędu Win32 dla rzeczywistej przyczyny — nie można wysłać żądania do pliku Node. exe. |
| 503 |1003 |Nazwany potok jest zbyt zajęty — Sprawdź, czy Node. exe zużywa nadmierny procesor CPU |

Plik NODE. exe ma ustawienie o nazwie `NODE_PENDING_PIPE_INSTANCES`. Na Azure App Service wartość ta jest równa 5000. Oznacza to, że Node. exe może akceptować 5000 żądań jednocześnie w nazwanym potoku. Ta wartość powinna być wystarczająca dla większości aplikacji węzłów działających na Azure App Service. Na Azure App Service nie powinno być widoczne 503,1003 ze względu na wysoką wartość `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Więcej zasobów

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat aplikacji node. js na Azure App Service.

* [Get started with Node.js web apps in Azure App Service (Rozpoczynanie pracy z aplikacjami internetowymi Node.js w usłudze Azure App Service)](app-service-web-get-started-nodejs.md)
* [How to debug a Node.js web app in Azure App Service (Jak debugować aplikację internetową Node.js w usłudze Azure App Service)](https://blogs.msdn.microsoft.com/azureossds/2018/08/03/debugging-node-js-apps-on-azure-app-services/)
* [Using Node.js Modules with Azure applications (Używanie modułów Node.js z aplikacjami platformy Azure)](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js (Aplikacje internetowe w usłudze Azure App Service: Node.js)](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centrum deweloperów środowiska Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Exploring the Super Secret Kudu Debug Console (Szczegółowe informacje o ściśle tajnej konsoli debugowania aparatu Kudu)](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
