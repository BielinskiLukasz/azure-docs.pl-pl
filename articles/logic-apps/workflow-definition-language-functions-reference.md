---
title: Przewodnik referencyjny dotyczący funkcji w wyrażeniach
description: Przewodnik referencyjny dotyczący funkcji w wyrażeniach dla Azure Logic Apps i automatyzacji
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: e449eed0a8ae9ed6e1847ea70db6968bf9ede9bd
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74784531"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Przewodnik referencyjny dotyczący używania funkcji w wyrażeniach dla Azure Logic Apps i automatyzacji

W przypadku definicji przepływu pracy w [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [automatyzacji](https://docs.microsoft.com/flow/getting-started), niektóre [wyrażenia](../logic-apps/logic-apps-workflow-definition-language.md#expressions) pobierają wartości z akcji środowiska uruchomieniowego, które mogą jeszcze nie istnieć podczas uruchamiania przepływu pracy. Aby odwołać się do tych wartości lub przetworzyć wartości w tych wyrażeniach, można użyć *funkcji* udostępnianych przez [Język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Ta strona referencyjna dotyczy zarówno Azure Logic Apps, jak i automatyzacji, ale pojawia się w dokumentacji Azure Logic Apps. Mimo że ta strona odwołuje się głównie do usługi Logic Apps, te funkcje działają zarówno dla przepływów, jak i aplikacji logiki. Aby uzyskać więcej informacji o funkcjach i wyrażeniach w programie do automatyzowania, zobacz [use Expressions in Conditions](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Na przykład można obliczyć wartości przy użyciu funkcji matematycznych, takich jak [Funkcja Add ()](../logic-apps/workflow-definition-language-functions-reference.md#add), jeśli chcesz, aby suma była sumą z liczb całkowitych lub zmiennoprzecinkowych. Oto inne przykładowe zadania, które można wykonywać za pomocą funkcji:

| Zadanie | Składnia funkcji | Wynik |
| ---- | --------------- | ------ |
| Zwraca ciąg w formacie małymi literami. | toLower ("< >*tekstu*") <p>Na przykład: toLower ("Hello") | Hello |
| Zwraca unikatowy identyfikator globalny (GUID). | Identyfikator GUID () |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Aby znaleźć funkcje [na podstawie ich ogólnego przeznaczenia](#ordered-by-purpose), zapoznaj się z poniższymi tabelami. Lub, aby uzyskać szczegółowe informacje na temat każdej funkcji, zobacz [alfabetyczną listę](#alphabetical-list).

> [!NOTE]
> W składni definicji parametrów, znak zapytania (?), który pojawia się po parametrze oznacza, że parametr jest opcjonalny.
> Na przykład zobacz [getFutureTime ()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funkcje w wyrażeniach

Aby pokazać, jak używać funkcji w wyrażeniu, ten przykład pokazuje, jak można uzyskać wartość z parametru `customerName` i przypisać tę wartość do właściwości `accountName` przy użyciu funkcji [Parameters ()](#parameters) w wyrażeniu:

```json
"accountName": "@parameters('customerName')"
```

Poniżej przedstawiono inne ogólne sposoby używania funkcji w wyrażeniach:

| Zadanie | Składnia funkcji w wyrażeniu |
| ---- | -------------------------------- |
| Wykonaj prace z elementem, przekazując ten element do funkcji. | "\@<*functionname*> (<*elementu*>)" |
| 1. Pobierz wartość *parametruname*przy użyciu zagnieżdżonej funkcji `parameters()`. </br>2. wykonaj prace z wynikiem, przekazując tę wartość do *funkcji FunctionName*. | "\@<*functionname*> (parametry (" <*ParameterName*> "))" |
| 1. Pobierz wynik z zagnieżdżonej *funkcji wewnętrznejname.* </br>2. Przekaż wynik do funkcji zewnętrznej *functionName2*. | "\@<*functionName2*> (<*FunctionName*> (<*Item*>))" |
| 1. Pobierz wynik z *funkcji FunctionName*. </br>2. Jeśli wynik jest obiektem z właściwością *PropertyName*, Pobierz wartość tej właściwości. | "\@<*functionname*> (<*elementu*>). <*PropertyName*>" |
|||

Na przykład funkcja `concat()` może przyjmować jako parametry dwie lub więcej wartości ciągu. Ta funkcja łączy te ciągi w jeden ciąg.
Można przekazać literały ciągu, na przykład "Sophia" i "Owen", aby uzyskać ciąg połączony "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Lub można uzyskać wartości ciągu z parametrów. Ten przykład używa funkcji `parameters()` w każdym parametrze `concat()` i `firstName` i `lastName` parametrów. Następnie można przekazać otrzymane ciągi do funkcji `concat()`, aby uzyskać ciąg połączony, na przykład "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

W obu przypadkach oba przykłady przypisują wynik do właściwości `customerName`.

Poniżej przedstawiono dostępne funkcje uporządkowane według ich ogólnego przeznaczenia lub można przeglądać funkcje w oparciu o [kolejność alfabetyczną](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funkcje ciągów

Aby korzystać z ciągów, można użyć tych funkcji ciągów, a także niektórych [funkcji kolekcji](#collection-functions).
Funkcje ciągów działają tylko na ciągach.

| Funkcja String | Zadanie |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Połącz dwa lub więcej ciągów i zwróć połączony ciąg. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Sprawdź, czy ciąg jest kończący się określonym podciągiem. |
| [ident](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generuj unikatowy identyfikator globalny (GUID) jako ciąg. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Zwróć pozycję początkową dla podciągu. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Zwróć pozycję początkową dla ostatniego wystąpienia podciągu. |
| [stępować](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Zamień podciąg na określony ciąg i zwróć zaktualizowany ciąg. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Zwraca tablicę zawierającą podciągi, rozdzieloną przecinkami, od większego ciągu na podstawie określonego znaku ogranicznika w oryginalnym ciągu. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Sprawdź, czy ciąg rozpoczyna się od określonego podciągu. |
| [podciąg](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Zwraca znaki z ciągu, zaczynając od określonej pozycji. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Zwraca ciąg w formacie małymi literami. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Zwraca ciąg w formacie wielką literą. |
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Usuń spacje wiodące i końcowe z ciągu, a następnie Zwróć zaktualizowany ciąg. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funkcje kolekcji

Do pracy z kolekcjami, ogólnie tablicami, ciągami i czasami słownikami można używać tych funkcji kolekcji.

| Funkcja kolekcji | Zadanie |
| ------------------- | ---- |
| [wyświetlana](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Sprawdź, czy kolekcja zawiera określony element. |
| [ciągiem](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Sprawdź, czy kolekcja jest pusta. |
| [pierwszego](../logic-apps/workflow-definition-language-functions-reference.md#first) | Zwróć pierwszy element z kolekcji. |
| [część wspólną](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Zwróć kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji. |
| [elementów](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gdy wewnątrz powtarzającej się akcji nad tablicą, zwraca bieżący element w tablicy podczas bieżącej iteracji akcji. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Zwraca ciąg, który zawiera *wszystkie* elementy z tablicy, oddzielone określonym znakiem. |
| [ostatniego](../logic-apps/workflow-definition-language-functions-reference.md#last) | Zwróć ostatni element z kolekcji. |
| [Długość](../logic-apps/workflow-definition-language-functions-reference.md#length) | Zwraca liczbę elementów w ciągu lub tablicy. |
| [Skocz](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy. |
| [czasochłonn](../logic-apps/workflow-definition-language-functions-reference.md#take) | Zwróć elementy z przodu kolekcji. |
| [Unii](../logic-apps/workflow-definition-language-functions-reference.md#union) | Zwraca kolekcję zawierającą *wszystkie* elementy z określonych kolekcji. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logiczne funkcje porównania

Aby działać z warunkami, porównywać wartości i wyniki wyrażeń lub oceniać różne rodzaje logiki, można użyć tych logicznych funkcji porównywania.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Logiczna funkcja porównywania | Zadanie |
| --------------------------- | ---- |
| [lub](../logic-apps/workflow-definition-language-functions-reference.md#and) | Sprawdź, czy wszystkie wyrażenia mają wartość PRAWDA. |
| [ubiegł](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Sprawdź, czy obie wartości są równoważne. |
| [mniejszą](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Sprawdź, czy pierwsza wartość jest większa od drugiej wartości. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości. |
| [przypadku](../logic-apps/workflow-definition-language-functions-reference.md#if) | Sprawdź, czy wyrażenie ma wartość true lub false. W oparciu o wynik Zwraca określoną wartość. |
| [wcześniejsz](../logic-apps/workflow-definition-language-functions-reference.md#less) | Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości. |
| [niemożliwe](../logic-apps/workflow-definition-language-functions-reference.md#not) | Sprawdź, czy wyrażenie ma wartość false. |
| [oraz](../logic-apps/workflow-definition-language-functions-reference.md#or) | Sprawdź, czy co najmniej jedno wyrażenie ma wartość true. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funkcje konwersji

Aby zmienić typ lub format wartości, można użyć tych funkcji konwersji.
Na przykład można zmienić wartość z wartości logicznej na liczbę całkowitą.
Aby uzyskać więcej informacji na temat sposobu, w jaki Logic Apps obsługuje typy zawartości podczas konwersji, zobacz [Obsługa typów zawartości](../logic-apps/logic-apps-content-type.md).
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja konwersji | Zadanie |
| ------------------- | ---- |
| [macierzy](../logic-apps/workflow-definition-language-functions-reference.md#array) | Zwraca tablicę z pojedynczego określonego danych wejściowych. W przypadku wielu danych wejściowych [Zobacz sekcję](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [zakodowan](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Zwróć wersję z kodowaniem Base64 dla ciągu. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Zwróć wersję binarną dla ciągu zakodowanego algorytmem Base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64. |
| [binarny](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Zwróć wersję binarną dla wartości wejściowej. |
| [logiczna](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Zwróć wersję logiczną dla wartości wejściowej. |
| [przearray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Zwróć tablicę z wielu danych wejściowych. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Zwróć identyfikator URI danych dla wartości wejściowej. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Zwróć wersję binarną dla identyfikatora URI danych. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Zwraca wersję ciągu dla identyfikatora URI danych. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Zwróć wersję binarną dla identyfikatora URI danych. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Zwraca ciąg, który zastępuje znaki ucieczki z zdekodowanymi wersjami. |
| [encodeURIComponent —](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Zwraca ciąg, który zastępuje znaki w postaci niebezpiecznej adresów URL. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Zwróć liczbę zmiennoprzecinkową dla wartości wejściowej. |
| [ZAOKR](../logic-apps/workflow-definition-language-functions-reference.md#int) | Zwróć wersję całkowitą dla ciągu. |
| [kodu](../logic-apps/workflow-definition-language-functions-reference.md#json) | Zwróć wartość typu JavaScript Object Notation (JSON) lub obiekt dla ciągu lub XML. |
| [parametry](../logic-apps/workflow-definition-language-functions-reference.md#string) | Zwraca wersję ciągu dla wartości wejściowej. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Zwróć wersję z kodowaniem URI dla wartości wejściowej przez zastępowanie znaków w adresie URL bez znaku ucieczki. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Zwróć wersję binarną dla ciągu zakodowanego za pomocą identyfikatora URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Zwraca wersję ciągu dla ciągu zakodowanego przy użyciu identyfikatora URI. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Zwraca wersję XML dla ciągu. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Funkcje matematyczne

Aby korzystać z liczb całkowitych i zmiennoprzecinkowych, można użyć tych funkcji matematycznych.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja matematyczna | Zadanie |
| ------------- | ---- |
| [dodana](../logic-apps/workflow-definition-language-functions-reference.md#add) | Zwróć wynik dodawania dwóch liczb. |
| [służąc](../logic-apps/workflow-definition-language-functions-reference.md#div) | Zwróć wynik dzielenia dwóch liczb. |
| [Maksymalny](../logic-apps/workflow-definition-language-functions-reference.md#max) | Zwraca najwyższą wartość z zestawu liczb lub tablicy. |
| [długości](../logic-apps/workflow-definition-language-functions-reference.md#min) | Zwróć najniższą wartość z zestawu liczb lub tablicy. |
| [Funkcja](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Zwróć resztę z dzielenia dwóch liczb. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Zwróć produkt z mnożenia dwóch liczb. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Zwraca losową liczbę całkowitą z podanego zakresu. |
| [zakresu](../logic-apps/workflow-definition-language-functions-reference.md#range) | Zwróć tablicę liczb całkowitych, która zaczyna się od określonej liczby całkowitej. |
| [Sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Zwraca wynik odejmowania drugiej liczby od pierwszej liczby. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funkcje daty i godziny

Aby działać z datami i godzinami, można użyć tych funkcji daty i godziny.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja daty lub godziny | Zadanie |
| --------------------- | ---- |
| [Liczba dni](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Dodaj liczbę dni do sygnatury czasowej. |
| [addgodz.](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Dodaj liczbę godzin do sygnatury czasowej. |
| [addminut](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Dodaj liczbę minut do sygnatury czasowej. |
| [AddSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Dodaj liczbę sekund do sygnatury czasowej. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Dodaj liczbę jednostek czasu do sygnatury czasowej. Zobacz również [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Przekształć sygnaturę czasową od uniwersalnego czasu koordynowanego (UTC) na docelową strefę czasową. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Przekonwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalne czas uniwersalny (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Zwróć dzień składnika miesiąca z sygnatury czasowej. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Zwróć dzień składnika tygodnia z sygnatury czasowej. |
| [dzieńroku](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Zwróć dzień składnika roku z sygnatury czasowej. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Zwróć datę z sygnatury czasowej. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Zwróć bieżącą sygnaturę czasową powiększoną o określoną liczbę jednostek czasu. Zobacz również [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Zwróć bieżącą sygnaturę czasową minus określoną liczbę jednostek czasu. Zobacz również [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Zwróć początek dnia dla sygnatury czasowej. |
| [Początek godziny](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Zwróć początek godziny dla sygnatury czasowej. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Zwróć początek miesiąca dla sygnatury czasowej. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odejmij wiele jednostek czasu od sygnatury czasowej. Zobacz również [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [taktów](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Zwróć `ticks` wartość właściwości dla określonego sygnatury czasowej. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Zwróć bieżącą sygnaturę czasową jako ciąg. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funkcje przepływu pracy

Te funkcje przepływu pracy mogą pomóc:

* Pobierz szczegóły dotyczące wystąpienia przepływu pracy w czasie wykonywania.
* Pracuj z danymi wejściowymi używanymi do tworzenia wystąpień aplikacji logiki lub przepływów.
* Odwołuje się do danych wyjściowych z wyzwalaczy i akcji.

Na przykład można odwoływać się do danych wyjściowych z jednej akcji i używać ich w późniejszej akcji.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja przepływu pracy | Zadanie |
| ----------------- | ---- |
| [transakcji](../logic-apps/workflow-definition-language-functions-reference.md#action) | Zwraca dane wyjściowe bieżącej akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz także [Akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Zwróć `body` danych wyjściowych akcji w czasie wykonywania. Zobacz także [treść](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Zwraca dane wyjściowe akcji w czasie wykonywania. Zobacz dane [wyjściowe](../logic-apps/workflow-definition-language-functions-reference.md#outputs) i [Akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [wykonane](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Zwraca dane wyjściowe akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz również [Akcja](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [jednostce](#body) | Zwróć `body` danych wyjściowych akcji w czasie wykonywania. Zobacz również [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Utwórz tablicę z wartościami, które pasują do nazwy klucza w danych wyjściowych akcji *form-Data* lub *form-Encoded* . |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Zwróć pojedynczą wartość, która pasuje do nazwy klucza w danych *wyjściowych* *formularza* akcji lub formularza. |
| [elementów](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gdy wewnątrz powtarzającej się akcji nad tablicą, zwraca bieżący element w tablicy podczas bieżącej iteracji akcji. |
| [produktów](../logic-apps/workflow-definition-language-functions-reference.md#items) | Gdy wewnątrz pętli Foreach lub do until zwraca bieżący element z określonej pętli.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | W pętli until należy zwrócić wartość indeksu bieżącej iteracji. Ta funkcja może być używana wewnątrz zagnieżdżonych pętli do. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Zwróć "adres URL wywołania zwrotnego", który wywołuje wyzwalacz lub akcję. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Zwróć treść określonej części w danych wyjściowych akcji z wieloma częściami. |
| [wydajności](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Zwraca dane wyjściowe akcji w czasie wykonywania. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Zwraca wartość parametru, który jest opisany w definicji przepływu pracy. |
| [wynika](../logic-apps/workflow-definition-language-functions-reference.md#result) | Zwraca dane wejściowe i wyjściowe ze wszystkich akcji w określonym zakresie akcji, takich jak `For_each`, `Until`i `Scope`. |
| [uruchamiać](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub z innych par nazw i wartości JSON. Zobacz również [triggerOutputs](#triggerOutputs) i [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Zwraca dane wyjściowe wyzwalacza `body` w czasie wykonywania. Zobacz [wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Zwróć pojedynczą wartość odpowiadającą nazwie klucza w danych wyjściowych wyzwalacza *form-Data* lub *form* . |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Zwróć treść konkretnej części w wyjściu wyzwalacza. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Utwórz tablicę, której wartości pasują do nazwy klucza w danych wyjściowych wyzwalacza *form-Data* lub *form-Encoded* . |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz [wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [modyfikacj](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Zwraca wartość dla określonej zmiennej. |
| [utworzonego](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Zwróć wszystkie szczegóły dotyczące przepływu pracy w czasie wykonywania. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funkcje analizy identyfikatorów URI

Aby korzystać z identyfikatorów URI (Uniform Resource Identifier) i uzyskać różne wartości właściwości dla tych identyfikatorów URI, można użyć tych funkcji analizy URI.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja analizy identyfikatora URI | Zadanie |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Zwróć wartość `host` dla identyfikatora URI (Uniform Resource Identifier). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Zwróć wartość `path` dla identyfikatora URI (Uniform Resource Identifier). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Zwróć wartości `path` i `query` dla identyfikatora URI (Uniform Resource Identifier). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Zwróć wartość `port` dla identyfikatora URI (Uniform Resource Identifier). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Zwróć wartość `query` dla identyfikatora URI (Uniform Resource Identifier). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Zwróć wartość `scheme` dla identyfikatora URI (Uniform Resource Identifier). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funkcje manipulowania: kod JSON & XML

Aby współdziałać z obiektami JSON i węzłami XML, można użyć tych funkcji manipulowania.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja manipulowania | Zadanie |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Dodaj właściwość i jej wartość lub parę nazwa-wartość do obiektu JSON i zwróć zaktualizowany obiekt. |
| [łączonych](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Zwróć pierwszą wartość inną niż null z co najmniej jednego parametru. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Usunięcie właściwości z obiektu JSON i zwrócenie zaktualizowanego obiektu. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ustaw wartość właściwości obiektu JSON i zwróć zaktualizowany obiekt. |
| [Lokalizacja](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Sprawdź XML dla węzłów lub wartości, które pasują do wyrażenia XPath (język ścieżki XML) i zwracają pasujące węzły lub wartości. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Wszystkie funkcje — Alfabetyczna lista

Ta sekcja zawiera listę wszystkich dostępnych funkcji w kolejności alfabetycznej.

<a name="action"></a>

### <a name="action"></a>action

Zwraca dane wyjściowe *bieżącej* akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia.
Domyślnie ta funkcja odwołuje się do całego obiektu Action, ale można opcjonalnie określić właściwość, której wartość chcesz.
Zobacz także [Akcje ()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Funkcji `action()` można używać tylko w następujących miejscach:

* Właściwość `unsubscribe` akcji elementu webhook, aby można było uzyskać dostęp do wyniku oryginalnego żądania `subscribe`
* Właściwość `trackedProperties` dla akcji
* Warunek pętli `do-until` dla akcji

```
action()
action().outputs.body.<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*właściwość*> | Nie | Ciąg | Nazwa właściwości obiektu akcji, której wartość ma być: **name**, **StartTime**, **Endtime**, **Inputs** **, Output,** **status**, **Code**, **trackingId**i **clientTrackingId**. W Azure Portal można znaleźć te właściwości, przeglądając szczegóły konkretnej historii uruchamiania. Aby uzyskać więcej informacji, zobacz [działania interfejsu API REST — przepływ pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| *akcja <— dane wyjściowe*> | Ciąg | Dane wyjściowe z bieżącej akcji lub właściwości |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Zwróć `body` danych wyjściowych akcji w czasie wykonywania.
Skrót `actions('<actionName>').outputs.body`.
Zobacz [treść ()](#body) i [Akcje ()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionname*> | Tak | Ciąg | Nazwa `body` danych wyjściowych akcji, które chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| *akcja <— dane wyjściowe*> | Ciąg | `body` dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera `body` dane wyjściowe z akcji usługi Twitter `Get user`:

```
actionBody('Get_user')
```

I zwraca ten wynik:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Zwraca dane wyjściowe akcji w czasie wykonywania.  i jest skrótem `actions('<actionName>').outputs`. Zobacz [Akcje ()](#actions). Funkcja `actionOutputs()` jest rozpoznawana jako `outputs()` w Projektancie aplikacji logiki, dlatego Rozważ użycie danych [wyjściowych ()](#outputs), a nie `actionOutputs()`. Chociaż obie funkcje działają tak samo, `outputs()` jest preferowane.

```
actionOutputs('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionname*> | Tak | Ciąg | Nazwa żądanego danych wyjściowych akcji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*dane wyjściowe*> | Ciąg | Dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera dane wyjściowe z akcji usługi Twitter `Get user`:

```
actionOutputs('Get_user')
```

I zwraca ten wynik:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>wykonane

Zwraca dane wyjściowe akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia. Domyślnie funkcja odwołuje się do całego obiektu akcji, ale można opcjonalnie określić właściwość, której wartość chcesz.
W przypadku wersji skróconych zobacz [actionBody ()](#actionBody), [actionOutputs ()](#actionOutputs)i [Body ()](#body).
Aby uzyskać bieżącą akcję, zobacz [Akcja ()](#action).

> [!NOTE]
> Wcześniej można użyć funkcji `actions()` lub elementu `conditions` podczas określania, czy akcja została uruchomiona na podstawie danych wyjściowych z innej akcji. Jednak aby zadeklarować jawnie zależności między akcjami, musisz teraz użyć właściwości `runAfter` zależnej akcji.
> Aby dowiedzieć się więcej na temat właściwości `runAfter`, zobacz [awarie catch i Handle przy użyciu właściwości runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionname*> | Tak | Ciąg | Nazwa obiektu akcji, dla którego chcesz uzyskać dane wyjściowe.  |
| <*właściwość*> | Nie | Ciąg | Nazwa właściwości obiektu akcji, której wartość ma być: **name**, **StartTime**, **Endtime**, **Inputs** **, Output,** **status**, **Code**, **trackingId**i **clientTrackingId**. W Azure Portal można znaleźć te właściwości, przeglądając szczegóły konkretnej historii uruchamiania. Aby uzyskać więcej informacji, zobacz [działania interfejsu API REST — przepływ pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| *akcja <— dane wyjściowe*> | Ciąg | Dane wyjściowe z określonej akcji lub właściwości |
||||

*Przykład*

Ten przykład pobiera wartość właściwości `status` z akcji Twitter `Get user` w czasie wykonywania:

```
actions('Get_user').outputs.body.status
```

I zwraca ten wynik: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Zwróć wynik dodawania dwóch liczb.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub mieszana | Liczby do dodania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*Result-sum*> | Liczba całkowita lub zmiennoprzecinkowa | Wynik dodawania określonych liczb |
||||

*Przykład*

Ten przykład dodaje określone liczby:

```
add(1, 1.5)
```

I zwraca ten wynik: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>Liczba dni

Dodaj liczbę dni do sygnatury czasowej.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*dni*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba dni do dodania |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Sygnatura czasowa powiększona o określoną liczbę dni  |
||||

*Przykład 1*

Ten przykład dodaje 10 dni do określonej sygnatury czasowej:

```
addDays('2018-03-15T13:00:00Z', 10)
```

I zwraca ten wynik: `"2018-03-25T00:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć dni od określonej sygnatury czasowej:

```
addDays('2018-03-15T00:00:00Z', -5)
```

I zwraca ten wynik: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addgodz.

Dodaj liczbę godzin do sygnatury czasowej.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*godz* .> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba godzin do dodania |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Sygnatura czasowa powiększona o określoną liczbę godzin  |
||||

*Przykład 1*

Ten przykład dodaje 10 godzin do określonej sygnatury czasowej:

```
addHours('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik: `"2018-03-15T10:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć godzin od określonej sygnatury czasowej:

```
addHours('2018-03-15T15:00:00Z', -5)
```

I zwraca ten wynik: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addminut

Dodaj liczbę minut do sygnatury czasowej.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*minut*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba minut do dodania |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Sygnatura czasowa powiększona o określoną liczbę minut |
||||

*Przykład 1*

Ten przykład dodaje 10 minut do określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

I zwraca ten wynik: `"2018-03-15T00:20:00.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć minut od określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

I zwraca ten wynik: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Dodaj właściwość i jej wartość lub parę nazwa-wartość do obiektu JSON i zwróć zaktualizowany obiekt. Jeśli właściwość już istnieje w czasie wykonywania, funkcja zakończy się niepowodzeniem i zgłosi błąd.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > <*obiektu* | Tak | Obiekt | Obiekt JSON, w którym chcesz dodać właściwość |
| <*właściwość*> | Tak | Ciąg | Nazwa właściwości do dodania |
| <*wartość*> | Tak | Dowolne | Wartość właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Zaktualizowano —> obiektu* | Obiekt | Zaktualizowany obiekt JSON z określoną właściwością. |
||||

Aby dodać właściwość podrzędną do istniejącej właściwości, użyj następującej składni:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > <*obiektu* | Tak | Obiekt | Obiekt JSON, w którym chcesz dodać właściwość |
| <*Właściwość parent*> | Tak | Ciąg | Nazwa właściwości nadrzędnej, w której ma zostać dodana Właściwość podrzędna |
| <*Właściwość podrzędna*> | Tak | Ciąg | Nazwa właściwości podrzędnej do dodania |
| <*wartość*> | Tak | Dowolne | Wartość do ustawienia dla określonej właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Zaktualizowano —> obiektu* | Obiekt | Zaktualizowany obiekt JSON, którego właściwość została ustawiona |
||||

*Przykład 1*

Ten przykład dodaje właściwość `middleName` do obiektu JSON, który jest konwertowany z ciągu na notację JSON przy użyciu funkcji [JSON ()](#json) . Obiekt zawiera już właściwości `firstName` i `surName`. Funkcja przypisuje określoną wartość do nowej właściwości i zwraca zaktualizowany obiekt:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Oto bieżący obiekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Przykład 2*

Ten przykład dodaje właściwość podrzędną `middleName` do istniejącej właściwości `customerName` w obiekcie JSON, który jest konwertowany z ciągu na notację JSON przy użyciu funkcji [JSON ()](#json) . Funkcja przypisuje określoną wartość do nowej właściwości i zwraca zaktualizowany obiekt:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Oto bieżący obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>AddSeconds

Dodaj liczbę sekund do sygnatury czasowej.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*sekund*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba sekund do dodania |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Sygnatura czasowa powiększona o określoną liczbę sekund.  |
||||

*Przykład 1*

Ten przykład dodaje 10 sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik: `"2018-03-15T00:00:10.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

I zwraca ten wynik: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Dodaj liczbę jednostek czasu do sygnatury czasowej.
Zobacz również [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| *interwał* <> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem*: "sekunda", "minuta", "godzina", "dzień", "tydzień", "Month", "Year" |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Sygnatura czasowa powiększona o określoną liczbę jednostek czasu  |
||||

*Przykład 1*

Ten przykład dodaje jeden dzień do określonego znacznika czasu:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik: `"2018-01-02T00:00:00.0000000Z"`

*Przykład 2*

Ten przykład dodaje jeden dzień do określonego znacznika czasu:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

I zwraca wynik przy użyciu opcjonalnego formatu "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>oraz

Sprawdź, czy wszystkie wyrażenia mają wartość PRAWDA.
Zwraca wartość true, jeśli wszystkie wyrażenia są prawdziwe, lub zwraca wartość false, jeśli co najmniej jedno wyrażenie ma wartość false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*>,... | Tak | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli wszystkie wyrażenia mają wartość true. Zwraca wartość false, jeśli co najmniej jedno wyrażenie ma wartość false. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy określone wartości logiczne są spełnione:

```
and(true, true)
and(false, true)
and(false, false)
```

I zwraca te wyniki:

* Pierwszy przykład: oba wyrażenia są prawdziwe, dlatego zwraca `true`.
* Drugi przykład: jedno wyrażenie ma wartość false, więc zwraca `false`.
* Trzeci przykład: oba wyrażenia mają wartość false, więc zwraca `false`.

*Przykład 2*

Te przykłady sprawdzają, czy określone wyrażenia są prawdziwe:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

I zwraca te wyniki:

* Pierwszy przykład: oba wyrażenia są prawdziwe, dlatego zwraca `true`.
* Drugi przykład: jedno wyrażenie ma wartość false, więc zwraca `false`.
* Trzeci przykład: oba wyrażenia mają wartość false, więc zwraca `false`.

<a name="array"></a>

### <a name="array"></a>tablica

Zwraca tablicę z pojedynczego określonego danych wejściowych.
W przypadku wielu danych wejściowych zobacz polecenie " [XmlArray" ()](#createArray).

```
array('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg do tworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*wartość*>] | Tablica | Tablica zawierająca pojedyncze określone dane wejściowe |
||||

*Przykład*

Ten przykład tworzy tablicę z ciągu "Hello":

```
array('hello')
```

I zwraca ten wynik: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>zakodowan

Zwróć wersję z kodowaniem Base64 dla ciągu.

```
base64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg wejściowy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *ciągu Base64* | Ciąg | Wersja zakodowana algorytmem Base64 dla ciągu wejściowego |
||||

*Przykład*

Ten przykład konwertuje ciąg "Hello" na ciąg szyfrowany algorytmem Base64:

```
base64('hello')
```

I zwraca ten wynik: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Zwróć wersję binarną dla ciągu zakodowanego algorytmem Base64.

```
base64ToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg zakodowany w formacie base64 do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *typu binary-for-Base64* | Ciąg | Wersja binarna dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład konwertuje ciąg "aGVsbG8 =" zakodowany algorytmem Base64 na ciąg binarny:

```
base64ToBinary('aGVsbG8=')
```

I zwraca ten wynik:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64, efektywnie dekodowanie ciągu Base64.
Użyj tej funkcji zamiast [decodeBase64 ()](#decodeBase64).
Chociaż obie funkcje działają tak samo, `base64ToString()` jest preferowane.

```
base64ToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Zakodowany w formacie base64 ciąg do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowane — ciąg base64*> | Ciąg | Wersja ciągu dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład konwertuje ciąg "aGVsbG8 =" zakodowany algorytmem Base64 na tylko ciąg:

```
base64ToString('aGVsbG8=')
```

I zwraca ten wynik: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binarny

Zwróć wersję binarną ciągu.

```
binary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *wartości binarnej* | Ciąg | Wersja binarna określonego ciągu |
||||

*Przykład*

Ten przykład konwertuje ciąg "Hello" na ciąg binarny:

```
binary('hello')
```

I zwraca ten wynik:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>jednostce

Zwróć `body` danych wyjściowych akcji w czasie wykonywania.
Skrót `actions('<actionName>').outputs.body`.
Zobacz [actionBody ()](#actionBody) i [Akcje ()](#actions).

```
body('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionname*> | Tak | Ciąg | Nazwa `body` danych wyjściowych akcji, które chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| *akcja <— dane wyjściowe*> | Ciąg | `body` dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera `body` dane wyjściowe z akcji usługi Twitter `Get user`:

```
body('Get_user')
```

I zwraca ten wynik:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Zwróć wersję logiczną dla wartości.

```
bool(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Wersja logiczna dla określonej wartości |
||||

*Przykład*

Te przykłady umożliwiają konwersję określonych wartości na wartości logiczne:

```
bool(1)
bool(0)
```

I zwraca te wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>łączonych

Zwróć pierwszą wartość inną niż null z co najmniej jednego parametru.
Puste ciągi, puste tablice i puste obiekty nie mają wartości null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Tak | Any, można mieszać typy | Co najmniej jeden element do sprawdzenia dla wartości null |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*element "First-Non-null"* > | Dowolne | Pierwszy element lub wartość, która nie jest równa null. Jeśli wszystkie parametry mają wartość null, ta funkcja zwraca wartość null. |
||||

*Przykład*

Te przykłady zwracają pierwszą wartość o wartości innej niż null z określonych wartości lub wartości null, jeśli wszystkie wartości mają wartość null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

I zwraca te wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `"hello"`
* Trzeci przykład: `null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Połącz dwa lub więcej ciągów i zwróć połączony ciąg.

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*tekst1*>, <*Tekst2*>,... | Tak | Ciąg | Co najmniej dwa ciągi do połączenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Ciąg | Ciąg utworzony na podstawie połączonych ciągów wejściowych |
||||

*Przykład*

Ten przykład łączy ciągi "Hello" i "World":

```
concat('Hello', 'World')
```

I zwraca ten wynik: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>wyświetlana

Sprawdź, czy kolekcja zawiera określony element.
Zwraca wartość true, jeśli element zostanie znaleziony lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja uwzględnia wielkość liter.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

W przypadku tej funkcji działa ona na następujących typach kolekcji:

* *Ciąg* , aby znaleźć *podciąg*
* *Tablica* , aby znaleźć *wartość*
* *Słownik* służący do znajdowania *klucza*

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbieranie*> | Tak | Ciąg, tablica lub słownik | Kolekcja do sprawdzenia |
| <*wartość*> | Tak | Odpowiednio ciąg, tablica lub słownik | Element do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli element zostanie znaleziony. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza ciąg "Hello World" dla podciągu "World" i zwraca wartość true:

```
contains('hello world', 'world')
```

*Przykład 2*

Ten przykład sprawdza ciąg "Hello World" dla podciągu "Universe" i zwraca wartość false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Przekształć sygnaturę czasową od uniwersalnego czasu koordynowanego (UTC) na docelową strefę czasową.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*destinationTimeZone*> | Tak | Ciąg | Nazwa docelowej strefy czasowej. W przypadku nazw stref czasowych zobacz [wartości indeksów strefy czasowej firmy Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale może być konieczne usunięcie znaków interpunkcyjnych z nazwy strefy czasowej. |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*przekonwertowanej sygnatury czasowej*> | Ciąg | Sygnatura czasowa konwertowana na docelową strefę czasową |
||||

*Przykład 1*

Ten przykład konwertuje sygnaturę czasową na określoną strefę czasową:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

Ten przykład konwertuje sygnaturę czasową na określoną strefę czasową i format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Przekonwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*sourceTimeZone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. W przypadku nazw stref czasowych zobacz [wartości indeksów strefy czasowej firmy Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale może być konieczne usunięcie znaków interpunkcyjnych z nazwy strefy czasowej. |
| <*destinationTimeZone*> | Tak | Ciąg | Nazwa docelowej strefy czasowej. W przypadku nazw stref czasowych zobacz [wartości indeksów strefy czasowej firmy Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale może być konieczne usunięcie znaków interpunkcyjnych z nazwy strefy czasowej. |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*przekonwertowanej sygnatury czasowej*> | Ciąg | Sygnatura czasowa konwertowana na docelową strefę czasową |
||||

*Przykład 1*

Ten przykład konwertuje źródłową strefę czasową na docelową strefę czasową:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

Ten przykład konwertuje strefę czasową na określoną strefę czasową i format:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalne czas uniwersalny (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*sourceTimeZone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. W przypadku nazw stref czasowych zobacz [wartości indeksów strefy czasowej firmy Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale może być konieczne usunięcie znaków interpunkcyjnych z nazwy strefy czasowej. |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*przekonwertowanej sygnatury czasowej*> | Ciąg | Sygnatura czasowa konwertowana na UTC |
||||

*Przykład 1*

Ten przykład konwertuje sygnaturę czasową na czas UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T08:00:00.0000000Z"`

*Przykład 2*

Ten przykład konwertuje sygnaturę czasową na czas UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>przearray

Zwróć tablicę z wielu danych wejściowych.
Dla pojedynczych tablic wejściowych zobacz [Array ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*obiekt1*>, <*Obiekt2*>,... | Tak | Wszystkie, ale nie mieszane | Co najmniej dwa elementy, aby utworzyć tablicę |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*obiekt1*>, <*Obiekt2*>,...] | Tablica | Tablica utworzona na podstawie wszystkich elementów wejściowych |
||||

*Przykład*

Ten przykład tworzy tablicę z następujących danych wejściowych:

```
createArray('h', 'e', 'l', 'l', 'o')
```

I zwraca ten wynik: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Zwróć identyfikator URI (Uniform Resource Identifier) danych dla ciągu.

```
dataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *identyfikatorów URI* | Ciąg | Identyfikator URI danych dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie tworzony jest identyfikator URI danych dla ciągu "Hello":

```
dataUri('hello')
```

I zwraca ten wynik: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Zwróć wersję binarną dla identyfikatora URI (Uniform Resource Identifier) danych.
Użyj tej funkcji zamiast [decodeDataUri ()](#decodeDataUri).
Chociaż obie funkcje działają tak samo, `dataUriBinary()` jest preferowane.

```
dataUriToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Identyfikator URI danych do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Identyfikator URI danych binarnych*> | Ciąg | Wersja binarna identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzona jest wersja binarna dla tego identyfikatora URI danych:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Zwraca wersję ciągu dla identyfikatora URI (Uniform Resource Identifier) danych.

```
dataUriToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Identyfikator URI danych do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg — dla identyfikatora URI dla danych*> | Ciąg | Wersja ciągu dla identyfikatora URI danych |
||||

*Przykład*

Ten przykład tworzy ciąg dla tego identyfikatora URI danych:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Zwróć dzień miesiąca z sygnatury czasowej.

```
dayOfMonth('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień miesiąca*> | Liczba całkowita | Dzień miesiąca od określonej sygnatury czasowej |
||||

*Przykład*

Ten przykład zwraca liczbę dla dnia miesiąca z tej sygnatury czasowej:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Zwróć dzień tygodnia z sygnatury czasowej.

```
dayOfWeek('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień tygodnia*> | Liczba całkowita | Dzień tygodnia od określonej sygnatury czasowej, gdzie Niedziela ma wartość 0, poniedziałek wynosi 1 itd. |
||||

*Przykład*

Ten przykład zwraca liczbę dni tygodnia od tej sygnatury czasowej:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dzieńroku

Zwróć dzień roku z sygnatury czasowej.

```
dayOfYear('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień roku*> | Liczba całkowita | Dzień roku od określonej sygnatury czasowej |
||||

*Przykład*

Ten przykład zwraca numer dnia roku z tej sygnatury czasowej:

```
dayOfYear('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64, efektywnie dekodowanie ciągu Base64.
Rozważ użycie [base64ToString ()](#base64ToString) , a nie `decodeBase64()`.
Chociaż obie funkcje działają tak samo, `base64ToString()` jest preferowane.

```
decodeBase64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Zakodowany w formacie base64 ciąg do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowane — ciąg base64*> | Ciąg | Wersja ciągu dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład tworzy ciąg dla ciągu zakodowanego algorytmem Base64:

```
decodeBase64('aGVsbG8=')
```

I zwraca ten wynik: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Zwróć wersję binarną dla identyfikatora URI (Uniform Resource Identifier) danych.
Rozważ użycie [dataUriToBinary ()](#dataUriToBinary), a nie `decodeDataUri()`.
Chociaż obie funkcje działają tak samo, `dataUriToBinary()` jest preferowane.

```
decodeDataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg identyfikatora URI danych do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Identyfikator URI danych binarnych*> | Ciąg | Wersja binarna ciągu identyfikatora URI danych |
||||

*Przykład*

Ten przykład zwraca wersję binarną dla tego identyfikatora URI danych:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Zwraca ciąg, który zastępuje znaki ucieczki z zdekodowanymi wersjami.

```
decodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg znaków ucieczki do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowana-uri*> | Ciąg | Zaktualizowany ciąg z zdekodowanymi znakami ucieczki |
||||

*Przykład*

Ten przykład zastępuje znaki ucieczki w tym ciągu z zdekodowanymi wersjami:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>służąc

Zwraca liczbę całkowitą z wyniku dzielenia dwóch liczb.
Aby uzyskać resztę, zobacz [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dzielną*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik* |
| <*dzielnik*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba dzieląca *dzielną*, ale nie może być równa 0. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wyniku ilorazu*> | Liczba całkowita | Wynik z wartości całkowitej dzielącej pierwszą liczbę przez drugą liczbę |
||||

*Przykład*

Oba przykłady dzielą pierwszą liczbę o drugą liczbę:

```
div(10, 5)
div(11, 5)
```

I zwróć ten wynik: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeURIComponent —

Zwróć jednolity identyfikator zasobów (URI) dla ciągu przez zastępowanie znaków w adresie URL bez znaku ucieczki.
Rozważ użycie [uriComponent ()](#uriComponent), a nie `encodeUriComponent()`.
Chociaż obie funkcje działają tak samo, `uriComponent()` jest preferowane.

```
encodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg do przekonwertowania na format zakodowany przy użyciu identyfikatora URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zakodowany> URI* | Ciąg | Ciąg znaków w formacie URI z znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzona jest wersja zakodowana przy użyciu identyfikatora URI dla tego ciągu:

```
encodeUriComponent('https://contoso.com')
```

I zwraca ten wynik: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>ciągiem

Sprawdź, czy kolekcja jest pusta.
Zwraca wartość true, jeśli kolekcja jest pusta lub zwraca wartość false, jeśli nie jest pusta.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbieranie*> | Tak | Ciąg, tablica lub obiekt | Kolekcja do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli kolekcja jest pusta. Zwraca wartość false, jeśli nie jest pusta. |
||||

*Przykład*

Te przykłady sprawdzają, czy określone kolekcje są puste:

```
empty('')
empty('abc')
```

I zwraca te wyniki:

* Pierwszy przykład: przekazuje pusty ciąg, więc funkcja zwraca `true`.
* Drugi przykład: przekazuje ciąg "ABC", więc funkcja zwraca `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Sprawdź, czy ciąg jest kończący się określonym podciągiem.
Zwraca wartość true, jeśli znaleziono podciąg lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja nie uwzględnia wielkości liter.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg do sprawdzenia |
| <*tekstprzeszukiwany*> | Tak | Ciąg | Końcowy podciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ  | Wartość logiczna | Zwraca wartość true, gdy zostanie znaleziony końcowy podciąg. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza, czy ciąg "Hello World" jest zakończony ciągiem "World":

```
endsWith('hello world', 'world')
```

I zwraca ten wynik: `true`

*Przykład 2*

Ten przykład sprawdza, czy ciąg "Hello World" jest zakończony ciągiem "Universe":

```
endsWith('hello world', 'universe')
```

I zwraca ten wynik: `false`

<a name="equals"></a>

### <a name="equals"></a>ubiegł

Sprawdź, czy obie wartości, wyrażenia lub obiekty są równoważne.
Zwraca wartość true, jeśli oba są równoważne, lub zwraca wartość false, jeśli nie są równoważne.

```
equals('<object1>', '<object2>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*obiekt1*>, <*Obiekt2*> | Tak | Poszczególne | Wartości, wyrażenia lub obiekty do porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli oba są równoważne. Zwraca wartość false, jeśli nie jest równoważne. |
||||

*Przykład*

Te przykłady sprawdzają, czy określone dane wejściowe są równoważne.

```
equals(true, 1)
equals('abc', 'abcd')
```

I zwraca te wyniki:

* Pierwszy przykład: obie wartości są równoważne, więc funkcja zwraca `true`.
* Drugi przykład: obie wartości nie są równoważne, więc funkcja zwraca `false`.

<a name="first"></a>

### <a name="first"></a>pierwszego

Zwróć pierwszy element z ciągu lub tablicy.

```
first('<collection>')
first([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbieranie*> | Tak | Ciąg lub tablica | Kolekcja, w której ma zostać znaleziony pierwszy element. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *pierwszej kolekcji* | Dowolne | Pierwszy element w kolekcji |
||||

*Przykład*

W tych przykładach znaleziono pierwszy element w tych kolekcjach:

```
first('hello')
first(createArray(0, 1, 2))
```

I zwróć następujące wyniki:

* Pierwszy przykład: `"h"`
* Drugi przykład: `0`

<a name="float"></a>

### <a name="float"></a>float

Konwertuj wersję ciągu dla liczby zmiennoprzecinkowej na rzeczywistą liczbę zmiennoprzecinkową.
Tej funkcji można użyć tylko w przypadku przekazywania parametrów niestandardowych do aplikacji, na przykład aplikacji logiki lub przepływu.

```
float('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg, który ma prawidłową liczbę zmiennoprzecinkową do przekonwertowania. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość zmiennoprzecinkowa*> | Float | Liczba zmiennoprzecinkowa dla podanego ciągu. |
||||

*Przykład*

W tym przykładzie tworzona jest wersja ciągu dla tej liczby zmiennoprzecinkowej:

```
float('10.333')
```

I zwraca ten wynik: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Zwróć sygnaturę czasową w określonym formacie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <ponownie *sformatowana sygnatura czasowa*> | Ciąg | Zaktualizowana sygnatura czasowa w określonym formacie |
||||

*Przykład*

Ten przykład konwertuje sygnaturę czasową do określonego formatu:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

I zwraca ten wynik: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Zwraca tablicę z wartościami, które pasują do nazwy klucza w danych wyjściowych *formularza* akcji lub *formularza* .

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionname*> | Tak | Ciąg | Akcja, której dane wyjściowe mają żądaną wartość klucza |
| > *klucza* < | Tak | Ciąg | Nazwa klucza, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*Array-with-Key-values*>] | Tablica | Tablica ze wszystkimi wartościami, które pasują do określonego klucza |
||||

*Przykład*

W tym przykładzie tworzona jest tablica z wartości klucza "subject" w danych wyjściowych formularza z danymi lub formularza:

```
formDataMultiValues('Send_an_email', 'Subject')
```

I zwraca tekst podmiotu w tablicy, na przykład: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Zwróć pojedynczą wartość, która pasuje do nazwy klucza w danych wyjściowych *formularza* akcji lub *formularza* .
Jeśli funkcja odnajdzie więcej niż jedno dopasowanie, funkcja zgłasza błąd.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionname*> | Tak | Ciąg | Akcja, której dane wyjściowe mają żądaną wartość klucza |
| > *klucza* < | Tak | Ciąg | Nazwa klucza, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*klucz-wartość*> | Ciąg | Wartość w określonym kluczu  |
||||

*Przykład*

W tym przykładzie tworzony jest ciąg z wartości klucza "subject" w danych wyjściowych formularza określonej przez daną akcję lub formularza:

```
formDataValue('Send_an_email', 'Subject')
```

I zwraca tekst podmiotu jako ciąg, na przykład: `"Hello world"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Zwróć bieżącą sygnaturę czasową powiększoną o określoną liczbę jednostek czasu.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| *interwał* <> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem*: "sekunda", "minuta", "godzina", "dzień", "tydzień", "Month", "Year" |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Bieżąca sygnatura czasowa powiększona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-03-01T00:00:00.0000000 Z".
Ten przykład dodaje pięć dni do tej sygnatury czasowej:

```
getFutureTime(5, 'Day')
```

I zwraca ten wynik: `"2018-03-06T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-03-01T00:00:00.0000000 Z".
Ten przykład dodaje pięć dni i konwertuje wynik na format "D":

```
getFutureTime(5, 'Day', 'D')
```

I zwraca ten wynik: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Zwróć bieżącą sygnaturę czasową minus określoną liczbę jednostek czasu.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| *interwał* <> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odjęcia |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem*: "sekunda", "minuta", "godzina", "dzień", "tydzień", "Month", "Year" |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Bieżąca sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-02-01T00:00:00.0000000 Z".
Ten przykład odejmuje pięć dni od tej sygnatury czasowej:

```
getPastTime(5, 'Day')
```

I zwraca ten wynik: `"2018-01-27T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-02-01T00:00:00.0000000 Z".
Ten przykład odejmuje pięć dni i konwertuje wynik na format "D":

```
getPastTime(5, 'Day', 'D')
```

I zwraca ten wynik: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>mniejszą

Sprawdź, czy pierwsza wartość jest większa od drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest większa lub zwraca wartość false, jeśli jest mniejsza.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy większa niż druga wartość |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli pierwsza wartość jest większa niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa lub mniejsza od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest większa niż druga wartość:

```
greater(10, 5)
greater('apple', 'banana')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest większa lub równa lub zwraca wartość false, gdy pierwsza wartość jest mniejsza.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy większa lub równa drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli pierwsza wartość jest większa lub równa drugiej wartości. Zwraca wartość false, jeśli pierwsza wartość jest mniejsza od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest większa lub równa drugiej wartości:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="guid"></a>

### <a name="guid"></a>Ident

Generuj unikatowy identyfikator globalny (GUID) jako ciąg, na przykład "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ponadto można określić inny format dla identyfikatora GUID innego niż format domyślny, "D", który jest 32 cyfr oddzielonych łącznikiem.

```
guid('<format>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*format*> | Nie | Ciąg | Pojedynczy [specyfikator formatu](https://msdn.microsoft.com/library/97af8hh4) dla ZWRÓCONEGO identyfikatora GUID. Domyślnie formatem jest "D", ale można użyć "N", "D", "B", "P" lub "X". |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość identyfikatora GUID*> | Ciąg | Losowo wygenerowany identyfikator GUID |
||||

*Przykład*

Ten przykład generuje ten sam identyfikator GUID, ale jako 32 cyfr, rozdzielone łącznikami i ujęte w nawiasy:

```
guid('P')
```

I zwraca ten wynik: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>przypadku

Sprawdź, czy wyrażenie ma wartość true lub false.
W oparciu o wynik Zwraca określoną wartość.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| *wyrażenie* <> | Tak | Wartość logiczna | Wyrażenie do sprawdzenia |
| <*valueIfTrue*> | Tak | Dowolne | Wartość, która ma zostać zwrócona, gdy wyrażenie ma wartość true. |
| <*valueIfFalse*> | Tak | Dowolne | Wartość, która ma zostać zwrócona, gdy wyrażenie ma wartość false. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*określone — zwraca wartość*> | Dowolne | Określona wartość, która zwraca w zależności od tego, czy wyrażenie ma wartość Prawda czy fałsz. |
||||

*Przykład*

Ten przykład zwraca `"yes"`, ponieważ określone wyrażenie zwraca wartość true.
W przeciwnym razie przykład zwraca `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Zwraca pozycję początkową lub wartość indeksu podciągu.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg, który zawiera podciąg do znalezienia |
| <*tekstprzeszukiwany*> | Tak | Ciąg | Podciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość indeksu*>| Liczba całkowita | Pozycja początkowa lub wartość indeksu dla podanego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, Zwróć liczbę-1. |
||||

*Przykład*

W tym przykładzie znaleziono początkową wartość indeksu dla podciągu "World" w ciągu "Hello World":

```
indexOf('hello world', 'world')
```

I zwraca ten wynik: `6`

<a name="int"></a>

### <a name="int"></a>int

Zwróć wersję całkowitą dla ciągu.

```
int('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| < *— Liczba całkowita — wynik*> | Liczba całkowita | Wersja całkowita dla podanego ciągu |
||||

*Przykład*

Ten przykład tworzy wersję całkowitą dla ciągu "10":

```
int('10')
```

I zwraca ten wynik: `10`

<a name="item"></a>

### <a name="item"></a>elementów

W przypadku użycia wewnątrz powtarzającej się akcji nad tablicą zwraca bieżący element w tablicy podczas bieżącej iteracji akcji.
Możesz również pobrać wartości z właściwości tego elementu.

```
item()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*element bieżący-array*> | Dowolne | Bieżący element w tablicy dla bieżącej iteracji akcji |
||||

*Przykład*

Ten przykład pobiera `body` elementu z bieżącego komunikatu dla akcji "Send_an_email" wewnątrz bieżącej iteracji pętli for-each:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>produktów

Zwraca bieżący element z każdego cyklu w pętli for-each.
Użyj tej funkcji wewnątrz pętli for-each.

```
items('<loopName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*loopname*> | Tak | Ciąg | Nazwa pętli for-each |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| *element* <> | Dowolne | Element z bieżącego cyklu w określonej pętli for-each |
||||

*Przykład*

Ten przykład pobiera bieżący element z określonej pętli for-each:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Zwraca wartość indeksu bieżącej iteracji w pętli until. Ta funkcja może być używana wewnątrz zagnieżdżonych pętli do. 

```
iterationIndexes('<loopName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*loopname*> | Tak | Ciąg | Nazwa pętli until | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| > *indeksu* < | Liczba całkowita | Wartość indeksu bieżącej iteracji w pętli określonej do momentu | 
|||| 

*Przykład* 

Ten przykład tworzy zmienną licznika i zwiększa tę zmienną o jeden podczas każdej iteracji w pętli until do momentu osiągnięcia pięciu wartości licznika. W przykładzie tworzony jest również zmienna, która śledzi bieżący indeks dla każdej iteracji. W pętli until podczas każdej iteracji, przykład zwiększa licznik, a następnie przypisuje wartość licznika do bieżącej wartości indeksu, a następnie zwiększa licznik. W dowolnym momencie można określić bieżącą liczbę iteracji, pobierając bieżącą wartość indeksu.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>kodu

Zwróć wartość typu JavaScript Object Notation (JSON) lub obiekt dla ciągu lub XML.

```
json('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg lub XML | Ciąg lub XML do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *wyniku JSON* | Natywny typ lub obiekt JSON | Wartość lub obiekt natywnego typu JSON dla określonego ciągu lub XML. Jeśli ciąg ma wartość null, funkcja zwraca pusty obiekt. |
||||

*Przykład 1*

Ten przykład konwertuje ten ciąg na wartość JSON:

```
json('[1, 2, 3]')
```

I zwraca ten wynik: `[1, 2, 3]`

*Przykład 2*

Ten przykład konwertuje ten ciąg na format JSON:

```
json('{"fullName": "Sophia Owen"}')
```

I zwraca ten wynik:

```
{
  "fullName": "Sophia Owen"
}
```

*Przykład 3*

Ten przykład konwertuje ten kod XML na format JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

I zwraca ten wynik:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>część wspólną

Zwróć kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji.
Aby pojawił się w wyniku, element musi znajdować się we wszystkich kolekcjach przekazaniu do tej funkcji.
Jeśli co najmniej jeden element ma taką samą nazwę, w wyniku zostanie wyświetlony ostatni element o tej nazwie.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcji collection1*>, <*Collection2*>,... | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają być *tylko* wspólne elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*elementów wspólnych*> | Odpowiednio tablica lub obiekt | Kolekcja, która ma tylko wspólne elementy w określonej kolekcji |
||||

*Przykład*

Ten przykład umożliwia znalezienie typowych elementów w następujących tablicach:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

I zwraca tablicę zawierającą *tylko* te elementy: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Zwraca ciąg, który zawiera wszystkie elementy z tablicy i zawiera każdy znak oddzielony *ogranicznikiem*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbieranie*> | Tak | Tablica | Tablica zawierająca elementy do przyłączenia. |
| <*ogranicznik*> | Tak | Ciąg | Separator, który pojawia się między poszczególnymi znakami w ciągu wynikiem |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*char1*><*ogranicznik*><*CHAR2*><*ogranicznika*>... | Ciąg | Powstały ciąg utworzony na podstawie wszystkich elementów w określonej tablicy. |
||||

*Przykład*

Ten przykład tworzy ciąg ze wszystkich elementów w tej tablicy z określonym znakiem jako ogranicznikiem:

```
join(createArray('a', 'b', 'c'), '.')
```

I zwraca ten wynik: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>ostatniego

Zwróć ostatni element z kolekcji.

```
last('<collection>')
last([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbieranie*> | Tak | Ciąg lub tablica | Kolekcja, w której ma zostać znaleziony ostatni element |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ostatnią kolekcją elementów*> | Odpowiednio ciąg lub tablicę | Ostatni element w kolekcji |
||||

*Przykład*

Te przykłady znajdują ostatni element w tych kolekcjach:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

I zwraca te wyniki:

* Pierwszy przykład: `"d"`
* Drugi przykład: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Zwraca pozycję początkową lub wartość indeksu dla ostatniego wystąpienia podciągu.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg, który zawiera podciąg do znalezienia |
| <*tekstprzeszukiwany*> | Tak | Ciąg | Podciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość końcowa indeksu*> | Liczba całkowita | Pozycja początkowa lub wartość indeksu dla ostatniego wystąpienia określonego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, Zwróć liczbę-1. |
||||

*Przykład*

W tym przykładzie znaleziono początkową wartość indeksu dla ostatniego wystąpienia podciągu "World" w ciągu "Hello World":

```
lastIndexOf('hello world', 'world')
```

I zwraca ten wynik: `6`

<a name="length"></a>

### <a name="length"></a>Długość

Zwraca liczbę elementów w kolekcji.

```
length('<collection>')
length([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbieranie*> | Tak | Ciąg lub tablica | Kolekcja zawierająca elementy do zliczenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Długość lub licznik*> | Liczba całkowita | Liczba elementów w kolekcji |
||||

*Przykład*

Te przykłady zliczają liczbę elementów w tych kolekcjach:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

I zwróć ten wynik: `4`

<a name="less"></a>

### <a name="less"></a>wcześniejsz

Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy mniejsza od drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Element porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli pierwsza wartość jest mniejsza od drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest równa lub większa od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest mniejsza od drugiej wartości.

```
less(5, 10)
less('banana', 'apple')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub równa lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy mniejsza lub równa drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Element porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ  | Wartość logiczna | Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest większa niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Zwróć "adres URL wywołania zwrotnego", który wywołuje wyzwalacz lub akcję.
Ta funkcja działa tylko z wyzwalaczami i akcjami dla typów łączników **HttpWebhook** i **ApiConnectionWebhook** , ale nie z typami **ręcznymi**, **cyklem**, **http**i **APIConnection** .

```
listCallbackUrl()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wywołanie zwrotne — adres URL*> | Ciąg | Adres URL wywołania zwrotnego dla wyzwalacza lub akcji |
||||

*Przykład*

Ten przykład przedstawia przykładowy adres URL wywołania zwrotnego, który może zwrócić ta funkcja:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>Maksymalny

Zwraca najwyższą wartość z listy lub tablicy z liczbami, które są włącznie na obu końcach.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*liczba1*>, <*liczba2*>,... | Tak | Liczba całkowita, zmiennoprzecinkowa lub oba | Zbiór liczb, z których ma być najwyższa wartość |
| [<*liczba1*>, <*liczba2*>,...] | Tak | Array-Integer, float lub Both | Tablica liczb, z której ma być najwyższa wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Max-value*> | Liczba całkowita lub zmiennoprzecinkowa | Najwyższa wartość w określonej tablicy lub zestawie liczb |
||||

*Przykład*

Te przykłady uzyskują najwyższą wartość z zestawu liczb i tablicy:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

I zwróć ten wynik: `3`

<a name="min"></a>

### <a name="min"></a>min.

Zwróć najniższą wartość z zestawu liczb lub tablicy.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*liczba1*>, <*liczba2*>,... | Tak | Liczba całkowita, zmiennoprzecinkowa lub oba | Zbiór liczb, z których ma być najmniejsza wartość. |
| [<*liczba1*>, <*liczba2*>,...] | Tak | Array-Integer, float lub Both | Tablica liczb, z której ma być najmniejsza wartość. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *minimalnej wartości* | Liczba całkowita lub zmiennoprzecinkowa | Najniższa wartość w określonym zestawie liczb lub określonej tablicy. |
||||

*Przykład*

Te przykłady uzyskują najniższą wartość w zestawie liczb i tablicy:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

I zwróć ten wynik: `1`

<a name="mod"></a>

### <a name="mod"></a>Funkcja

Zwróć resztę z dzielenia dwóch liczb.
Aby uzyskać wynik całkowity, zobacz [DIV ()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dzielną*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik* |
| <*dzielnik*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba dzieląca *dzielną*, ale nie może być równa 0. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*modulo — wynik*> | Liczba całkowita lub zmiennoprzecinkowa | Reszta z dzielenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

Ten przykład dzieli pierwszą liczbę przez drugą liczbę:

```
mod(3, 2)
```

I zwróć ten wynik: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Zwróć produkt z mnożenia dwóch liczb.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do pomnożenia przez *multiplicand2* |
| <*multiplicand2*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która *multiplicand1* wielokrotność |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*produktu —> wynik* | Liczba całkowita lub zmiennoprzecinkowa | Produkt nie pomnożyć pierwszej liczby przez drugą liczbę |
||||

*Przykład*

Te przykłady wielokrotności pierwszej liczby przez drugą liczbę:

```
mul(1, 2)
mul(1.5, 2)
```

I zwróć następujące wyniki:

* Pierwszy przykład: `2`
* Drugi przykład `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Zwróć treść określonej części w danych wyjściowych akcji z wieloma częściami.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionname*> | Tak | Ciąg | Nazwa akcji, która zawiera dane wyjściowe z wieloma częściami. |
| > *indeksu* < | Tak | Liczba całkowita | Wartość indeksu dla potrzebnej części |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*treści*> | Ciąg | Treść określonej części |
||||

<a name="not"></a>

### <a name="not"></a>niemożliwe

Sprawdź, czy wyrażenie ma wartość false.
Zwraca wartość true, jeśli wyrażenie ma wartość false, lub zwraca wartość false, gdy wartość jest równa true.

```json
not(<expression>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| *wyrażenie* <> | Tak | Wartość logiczna | Wyrażenie do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli wyrażenie ma wartość false. Zwraca wartość false, jeśli wyrażenie ma wartość true. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy określone wyrażenia mają wartość false:

```json
not(false)
not(true)
```

I zwróć następujące wyniki:

* Pierwszy przykład: wyrażenie ma wartość false, więc funkcja zwraca `true`.
* Drugi przykład: wyrażenie ma wartość true, więc funkcja zwraca `false`.

*Przykład 2*

Te przykłady sprawdzają, czy określone wyrażenia mają wartość false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

I zwróć następujące wyniki:

* Pierwszy przykład: wyrażenie ma wartość false, więc funkcja zwraca `true`.
* Drugi przykład: wyrażenie ma wartość true, więc funkcja zwraca `false`.

<a name="or"></a>

### <a name="or"></a>lub

Sprawdź, czy co najmniej jedno wyrażenie ma wartość true.
Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true lub zwraca wartość false, jeśli wszystkie mają wartość false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*>,... | Tak | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Wartość logiczna | Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true. Zwraca wartość false, jeśli wszystkie wyrażenia mają wartość false. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(true, false)
or(false, false)
```

I zwróć następujące wyniki:

* Pierwszy przykład: co najmniej jedno wyrażenie ma wartość true, więc funkcja zwraca `true`.
* Drugi przykład: oba wyrażenia mają wartość false, więc funkcja zwraca `false`.

*Przykład 2*

Te przykłady sprawdzają, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

I zwróć następujące wyniki:

* Pierwszy przykład: co najmniej jedno wyrażenie ma wartość true, więc funkcja zwraca `true`.
* Drugi przykład: oba wyrażenia mają wartość false, więc funkcja zwraca `false`.

<a name="outputs"></a>

### <a name="outputs"></a>wydajności

Zwraca dane wyjściowe akcji w czasie wykonywania. Użyj tej funkcji, a nie `actionOutputs()`, która jest rozpoznawana jako `outputs()` w Projektancie aplikacji logiki. Chociaż obie funkcje działają tak samo, `outputs()` jest preferowane.

```
outputs('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionname*> | Tak | Ciąg | Nazwa żądanego danych wyjściowych akcji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*dane wyjściowe*> | Ciąg | Dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera dane wyjściowe z akcji usługi Twitter `Get user`:

```
outputs('Get_user')
```

I zwraca ten wynik:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Zwraca wartość parametru, który jest opisany w definicji przepływu pracy.

```
parameters('<parameterName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| *parametr <parametername*> | Tak | Ciąg | Nazwa parametru, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*parametru-value*> | Dowolne | Wartość dla określonego parametru |
||||

*Przykład*

Załóżmy, że masz tę wartość JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Ten przykład pobiera wartość dla określonego parametru:

```
parameters('fullName')
```

I zwraca ten wynik: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>Rand

Zwraca losową liczbę całkowitą z podanego zakresu, która jest dopuszczająca tylko do końca początkowego.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Tak | Liczba całkowita | Najmniejsza liczba całkowita z zakresu |
| <*maxValue*> | Tak | Liczba całkowita | Liczba całkowita, która następuje po największej liczbie całkowitej z zakresu, który może zwracać funkcja |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *wyniku losowego* | Liczba całkowita | Losowa liczba całkowita zwrócona z określonego zakresu |
||||

*Przykład*

Ten przykład pobiera losową liczbę całkowitą z podanego zakresu, z wyłączeniem wartości maksymalnej:

```
rand(1, 5)
```

I zwraca jedną z tych liczb jako wynik: `1`, `2`, `3`lub `4`

<a name="range"></a>

### <a name="range"></a>zakresu

Zwróć tablicę liczb całkowitych, która zaczyna się od określonej liczby całkowitej.

```
range(<startIndex>, <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Tak | Liczba całkowita | Wartość całkowita, która uruchamia tablicę jako pierwszy element |
| *liczba* <> | Tak | Liczba całkowita | Liczba liczb całkowitych w tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*zakres — wynik*>] | Tablica | Tablica z liczbami całkowitymi rozpoczynającymi się od określonego indeksu |
||||

*Przykład*

Ten przykład tworzy tablicę liczb całkowitych, która zaczyna się od określonego indeksu i ma określoną liczbę liczb całkowitych:

```
range(1, 4)
```

I zwraca ten wynik: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>stępować

Zamień podciąg na określony ciąg i zwróć ciąg wynikowy. Ta funkcja uwzględnia wielkość liter.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg zawierający podciąg, który ma zostać zamieniony. |
| <*oldText*> | Tak | Ciąg | Podciąg, który ma zostać zamieniony. |
| <*newText*> | Tak | Ciąg | Ciąg zamienny |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany —> tekstu* | Ciąg | Zaktualizowany ciąg po zamianie podciągu <p>Jeśli podciąg nie zostanie znaleziony, zwróć oryginalny ciąg. |
||||

*Przykład*

Ten przykład wyszukuje "stary" podciąg w "starym ciągu" i zamienia "stary" z "New":

```
replace('the old string', 'old', 'new')
```

I zwraca ten wynik: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Usunięcie właściwości z obiektu i zwrócenie zaktualizowanego obiektu. Jeśli właściwość, którą próbujesz usunąć, nie istnieje, funkcja zwraca oryginalny obiekt.

```
removeProperty(<object>, '<property>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > <*obiektu* | Tak | Obiekt | Obiekt JSON, z którego chcesz usunąć Właściwość |
| <*właściwość*> | Tak | Ciąg | Nazwa właściwości do usunięcia. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Zaktualizowano —> obiektu* | Obiekt | Zaktualizowany obiekt JSON bez określonej właściwości |
||||

Aby usunąć Właściwość podrzędną z istniejącej właściwości, użyj następującej składni:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > <*obiektu* | Tak | Obiekt | Obiekt JSON, którego właściwość ma zostać usunięta. |
| <*Właściwość parent*> | Tak | Ciąg | Nazwa właściwości nadrzędnej z właściwością podrzędną, która ma zostać usunięta. |
| <*Właściwość podrzędna*> | Tak | Ciąg | Nazwa właściwości podrzędnej do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Zaktualizowano —> obiektu* | Obiekt | Zaktualizowany obiekt JSON, którego usunięto Właściwość podrzędną. |
||||

*Przykład 1*

Ten przykład usuwa Właściwość `middleName` z obiektu JSON, który jest konwertowany z ciągu na notację JSON przy użyciu funkcji [JSON ()](#json) i zwraca zaktualizowany obiekt:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Oto bieżący obiekt JSON:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Przykład 2*

Ten przykład usuwa Właściwość podrzędną `middleName` z `customerName` właściwości nadrzędnej w obiekcie JSON, który jest konwertowany z ciągu na notację JSON przy użyciu funkcji [JSON ()](#json) i zwraca zaktualizowany obiekt:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Oto bieżący obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>wynika

Zwraca dane wejściowe i wyjściowe ze wszystkich akcji, które znajdują się w określonej akcji w zakresie, takiej jak akcja `For_each`, `Until`lub `Scope`. Ta funkcja jest przydatna zwracająca wyniki akcji zakończonej niepowodzeniem, aby można było zdiagnozować i obsłużyć wyjątki. Aby uzyskać więcej informacji, zobacz [pobieranie kontekstu i wyników dla niepowodzeń](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Tak | Ciąg | Nazwa akcji z zakresem, z której mają zostać zwrócone dane wejściowe i wyjściowe ze wszystkich akcji wewnętrznych |
||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*obiektu array*> | Array — obiekt | Tablica zawierająca tablice danych wejściowych i wyjść z poszczególnych akcji, które pojawiają się w określonej akcji w zakresie |
||||

*Przykład*

Ten przykład zwraca dane wejściowe i wyjściowe z każdej iteracji akcji HTTP wewnątrz pętli `For_each` przy użyciu funkcji `result()` w akcji `Compose`:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Poniżej przedstawiono sposób, w jaki Przykładowa zwracana tablica może wyglądać, gdzie zewnętrzny obiekt `outputs` zawiera dane wejściowe i wyjściowe z każdej iteracji akcji wewnątrz akcji `For_each`.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Ustaw wartość właściwości obiektu JSON i zwróć zaktualizowany obiekt. Jeśli właściwość, którą próbujesz ustawić, nie istnieje, właściwość zostanie dodana do obiektu. Aby dodać nową właściwość, użyj funkcji [AddProperty ()](#addProperty) .

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > <*obiektu* | Tak | Obiekt | Obiekt JSON, którego właściwość ma zostać ustawiona. |
| <*właściwość*> | Tak | Ciąg | Nazwa istniejącej lub nowej właściwości do ustawienia |
| <*wartość*> | Tak | Dowolne | Wartość do ustawienia dla określonej właściwości |
|||||

Aby ustawić właściwość podrzędną w obiekcie podrzędnym, należy zamiast tego użyć zagnieżdżonego wywołania `setProperty()`. W przeciwnym razie funkcja zwraca tylko obiekt podrzędny jako dane wyjściowe.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > <*obiektu* | Tak | Obiekt | Obiekt JSON, którego właściwość ma zostać ustawiona. |
| <*Właściwość parent*> | Tak | Ciąg | Nazwa właściwości nadrzędnej z właściwością podrzędną, która ma zostać ustawiona. |
| <*Właściwość podrzędna*> | Tak | Ciąg | Nazwa właściwości podrzędnej do ustawienia |
| <*wartość*> | Tak | Dowolne | Wartość do ustawienia dla określonej właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Zaktualizowano —> obiektu* | Obiekt | Zaktualizowany obiekt JSON, którego właściwość została ustawiona |
||||

*Przykład 1*

Ten przykład ustawia właściwość `surName` w obiekcie JSON, który jest konwertowany z ciągu na notację JSON przy użyciu funkcji [JSON ()](#json) . Funkcja przypisuje określoną wartość do właściwości i zwraca zaktualizowany obiekt:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Oto bieżący obiekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Przykład 2*

Ten przykład ustawia właściwość podrzędną `surName` dla właściwości Parent `customerName` w obiekcie JSON, który jest konwertowany z ciągu na notację JSON przy użyciu funkcji [JSON ()](#json) . Funkcja przypisuje określoną wartość do właściwości i zwraca zaktualizowany obiekt:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Oto bieżący obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>Skocz

Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy.

```
skip([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbieranie*> | Tak | Tablica | Kolekcja, której elementy chcesz usunąć. |
| *liczba* <> | Tak | Liczba całkowita | Dodatnia liczba całkowita liczby elementów do usunięcia na przedniej |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*Zaktualizowano — zbieranie*>] | Tablica | Zaktualizowana kolekcja po usunięciu określonych elementów |
||||

*Przykład*

Ten przykład usuwa jeden element, liczbę 0 z przodu określonej tablicy:

```
skip(createArray(0, 1, 2, 3), 1)
```

I zwraca tę tablicę z pozostałymi elementami: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Zwraca tablicę zawierającą podciągi, rozdzieloną przecinkami, na podstawie określonego znaku ogranicznika w oryginalnym ciągu.

```
split('<text>', '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg do rozdzielenia na podciągi na podstawie określonego ogranicznika w oryginalnym ciągu |
| <*ogranicznik*> | Tak | Ciąg | Znak w oryginalnym ciągu, który ma być używany jako ogranicznik |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*podciąg1*>, <*subciąg2*>,...] | Tablica | Tablica zawierająca podciągi z oryginalnego ciągu, rozdzielona przecinkami |
||||

*Przykład*

Ten przykład tworzy tablicę z podciągami z określonego ciągu na podstawie określonego znaku jako ogranicznika:

```
split('a_b_c', '_')
```

I zwraca tę tablicę jako wynik: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Zwróć początek dnia dla sygnatury czasowej.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Określona sygnatura czasowa, ale rozpoczynająca się od znaku zero godziny na dzień |
||||

*Przykład*

Ten przykład umożliwia znalezienie początku dnia dla tej sygnatury czasowej:

```
startOfDay('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>Początek godziny

Zwróć początek godziny dla sygnatury czasowej.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Określona sygnatura czasowa, ale rozpoczynająca się od znaku 0 minuty dla godziny |
||||

*Przykład*

Ten przykład umożliwia znalezienie początku godziny dla tej sygnatury czasowej:

```
startOfHour('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Zwróć początek miesiąca dla sygnatury czasowej.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Określona sygnatura czasowa, ale rozpoczyna się pierwszego dnia miesiąca w znaku 0-godzinnym |
||||

*Przykład*

Ten przykład zwraca początek miesiąca dla tej sygnatury czasowej:

```
startOfMonth('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Sprawdź, czy ciąg rozpoczyna się od określonego podciągu.
Zwraca wartość true, jeśli znaleziono podciąg lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja nie uwzględnia wielkości liter.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg do sprawdzenia |
| <*tekstprzeszukiwany*> | Tak | Ciąg | Ciąg początkowy do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ  | Wartość logiczna | Zwraca wartość true, gdy zostanie znaleziony początkowy podciąg. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza, czy ciąg "Hello World" rozpoczyna się od ciągu "Hello":

```
startsWith('hello world', 'hello')
```

I zwraca ten wynik: `true`

*Przykład 2*

Ten przykład sprawdza, czy ciąg "Hello World" rozpoczyna się od ciągu "Greetings":

```
startsWith('hello world', 'greetings')
```

I zwraca ten wynik: `false`

<a name="string"></a>

### <a name="string"></a>string

Zwraca wersję ciągu dla wartości.

```
string(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość ciągu*> | Ciąg | Wersja ciągu dla określonej wartości. |
||||

*Przykład 1*

W tym przykładzie zostanie utworzona wersja ciągu dla tej liczby:

```
string(10)
```

I zwraca ten wynik: `"10"`

*Przykład 2*

Ten przykład tworzy ciąg dla określonego obiektu JSON i używa znaku ukośnika odwrotnego (\\) jako znaku ucieczki dla znaku podwójnego cudzysłowu (").

```
string( { "name": "Sophie Owen" } )
```

I zwraca ten wynik: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Zwraca wynik odejmowania drugiej liczby od pierwszej liczby.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*odjemna*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, z której ma zostać odjęta *odjemnik* |
| <*odjemnik*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która ma zostać odjęta od *odjemna* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *wyników* | Liczba całkowita lub zmiennoprzecinkowa | Wynik odejmowania drugiej liczby od pierwszej liczby |
||||

*Przykład*

Ten przykład odejmuje drugą liczbę od pierwszej liczby:

```
sub(10.3, .3)
```

I zwraca ten wynik: `10`

<a name="substring"></a>

### <a name="substring"></a>podciąg

Zwraca znaki z ciągu, zaczynając od określonej pozycji lub indeksu.
Wartości indeksu zaczynają się od liczby 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg, którego znaki mają być potrzebne. |
| <*startIndex*> | Tak | Liczba całkowita | Liczba dodatnia równa lub większa od 0, która ma być używana jako pozycja początkowa lub wartość indeksu. |
| *długość* <> | Tak | Liczba całkowita | Dodatnia liczba znaków, które mają być używane w podciągu. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*podciąg — wynik*> | Ciąg | Podciąg z określoną liczbą znaków, zaczynając od określonej pozycji indeksu w ciągu źródłowym. |
||||

*Przykład*

Ten przykład tworzy znak podciągu pięć znaków z określonego ciągu, rozpoczynając od wartości indeksu 6:

```
substring('hello world', 6, 5)
```

I zwraca ten wynik: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Odejmij wiele jednostek czasu od sygnatury czasowej.
Zobacz również [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| *interwał* <> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odjęcia |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem*: "sekunda", "minuta", "godzina", "dzień", "tydzień", "Month", "Year" |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *sygnatura czasowa aktualizacji* | Ciąg | Sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Ten przykład odejmuje jeden dzień od tej sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik: `"2018-01-01T00:00:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje jeden dzień od tej sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

I zwraca ten wynik przy użyciu opcjonalnego formatu "D": `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>czasochłonn

Zwróć elementy z przodu kolekcji.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbieranie*> | Tak | Ciąg lub tablica | Kolekcja, której elementy chcesz |
| *liczba* <> | Tak | Liczba całkowita | Dodatnia liczba całkowita liczby elementów, które mają być od przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| *podzbiór*<> lub [*podzbiór*< >] | Odpowiednio ciąg lub tablicę | Ciąg lub tablica, która ma określoną liczbę elementów pobranych z przodu oryginalnej kolekcji |
||||

*Przykład*

Te przykłady pobierają określoną liczbę elementów z przodu tych kolekcji:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

I zwróć następujące wyniki:

* Pierwszy przykład: `"abc"`
* Drugi przykład: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>taktów

Zwróć `ticks` wartość właściwości dla określonego sygnatury czasowej.
*Cykl* jest interwałem 100-nanosekund.

```
ticks('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*sygnatura czasowa*> | Tak | Ciąg | Ciąg dla sygnatury czasowej |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Takty — liczba*> | Liczba całkowita | Liczba taktów od określonej sygnatury czasowej |
||||

<a name="toLower"></a>

### <a name="tolower"></a>ToLower

Zwraca ciąg w formacie małymi literami. Jeśli znak w ciągu nie ma małych wersji, ten znak pozostaje niezmieniony w zwracanym ciągu.

```
toLower('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg do zwrócenia w formacie małymi literami |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*małe> tekstu* | Ciąg | Oryginalny ciąg w formacie małymi literami |
||||

*Przykład*

Ten przykład konwertuje ten ciąg na małe litery:

```
toLower('Hello World')
```

I zwraca ten wynik: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>ToUpper

Zwraca ciąg w formacie wielką literą. Jeśli znak w ciągu nie ma Wielkiej wersji, ten znak pozostaje niezmieniony w zwracanym ciągu.

```
toUpper('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg do zwrócenia w formacie wielką literą |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wielkie> tekstu* | Ciąg | Oryginalny ciąg w formacie wielką literą |
||||

*Przykład*

Ten przykład konwertuje ten ciąg na wielkie litery:

```
toUpper('Hello World')
```

I zwraca ten wynik: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Uruchamiać

Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia.

* W danych wejściowych wyzwalacza ta funkcja zwraca dane wyjściowe z poprzedniego wykonania.

* W warunku wyzwalacza ta funkcja zwraca dane wyjściowe z bieżącego wykonania.

Domyślnie funkcja odwołuje się do całego obiektu wyzwalacza, ale można opcjonalnie określić właściwość, której wartość chcesz.
Ponadto ta funkcja ma dostępne wersje skrócone, zobacz [triggerOutputs ()](#triggerOutputs) i [triggerBody ()](#triggerBody).

```
trigger()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wyzwalacz-wyjście*> | Ciąg | Dane wyjściowe wyzwalacza w czasie wykonywania |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Zwraca dane wyjściowe wyzwalacza `body` w czasie wykonywania.
Skrót `trigger().outputs.body`.
Zobacz [wyzwalacz ()](#trigger).

```
triggerBody()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| *wyzwalacz <— dane wyjściowe*> | Ciąg | Dane wyjściowe `body` z wyzwalacza |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Zwraca tablicę z wartościami, które pasują do nazwy klucza w *danych* wyjściowych formularza wyzwalacza *lub formularza.*

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > *klucza* < | Tak | Ciąg | Nazwa klucza, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*Array-with-Key-values*>] | Tablica | Tablica ze wszystkimi wartościami, które pasują do określonego klucza |
||||

*Przykład*

Ten przykład tworzy tablicę z wartości klucza "feedUrl" w danych wyjściowych formularza wyzwalacza RSS lub formularza:

```
triggerFormDataMultiValues('feedUrl')
```

I zwraca tę tablicę jako przykładowy wynik: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Zwraca ciąg z pojedynczą wartością, która pasuje do nazwy klucza w danych wyjściowych *formularza* wyzwalacza lub *formularza.*
Jeśli funkcja odnajdzie więcej niż jedno dopasowanie, funkcja zgłasza błąd.

```
triggerFormDataValue('<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > *klucza* < | Tak | Ciąg | Nazwa klucza, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*klucz-wartość*> | Ciąg | Wartość w określonym kluczu |
||||

*Przykład*

Ten przykład tworzy ciąg z wartości klucza "feedUrl" w danych wyjściowych formularza wyzwalacza RSS lub formularza:

```
triggerFormDataValue('feedUrl')
```

I zwraca ten ciąg jako przykładowy wynik: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Zwróć treść określonej części w danych wyjściowych wyzwalacza, który ma wiele części.

```
triggerMultipartBody(<index>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > *indeksu* < | Tak | Liczba całkowita | Wartość indeksu dla potrzebnej części |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*treści*> | Ciąg | Treść określonej części w wyzwalaczu z wieloczęściowym wynikiem |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON.
Skrót `trigger().outputs`.
Zobacz [wyzwalacz ()](#trigger).

```
triggerOutputs()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wyzwalacz-wyjście*> | Ciąg | Dane wyjściowe wyzwalacza w czasie wykonywania  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trim

Usuń spacje wiodące i końcowe z ciągu, a następnie Zwróć zaktualizowany ciąg.

```
trim('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *tekstu* | Tak | Ciąg | Ciąg, który zawiera spacje wiodące i końcowe do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updatedText*> | Ciąg | Zaktualizowana wersja oryginalnego ciągu bez spacji wiodących lub końcowych |
||||

*Przykład*

Ten przykład usuwa spacje wiodące i końcowe z ciągu "Hello world":

```
trim(' Hello World  ')
```

I zwraca ten wynik: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unii

Zwraca kolekcję zawierającą *wszystkie* elementy z określonych kolekcji.
Aby pojawił się w wyniku, element może pojawić się w dowolnej kolekcji przekazaną do tej funkcji. Jeśli co najmniej jeden element ma taką samą nazwę, w wyniku zostanie wyświetlony ostatni element o tej nazwie.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcji collection1*>, <*Collection2*>,...  | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają być *wszystkie* elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowana*> | Odpowiednio tablica lub obiekt | Kolekcja zawierająca wszystkie elementy z określonych kolekcji — brak duplikatów |
||||

*Przykład*

Ten przykład pobiera *wszystkie* elementy z tych kolekcji:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

I zwraca ten wynik: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Zwróć jednolity identyfikator zasobów (URI) dla ciągu przez zastępowanie znaków w adresie URL bez znaku ucieczki.
Użyj tej funkcji zamiast [encodeURIComponent — ()](#encodeUriComponent).
Chociaż obie funkcje działają tak samo, `uriComponent()` jest preferowane.

```
uriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg do przekonwertowania na format zakodowany przy użyciu identyfikatora URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zakodowany> URI* | Ciąg | Ciąg znaków w formacie URI z znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzona jest wersja zakodowana przy użyciu identyfikatora URI dla tego ciągu:

```
uriComponent('https://contoso.com')
```

I zwraca ten wynik: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Zwróć wersję binarną dla składnika Uniform Resource Identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg zakodowany przy użyciu identyfikatora URI do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *identyfikatorów URI w formacie binarnym* | Ciąg | Wersja binarna dla ciągu zakodowanego przy użyciu identyfikatora URI. Zawartość binarna jest zakodowana algorytmem Base64 i reprezentowana przez `$content`. |
||||

*Przykład*

W tym przykładzie tworzona jest wersja binarna dla tego ciągu zakodowanego przy użyciu identyfikatora URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Zwróć wersję ciągu dla zakodowanego ciągu identyfikatora URI (Uniform Resource Identifier) w celu efektywnego dekodowania ciągu zakodowanego przy użyciu identyfikatora URI.

```
uriComponentToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg zakodowany przy użyciu identyfikatora URI, który ma zostać zdekodowany |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowana-uri*> | Ciąg | Zdekodowana wersja dla ciągu zakodowanego przy użyciu identyfikatora URI |
||||

*Przykład*

Ten przykład tworzy zdekodowaną wersję ciągu dla tego ciągu kodowanego URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Zwróć wartość `host` dla identyfikatora URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *URI* | Tak | Ciąg | Identyfikator URI, którego wartość `host` chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość hosta*> | Ciąg | Wartość `host` dla określonego identyfikatora URI |
||||

*Przykład*

Ten przykład umożliwia znalezienie wartości `host` dla tego identyfikatora URI:

```
uriHost('https://www.localhost.com:8080')
```

I zwraca ten wynik: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Zwróć wartość `path` dla identyfikatora URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *URI* | Tak | Ciąg | Identyfikator URI, którego wartość `path` chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Path-value*> | Ciąg | Wartość `path` dla określonego identyfikatora URI. Jeśli `path` nie ma wartości, zwróć znak "/". |
||||

*Przykład*

Ten przykład umożliwia znalezienie wartości `path` dla tego identyfikatora URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Zwróć wartości `path` i `query` dla identyfikatora URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *URI* | Tak | Ciąg | Identyfikator URI, którego wartości `path` i `query` mają być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| *ścieżka <—> wartości zapytania* | Ciąg | `path` i `query` wartości dla określonego identyfikatora URI. Jeśli `path` nie określa wartości, zwróć znak "/". |
||||

*Przykład*

Ten przykład umożliwia znalezienie wartości `path` i `query` dla tego identyfikatora URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Zwróć wartość `port` dla identyfikatora URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *URI* | Tak | Ciąg | Identyfikator URI, którego wartość `port` chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *wartości portu* | Liczba całkowita | Wartość `port` dla określonego identyfikatora URI. Jeśli `port` nie określi wartości, zwróć domyślny port dla protokołu. |
||||

*Przykład*

Ten przykład zwraca wartość `port` dla tego identyfikatora URI:

```
uriPort('http://www.localhost:8080')
```

I zwraca ten wynik: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Zwróć wartość `query` dla identyfikatora URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *URI* | Tak | Ciąg | Identyfikator URI, którego wartość `query` chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *wartości zapytania* | Ciąg | Wartość `query` dla określonego identyfikatora URI |
||||

*Przykład*

Ten przykład zwraca wartość `query` dla tego identyfikatora URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Zwróć wartość `scheme` dla identyfikatora URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <> *URI* | Tak | Ciąg | Identyfikator URI, którego wartość `scheme` chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*schemat-wartość*> | Ciąg | Wartość `scheme` dla określonego identyfikatora URI |
||||

*Przykład*

Ten przykład zwraca wartość `scheme` dla tego identyfikatora URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Zwróć bieżącą sygnaturę czasową.

```
utcNow('<format>')
```

Opcjonalnie można określić inny format z parametrem > <*Format*.


| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*format*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa current*> | Ciąg | Bieżąca data i godzina |
||||

*Przykład 1*

Załóżmy, że dzisiaj to 15 kwietnia 2018 o 1:00:00 PM.
Ten przykład pobiera bieżącą sygnaturę czasową:

```
utcNow()
```

I zwraca ten wynik: `"2018-04-15T13:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że dzisiaj to 15 kwietnia 2018 o 1:00:00 PM.
Ten przykład pobiera bieżącą sygnaturę czasową przy użyciu opcjonalnego formatu "D":

```
utcNow('D')
```

I zwraca ten wynik: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>modyfikacj

Zwraca wartość dla określonej zmiennej.

```
variables('<variableName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zmiennaname*> | Tak | Ciąg | Nazwa zmiennej, której wartość chcesz wykonać. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość zmiennej*> | Dowolne | Wartość dla określonej zmiennej |
||||

*Przykład*

Załóżmy, że bieżąca wartość dla zmiennej "numItems" wynosi 20.
Ten przykład pobiera wartość całkowitą dla tej zmiennej:

```
variables('numItems')
```

I zwraca ten wynik: `20`

<a name="workflow"></a>

### <a name="workflow"></a>przepływ pracy

Zwróć wszystkie szczegóły dotyczące przepływu pracy w czasie wykonywania.

```
workflow().<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*właściwość*> | Nie | Ciąg | Nazwa właściwości przepływu pracy, której wartość ma być <p>Obiekt przepływu pracy ma następujące właściwości: **name**, **Type**, **ID**, **Location**i **Run**. Wartość właściwości **Run** jest również obiektem, który ma następujące właściwości: **name**, **Type**i **ID**. |
|||||

*Przykład*

Ten przykład zwraca nazwę bieżącego przebiegu przepływu pracy:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Zwróć wersję XML dla ciągu, który zawiera obiekt JSON.

```
xml('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Tak | Ciąg | Ciąg z obiektem JSON do przekonwertowania <p>Obiekt JSON musi mieć tylko jedną właściwość root, która nie może być tablicą. <br>Użyj znaku ukośnika odwrotnego (\\) jako znaku ucieczki dla podwójnego cudzysłowu ("). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> w *wersji XML* | Obiekt | Zakodowany kod XML dla określonego ciągu lub obiektu JSON. |
||||

*Przykład 1*

W tym przykładzie jest tworzona wersja XML dla tego ciągu, który zawiera obiekt JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

I zwraca ten wynik XML:

```xml
<name>Sophia Owen</name>
```

*Przykład 2*

Załóżmy, że masz ten obiekt JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Ten przykład tworzy kod XML dla ciągu, który zawiera ten obiekt JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

I zwraca ten wynik XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Lokalizacja

Sprawdź XML dla węzłów lub wartości, które pasują do wyrażenia XPath (język ścieżki XML) i zwracają pasujące węzły lub wartości. Wyrażenie XPath lub "XPath" ułatwia nawigowanie po strukturze dokumentu XML, dzięki czemu można wybrać węzły lub wartości obliczeniowe w zawartości XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| > <*XML* | Tak | Dowolne | Ciąg XML służący do wyszukiwania węzłów lub wartości, które pasują do wartości wyrażenia XPath. |
| <> *XPath* | Tak | Dowolne | Wyrażenie XPath używane do znajdowania pasujących węzłów lub wartości XML |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <> *węzła XML* | DOKUMENT | Węzeł XML, gdy tylko jeden węzeł pasuje do określonego wyrażenia XPath |
| <*wartość*> | Dowolne | Wartość z węzła XML, gdy tylko jedna wartość jest zgodna z określonym wyrażeniem XPath |
| [<*XML-węzeł1*>, <*xml-Węzeł2*>,...] </br>— lub — </br>[<*wartość1*>, <*wartość2*>,...] | Tablica | Tablica z węzłami lub wartościami XML, które pasują do określonego wyrażenia XPath |
||||

*Przykład 1*

Ten przykład umożliwia znalezienie węzłów, które pasują do węzła `<name></name>` w określonych argumentach, i zwraca tablicę z tymi wartościami węzła:

`xpath(xml(parameters('items')), '/produce/item/name')`

Oto argumenty:

* Ciąg "Items", który zawiera ten kod XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  W przykładzie użyto funkcji [Parameters ()](#parameters) , aby uzyskać ciąg XML z argumentu "Items", ale musi także przekonwertować ciąg na format XML przy użyciu funkcji [XML ()](#xml) .

* To wyrażenie XPath, które jest przesyłane jako ciąg:

  `"/produce/item/name"`

Oto tablica wyników z węzłami, które pasują `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Przykład 2*

Jak pokazano na przykładzie 1, ten przykład odnajduje węzły, które pasują do węzła `<count></count>` i dodaje te wartości węzłów za pomocą funkcji `sum()`:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

I zwraca ten wynik: `30`

*Przykład 3*

W tym przykładzie oba wyrażenia znajdą węzły, które pasują do węzła `<location></location>`, w określonych argumentach, które zawierają XML z przestrzenią nazw. Wyrażenia używają znaku ukośnika odwrotnego (\\) jako znaku ucieczki dla podwójnego cudzysłowu (").

* *Wyrażenie 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Wyrażenie 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Oto argumenty:

* Ten plik XML, który zawiera przestrzeń nazw dokumentu XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Wyrażenie XPath tutaj:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Oto węzeł wyniku pasujący do węzła `<location></location>`:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Przykład 4*

Poniższy przykład znajduje się w tym przykładzie w węźle `<location></location>`:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

I zwraca ten wynik: `"Paris"`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [języku definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)
