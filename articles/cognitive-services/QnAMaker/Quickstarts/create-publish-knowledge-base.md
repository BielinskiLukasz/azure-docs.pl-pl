---
title: 'Szybki Start: Tworzenie, uczenie i publikowanie bazy wiedzy — QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia QnA Maker bazy wiedzy (KB) na podstawie zawartości, na przykład często zadawanych pytań lub podręczników produktów. Baza wiedzy QnA Maker w tym przykładzie jest tworzona na podstawie prostych często zadawanych pytań i odpowiedzi na pytania dotyczące odzyskiwania klucza funkcji BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/29/2020
ms.author: diberry
ms.openlocfilehash: a3bdc118be96630ebcf3bf63a2948976dc9b4261
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901673"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Szybki Start: Tworzenie, uczenie i publikowanie bazy wiedzy QnA Maker

Na podstawie własnej zawartości, takiej jak często zadawane pytania lub podręczniki produktów, możesz utworzyć bazę wiedzy usługi QnA Maker. Ten artykuł zawiera przykład tworzenia bazy wiedzy QnA Makerej na podstawie prostej witryny sieci Web często zadawanych pytań i odpowiedzi na pytania dotyczące odzyskiwania klucza funkcji BitLocker.

Uwzględnij osobowość chit-chat, aby Twoja wiedza była bardziej interesująca dla użytkowników.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Warunek wstępny

> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-new-qna-maker-knowledge-base"></a>Tworzenie nowej bazy wiedzy QnA Maker

