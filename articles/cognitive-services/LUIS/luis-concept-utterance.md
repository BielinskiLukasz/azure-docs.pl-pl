---
title: Dobry przykład wyrażenia długości-LUIS
titleSuffix: Azure Cognitive Services
description: Wyrażenia długości są danymi wejściowymi od użytkownika, który musi być interpretowany przez aplikację. Zbierz frazy, które będą wprowadzane przez użytkowników. Uwzględnij wyrażenia długości, które oznaczają te same czynności, ale są konstruowane inaczej niż długość słowa i umieszczanie wyrazów.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219919"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Dowiedz się, co to jest dobry wyrażenia długości dla aplikacji LUIS

**Wyrażenia długości** są danymi wejściowymi od użytkownika, który musi być interpretowany przez aplikację. Aby przeprowadzić uczenie LUIS w celu wyodrębnienia z nich intencji i jednostek, ważne jest, aby przechwycić wiele różnych przykładowych wyrażenia długości dla każdego zamiaru. Proaktywne uczenie lub proces ciągłego uczenia się z nowymi wyrażenia długościami jest niezwykle istotny dla analizy maszynowej, która zapewnia LUIS.

Zbieraj wyrażenia długości, że użytkownicy będą wprowadzać. Uwzględnij wyrażenia długości, co oznacza, że są one takie same, ale są zbudowane na różne sposoby:

* Wypowiedź długość — krótko, średnia i długa dla aplikacji klienckiej
* Długość wyrazu i frazy 
* Położenie wyrazów — jednostka na początku, na środku i na końcu wypowiedź
* Poprawności 
* Pluralizacja
* Uszkodzeniem
* Wybór rzeczowników i czasowników
* Interpunkcja — dobra odmiana przy użyciu poprawnych, niepoprawnych i bez gramatyki

## <a name="how-to-choose-varied-utterances"></a>Jak wybrać różne wyrażenia długości

Po pierwszym uruchomieniu dodając [przykład wyrażenia długości](luis-how-to-add-example-utterances.md) do modelu Luis, należy wziąć pod uwagę pewne zasady.

### <a name="utterances-arent-always-well-formed"></a>Wyrażenia długości nie zawsze są poprawnie sformułowane

Może to być zdanie, na przykład "książka biletowa do Paryż dla mnie" lub fragment zdania, taki jak "rezerwacja" lub "lot paryski".  Użytkownicy często sprawiają błędy pisowni. Podczas planowania aplikacji należy rozważyć, czy przed przekazaniem jej do LUIS należy używać [Sprawdzanie pisowni Bing](luis-tutorial-bing-spellcheck.md) do poprawienia danych wejściowych użytkownika. 

Jeśli nie sprawdzisz pisowni wyrażenia długości użytkownika, należy poszkolić LUIS na wyrażenia długości, które zawierają literówki i błędy pisowni.

### <a name="use-the-representative-language-of-the-user"></a>Korzystanie z języka reprezentatywnego dla użytkownika

Podczas wybierania wyrażenia długości należy pamiętać, że to, co myślisz, jest powszechny termin lub fraza może być niepoprawna dla typowego użytkownika aplikacji klienckiej. Mogą nie mieć środowiska domeny. Należy zachować ostrożność w przypadku używania warunków lub fraz, które użytkownik może powiedzieć tylko, jeśli byli ekspertami.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Wybierz zróżnicowaną terminologię, a także sformułowanie

Zobaczysz, że nawet jeśli podejmujesz wysiłki w celu utworzenia różnych wzorców zdań, nadal powtarzamy niektóre słownictwo.

Wykonaj następujące przykładowe wyrażenia długości:

|Przykładowe wypowiedzi|
|--|
|how do I get a computer?|
|Where do I get a computer?|
|I want to get a computer, how do I go about it?|
|When can I have a computer?| 

W tym miejscu termin "Computer" nie jest zróżnicowany. Używaj rozwiązań alternatywnych, takich jak komputer stacjonarny, laptop, stacja robocza, lub nawet dla komputera. LUIS może inteligentnie wywnioskować synonimy z kontekstu, ale w przypadku tworzenia wyrażenia długości do szkolenia są one zawsze lepsze od siebie.

## <a name="example-utterances-in-each-intent"></a>Przykład wyrażenia długości w każdym zamiarze

Każdy cel musi mieć przykład wyrażenia długości, co najmniej 15. Jeśli masz intencję, która nie ma żadnego przykładu wyrażenia długości, nie będziesz mieć możliwości uczenia się LUIS. Jeśli masz zamiar z jednym lub kilkoma przykładami wyrażenia długości, LUIS może nie dokładnie przewidzieć zamiar. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Dodaj małe grupy 15 wyrażenia długości dla każdej iteracji tworzenia

W każdej iteracji modelu nie należy dodawać dużej liczby wyrażenia długości. Dodaj wyrażenia długości w ilościach 15. Ponownie [nauczenie](luis-how-to-train.md), [opublikowanie](luis-how-to-publish-app.md)i [przetestowanie](luis-interactive-test.md) .  

LUIS kompiluje wydajne modele z wyrażenia długości, które są starannie wybierane przez autora modelu LUIS. Dodanie zbyt wielu wyrażenia długości nie jest cenne, ponieważ wprowadza pomyłkę.

Lepiej zacząć od kilku wyrażenia długościów, a następnie [przejrzeć punkt końcowy wyrażenia długości](luis-how-to-review-endpoint-utterances.md) w celu poprawnego przewidywania zamierzeń i wyodrębnienia jednostek.

## <a name="utterance-normalization"></a>Normalizacja wypowiedź

Normalizacja wypowiedź jest procesem ignorowania efektów interpunkcji i znaków diakrytycznych podczas szkolenia i przewidywania.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Normalizacja wypowiedź dla znaków diakrytycznych i interpunkcji

