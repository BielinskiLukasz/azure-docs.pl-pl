---
title: 'Szybki Start: Publikowanie bazy wiedzy, REST C# i QNA Maker'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start oparty na protokole REST języka C# przeprowadzi Cię przez proces publikowania bazy wiedzy, który polega na wypchnięciu najnowszej wersji przetestowanej bazy wiedzy do dedykowanego indeksu usługi Azure Search reprezentującego opublikowaną bazę wiedzy. Zostanie również utworzony punkt końcowy, który można wywoływać w aplikacji lub czatbocie.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 2b2c2ed43a229d929353767b229f8331b49a0e46
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802899"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Szybki start: publikowanie bazy wiedzy w usłudze QnA Maker przy użyciu języka C#

Ten przewodnik Szybki start oparty na protokole REST przeprowadzi Cię przez programowe publikowanie bazy wiedzy. Publikowanie wypycha najnowszą wersję bazy wiedzy do dedykowanego indeksu usługi Azure Search i tworzy punkt końcowy, który może być wywoływany w Twojej aplikacji lub czatbocie.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) — ten interfejs API nie wymaga żadnych informacji zawartych w treści żądania.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza [**wersja programu Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (w tym nazwę zasobu), wybierz pozycję **Szybki Start** dla zasobu w Azure Portal.
* Identyfikator bazy wiedzy usługi QnA Maker dostępny w adresie URL w parametrze ciągu zapytania kbid, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykładową bazę na potrzeby tego podręcznika Szybki start: [Tworzenie nowej bazy wiedzy](create-new-kb-csharp.md).

> [!NOTE] 
> Pliki kompletnego rozwiązania są dostępne w [repozytorium GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Tworzenie projektu bazy wiedzy

1. Otwórz program Visual Studio 2019 Community Edition.
1. Utwórz nowy projekt **aplikacja konsoli (.NET Core)** i nazwij projekt `QnaMakerQuickstart`. Zaakceptuj wartości domyślne pozostałych ustawień.

## <a name="add-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku Program.cs zastąp pojedynczą instrukcję using następującymi wierszami, aby dodać do projektu niezbędne zależności:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Dodawanie wymaganych stałych

W klasie **program** Dodaj wymagane stałe, aby uzyskać dostęp do QNA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Dodaj metodę Main, aby opublikować bazę wiedzy

Poniżej wymaganych stałych dodaj następujący kod, który wysyła żądanie HTTPS do interfejsu API usługi QnA Maker, aby opublikować bazę wiedzy, oraz odbiera odpowiedź:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

W przypadku pomyślnego publikowania wywołanie interfejsu API zwraca stan 204 bez jakiejkolwiek zawartości w treści odpowiedzi. 
 
## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Skompiluj i uruchom program. Spowoduje to automatyczne wysłanie do interfejsu API usługi QnA Maker żądania opublikowania bazy wiedzy. Odpowiedź jest następnie wyświetlana w oknie konsoli.

Po opublikowaniu bazy wiedzy można tworzyć do niej zapytania z punktu końcowego przy użyciu aplikacji klienckiej lub czatbota. 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Następne kroki

Po opublikowaniu bazy wiedzy potrzebny jest [adres URL punktu końcowego do wygenerowania odpowiedzi](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)
