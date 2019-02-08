---
title: Baza wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker wiedzy składa się z zestaw par pytanie/odpowiedź (pytań i odpowiedzi) i opcjonalne metadane skojarzone z każdej pary pytań i odpowiedzi.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2173bc46471fec6bfbacbda9362e5530075faf18
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857345"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co to jest usługa QnA Maker wiedzy?

Usługa QnA Maker wiedzy składa się z zestaw par pytanie/odpowiedź (pytań i odpowiedzi) i opcjonalne metadane skojarzone z każdej pary pytań i odpowiedzi.

## <a name="key-knowledge-base-concepts"></a>Pojęcia dotyczące klucza bazy wiedzy

* **Pytania dotyczące** -pytanie zawiera tekst, który najlepiej reprezentuje zapytanie użytkownika. 
* **Odpowiedzi** — odpowiedź jest odpowiedzi, który jest zwracany, jeśli kwerenda użytkownika jest dopasowywany skojarzone zapytania.  
* **Metadane** -metadane są znaczniki skojarzone z pary pytań i odpowiedzi i są reprezentowane jako pary klucz wartość. Znaczniki metadane są używane do filtrowania pary pytań i odpowiedzi i ograniczyć zestaw, w którym zapytaniu odpowiedników.

Pojedynczy QnA, reprezentowany przez identyfikator numeryczny pytań i odpowiedzi ma wiele wariantów pytanie (alternatywny pytań) wszystkie mapowane do jednej odpowiedzi. Ponadto każda para takie może mieć wiele pól metadane skojarzone z nią.

![Usługa QnA Maker baz wiedzy](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Format zawartości bazy wiedzy

Po sformatowanej zawartości jest pozyskiwania w bazie wiedzy, narzędzie QnA Maker próbuje przekonwertować zawartości markdown. Odczyt [to](https://aka.ms/qnamaker-docs-markdown-support) blog, aby zrozumieć języku znaczników markdown formatuje zrozumiały dla większości klientów rozmowy.

Pola metadanych składają się z pary klucz wartość oddzielone dwukropkiem **(produkt: strzępiarka)**. Klucz i wartość musi być tylko tekst. Klucza metadanych nie może zawierać spacji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Cykl życia projektowania bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz także

[Omówienie usługi QnA Maker](../Overview/overview.md)