Normalizacja wypowiedź jest definiowana podczas tworzenia lub importowania aplikacji, ponieważ jest to ustawienie w pliku JSON aplikacji. Ustawienia normalizacji wypowiedź są domyślnie wyłączone. 

Znaki diakrytyczne są znakami lub znakami w tekście, na przykład: 

```
İ ı Ş Ğ ş ğ ö ü
```

Jeśli aplikacja zostanie przesunięta na, wyniki w okienku **testów** , testy wsadowe i zapytania dotyczące punktów końcowych zmienią się dla wszystkich wyrażenia długości za pomocą znaków diakrytycznych lub interpunkcji.

Włącz normalizację wypowiedź dla znaków diakrytycznych lub interpunkcji do pliku aplikacji LUIS JSON w parametrze `settings`.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Normalizacja **interpunkcji** oznacza, że zanim modele są przeszkolone i zanim zapytania punktu końcowego zostaną przewidywalne, interpunkcja zostanie usunięta z wyrażenia długości. 

Normalizacja znaków **diakrytycznych** zastępuje znaki znakami diakrytycznymi w wyrażenia długości z regularnymi znakami. Na przykład: `Je parle français` jest `Je parle francais`. 

Normalizacja nie oznacza, że w przykładach wyrażenia długości lub przewidywania nie zobaczysz interpunkcji ani znaków diakrytycznych, tylko te, które zostaną zignorowane podczas uczenia się i przewidywania.


### <a name="punctuation-marks"></a>Znaki interpunkcyjne

Znak interpunkcyjny jest tokenu oddzielnych w usługi LUIS. Wypowiedź, który zawiera kropkę na końcu, a wypowiedź, który nie zawiera kropki na końcu, to dwa oddzielne wyrażenia długości i mogą uzyskać dwa różne przewidywania. 

Jeśli interpunkcja nie jest znormalizowana, LUIS nie ignoruje znaków interpunkcyjnych, domyślnie, ponieważ niektóre aplikacje klienckie mogą umieścić istotny wpływ na te znaki. Upewnij się, że przykład wyrażenia długości używać interpunkcji i bez znaków interpunkcyjnych, aby oba style zwracały te same wyniki względne. 

Upewnij się, że model obsługuje interpunkcję w przykład wyrażenia długości (bez znaków interpunkcyjnych) lub w [wzorcach](luis-concept-patterns.md) , w których łatwiej jest zignorować interpunkcję z specjalną składnią: `I am applying for the {Job} position[.]`

Jeśli interpunkcja nie ma określonego znaczenia w aplikacji klienckiej, należy wziąć pod uwagę [Ignorowanie interpunkcji](#utterance-normalization) przez normalizowanie interpunkcji. 

### <a name="ignoring-words-and-punctuation"></a>Ignorowanie wyrazów i interpunkcji

Jeśli chcesz zignorować określone wyrazy lub interpunkcję w wzorcach, użyj [wzorca](luis-concept-patterns.md#pattern-syntax) z _ignorowaną_ składnią nawiasów kwadratowych, `[]`. 

## <a name="training-utterances"></a>Szkolenia wyrażenia długości

Szkolenie jest ogólnie niedeterministyczne: przewidywania wypowiedź może się nieco różnić w różnych wersjach lub aplikacjach. Można usunąć niedeterministyczne szkolenie przez zaktualizowanie interfejsu API [ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) za pomocą pary nazwa `UseAllTrainingData`/wartość, aby używać wszystkich danych szkoleniowych.

## <a name="testing-utterances"></a>Testowanie wyrażenia długości 

Deweloperzy powinni zacząć testować swoją aplikację LUIS z rzeczywistym ruchem, wysyłając wyrażenia długości do adresu URL [punktu końcowego przewidywania](luis-how-to-azure-subscription.md) . Te wyrażenia długości są używane do ulepszania wydajności intencji i jednostek z [przeglądem wyrażenia długości](luis-how-to-review-endpoint-utterances.md). Testy przesłane za pomocą okienka testowania witryny sieci Web LUIS nie są wysyłane za pomocą punktu końcowego i nie przyczyniają się do aktywnego uczenia. 

## <a name="review-utterances"></a>Przegląd wyrażenia długości

Po przeszkoleniu, opublikowaniu i odebraniu zapytania dotyczącego [punktu końcowego](luis-glossary.md#endpoint) [Sprawdź wyrażenia długości](luis-how-to-review-endpoint-utterances.md) sugerowane przez Luis. LUIS wybiera punkt końcowy wyrażenia długości, który ma niskie wyniki dla zamiaru lub jednostki. 

## <a name="best-practices"></a>Najlepsze praktyki

Zapoznaj się z [najlepszymi rozwiązaniami](luis-concept-best-practices.md) i zastosuj je w ramach regularnego cyklu tworzenia.

## <a name="label-for-word-meaning"></a>Etykieta znaczenie słowa

Jeśli wybór programu word lub rozmieszczeniu word jest taka sama, ale nie oznaczają to samo, nie przypisuj jej etykiety bez jednostki. 

Następujący wyrażenia długości, słowo `fair` to homograph. Została wpisana takie same, ale ma inne znaczenie:

|Wypowiedź|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

Jeśli chcesz, aby jednostka zdarzenia znalazła wszystkie dane zdarzeń, Oznacz słowo `fair` w pierwszej wypowiedź, ale nie w drugim.


## <a name="next-steps"></a>Następne kroki
Zobacz [Dodawanie przykładu wyrażenia długości](luis-how-to-add-example-utterances.md) , aby uzyskać informacje na temat szkolenia aplikacji Luis, aby poznać wyrażenia długości użytkownika.

