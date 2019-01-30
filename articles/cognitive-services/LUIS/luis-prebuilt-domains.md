---
title: Ze wstępnie utworzonych domen dla Understa języka
titleSuffix: Azure Cognitive Services
description: Usługa LUIS obejmuje zestaw wstępnie utworzonych domen do szybkiego dodawania scenariuszy użytkowników typowe i konwersacyjny.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cgronlun
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 74b28c926e077bb35122d3435fcfd93816acc9fd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215218"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Dodaj ze wstępnie utworzonych domen dla typowych scenariuszy użycia 

Usługa LUIS zawiera zestaw wbudowanych intencji ze wstępnie utworzonych domen do szybkiego dodawania typowych intencje i wypowiedzi. Jest to szybki i łatwy sposób dodać możliwości do aplikacji klienckiej konwersacji, bez konieczności projektowania modeli dla tych możliwości. 

## <a name="add-a-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

1. Na **Moje aplikacje** wybierz aplikację. Spowoduje to otwarcie aplikacji **kompilacji** części aplikacji. 

1. Na **intencji** wybierz opcję **Dodaj ze wstępnie utworzonych domen** dolnej, lewej paska narzędzi. 

1. Wybierz **kalendarza** intencji, a następnie wybierz opcję **Dodaj domenę** przycisku.

    ![Dodawanie domeny wbudowanych kalendarza](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Wybierz **intencji** w nawigacji po lewej stronie, aby wyświetlić intencji kalendarza. Każdy intencji z tej domeny jest poprzedzony znakiem `Calendar.`. Wraz z wypowiedzi, dwie jednostki dla tej domeny są dodawane do aplikacji: `Calendar.Location` i `Calendar.Subject`. 

## <a name="train-and-publish"></a>Uczenie i publikowanie

1. Po dodaniu domeny uczenie aplikacji, wybierając **szkolenie** w górnym rogu, kliknij prawym przyciskiem myszy pasek narzędzi. 

1. Na górnym pasku narzędzi wybierz **Publikuj**. Publikowanie **produkcji**. 

1. Gdy pojawi się powiadomienie o powodzeniu zielony, wybierz **można znaleźć na liście punktów końcowych** linku umożliwia wyświetlenie listy punktów końcowych.

1. Wybierz punkt końcowy. Punkt końcowy zostanie otwarta nowa karta przeglądarki. Nie zamykaj kartę przeglądarki i w dalszym ciągu **testu** sekcji.

## <a name="test"></a>Testowanie

Test nowe opcje w punkcie końcowym dodany przez wartość **q** parametru: `Schedule a meeting with John Smith in Seattle next week`.

Usługa LUIS zwraca poprawne intencji i przedmiot spotkania:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Dokumentacja ze wstępnie utworzonych domen](./luis-reference-prebuilt-domains.md)