1. Zaloguj się do portalu [QnAMaker.AI](https://QnAMaker.ai) przy użyciu poświadczeń platformy Azure.

1. W portalu QnA Maker wybierz pozycję **Utwórz bazę wiedzy**.

1. Na stronie **Tworzenie** wybierz pozycję **Utwórz usługę QNA**. Nastąpi przekierowanie do witryny [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w celu skonfigurowania usługi QnA Maker w ramach subskrypcji.

1. W Azure Portal Utwórz zasób. Pamiętaj o IDENTYFIKATORze Azure Active Directory, subskrypcji, nazwie zasobu QnA wybranej podczas tworzenia zasobu.
1. Wróć do portalu QnA Maker, Odśwież stronę sieci Web w portalu, aby kontynuować tworzenie bazy wiedzy. Wybierz istniejącą dzierżawę, subskrypcję i nowy zasób. Wybierz język. Będzie to język używany dla wszystkich baz wiedzy w tej usłudze QnA Maker.

   ![Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker Service](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Nazwij swoją bazę wiedzy, **QNA KB**.

1. Dodaj przykładowy dokument programu Word jako adres URL:

    `https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`

1. Wybierz pozycję `+ Add URL`.

1. Dodaj  **_profesjonalne_ Chit — czat** do swojej bazy wiedzy.

1. Wybierz **tworzenie wiedzy**.

    Proces wyodrębniania może potrwać kilka minut, aby zapoznać się z dokumentem i zidentyfikować pytania i odpowiedzi.

    Po pomyślnym utworzeniu bazy wiedzy przez QnA Maker zostanie otwarta strona **bazy wiedzy** . Zawartość bazy wiedzy można edytować na tej stronie.

## <a name="add-a-new-question-and-answer-set"></a>Dodaj nowe pytanie i zestaw odpowiedzi

1. W portalu QnA Maker na stronie **Edycja** wybierz pozycję **+ Dodaj parę QNA** z poziomu paska narzędzi kontekstu.
1. Dodaj następujące pytanie:

    `How many Azure services are used by a knowledge base?`

1. Dodaj odpowiedź sformatowaną z uwzględnieniem _promocji_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Dodaj pytanie jako tekst i odpowiedź sformatowaną z uwzględnieniem promocji.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Symbol promocji, `*`jest używany dla punktów punktorów. `\n` jest używany w nowym wierszu.

    Na stronie **Edytuj** zostanie wyświetlona wartość promocji. Gdy używasz panelu **testowego** później, zobaczysz poprawność wyświetlana w promocji.

## <a name="save-and-train"></a>Zapisywanie i szkolenie

W prawym górnym rogu wybierz pozycję **Zapisz i przeszkol**, aby zapisać wprowadzone zmiany i przeszkolić model usługi QnA Maker. Zmiany nie są przechowywane, o ile nie zostaną zapisane.

## <a name="test-the-knowledge-base"></a>Testowanie bazy wiedzy

1. W portalu QnA Maker w prawym górnym rogu wybierz pozycję **test** , aby sprawdzić, czy wprowadzone zmiany zostały wprowadzone.
1. Wprowadź przykładowe zapytanie użytkownika w polu tekstowym.

    `How many Azure services are used by a knowledge base?`

    ![ Wprowadź przykładowe zapytanie użytkownika w polu tekstowym. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Wybierz pozycję **Zbadaj**, aby bardziej szczegółowo sprawdzić odpowiedź. Okno testowe służy do testowania zmian w bazie wiedzy przed opublikowaniem bazy wiedzy.

1. Ponownie wybierz **test** , aby zamknąć panel **testu** .

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy

Po opublikowaniu bazy wiedzy zawartość bazy wiedzy jest przenoszona z indeksu `test` do indeksu `prod` w usłudze Azure Search.

![Zrzut ekranu przedstawiający przemieszczenie zawartości bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. W portalu QnA Maker wybierz pozycję **Publikuj**. Aby potwierdzić, wybierz na stronie pozycję **Opublikuj**.

    Usługa QnA Maker została teraz pomyślnie opublikowana. Możesz użyć punktu końcowego w swojej aplikacji lub kodu bota.

    ![Zrzut ekranu przedstawiający pomyślne opublikowanie](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Utwórz bota

Po opublikowaniu można utworzyć Bot na stronie **publikowania** :

* Możesz szybko utworzyć kilka botów, wskazując tę samą bazę wiedzy dla różnych regionów lub planów cenowych dla poszczególnych botówów.
* Jeśli potrzebujesz tylko jednego bot dla bazy wiedzy, użyj **widoku wszystkie Twoje botów w** łączu Azure Portal, aby wyświetlić listę bieżącego botów.

Po wprowadzeniu zmian w bazie wiedzy i ponownym opublikowaniu nie trzeba podejmować dalszych działań z bot. Jest już skonfigurowana do pracy z bazą wiedzy i współpracuje ze wszystkimi przyszłymi zmianami w bazie wiedzy. Za każdym razem, gdy publikujesz bazę wiedzy, wszystkie połączone z nią botów są automatycznie aktualizowane.

1. W portalu QnA Maker na stronie **Publikowanie** wybierz pozycję **Utwórz bot**. Ten przycisk jest wyświetlany dopiero po opublikowaniu bazy wiedzy.

    ![Zrzut ekranu przedstawiający tworzenie bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Na stronie tworzenia Azure Bot Service zostanie otwarta nowa karta przeglądarki dla Azure Portal. Skonfiguruj usługę Azure bot. Bot i QnA Maker mogą korzystać z planu usługi aplikacji sieci Web, ale nie mogą udostępniać aplikacji sieci Web. Oznacza to, że **Nazwa aplikacji** dla bot musi być inna niż nazwa aplikacji dla usługi QNA Maker.

    * **Do**
        * Zmień obsługę bot — Jeśli nie jest unikatowa.
        * Wybierz pozycję język zestawu SDK. Po utworzeniu bot można pobrać kod do lokalnego środowiska deweloperskiego i kontynuować proces opracowywania.
    * **Nie**
        * podczas tworzenia bot Zmień poniższe ustawienia w Azure Portal. Są one wstępnie wypełnione dla istniejącej bazy wiedzy:
           * Klucz uwierzytelniania QnA
           * Plan i lokalizacja usługi App Service


1. Po utworzeniu bot Otwórz zasób **usługi bot** .
1. W obszarze **Zarządzanie bot**wybierz pozycję **Testuj w rozmowie w sieci Web**.
1. Po wyświetleniu monitu czatu o **wpisanie komunikatu**wpisz:

    `Azure services?`

    Bot rozmowy reaguje na odpowiedź z bazy wiedzy.

    ![Wprowadź zapytanie użytkownika do programu testowego rozmowy w sieci Web.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>Co zostało wykonane?

Utworzono nową bazę wiedzy, dodano publiczny adres URL do bazy wiedzy, dodano własny zestaw QnA, przeszkolony, przetestowany i opublikowany w bazie wiedzy.

Po opublikowaniu bazy wiedzy utworzono bot i przetestowano bot.

Zostało to wykonane w ciągu kilku minut bez konieczności pisania kodu i czyszczenia zawartości.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść QnA Maker i zasoby struktury bot w Azure Portal.

## <a name="next-steps"></a>Następne kroki

Więcej informacji:

* [Format promocji w odpowiedziach](../reference-markdown-format.md)
* QnA Maker [źródła danych](../concepts/knowledge-base.md).
* [Ustawienia konfiguracji zasobów bot](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Dodawanie pytań za pomocą metadanych](add-question-metadata-portal.md)
