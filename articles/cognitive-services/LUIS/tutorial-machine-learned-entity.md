---
title: 'Samouczek: wyodrębnianie danych strukturalnych przy użyciu jednostki z obsługą maszyn — LUIS'
titleSuffix: Azure Cognitive Services
description: Wyodrębnij dane strukturalne z wypowiedź przy użyciu jednostki, która jest pouczenia maszynowego. Aby zwiększyć dokładność wyodrębniania, Dodaj podskładniki z deskryptorami i ograniczeniami.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 2d3bffd025d484ac928e21003b7cba9c63d2c514
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885959"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Samouczek: wyodrębnianie danych strukturalnych z wypowiedź użytkownika z jednostkami maszynowymi w Language Understanding (LUIS)

W tym samouczku Wyodrębnij dane strukturalne z wypowiedź przy użyciu jednostki, która jest poznania maszynowego.

Jednostka uczenia maszynowego obsługuje [koncepcję dekompozycji modelu](luis-concept-model.md#v3-authoring-model-decomposition) przez zapewnienie jednostkowych elementów z własnymi deskryptorami i ograniczeniami.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Dodaj jednostkę uczenia maszynowego
> * Dodaj podskładnik
> * Dodaj deskryptor podskładnika
> * Dodaj ograniczenie podskładnika
> * Szkolenie aplikacji
> * Aplikacja testowa
> * Publikowanie aplikacji
> * Pobierz prognozowanie jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Dlaczego warto używać jednostki z obsługą maszyn?

W tym samouczku dodano jednostkę uczenia maszynowego w celu wyodrębnienia danych z wypowiedź.

Celem jednostki jest zdefiniowanie danych do wyodrębnienia. Obejmuje to nadanie danych nazwy, typu (jeśli to możliwe), dowolnej rozdzielczości danych, jeśli występuje niejednoznaczność i dokładny tekst, który tworzy dane.

W celu zdefiniowania jednostki należy utworzyć jednostkę, a następnie oznaczyć tekst reprezentujący jednostkę w przykładzie wypowiedź. Te przykładowe przykłady uczyją LUIS, czym jest jednostka i gdzie można ją znaleźć w wypowiedź.

## <a name="entity-decomposability-is-important"></a>Odtworzenie jednostek jest ważne

Odtworzenie jednostek jest ważne w przypadku prognozowania intencji i wyodrębnienia danych.

Zacznij od jednostki uczeniej maszynowej, która jest jednostką początkową i najwyższego poziomu na potrzeby wyodrębniania danych. Następnie Rozłóż jednostkę do części wymaganych przez aplikację kliencką.

Mimo że użytkownik może nie wiedzieć, w jaki sposób chcesz, aby Twoja jednostka była uruchamiana w aplikacji, najlepszym rozwiązaniem jest rozpoczęcie od jednostki, która jest obsługiwana przez maszynę, a następnie rozdzielenia z podskładnikami jako dojrzałą aplikację.

W praktyce można utworzyć jednostkę uczenia maszynowego do reprezentowania zamówienia dla aplikacji Pizza. Kolejność powinna zawierać wszystkie części, które są niezbędne do fullfil zamówienia. Aby rozpocząć, jednostka wyodrębni tekst związany z kolejnością, pobierając rozmiar i ilość.

Wypowiedź dla `Please deliver one large cheese pizza to me` powinien wyodrębnić `one large cheese pizza` jako kolejność, a następnie wyodrębnić `1` i `large`.

Istnieje dalsze dekompozycje, które można dodać, takich jak tworzenie podskładników dla toppings lub crust. Po tym samouczku należy zaczuć się, że należy dodać te podskładniki do istniejącej jednostki `Order`.

## <a name="import-example-json-to-begin-app"></a>Importuj plik example. JSON, aby rozpocząć aplikację

1.  Pobierz i Zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Oznacz tekst jako jednostki na przykład wyrażenia długości

Aby wyodrębnić szczegółowe informacje o Pizza kolejności, Utwórz jednostkę `Order`ą najwyższego poziomu.

1. Na stronie **intencje** wybierz zamiar **OrderPizza** .

1. Na liście przykład wyrażenia długości wybierz następujące wypowiedź.

    |Przykład kolejności wypowiedź|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Zacznij wybierać tuż przed lewym tekstem `pickup` (#1), a następnie przejdź bezpośrednio do tekstu znajdującego się po prawej stronie, `anchovies` (#2 — to kończy proces etykietowania). Zostanie wyświetlone menu podręczne. W oknie podręcznym wprowadź nazwę jednostki jako `Order` (#3). Następnie wybierz pozycję `Order - Create new entity` z listy (#4).

    ![Etykieta początku i końca tekstu dla kompletnego zamówienia](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Jednostka nie zawsze będzie całą wypowiedź. W tym konkretnym przypadku `pickup` wskazuje, w jaki sposób zamówienie ma zostać odebrane. Z perspektywy koncepcyjnej `pickup` powinna być częścią oznaczonej jednostki dla zamówienia.

1. W polu **Wybierz typ jednostki** wybierz pozycję **Dodaj strukturę** , a następnie wybierz przycisk **dalej**. Struktura jest niezbędna do dodawania podskładników, takich jak rozmiar i ilość.

    ![Dodaj strukturę do jednostki](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. W polu **Utwórz jednostkę uczenia maszynowego** w polu **Struktura** Dodaj `Size` a następnie wybierz pozycję Wprowadź.
1. Aby dodać **deskryptor**, wybierz `+` w **deskryptorach dla obszaru rozmiaru** , a następnie wybierz pozycję **Utwórz nową listę fraz**.

1. W polu **deskryptor Utwórz nową listę fraz** wpisz nazwę `SizeDescriptor` następnie wprowadź wartości: `small`, `medium`i `large`. Po wypełnieniu pola **sugestie** wybierz pozycję `extra large`i `xl`. Wybierz pozycję **gotowe** , aby utworzyć nową listę fraz.

    Ten deskryptor listy wyrazów pomaga `Size` podskładnikom Znajdź wyrazy związane z rozmiarem, dostarczając je za pomocą przykładowych wyrazów. Ta lista nie musi zawierać każdego wyrazu o rozmiarze, ale powinna zawierać słowa, które powinny wskazywać rozmiar.

    ![Utwórz deskryptor dla podskładnika size](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. W oknie **Utwórz maszynę** dodaną do obiektu wybierz pozycję **Utwórz** , aby zakończyć tworzenie podskładnika `Size`.

    Jednostka `Order` ze składnikiem `Size` jest tworzona, ale tylko jednostka `Order` została zastosowana do wypowiedź. Należy oznaczyć `Size` tekst jednostki w przykładzie wypowiedź.

1. W tym samym przykładzie wypowiedź należy oznaczyć podskładnik **rozmiaru** `large`, wybierając wyraz, a następnie wybierając jednostkę **rozmiaru** z listy rozwijanej.

    ![Oznacz jednostkę rozmiaru dla tekstu w wypowiedź.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    Wiersz jest pełny pod tekstem, ponieważ zarówno dopasowanie etykietowania, jak i przewidywania, ponieważ tekst został jawnie oznaczony etykietą.

1. Oznacz jednostkę `Order` w pozostałej wyrażenia długości wraz z jednostką rozmiar. Nawiasy kwadratowe w tekście wskazują `Order` jednostki i jednostki `Size` w elemencie.

    |Przykład kolejności wyrażenia długości|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Utwórz jednostkę i podskładniki we wszystkich pozostałych przykładach wyrażenia długości.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Jak traktuje się implikowane dane, takie jak litera `a` implikuje pojedynczy Pizza? Lub brak `pickup` i `delivery`, aby wskazać, gdzie oczekiwany jest Pizza? Lub brak rozmiaru do wskazania domyślnego rozmiaru małego lub dużego? Rozważ traktowanie implikowanej obsługi danych jako części reguł firmy w aplikacji klienckiej zamiast lub oprócz LUIS.

1. Aby nauczyć aplikację, wybierz pozycję **pouczenie**. Szkolenie stosuje zmiany, takie jak nowe jednostki i etykiety wyrażenia długości, do aktywnego modelu.

1. Po przeprowadzeniu szkolenia Dodaj nowy przykład wypowiedź do zamiaru, aby zobaczyć, jak dobrze LUIS rozumie obiekt.

    |Przykład kolejności wypowiedź|
    |--|
    |`pickup XL meat lovers pizza`|

    Ogólna Górna jednostka `Order` ma etykietę, a podskładnik `Size` jest również oznaczony liniami kropkowanymi. Jest to pomyślne prognozowanie.

    ![Nowy przykład wypowiedź przewidziany dla jednostki](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Linia kropkowana wskazuje prognozę.

1. Aby zmienić prognozowanie na jednostkę oznaczoną etykietą, zaznacz wiersz, a następnie wybierz pozycję **Potwierdź przewidywania jednostek**.

    ![Zaakceptuj prognozowanie, wybierając pozycję Potwierdź prognozowanie jednostek.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    W tym momencie jednostka uczenia maszynowego działa, ponieważ może znaleźć jednostkę w ramach nowego przykładu wypowiedź. Po dodaniu przykładu wyrażenia długości, jeśli jednostka nie jest przewidywalna prawidłowo, Oznacz jednostkę i podskładniki. Jeśli jednostka jest przewidywalna prawidłowo, upewnij się, że są one potwierdzone.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Dodawanie wstępnie skompilowanego numeru w celu ułatwienia wyodrębnienia danych

Informacje o zamówieniu powinny również zawierać liczbę elementów w kolejności, takich jak liczba pizzami. Aby wyodrębnić te dane, należy dodać nowy podskładnik maszynowy do `Order`, a ten składnik wymaga ograniczenia wstępnie skompilowanego numeru. Ograniczając jednostkę do wstępnie skompilowanego numeru, jednostka znajdzie i wyodrębni liczbę, czy tekst jest cyfrą, `2`lub tekstem, `two`.

Zacznij od dodania wbudowanej jednostki numeru do aplikacji.

1. Wybierz pozycję **jednostki** z menu po lewej stronie, a następnie wybierz pozycję **+ Dodaj wstępnie utworzoną jednostkę**.

1. W polu **Dodaj wstępnie utworzone jednostki** Wyszukaj i wybierz pozycję **Liczba** , a następnie wybierz pozycję **gotowe**.

    ![Dodawanie wstępnie utworzone jednostki](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Wstępnie utworzona jednostka jest dodawana do aplikacji, ale nie jest jeszcze ograniczeniem.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Tworzenie jednostki podskładnikej z ograniczeniami w celu ułatwienia wyodrębnienia danych

Jednostka `Order` powinna mieć podskładnik `Quantity`, aby określić, ile elementów znajduje się w kolejności. Ilość powinna być ograniczona do liczby, aby wyodrębnione dane były natychmiast wykorzystywane przez aplikację kliencką.

Ograniczenie jest stosowane jako dopasowanie tekstu, z dokładnym dopasowaniem (na przykład jednostki listy) lub za pomocą wyrażeń regularnych (takich jak jednostka wyrażenia regularnego lub prekompilowanego obiektu).

Przy użyciu ograniczenia, wyodrębniany jest tylko tekst pasujący do tego ograniczenia.

1. Wybierz pozycję **jednostki** , a następnie wybierz jednostkę `Order`.
1. Wybierz pozycję **+ Dodaj składnik** , a następnie wprowadź nazwę `Quantity` a następnie wybierz pozycję ENTER, aby dodać nową jednostkę do aplikacji.
1. Po powodzeniu powiadomienia wybierz `Quantity` podskładniku, a następnie wybierz ołówek.
1. Z listy rozwijanej wybierz wstępnie skompilowany numer.

    ![Utwórz jednostkę ilościową przy użyciu wstępnie skompilowanego numeru jako ograniczenia.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Jednostka `Quantity` jest stosowana, jeśli i tylko wtedy, gdy zostanie znaleziony tekst pasujący do wstępnie skompilowanej jednostki numeru.

    Jednostka z ograniczeniem jest tworzona, ale nie została jeszcze zastosowana do przykładu wyrażenia długości.

    > [!NOTE]
    > Podskładnik może być zagnieżdżony w ramach podskładniku do 5 poziomów. Chociaż ta wartość nie jest wyświetlana w tym artykule, jest dostępna w portalu i interfejsie API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Przykładowa etykieta wypowiedź do uczenia LUIS o jednostce

1. Wybierz pozycję **intencje** w obszarze nawigacji po lewej stronie, a następnie wybierz opcję **OrderPizza** . Trzy cyfry w następujących wyrażenia długości są oznaczone etykietami, ale są wizualnie poniżej linii jednostki `Order`. Ten niższy poziom oznacza, że jednostki są odnalezione, ale nie są uważane za `Order` siebie.

    ![Wstępnie skompilowany numer jest znaleziony, ale nie jest jeszcze traktowany poza jednostką zamówienia.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Oznacz numery przy użyciu jednostki `Quantity`, wybierając `2` w przykładzie wypowiedź, a następnie wybierając pozycję `Quantity` z listy. Oznacz `6` i `1` w tym samym przykładzie wypowiedź.

    ![Etykieta tekstu z jednostką ilości.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Uczenie aplikacji w celu zastosowania zmian jednostek w aplikacji

Wybierz pozycję **uczenie** , aby nauczyć aplikację przy użyciu tych nowych wyrażenia długości.

![Przeszkol aplikację, a następnie zapoznaj się z przykładem wyrażenia długości.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

W tym momencie kolejność zawiera pewne szczegóły, które mogą zostać wyodrębnione (rozmiar, ilość i tekst zamówienia). Dalsze poprawianie jednostki `Order`ej, takiej jak Pizza toppings, Type of Crust i Orders. Każdy z tych elementów powinien zostać utworzony jako podskładniki jednostki `Order`.

## <a name="test-the-app-to-validate-the-changes"></a>Przetestuj aplikację, aby zweryfikować zmiany

Przetestuj aplikację przy użyciu interaktywnego panelu **testów** . Ten proces umożliwia wprowadzenie nowego wypowiedź, a następnie wyświetlenie wyników przewidywania w celu sprawdzenia, jak dobrze aktywna i przeszkolone aplikacje działają. Przewidywanie intencji powinno być dość świadome (powyżej 70%) a ekstrakcja jednostek powinna pobrać co najmniej jednostkę `Order`. Szczegóły jednostki Order mogą nie być widoczne, ponieważ 5 wyrażenia długości nie wystarcza do obsługi każdego przypadku.

1. Wybierz opcję **Test** (Testuj) w górnym menu nawigacyjnym.
1. Wprowadź wypowiedź `deliver a medium veggie pizza` a następnie wybierz klawisz ENTER. Aktywny model przewidział prawidłowy cel z ponad 70% pewnością.

    ![Wprowadź nowy wypowiedź, aby przetestować zamiar.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Wybierz pozycję **Sprawdź** , aby zobaczyć prognozy jednostek.

    ![Wyświetl przewidywania jednostek w panelu testów interaktywnych.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    Rozmiar został poprawnie zidentyfikowany. Należy pamiętać, że przykład wyrażenia długości w zamiarze `OrderPizza` nie ma przykładu `medium` jako rozmiaru, ale używa deskryptora `SizeDescriptor` liście fraz, która zawiera średnią.

    Ilość nie jest prawidłowo przewidywalna. Aby rozwiązać ten problem, możesz dodać więcej przykładowych wyrażenia długości za pomocą tego słowa, aby wskazać ilość i oznaczyć słowo jako jednostkę `Quantity`.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Opublikuj aplikację, aby uzyskać do niej dostęp z punktu końcowego HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Pobierz zamierzenia i prognozowanie jednostek z punktu końcowego HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL w adresie i wprowadź to samo zapytanie, które zostało wprowadzone w panelu testów interakcyjnych.

    `deliver a medium veggie pizza`

    Ostatni parametr ciągu zapytania to `query`, czyli **query** (zapytanie) wypowiedzi.

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Samouczek — intencje](luis-quickstart-intents-only.md)
* [Pojęcia —](luis-concept-entity-types.md) informacje koncepcyjne jednostek
* [Koncepcja —](luis-concept-feature.md) informacje o pojęciach
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku aplikacja korzysta z jednostki poświęconej maszynom w celu znalezienia zamiaru wypowiedź użytkownika i wyodrębnienia szczegółów z tego wypowiedź. Użycie jednostki, której dotyczy dana maszyna, pozwala na rozłożyć szczegóły jednostki.

> [!div class="nextstepaction"]
> [Dodawanie wstępnie skompilowanej jednostki KeyPhrase](luis-quickstart-intent-and-key-phrase.md)
