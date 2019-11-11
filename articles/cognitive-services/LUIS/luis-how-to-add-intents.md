---
title: Dodawanie intencji — LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj intencje do aplikacji LUIS, aby zidentyfikować grupy pytań lub poleceń, które mają takie same intencje.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904322"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Dodawanie intencji w celu określenia zamiaru użytkownika wyrażenia długości

Dodaj [intencje](luis-concept-intent.md) do aplikacji Luis, aby zidentyfikować grupy pytań lub poleceń, które mają ten sam zamiar. 

Intencje są zarządzane z sekcji **kompilacja** górnego paska nawigacyjnego, a następnie od **intencji**panelu po lewej stronie. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Dodawanie intencji

1. W [portalu Luis w wersji zapoznawczej](https://preview.luis.ai)wybierz pozycję **kompilacja** , aby wyświetlić jej intencje. 
1. Na stronie **intencje** wybierz pozycję **+ Utwórz**.
1. W oknie dialogowym **Tworzenie nowego zamiaru** wprowadź nazwę celu, na przykład `ModifyOrder`, i wybierz pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Dodaj zamierzenia](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Wymaga przykład wyrażenia długości.

## <a name="add-an-example-utterance"></a>Dodawanie przykładu wypowiedź

Przykład wyrażenia długości to tekstowe przykłady pytań lub poleceń użytkownika. Aby nauczyć się Language Understanding (LUIS) w celu przewidywania tego celu, należy dodać przykład wyrażenia długości do celu. LUIS muszą mieć zakres od 15 do 30 przykład wyrażenia długości, aby rozpocząć zrozumienie zamiaru. Nie dodawaj przykładu wyrażenia długości zbiorczo. Każdy wypowiedź powinien być starannie wybierany w taki sposób, w jaki różni się od przykładów już w zamierzeniu. 

1. Na stronie Szczegóły zamiaru wprowadź odpowiednie wypowiedź od użytkowników, takie jak `Deliver a large cheese pizza` w polu tekstowym poniżej nazwy celu, a następnie naciśnij klawisz ENTER.
 
    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu strony Szczegóły dotyczącej intencji z wyróżnioną pozycją wypowiedź](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS konwertuje wszystkie wyrażenia długości na małe litery i dodaje spacje wokół [tokenów](luis-language-support.md#tokenization) , takich jak łączniki.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Błędy przewidywania intencji 

Przykład wypowiedź w zamiarach może mieć błąd przewidywania intencji między zamiarem, a przykładem wypowiedź jest obecnie w i intencją określoną podczas szkolenia. 

Aby znaleźć błędy przewidywania wypowiedź i rozwiązać te problemy, użyj opcji **filtru** nieprawidłowych i niejasnych w połączeniu z opcją **Widok** **widoku szczegółowego**. 

![Aby znaleźć błędy przewidywania wypowiedź i rozwiązać te problemy, użyj opcji filtrowania.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Gdy filtry i widok są stosowane i występuje przykład wyrażenia długości z błędami, przykładowa lista wypowiedź zawiera wyrażenia długości i problemy.

> [!div class="mx-imgBorder"]
> ![! [Kiedy filtry i widok są stosowane i występuje przykład wyrażenia długości z błędami, przykładowa lista wypowiedź zawiera wyrażenia długości i problemy.] (/Media/Luis-How-to-Add-Intents/Find-Errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Każdy wiersz przedstawia ocenę bieżącego szkolenia dla przykładu wypowiedź, najbliższej oceny Rival, która jest różnicą w tych dwóch wskaźnikach. 

### <a name="fixing-intents"></a>Ustalanie intencji

Aby dowiedzieć się, jak naprawić błędy prognozowania intencji, użyj [pulpitu nawigacyjnego podsumowanie](luis-how-to-use-dashboard.md). Pulpit nawigacyjny podsumowania zawiera analizę dla ostatniego szkolenia wersji aktywnej i oferuje najważniejsze sugestie dotyczące naprawienia modelu.  

## <a name="using-the-contextual-toolbar"></a>Korzystanie z kontekstowego paska narzędzi

Pasek narzędzi kontekstowych zawiera inne akcje:

* Edytuj lub Usuń przykład wypowiedź
* Ponowne przypisanie przykładu wypowiedź do innego celu
* Filtry i widoki: Pokaż tylko wyrażenia długości zawierające odfiltrowane jednostki lub Wyświetl opcjonalne szczegóły
* Wyszukaj na przykład wyrażenia długości

## <a name="train-your-app-after-changing-model-with-intents"></a>Uczenie aplikacji po zmianie modelu z intencjami

Po dodaniu, przeprowadzeniu edycji lub usunięciu zamiarów możesz [nauczyć](luis-how-to-train.md) i [opublikować](luis-how-to-publish-app.md) aplikację, aby zmiany zostały zastosowane do zapytań punktów końcowych. Nie wolno uczenia się po każdej pojedynczej zmianie. Uczenie po grupie zmian. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat dodawania [przykładowej wyrażenia długości](luis-how-to-add-example-utterances.md) z obiektami. 
