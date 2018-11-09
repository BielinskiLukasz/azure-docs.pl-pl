---
title: Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web | Microsoft Docs
description: Konfigurowanie testów sieci Web w usłudze Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: e8d07922334855a8133cd6271ee98bcfac2243d5
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282615"
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web
Po wdrożeniu aplikacji internetowej lub witryny internetowej na dowolnym serwerze możesz skonfigurować testy, aby monitorować jej dostępność i czas odpowiedzi. Usługa [Azure Application Insights](app-insights-overview.md) wysyła żądania sieci Web do aplikacji w regularnych odstępach czasu z punktów na całym świecie. Jeśli aplikacja będzie odpowiadać powoli lub wcale, usługa powiadomi Cię o tym za pomocą alertu.

Testy dostępności możesz skonfigurować dla dowolnego punktu końcowego protokołów HTTP lub HTTPS, który jest dostępny za pośrednictwem publicznej sieci Internet. Do testowanej witryny sieci Web nie trzeba niczego dodawać. Nie musi być to nawet Twoja witryna: możesz testować usługę interfejsu API REST, od której zależy Twoja praca.

Istnieją dwa rodzaje testów dostępności:

* [Test ping adresu URL](#create): prosty test, który można utworzyć w portalu Azure.
* [Wieloetapowy test sieci Web](#multi-step-web-tests): tworzony w programie Visual Studio Enterprise i przekazywany do portalu.

Dla każdego zasobu aplikacji możesz utworzyć maksymalnie 100 testów dostępności.


## <a name="create"></a>Otwieranie zasobu dla własnych raportów testów dostępności

**Jeśli już skonfigurowano usługę Application Insights** dla aplikacji internetowej, otwórz zasób usługi Application Insights w witrynie [Azure Portal](https://portal.azure.com).

**Lub jeśli chcesz zobaczyć raporty w nowym zasobie**, przejdź do witryny [Azure Portal](https://portal.azure.com) i utwórz zasób usługi Application Insights.

![Nowy > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Kliknij pozycję **Wszystkie zasoby**, aby otworzyć blok Omówienie dla nowego zasobu.

## <a name="setup"></a>Tworzenie testu ping adresu URL
Otwórz blok Dostępność i dodaj test.

![Podaj przynajmniej adres URL swojej witryny sieci Web](./media/app-insights-monitor-web-app-availability/001-create-test.png)

* **Adres URL** może odnosić się do dowolnej strony sieci Web, którą chcesz przetestować, ale musi być widoczny w publicznym Internecie. Adres URL może zawierać ciąg zapytania. Możesz więc np. szybko sprawdzić działanie bazy danych. Jeśli adres URL jest rozpoznawany jako przekierowanie, zostanie prześledzonych maksymalnie 10 przekierowań.
* **Analizuj zależne żądania**: po zaznaczeniu tej opcji test zażąda obrazów, skryptów, plików stylów i innych plików, które są częścią testowanej strony sieci Web. Rejestrowany czas odpowiedzi obejmuje czas poświęcony na pobieranie tych plików. Test zakończy się niepowodzeniem, jeśli nie uda się pobrać tych zasobów w ramach limitu czasu dla całego testu. Jeśli pole opcji nie zostanie zaznaczone, test zażąda tylko pliku pod podanym adresem URL.

* **Włącz ponawianie próby**: zaznaczenie pola tej opcji spowoduje, że nieudany test zostanie ponowiony po krótkim czasie. Błąd jest zgłaszany dopiero wtedy, gdy trzy kolejne próby się nie powiodą. Kolejne testy są następnie wykonywane ze zwykłą częstotliwością. Ponawianie prób jest tymczasowo wstrzymane do czasu następnego sukcesu. Ta reguła jest stosowana niezależnie w każdej lokalizacji testu. Ta opcja jest zalecana. Średnio około 80% błędów znika po ponowieniu testu.

* **Częstotliwość testu**: określa, jak często wykonywane są testy w poszczególnych lokalizacjach testowych. Przy domyślnej częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.

* **Lokalizacje testu** są to miejsca, z których nasze serwery wysyłają żądania sieci Web do Twojego adresu URL. Nasze minimalna liczba lokalizacji testowych zalecane wynosi pięć, aby upewnić się, że móc odróżnić problemy z witryną od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.

> [!NOTE]
> * Zdecydowanie zaleca się testowania z wielu lokalizacji na co najmniej pięciu lokalizacjach. Ten parametr zapobiega fałszywych alarmów, które mogą wynikać z przejściowych problemów z określonej lokalizacji. Ponadto znaleźliśmy, to liczba lokalizacji testowych równa próg lokalizacji alertu + 2 optymalną konfigurację. 
> * Włączanie opcji powoduje "Analizuj zależne żądania" w bardziej rygorystyczne wyboru. Test może nie działać w sytuacjach, które mogą nie być widoczne podczas przeglądania ręcznie lokacji.

* **Kryteria powodzenia**:

    **Limit czasu testu**: zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.

    **Odpowiedź HTTP**: zwrócony kod stanu, który będzie uznawany za sukces. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.

    **Zgodność zawartości**: ciąg znaków, np. „Witaj!” Sprawdzamy, czy w każdej odpowiedzi występuje dokładna zgodność pod względem wielkości liter. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu.

* **Próg lokalizacji alertu**: zaleca się co najmniej 3 na dobę, 5 lokalizacjach. Jest optymalne relacji między próg lokalizacji alertu i liczba lokalizacji testowych **próg lokalizacji alertu** = **liczba lokalizacji testowych** - 2, co najmniej pięć testów lokalizacje.

## <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web
Możliwe jest monitorowanie scenariusza, który obejmuje sekwencję adresów URL. Jeśli na przykład monitorujesz witrynę sklepu, możesz sprawdzić, czy dodawanie towarów do koszyka działa prawidłowo.

> [!NOTE]
> Za wieloetapowe testy sieci Web są naliczane opłaty. [Schemat cennika](https://azure.microsoft.com/pricing/details/application-insights/).
> 

Aby utworzyć test wieloetapowy, nagraj scenariusz przy użyciu programu Visual Studio Enterprise, a następnie przekaż nagranie do usługi Application Insights. Usługa Application Insights odtwarza ten scenariusz w określonych odstępach czasu i weryfikuje odpowiedzi.

> [!NOTE]
> * W testach nie można używać pętli ani funkcji kodowanych. Test musi być całkowicie zawarty w skrypcie webtest. Można jednak używać wtyczek standardowych.
> * W wieloetapowych testach internetowych obsługiwane są tylko angielskie znaki. Jeśli używasz programu Visual Studio w innych językach, zaktualizuj plik definicji testu internetowego w celu zmiany/wykluczenia znaków innych niż angielskie.
>

#### <a name="1-record-a-scenario"></a>1. Nagrywanie scenariusza
Nagraj sesję sieci Web w programie Visual Studio Enterprise.

1. Utwórz projekt internetowego testu wydajnościowego.

    ![W programie Visual Studio Enterprise utwórz projekt z szablonu Projekt internetowych testów wydajnościowych i obciążeniowych.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *Nie widzisz szablonu Projekt internetowych testów wydajnościowych i obciążeniowych?* — Zamknij program Visual Studio Enterprise. Otwórz **Instalator programu Visual Studio** w celu zmodyfikowania instalacji programu Visual Studio Enterprise. W obszarze **Poszczególne składniki** wybierz pozycję **Narzędzia do internetowego testowania wydajnościowego i obciążeniowego**.

2. Otwórz plik .webtest i rozpocznij nagrywanie.

    ![Otwórz plik .webtest i kliknij przycisk Nagraj.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Wykonaj działania użytkownika, które chcesz symulować w teście: otwórz witrynę sieci Web, dodaj produkt do koszyka itd. Następnie zatrzymaj test.

    ![Nagrywanie testów sieci Web w programie Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Scenariusz nie powinien być długi. Jest ograniczony do 100 kroków i 2 minut.
4. Zmodyfikuj test, aby:

   * Dodać operacje sprawdzania poprawności odebranego tekstu i kodów odpowiedzi,
   * Usunąć zbędne interakcje. Możesz również usunąć zależne żądania dotyczące obrazów, witryn z reklamami lub witryn służących do śledzenia.

     Pamiętaj, że możesz edytować tylko skrypt testu — nie możesz dodawać niestandardowego kodu ani wywołań do innych testów sieci Web. Nie wstawiaj pętli do testu. Możesz używać standardowych wtyczek testów sieci Web.
5. Uruchom test w programie Visual Studio, aby upewnić się, że działa.

    Moduł uruchamiający testy sieci web otwiera przeglądarkę sieci Web i powtarza nagrane akcje. Upewnij się, że działanie jest zgodne z oczekiwaniami.

    ![Otwórz plik .webtest w programie Visual Studio i kliknij przycisk Uruchom.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Przekaż test sieci Web do usługi Application Insights
1. W portalu Application Insights utwórz test sieci Web.

    ![W bloku Testy sieci Web wybierz przycisk Dodaj.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Wybierz test wieloetapowy i przekaż plik .webtest.

    ![Wybieranie wieloetapowego testu sieci Web.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Ustaw lokalizacje testu, częstotliwość i parametry alertu w taki sam sposób, jak w przypadku testów ping.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Dodawanie wtyczek czasu i liczb losowych do testu wieloetapowego
Załóżmy, że testujesz narzędzie, które pobiera dane zależne od czasu (np. ceny akcji) z zewnętrznego źródła. Podczas rejestrowania testu sieci Web należy używać określonych godzin, ale ustawionych jako parametry testu: StartTime (Godzina rozpoczęcia) i EndTime (Godzina zakończenia).

![Test sieci Web z parametrami.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Po uruchomieniu testu parametr EndTime powinien zawsze zawierać aktualny czas, a StartTime wartość czasu 15 minut wcześniej.

Wtyczki testu sieci Web umożliwiają parametryzowanie czasu.

1. Dodaj wtyczkę testu sieci Web do każdej wartości parametru zmiennej, która jest potrzebna. Na pasku narzędzi testu sieci Web wybierz polecenie **Dodaj wtyczkę testu sieci Web**.

    ![Wybierz polecenie Dodaj wtyczkę testu sieci Web i wskaż jej typ.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    W tym przykładzie użyte zostaną dwa wystąpienia wtyczki typu Data i godzina. Jedno wystąpienie odpowiada wartości „15 minut temu”, a drugie „teraz”.
2. Otwórz właściwości każdej wtyczki. Nadaj jej nazwę i skonfiguruj, aby używać czasu bieżącego. W jednej z wtyczek ustaw właściwość Dodaj minuty = -15.

    ![Ustawianie właściwości Nazwa, Użyj czasu bieżącego i Dodaj minuty.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. W parametrach testu sieci Web użyj {{nazwa wtyczki}}, aby odwoływać się do nazwy wtyczki.

    ![Używanie {{nazwa wtyczki}} w parametrze testu.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Teraz przekaż test do portalu. Wartości dynamiczne zostaną zastosowane w każdym przebiegu testu.


## <a name="monitor"></a>Wyświetlanie wyników testów dostępności

Po kilku minutach kliknij pozycję **Odśwież**, aby zobaczyć wyniki testu. 

![Podsumowanie wyników w bloku głównym](./media/app-insights-monitor-web-app-availability/14-availSummary-3.png)

Wykres punktowy przedstawia przykłady wyników testów, które zawierają szczegóły diagnostycznego kroku testu. Aparat testowy przechowuje szczegółowe informacje diagnostyczne dla testów z błędami. W przypadku udanych testów szczegółowe informacje diagnostyczne są przechowywane dla podzbioru wykonań. Umieść kursor na zielonych/czerwonych kropkach, aby zobaczyć sygnaturę czasową testu, czas trwania testu, lokalizację i nazwę testu. Kliknij dowolną kropkę na wykresie punktowym, aby wyświetlić szczegóły wyniku testu.  

Wybierz określony test, lokalizację lub skróć okres czasu, aby zobaczyć więcej wyników w okolicy interesującego okresu czasu. Użyj Eksploratora wyszukiwania, aby zobaczyć wyniki z wszystkich wykonań, lub użyj zapytań analitycznych w celu uruchomienia niestandardowych raportów dla tych danych.

Oprócz nieprzetworzonych wyników w Eksploratorze metryk istnieją dwie metryki dostępności: 

1. Dostępność: procent testów, które zostały pomyślnie zakończone, dla wszystkich wykonań testów. 
2. Czas trwania testu: średni czas trwania testu dla wszystkich wykonań testów.

Filtry możesz zastosować do nazwy testu, lokalizacji, aby przeanalizować trendy określonego testu lub lokalizacji.

## <a name="edit"></a> Sprawdzanie i edytowanie testów

Na stronie podsumowania wybierz określony test. Strona ta będzie zawierać określone wyniki, można ją również edytować lub wyłączać.

![Edytowanie lub wyłączanie testu sieci Web](./media/app-insights-monitor-web-app-availability/19-availEdit-3.png)

Podczas przeprowadzania konserwacji swojej usługi możesz wyłączyć testy dostępności lub skojarzone z nimi reguły alertów. 

## <a name="failures"></a>Jeśli widzisz błędy
Kliknij czerwoną kropkę.

![Kliknij czerwoną kropkę](./media/app-insights-monitor-web-app-availability/open-instance-3.png)

W wyniku testu dostępności zobaczysz szczegółów transakcji dotyczące wszystkich składników. W tym miejscu możesz wykonywać następujące czynności:

* Zbadać odpowiedź odebraną z serwera.
* Diagnozowanie błędów przy użyciu danych telemetrycznych po stronie serwera skorelowany zebranych podczas przetwarzania testu dostępności nie powiodło się.
* Zarejestrować problem lub element roboczy w usłudze Git bądź VSTS w celu prześledzenia problemu. Błąd będzie zawierać link do tego zdarzenia.
* Otworzyć wynik testu sieci Web w programie Visual Studio.

Dowiedz się więcej o diagnostyce transakcji typu end to end środowisko [tutaj](app-insights-transaction-diagnostics.md).

Kliknij wiersz wyjątek, aby wyświetlić szczegóły dotyczące wyjątków po stronie serwera, który spowodował niepowodzenie testu dostępności syntetycznych. Możesz też pobrać [migawkę debugowania](app-insights-snapshot-debugger.md) dla bogatszych diagnostyki z poziomu kodu.

![Diagnostyczne po stronie serwera](./media/app-insights-monitor-web-app-availability/open-instance-4.png)

## <a name="alerts"></a> Alerty dostępności
Może mieć następujące typy reguł alertów na danych dotyczących dostępności za pomocą środowiska klasycznych alertów:
1. X z Y lokalizacji, raportowania awarii w przedziale czasu
2. Dostępność agregacji wartości procentowej spadnie poniżej wartości progowej
3. Test Średni czas trwania zwiększa powyżej wartości progowej

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alert po wystąpieniu X z Y lokalizacji, raportowanie błędów
X z Y lokalizacji, reguła alertu jest domyślnie włączone w [nowe alerty ujednolicone środowisko](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), podczas tworzenia nowego testu dostępności. Użytkownik może zrezygnować przez wybranie opcji "klasyczny" lub wyłączenie reguły alertu.

![Utwórz środowisko](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

**Ważne**: za pomocą [nowe alerty ujednoliconego](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), preferencje ważność i powiadomień regułę alertu z [grup akcji](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **musi być** skonfigurowane w środowisko alertów. Bez wykonać następujące kroki subskrybent otrzyma tylko powiadomienia w portalu. 

1. Po zapisaniu test dostępności, kliknij nową nazwę testu, aby przejść do jego szczegóły. Kliknij pozycję "Edytuj alert" ![edytować po zapisaniu](./media/app-insights-monitor-web-app-availability/editaftersave.png)

2. Ustaw poziom ważności, opis reguły, a co najważniejsze — grupę akcji, która ma preferencje powiadamiania, którego chcesz użyć dla tej reguły alertu.
![Edytuj po zapisaniu](./media/app-insights-monitor-web-app-availability/setactiongroup.png)


> [!NOTE]
> * Konfigurowanie grup akcji, aby otrzymywać powiadomienia po wyzwoleniu alertu, wykonując kroki opisane powyżej. Bez tego kroku subskrybent otrzyma tylko powiadomienia w portalu po wyzwoleniu reguły.
>

### <a name="alert-on-availability-metrics"></a>Alert po wystąpieniu metryki dostępności
Za pomocą [nowe alerty ujednoliconego](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), można alert po wystąpieniu segmentowanych dostępności agregacji i testowania oraz metryki czasu trwania:

1. Wybierz zasób usługi Application Insights w środowisku metryki, a następnie wybierz metrykę dostępności: ![wybór metryki dostępności](./media/app-insights-monitor-web-app-availability/selectmetric.png)

2. Konfigurowanie alertów, którą opcję z menu spowoduje przejście do nowego środowiska którym można wybrać określonych testów lub lokalizacji, aby skonfigurować reguły alertu dotyczącego. Można również skonfigurować grupy akcji dla tej reguły alertu, w tym miejscu.
    ![Konfiguracja alertów dostępności](./media/app-insights-monitor-web-app-availability/availabilitymetricalert.png)

### <a name="alert-on-custom-analytics-queries"></a>Alert po wystąpieniu zapytań analitycznych niestandardowe
Za pomocą [nowe alerty ujednoliconego](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), może generować alerty na [dzienników niestandardowych kwerend](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Za pomocą niestandardowych zapytań może generować alerty na dowolny warunek, który pomoże Ci w uzyskaniu najbardziej niezawodny sygnał problemy z dostępnością. Dotyczy to również szczególnie ma to zastosowanie, w przypadku wysyłania wyniki dostępności niestandardowych przy użyciu zestawu SDK TrackAvailability. 

> [!Tip]
> * Metryki dotyczące dostępności danych obejmują żadnych wyników dostępności niestandardowych, które mogą przesyłać przez wywołanie metody nasz zestaw SDK TrackAvailability. Możesz użyć alertów dotyczących obsługi różnych metryk w ramach alertu wyniki dostępności niestandardowych.
>

## <a name="dealing-with-sign-in"></a>Obsługa logowania
Jeśli użytkownicy logują się do aplikacji, dostępne są różne opcje symulowania logowania, które pozwolą przetestować strony dostępne po zalogowaniu. Zastosowane podejście zależy od typu zabezpieczeń zapewnianych przez aplikację.

We wszystkich przypadkach należy utworzyć konto w ramach aplikacji tylko na potrzeby testowania. Jeśli to możliwe, należy ograniczyć uprawnienia tego konta testowego, aby nie było możliwości, że testy sieci Web będą miały wpływ na rzeczywistych użytkowników.

### <a name="simple-username-and-password"></a>Prosta nazwa użytkownika i hasło
Rejestrowanie testu sieci Web w zwykły sposób. Najpierw usuń pliki cookie.

### <a name="saml-authentication"></a>Uwierzytelnianie SAML
Użyj wtyczki SAML, która jest dostępna do testów sieci Web.

### <a name="client-secret"></a>Klucz tajny klienta
Jeśli aplikacja ma trasę logowania, która obejmuje klucz tajny klienta, użyj tej trasy. Azure Active Directory (AAD) to przykład usługi, która umożliwia logowanie za pomocą klucza tajnego klienta. W usłudze AAD klucz tajny klienta jest kluczem aplikacji.

Poniżej przedstawiono przykładowy test internetowy aplikacji internetowej platformy Azure przy użyciu klucza aplikacji:

![Przykład klucza tajnego klienta](./media/app-insights-monitor-web-app-availability/110.png)

1. Pobierz token z usługi AAD przy użyciu klucza tajnego klienta (AppKey).
2. Wyodrębnij token elementu nośnego z odpowiedzi.
3. Wywołaj interfejs API przy użyciu tokenu elementu nośnego w nagłówku autoryzacji.

Upewnij się, że test sieci Web jest rzeczywistym klientem, to znaczy ma własną aplikację w usłudze AAD, i użyj jego identyfikatora klienta i klucza aplikacji. Testowana usługa również ma własną aplikację w usłudze AAD: identyfikator URI identyfikatora tej aplikacji jest uwzględniany w teście sieci Web w polu „resource”.

### <a name="open-authentication"></a>Uwierzytelnianie otwarte
Przykładem uwierzytelniania otwartego jest logowanie przy użyciu konta Microsoft lub Google. Wiele aplikacji, które używają protokołu OAuth, zapewnia alternatywną obsługę klucza tajnego klienta, więc przede wszystkim należy zbadać tę możliwość.

Jeśli w ramach testu należy zalogować się przy użyciu protokołu OAuth, ogólne podejście jest następujące:

* Użyj narzędzia takiego jak Fiddler, aby sprawdzić ruch między przeglądarką sieci Web, witryną uwierzytelniającą a aplikacją.
* Wykonaj co najmniej dwa logowania na różnych komputerach lub w różnych przeglądarkach albo w długich odstępach czasu (umożliwi to wygaśnięcie tokenów).
* Porównując różne sesje, zidentyfikuj token przekazywany z powrotem z witryny uwierzytelniającej, który jest następnie przekazywany do serwera aplikacji po zalogowaniu.
* Nagraj test sieci Web przy użyciu programu Visual Studio.
* Sparametryzuj tokeny, ustawiając parametr, gdy token jest zwracany z witryny uwierzytelniającej i używając go w zapytaniu do tej witryny.
  Program Visual Studio podejmie próby parametryzacji testu, ale parametryzacja tokenów nie przebiegnie poprawnie.

## <a name="performance-tests"></a>Testy wydajności
Witrynę internetową możesz poddać testowi obciążeniowemu. Podobnie jak w przypadku testu dostępności można wysłać proste żądania lub żądania wieloetapowe z naszych punktów na całym świecie. W przeciwieństwie do testu dostępności wysyłanych jest wiele żądań symulujących wielu równoczesnych użytkowników.

Z poziomu bloku Przegląd otwórz pozycję **Ustawienia**, **Testy wydajności**. Podczas tworzenia testu zaproszono Cię do łączenia lub utworzyć konto usługi DevOps platformy Azure.

Po zakończeniu testu wyświetlane są czasy reakcji i współczynniki powodzenia.


![Test wydajnościowy](./media/app-insights-monitor-web-app-availability/perf-test.png)

> [!TIP]
> Aby obserwować wyniki testów wydajnościowych, należy użyć [transmisji strumieniowej na żywo](app-insights-live-stream.md) i [profilera](app-insights-profiler.md).
>

## <a name="automation"></a>Automatyzacja
* Automatyczne [konfigurowanie testów dostępności za pomocą skryptów środowiska PowerShell](app-insights-powershell.md#add-an-availability-test).
* Konfigurowanie [elementu webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) który jest wywoływany przy zgłaszaniu alertu.

## <a name="qna"></a> FAQ

* *Witryna wygląda prawidłowo, ale występują niepowodzenia testów? Dlaczego usługa Application Insights jest ze mną alertów?*

    * Test ma "Analizy zależne requests" włączone? Który skutkuje rygorystyczne kontrole zasoby, takie jak skrypty, obrazy itd. Tego rodzaju błędów może nie być widoczne w przeglądarce. Sprawdź wszystkie obrazy, skrypty, arkusze stylów i inne pliki ładowane przez stronę. Jeśli pobranie dowolnego z nich nie powiedzie się, test zostanie zgłoszony jako nieudany — nawet wtedy, gdy główna strona HTML ładuje się poprawnie. Aby test ignorował takie błędy zasobów, wystarczy usunąć zaznaczenie pola „Analizuj zależne żądania” w konfiguracji testu. 

    * Aby zmniejszyć ryzyko wystąpienia szumu powodowanego przez drobne przejściowe problemy z siecią itp., zaznacz pole „Włącz ponawianie próby w przypadku niepowodzenia testów” w konfiguracji. Możesz także przeprowadzać testy z większej liczby lokalizacji i odpowiednio dostosować próg reguły alertu, aby zapobiec wywoływaniu niepotrzebnych alertów przez problemy występujące w jednej lokalizacji.

    * Kliknij dowolny czerwonych kropek doświadczeniu dostępności lub jakiekolwiek niepowodzenie dostępności Eksploratora wyszukiwania, aby wyświetlić szczegóły Dlaczego mamy zgłosił błąd. Wynik testu, wraz z skorelowanej telemetrii po stronie serwera (jeśli jest włączona) powinno pomóc zrozumieć, dlaczego test nie powiódł się. Typowe przyczyny problemy przejściowe problemy z połączeniem sieciowym lub. 

    * Czy limit czasu testu? Firma Microsoft przerwać testów po 2 minuty. Jeśli Twoje polecenie ping lub test wieloetapowy trwa dłużej niż 2 minuty, firma Microsoft będzie zgłaszać jako błąd. Rozważ podzielenie testu na wiele migawek, które można wykonać w krótszych czasów trwania.

    * Wszystkie lokalizacje zgłosili awarii lub tylko niektóre z nich? Jeśli tylko niektóre zgłoszone błędy, może to być spowodowane problemy sieciowe/usługa CDN. Ponownie klikając czerwonych kropek powinna pomagać zrozumieć, dlaczego lokalizacji zgłoszone błędy.

* *Nie pobrały I wiadomości e-mail, gdy alertu wyzwolona lub rozwiązane lub oba?*

    Sprawdź konfigurację alertów klasycznych, aby potwierdzić swój adres e-mail znajduje się bezpośrednio lub listy dystrybucyjnej, które znajdują się na jest skonfigurowany do otrzymywać powiadomienia. Jeśli tak jest, sprawdź konfigurację listy dystrybucji, aby potwierdzić, że może ona odbierać wiadomości e-mail zewnętrznych. Również szybko sprawdzić, jeśli administrator poczty może mieć żadnych zasady skonfigurowane, które mogą być przyczyną tego problemu.

* *Nie odebrał powiadomienie elementów webhook?*

    Sprawdź aplikację odbieranie powiadomień elementu webhook jest dostępny i pomyślnie przetwarza żądania elementu webhook. Zobacz [to](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) Aby uzyskać więcej informacji.

* *Sporadyczne niepowodzenia testu z błędem naruszenia protokołu*

    Błąd „Naruszenie protokołu (...) Po CR musi występować LF” oznacza problem związany z serwerem (lub zależnościami). Występuje w przypadku ustawienia nieprawidłowo sformułowanych nagłówków w odpowiedzi. Przyczyną mogą być moduły równoważenia obciążenia lub sieci dostarczania zawartości. Mówiąc bardziej szczegółowo, w niektórych nagłówkach koniec wiersza może nie być sygnalizowany znakiem CRLF, co narusza specyfikację protokołu HTTP i prowadzi do niepowodzenia walidacji na poziomie żądania internetowego .NET. Sprawdź odpowiedź, aby znaleźć nagłówki, które mogą powodować naruszenie.
    
    Uwaga: błąd adresu URL może nie występować w przeglądarkach mających bardziej swobodne reguły walidacji nagłówków HTTP. Zobacz ten wpis w blogu, aby uzyskać szczegółowe wyjaśnienie tego problemu: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  
    
* *Nie widzę żadnych danych telemetrycznych po stronie serwera do diagnozowania niepowodzeń testów*
    
    Jeśli usługa Application Insights została skonfigurowana dla aplikacji po stronie serwera, może to być spowodowane trwaniem [próbkowania](app-insights-sampling.md). Wybierz wynik w różnych dostępności.

* *Czy mogę wywołać kod z mojego testu sieci Web?*

    Nie. Kroki testu muszą być zawarte w pliku .webtest. Nie można też wywoływać innych testów sieci Web ani używać pętli. Istnieje jednak kilka wtyczek, które mogą być przydatne.

* *Czy jest obsługiwany protokół HTTPS?*

    Obsługujemy protokół TLS 1.1 i TLS 1.2.
* *Czym różnią się „testy sieci Web” i „testy dostępności”?*

    Te dwa terminy mogą być używane zamiennie. Testy dostępności to bardziej ogólny termin, który, oprócz wieloetapowych testów witryny, obejmuje testy ping pojedynczego adresu URL.
    
* *Chcę użyć testów dostępności na naszym serwerze wewnętrznym działającym za zaporą.*

    Istnieją dwa możliwe rozwiązania:
    
    * Skonfiguruj zaporę, aby zezwolić na żądania przychodzące z [adresów IP naszych agentów testów sieci Web](app-insights-ip-addresses.md).
    * Napisz własny kod do okresowego testowania wewnętrznego serwera. Uruchom kod jako proces w tle na serwerze testowym za zaporą. Proces testowania może wysyłać wyniki do usługi Application Insights za pomocą interfejsu API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) w podstawowym zestawie SDK. Wymaga to, aby serwer testowy miał dostęp do połączeń wychodzących punktu końcowego pozyskiwania usługi Application Insights, ale stanowi to dużo mniejsze zagrożenie bezpieczeństwa niż alternatywne dopuszczenie żądań przychodzących. Wyniki nie będą widoczne w blokach testów dostępności sieci Web, ale są wyświetlane jako wyniki dostępności w obszarach analizy, wyszukiwania i eksploratora metryk.

* *Przekazywanie wieloetapowego testu sieci Web kończy się niepowodzeniem*

    Niektóre przyczyny może się to zdarzyć:
    * Limit rozmiaru to 300 KB.
    * Pętle nie są obsługiwane.
    * Odwołania do innych testów sieci Web nie są obsługiwane.
    * Źródła danych nie są obsługiwane.

* *Mój test wieloetapowy nie jest wykonywany w całości*

    Istnieje limit 100 żądań na test. Ponadto test zostanie zatrzymany, jeśli działa dłużej niż dwie minuty.

* *Jak uruchomić test z wykorzystaniem certyfikatów klienta?*

    Niestety nie jest to obsługiwane.


## <a name="next"></a>Następne kroki
[Dzienniki diagnostyczne usługi Search][diagnostic]

[Rozwiązywanie problemów][qna]

[Adresy IP agentów testów sieci Web](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
