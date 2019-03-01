---
title: Wzorce Dodaj dokładności
titleSuffix: Language Understanding - Azure Cognitive Services
description: Informacje dotyczące dodawania szablonów wzorzec Language Understanding (LUIS) aplikacji w celu zwiększenia dokładności prognozy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: diberry
ms.openlocfilehash: 132f6eab86c02e28fe562a0c7d3357175e5813b8
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195051"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorców w celu zwiększenia dokładności prognozy
Gdy aplikacją usługi LUIS otrzyma wypowiedzi punktu końcowego, należy użyć [wzorzec](luis-concept-patterns.md) w celu zwiększenia dokładności prognozy dla wypowiedzi zawiera wzorca w porządku program word i wybór programu word. Wzorce użycia określonego [składni](luis-concept-patterns.md#pattern-syntax) aby wskazać lokalizację: [jednostek](luis-concept-entity-types.md), ról jednostki i opcjonalny tekst.

## <a name="add-template-utterance-to-create-pattern"></a>Dodaj wypowiedź szablonu, aby utworzyć wzorzec
1. Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** strony, a następnie wybierz pozycję **wzorców** w lewym panelu w obszarze **lepsza wydajność aplikacji**.

    ![Zrzut ekranu przedstawiający listę wzorców](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Wybierz prawidłowe opcje dla wzorca. 

    ![Wybierz opcje](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. W polu tekstowym szablonu wpisz wypowiedź szablonu, a następnie naciśnij klawisz Enter. Jeśli chcesz wprowadzić nazwę jednostki, należy użyć składni jednostki prawidłowy wzorzec. Rozpocznij składni jednostki za pomocą `{`. Lista zawiera jednostki. Wybierz prawidłowe jednostki, a następnie naciśnij klawisz Enter. 

    ![Zrzut ekranu przedstawiający jednostkę do wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli jednostka zawiera roli, określać rolę z dwukropkiem pojedynczego `:`po nazwę jednostki, takie jak `{Location:Origin}`. Wyświetla listę ról dla obiektów w postaci listy. Wybierz rolę, a następnie naciśnij klawisz Enter. 

    ![Zrzut ekranu przedstawiający jednostki z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu prawidłowe jednostki, Zakończ wprowadzanie wzorzec, a następnie naciśnij klawisz Enter. Po zakończeniu wprowadzania wzorców [szkolenie](luis-how-to-train.md) aplikacji.

    ![Zrzut ekranu przedstawiający wzorzec wprowadzone przy użyciu obu typów jednostek](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uczenie aplikacji po zmianie modelu wzorami
Po dodaniu, edytować, usunąć lub ponownie przypisać wzorzec [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Za pomocą narzędzi kontekstowych

Kontekstowe narzędzi powyżej listy wzorców pozwala na:

* Wyszukiwanie wzorców
* Edytuj wzorzec
* Ponowne przypisywanie poszczególnych wzorzec do innego zamiaru
* Ponowne przypisywanie kilka wzorców do innego zamiaru
* Delete a pojedynczy wzorzec
* Usuwanie wielu wzorców
* Filtrowanie listy wzorzec przez jednostkę
* Filtr — wzorzec — listy przez intencji
* Usuwanie jednostki lub filtr elementu intent
* Dodawanie wzorca z istniejących wypowiedź przeznaczenie lub jednostki strony

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [kompilacji wzorzec](luis-tutorial-pattern.md) pattern.any i ról z samouczka.
* Dowiedz się, jak [szkolenie](luis-how-to-train.md) aplikacji.
