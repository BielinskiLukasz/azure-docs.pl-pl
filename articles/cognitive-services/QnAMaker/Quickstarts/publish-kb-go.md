---
title: 'Szybki start: Publikowanie bazy wiedzy, REST, Go - QnA Maker'
description: Ten szybki start oparty na restie o restie publikuje bazę wiedzy i tworzy punkt końcowy, który można wywołać w aplikacji lub bota czatu.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 4ce655bdc7a913ecb281ce8a75e7ec4f2009a2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851697"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Szybki start: publikowanie bazy wiedzy w usłudze QnA Maker przy użyciu języka Go

Ten przewodnik Szybki start oparty na protokole REST przeprowadzi Cię przez programowe publikowanie bazy wiedzy. Publikowanie wypycha najnowszą wersję bazy wiedzy do dedykowanego indeksu usługi Azure Cognitive Search i tworzy punkt końcowy, który można wywołać w aplikacji lub bota czatu.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) — ten interfejs API nie wymaga żadnych informacji zawartych w treści żądania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Go w wersji 1.10.1](https://golang.org/dl/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (który zawiera nazwę zasobu), wybierz **szybki start** dla zasobu w witrynie Azure portal.

* Identyfikator bazy wiedzy QnA Maker (KB) znajdujący się w adresie URL w parametrze `kbid` ciągu zapytania, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykładową bazę na potrzeby tego podręcznika Szybki start: [Tworzenie nowej bazy wiedzy](create-new-kb-csharp.md).

> [!NOTE]
> Pliki kompletnego rozwiązania są dostępne w [repozytorium GitHub **Azure-Samples/cognitive-services-qnamaker-go**](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Tworzenie pliku Go

Otwórz program VSCode i utwórz nowy plik o nazwie `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `publish-kb.go` dodaj następujące wiersze, aby dodać niezbędne zależności do projektu:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Tworzenie funkcji main

Poniżej wymaganych zależności dodaj następującą klasę:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Dodawanie wymaganych stałych

W obrębie funkcji **main**


 dodaj stałe wymagane w celu uzyskiwania dostępu do usługi QnA Maker. Przedstawione wartości zastąp własnymi.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Dodawanie żądania POST w celu opublikowania bazy wiedzy

Poniżej wymaganych stałych dodaj następujący kod, który wysyła żądanie HTTPS do interfejsu API usługi QnA Maker, aby opublikować bazę wiedzy, oraz odbiera odpowiedź:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

W przypadku pomyślnego publikowania wywołanie interfejsu API zwraca stan 204 bez jakiejkolwiek zawartości w treści odpowiedzi. Ten kod dodaje zawartość dla odpowiedzi stanu 204.

W przypadku wszystkich innych odpowiedzi są one zwracane w niezmienionej postaci.

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Wprowadź następujące polecenie, aby skompilować plik. Wiersz polecenia nie zwraca żadnej informacji w przypadku pomyślnej kompilacji.

```bash
go build publish-kb.go
```

Wprowadź następujące polecenie w wierszu polecenia, aby uruchomić program. Spowoduje to wysłanie żądania do interfejsu API usługi QnA Maker w celu opublikowania bazy wiedzy. W przypadku powodzenia zwracany jest kod 204. W przeciwnym razie wyświetlane są błędy.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Po opublikowaniu bazy wiedzy potrzebny jest [adres URL punktu końcowego do wygenerowania odpowiedzi](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)