---
title: 'Szybki start: Tworzenie bazy wiedzy — środowisko REST, Go — QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ten oparty na protokole REST przewodnik Szybki start dla języka Go zawiera omówienie programowego tworzenia przykładowej bazy wiedzy usługi QnA Maker, która będzie wyświetlana na pulpicie nawigacyjnym platformy Azure w ramach konta interfejsu API usługi Cognitive Services.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: cfb8cb60a828220ea681ad06e86588d3453b8d1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313243"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Szybki start: Tworzenie bazy wiedzy w usłudze QnA Maker przy użyciu języka Go

Ten przewodnik Szybki start przeprowadzi Cię przez programowe tworzenie przykładowej bazy wiedzy usługi QnA Maker. Usługa QnA Maker automatycznie wyodrębnia pytania i odpowiedzi z częściowo ustrukturyzowanej zawartości, na przykład często zadawanych pytań, ze [źródeł danych](../Concepts/data-sources-supported.md). Model bazy wiedzy jest zdefiniowany w formacie JSON wysyłanym w treści żądania interfejsu API. 

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Tworzenie bazy wiedzy](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Pobieranie szczegółów operacji](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Go w wersji 1.10.1](https://golang.org/dl/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz, wybierz pozycję **Klucze** w obszarze **Zarządzanie zasobami** na pulpicie nawigacyjnym. 

## <a name="create-a-knowledge-base-go-file"></a>Tworzenie pliku Go bazy wiedzy

Utwórz plik o nazwie `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `create-new-knowledge-base.go` dodaj następujące wiersze, aby dodać niezbędne zależności do projektu:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Dodawanie wymaganych stałych
Po poprzednich wymaganych zależnościach dodaj wymagane stałe umożliwiające dostęp do usługi QnA Maker. Zastąp wartość zmiennej `subscriptionKey` własnym kluczem usługi QnA Maker.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Dodawanie definicji modelu bazy wiedzy
Po stałych dodaj następującą definicję modelu bazy wiedzy. Po definicji model jest konwertowany na ciąg.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Dodawanie pomocniczych struktur i funkcji

Następnie dodaj poniższe funkcje pomocnicze.

1. Dodaj strukturę żądania HTTP:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Dodaj następującą metodę w celu obsługi żądań POST wysyłanych do interfejsów API usługi QnA Maker. W tym przewodniku Szybki start żądanie POST służy do wysłania definicji bazy wiedzy do usługi QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Dodaj następującą metodę w celu obsługi żądań GET wysyłanych do interfejsów API usługi QnA Maker. W tym przewodniku Szybki start żądanie GET służy do sprawdzenia stanu operacji tworzenia. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Dodawanie funkcji tworzącej bazę wiedzy

Dodaj następujące funkcje, aby spowodować, że żądanie HTTP POST utworzy bazę wiedzy. **Identyfikator operacji** _create_ jest zwracany w polu **Location** nagłówka odpowiedzi POST, a następnie używany jako część trasy w żądaniu GET. `Ocp-Apim-Subscription-Key` to klucz usługi QnA Maker używany do uwierzytelniania. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera identyfikator operacji. Użyj tego identyfikatora operacji, aby określić, czy baza wiedzy została pomyślnie utworzona. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>Dodawanie funkcji pobierającej stan

Dodaj następującą funkcję, aby spowodować, że żądanie HTTP GET sprawdzi stan operacji. `Ocp-Apim-Subscription-Key` to klucz usługi QnA Maker używany do uwierzytelniania. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

Powtarzaj wywołanie do momentu uzyskania stanu powodzenia lub niepowodzenia: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```
## <a name="add-main-function"></a>Dodawanie funkcji main

Następująca funkcja jest główną funkcją i tworzy bazę wiedzy oraz powtarza sprawdzenie stanu. Tworzenie bazy wiedzy może nieco potrwać, dlatego musisz powtarzać wywołania sprawdzenia stanu do czasu uzyskania stanu powodzenia lub niepowodzenia.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Kompilowanie programu
Wprowadź następujące polecenie, aby skompilować plik. Wiersz polecenia nie zwraca żadnej informacji w przypadku pomyślnej kompilacji.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Uruchamianie programu

Wprowadź następujące polecenie w wierszu polecenia, aby uruchomić program. Program wyśle żądanie do interfejsu API usługi QnA Maker, aby utworzyć bazę wiedzy, a następnie będzie sondować wyniki co 30 sekund. Każda odpowiedź jest wypisywana w oknie konsoli.

```bash
go run create-new-knowledge-base
```

Utworzoną bazę wiedzy można wyświetlić w portalu usługi QnA Maker, na stronie [My knowledge bases (Moje bazy wiedzy)](https://www.qnamaker.ai/Home/MyServices). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
