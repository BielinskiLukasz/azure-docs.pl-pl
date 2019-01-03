---
title: Często zadawane pytania
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania na temat Language Understanding (LUIS).
author: diberry
manager: cgronlun
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 9a8bfa2e89e6bc0cbbd5af2efdff60aa406b3f1d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714206"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding — często zadawane pytania (FAQ)

Ten artykuł zawiera odpowiedzi na często zadawane pytania na temat Language Understanding (LUIS).

## <a name="luis-authoring"></a>Tworzenie usługi LUIS

### <a name="what-are-the-luis-best-practices"></a>Jakie są najlepsze rozwiązania usługi LUIS?
Rozpoczynać [cyklu tworzenia](luis-concept-app-iteration.md), następnie zapoznaj się z [najlepsze praktyki](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Co to jest najlepszym sposobem, aby rozpocząć tworzenie Moja aplikacja w LUIS?

Najlepszym sposobem tworzenia aplikacji jest za pośrednictwem [przyrostowego procesu](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co to jest dobrą praktyką jest model intencji mojej aplikacji? Należy utworzyć bardziej szczegółowe lub bardziej ogólnymi opcjami?

Wybierz intencji, które nie są tak ogólne, by były nakładające się, ale nie więc odnoszą się że go utrudnia LUIS rozróżnić podobne intencji. Tworzenie discriminative intencji określonych jest jednym z najlepszych rozwiązań dotyczących usługi LUIS modelowania.

### <a name="is-it-important-to-train-the-none-intent"></a>Ważne jest to w opracowywaniu intencji Brak?

Tak, jest dobre do nauczenia usługi **Brak** intencji za pomocą więcej wypowiedzi w miarę dodawania większej liczby etykiet do innej metody. Dobry wskaźnik jest 1 lub 2 etykiety dodawane do **Brak** co 10 etykiet dodane do intencji. Ta proporcja zwiększa możliwości discriminative usługi LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak można poprawić błędy pisowni w wypowiedzi

Zobacz [Bing pisowni Sprawdź interfejsu API w wersji 7](luis-tutorial-bing-spellcheck.md) samouczka. Usługa LUIS wymusza ograniczeń narzuconych przez Bing pisowni Sprawdź interfejsu API w wersji 7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak programowo edytować aplikację usługi LUIS?
Aby edytować aplikacją usługi LUIS programowo, należy użyć [tworzenia interfejsu API](https://aka.ms/luis-authoring-apis). Zobacz [wywołania tworzenia interfejsu API usługi LUIS](./luis-quickstart-node-add-utterance.md) i [tworzenie aplikacji usługi LUIS programowo przy użyciu środowiska Node.js](./luis-tutorial-node-import-utterances-csv.md) przykładów dotyczących sposobów wywołać interfejs API tworzenia. Tworzenie interfejsu API wymaga użycia [tworzenia klucza](luis-concept-keys.md#authoring-key) zamiast klucza punktu końcowego. Programowe tworzenie pozwala maksymalnie 1 000 000 wywołań na miesiąc i 5 transakcji na sekundę. Aby uzyskać więcej informacji na kluczach, korzystać z użyciem usługi LUIS, zobacz [zarządzanie kluczami](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Gdy funkcja wzorca, który podane wyrażenie regularne jest zgodny
Poprzedni **funkcja wzorca** jest obecnie przestarzałe, zastępuje  **[wzorców](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak używać jednostki, aby wysunąć poprawnych danych?
Zobacz [jednostek](luis-concept-entity-types.md) i [wyodrębnianie danych](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Odmiany wypowiedź przykładu, powinien zawierać znak interpunkcyjny?
Dodaj różne odmiany wypowiedzi przykład z intencją albo Dodaj wzorzec wypowiedź przykład za pomocą [składni, aby zignorować](luis-concept-patterns.md#pattern-syntax) znak interpunkcyjny.

### <a name="does-luis-currently-support-cortana"></a>Czy Cortana jest aktualnie obsługuje usługi LUIS?

Cortana, który wstępnie skompilowanych aplikacji zostały zaniechane w 2017 r. Nie są obsługiwane.

## <a name="luis-endpoint"></a>Punkt końcowy usługi LUIS

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Moje zapytanie punkt końcowy zwracany nieoczekiwane wyniki. Co mam zrobić?

Wyniki przewidywań nieoczekiwane zapytanie są oparte na stanie opublikowanego modelu. Aby naprawić model, możesz muszą się konieczna zmiana modelu, szkolenie i opublikuj go ponownie. 

Poprawianie modelu zaczyna się od [aktywne uczenie](luis-how-to-review-endoint-utt.md).

Możesz usunąć szkolenia deterministyczna, aktualizując [aplikacji interfejsu API z ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) aby można było używać wszystkich danych szkoleniowych. 

Przegląd [najlepsze praktyki](luis-concept-best-practices.md) inne porady. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Dlaczego usługa LUIS powoduje dodania miejsca do magazynowania do wykonywania zapytań w całym lub w środku słów?
Usługa LUIS [tokenizes](luis-glossary.md#token) na podstawie wypowiedź [kultury](luis-language-support.md#tokenization). Oryginalna wartość i tokenami wartości są dostępne dla [wyodrębnianie danych](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak utworzyć i przypisać klucza punktu końcowego usługi LUIS?
[Tworzenie klucza punktu końcowego](luis-how-to-azure-subscription.md#create-luis-endpoint-key) na platformie Azure Twoja [usługi](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) poziom. [Przypisany klawisz](luis-how-to-manage-keys.md#assign-endpoint-key) na **[kluczy i punktów końcowych](luis-how-to-manage-keys.md)** strony. Nie ma żadnych odpowiednich interfejsów API dla tej akcji. Następnie należy zmienić żądania HTTP do punktu końcowego do [użycia nowego klucza punktu końcowego](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Jak interpretować wyniki usługi LUIS?
System powinien używać najwyższy intencji oceniania niezależnie od jego wartość. Na przykład współczynnik poniżej 0,5 (mniej niż 50%) niekoniecznie oznacza, że usługa LUIS ma niski zaufania. Materiały szkoleniowe dotyczące usługi więcej danych może pomóc zwiększyć [wynik](luis-concept-prediction-score.md) intencji najbardziej prawdopodobną.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Dlaczego nie widzę mojego trafienia punktu końcowego na pulpicie nawigacyjnym mojej aplikacji?
Trafienia punktu końcowego całkowita na pulpicie nawigacyjnym aplikacji są okresowo aktualizowane, ale metryki skojarzone z klucza punktu końcowego usługi LUIS w witrynie Azure portal są aktualizowane częściej.

Jeśli nie widzisz trafień zaktualizowano punkt końcowy na pulpicie nawigacyjnym, zaloguj się do witryny Azure portal i Znajdź zasób skojarzony z klucza punktu końcowego usługi LUIS, a następnie otwórz **metryki** wybrać **łączna liczba wywołań** metryki. Jeśli klucza punktu końcowego jest używana do więcej niż jedną aplikacją usługi LUIS, metryki w witrynie Azure portal pokazuje łączna liczba wywołań ze wszystkich aplikacji usługi LUIS, które go używają.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moja aplikacja usługi LUIS była praca wczoraj, ale już dziś otrzymuję błędy 403. Nie można zmodyfikować aplikację tak. Jak go naprawić?
Następujące [instrukcje](#how-do-i-create-and-assign-a-luis-endpoint-key) w następnym — często zadawane pytania do tworzenia klucza punktu końcowego usługi LUIS i przypisać je do aplikacji. Następnie należy zmienić żądania HTTP do punktu końcowego do [użycia nowego klucza punktu końcowego](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak zabezpieczyć Mój punkt końcowy usługi LUIS?
Zobacz [zabezpieczenia punktu końcowego](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Praca w granicach usługi LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Co to jest maksymalna liczba intencje i podmioty, obsługujące aplikacją usługi LUIS?
Zobacz [granice](luis-boundaries.md) odwołania.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chcę, aby utworzyć aplikację usługi LUIS z więcej niż maksymalna liczba intencji. Co mam zrobić?

Zobacz [najlepsze rozwiązania dotyczące intencji](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chcę tworzenie aplikacji na platformie usługi LUIS z więcej niż maksymalna liczba jednostek. Co mam zrobić?

Zobacz [najlepsze rozwiązania dotyczące jednostek](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jakie są ograniczenia na liczbę i rozmiar frazy Wyświetla?
Na maksymalną długość [listy fraz](./luis-concept-feature.md), zobacz [granice](luis-boundaries.md) odwołania.

### <a name="what-are-the-limits-on-example-utterances"></a>Jakie są limity, na przykład wypowiedzi?
Zobacz [granice](luis-boundaries.md) odwołania.

## <a name="testing-and-training"></a>Testowania i szkolenia

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Czy mogę zobaczyć kilka błędów w usłudze batch testowania okienko w przypadku niektórych modeli w mojej aplikacji. Jak można rozwiązać ten problem?

Błędy oznacza, że pewne rozbieżności między etykiet i przewidywań modeli. Aby rozwiązać ten problem, wykonaj jedną lub obie z następujących zadań:
* Aby pomóc ulepszyć dyskryminacji intencje usługi LUIS, Dodaj więcej etykiety.
* Aby ułatwić LUIS uczysz się szybciej, Dodaj funkcje Lista fraz, które wprowadzają słownictwa specyficznego dla domeny.

Zobacz [testowania partii](luis-tutorial-batch-testing.md) samouczka.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Gdy aplikacja jest wyeksportowane, a następnie importowane z powrotem do nowej aplikacji (z nowym Identyfikatorem aplikacji), usługa LUIS wyniki prognozy różnią się. Dlaczego tak się dzieje?

Zobacz [prognozowania różnice między kopie tej samej aplikacji](luis-concept-prediction-score.md#differences-with-predictions).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Niektóre wypowiedzi przejdź do niewłaściwej intencji po zmiany wprowadzone do mojej aplikacji. Podejrzenie zniknięcie losowo. Jak go naprawić? 

Zobacz [szkolenie z wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publikowanie aplikacji

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Co to jest identyfikator dzierżawy w oknie "Dodaj klucz do aplikacji"?
Na platformie Azure dzierżawca reprezentuje klienta lub organizację, który jest skojarzony z usługą. Znajdź swój identyfikator dzierżawy w witrynie Azure portal w **identyfikator katalogu** pola, wybierając **usługi Azure Active Directory** > **Zarządzaj**  >  **Właściwości**.

![Identyfikator dzierżawy w witrynie Azure portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Dlaczego są więcej kluczy punktu końcowego przypisane do mojej aplikacji, niż przypisane
Każda aplikacja usługi LUIS ma klucz tworzenia starter liście punktów końcowych dla wygody. Ten klucz umożliwia tylko kilka trafienia punktu końcowego, dzięki czemu możesz wypróbować usługi LUIS.  

Jeśli aplikacja istniały przed LUIS jest ogólnie dostępna (GA), klucze punktu końcowego usługi LUIS w ramach subskrypcji są przypisywane automatycznie. Stało się ułatwić migrację wersji ogólnie dostępnej. Nowych kluczy punktu końcowego usługi LUIS w witrynie Azure portal są _nie_ automatycznie przypisywana do usługi LUIS.

## <a name="app-management"></a>Zarządzanie aplikacjami

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak przenieść własność aplikacją usługi LUIS?
Aby przetransferować aplikacją usługi LUIS do innej subskrypcji platformy Azure, Eksportuj aplikacji LUIS, a następnie importować go za pomocą nowego konta. Zaktualizuj identyfikator aplikacji usługi LUIS w aplikacji klienta, który ją wywołuje. Nowa aplikacja może zwrócić LUIS nieco inne wyniki z oryginalnej aplikacji.

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak pobrać dziennik wypowiedzi użytkowników?
Domyślnie aplikacją usługi LUIS rejestruje wypowiedzi użytkowników. Aby pobrać dziennik wypowiedzi użytkowników, wysyłać aplikacją usługi LUIS, przejdź do **Moje aplikacje**i wybierz aplikację. Na pasku narzędzi kontekstowych, wybierz **Eksportuj dzienniki punktu końcowego**. Dziennik jest formatowana jako plik wartości rozdzielanych przecinkami (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak wyłączyć rejestrowanie wypowiedzi
Można wyłączyć rejestrowanie wypowiedzi użytkowników, ustawiając `log=false` w adresie URL punktu końcowego, który aplikacja kliencka używa zapytania usługi LUIS. Jednak wyłączenie rejestrowania wyłącza możliwość aplikacją usługi LUIS zasugerować wypowiedzi lub zwiększyć wydajność, który jest oparty na [aktywne uczenie](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Jeśli ustawisz `log=false` ze względu na kwestie prywatności danych, nie można pobrać rekordu wyrażenia o tych użytkowników z usługi LUIS ani używać tych wypowiedzi ulepszenie aplikacji.

Rejestrowanie jest tylko magazyn wypowiedzi.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Dlaczego nie chcesz Moje wypowiedzi punktu końcowego rejestrowane?
Jeśli używasz dziennik analizy prognozowania, nie należy przechwytywać wypowiedzi testu w dzienniku.

## <a name="data-management"></a>Zarządzanie danymi

### <a name="can-i-delete-data-from-luis"></a>Czy można usunąć dane, z usługi LUIS?

* Zawsze możesz usunąć przykład wypowiedzi umożliwiający szkolenie usługi LUIS. Jeśli usuniesz wypowiedź przykład z aplikacją usługi LUIS, zostanie usunięty z usługi sieci web usługi LUIS i jest niedostępna w przypadku eksportu.
* Wypowiedzi można usunąć z listy wypowiedzi użytkowników usługi LUIS sugeruje w **Przejrzyj wypowiedzi punktu końcowego** strony. Usuwanie wypowiedzi z tej listy zapobiega sugerowane, ale nie powoduje usunięcia ich z dzienników.
* Jeśli usuniesz konto, wszystkie aplikacje zostaną usunięte wraz z ich wypowiedzi przykład i dzienniki. Dane są przechowywane na serwerach przez 60 dni, zanim zostanie trwale usunięty.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Jak Microsoft zarządzać danych wysyłanych do usługi LUIS?

[Centrum zaufania](https://www.microsoft.com/trustcenter) opisano nasze zobowiązania i opcje zarządzania danymi i dostępu w usługach Azure.

## <a name="language-and-translation-support"></a>Obsługa języka i tłumaczenia

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mam aplikację w jednym języku i chcę korzystać do tworzenia aplikacji równoległych w innym języku. Co to jest najprostszym sposobem, aby to zrobić?
1. Eksportowanie aplikacji.
2. Wykonuje translację elementu etykietami wypowiedzi w pliku JSON Wyeksportowano aplikację na język docelowy.
3. Może być konieczne zmiany nazw intencje i podmioty, lub pozostaw je, ponieważ są one.
4. Na koniec zaimportować aplikację, aby mieć aplikacją usługi LUIS w języku docelowym.

## <a name="app-notification"></a>Powiadomienie w aplikacji

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Dlaczego otrzymuję wiadomość e-mail z informacją, że jestem prawie poza limit przydziału?
Klucz tworzenia starter jest dozwolona tylko 1000 punkt końcowy zapytań przez miesiąc. Tworzenie klucza punktu końcowego usługi LUIS (bezpłatna lub płatna) i użyć tego klucza, podczas tworzenia kwerend punktu końcowego. Jeśli wykonujesz zapytania punktu końcowego z robota lub innej aplikacji, musisz zmienić istnieje klucza punktu końcowego usługi LUIS.

## <a name="integrating-luis"></a>Integrowanie usługi LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Gdzie Moja aplikacja usługi LUIS jest tworzony podczas procesu subskrypcji bot aplikacji sieci web platformy Azure?
Wybierz szablon usługi LUIS i zaznaczenie **wybierz** przycisku w okienku szablonów, w okienku po lewej stronie zmiany obejmują typ szablonu oraz pytanie, w jakim regionie, aby utworzyć szablon usługi LUIS. Proces bot aplikacji sieci web nie tworzy jednak subskrypcji usługi LUIS.

![Regionu bot aplikacji sieci web szablonu usługi LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jakie regiony usługi LUIS obsługuje zalewanie mowy platformy Bot Framework?
[Zalewanie mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) jest obsługiwana tylko dla aplikacji usługi LUIS w wystąpieniu centralny (USA).

## <a name="luis-service"></a>Usługa LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUIS) dostępne lokalnie lub w chmurze prywatnej?

Tak, można użyć usługa LUIS [kontenera](luis-container-howto.md) dla tych scenariuszy, jeśli masz niezbędne łączności do mierzenia użycia. 

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Na konferencji Build 2018 słyszałem o funkcji rozpoznawania języka lub pokaz, ale nie pamiętam, co zostało wywołane?

Następujące funkcje zostały wydane na konferencji Build 2018 r.:

|Name (Nazwa)|Zawartość|
|--|--|
|Ulepszenia|[Wyrażenie regularne](luis-concept-data-extraction.md##regular-expression-entity-data) jednostki i [frazy klucz](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) jednostki
|Wzorce|Wzorce [koncepcji](luis-concept-patterns.md), [samouczek](luis-tutorial-pattern.md), [porad](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) jednostki w tym koncepcji [jawną listę](luis-concept-patterns.md#explicit-lists) dla wyjątków<br>[Role](luis-concept-roles.md) pojęcia|
|Integracje|[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integracji [analizy tonacji](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>[Mowy](https://docs.microsoft.com/azure/cognitive-services/speech) integracji zalewanie mowy w połączeniu z [zestaw SDK rozpoznawania mowy](https://aka.ms/SpeechSDK)|
|Narzędzia wysyłki|Część [narzędzia BotBuilder](https://github.com/Microsoft/botbuilder-tools), wiersza polecenia wysyłania [narzędzie](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) Aby połączyć aplikacje dla wielu LUIS i usługi QnA Maker w pojedynczą aplikacją usługi LUIS do lepszego rozpoznawanie intencji na robota

Tworzenie dodatkowych [trasy interfejsu API](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/authoring-routes.md) zostały uwzględnione.

Wideo:
* [Azure Friday konferencji Build 2018: Cognitive Services — języka (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Pokaż sztucznej Inteligencji 2018 kompilacji — nowości pojawiły się z usługą interpretacji języka](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Sesja konferencji Build 2018 — inteligencja botów, możliwości rozpoznawania mowy i najlepsze rozwiązania dotyczące NLU](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - aktualizacje usługi LUIS](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projekty:
* [Bot contoso Cafe](https://github.com/botbuilderbuild2018/build2018demo) demo - kod źródłowy w serwisie GitHub

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi LUIS, zobacz następujące zasoby:
* [Pytania dotyczące przepełnienia stosu oznakowane za pomocą usługi LUIS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding Intelligent Services (LUIS) Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
