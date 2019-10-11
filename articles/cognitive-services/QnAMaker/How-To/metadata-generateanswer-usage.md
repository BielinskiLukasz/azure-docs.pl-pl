---
title: Metadane z interfejsem API GenerateAnswer — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umożliwia dodawanie metadanych w formie par klucz/wartość do zestawów pytań/odpowiedzi. Można filtrować wyniki do zapytań użytkowników i przechowywać dodatkowe informacje, które mogą być używane w konwersacjach z monitami.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: diberry
ms.openlocfilehash: a63b6773a7546f8add0b2f2ab6280801e90bccca
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248632"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Uzyskaj odpowiedź przy użyciu interfejsu API GenerateAnswer i metadanych

Aby uzyskać przewidywaną odpowiedź na pytanie użytkownika, użyj interfejsu API GenerateAnswer. Po opublikowaniu bazy wiedzy można wyświetlić informacje na temat sposobu korzystania z tego interfejsu API na stronie **Publikowanie** . Możesz również skonfigurować interfejs API do filtrowania odpowiedzi na podstawie znaczników metadanych i przetestować bazę wiedzy z punktu końcowego za pomocą parametru test ciągu zapytania.

QnA Maker umożliwia dodawanie metadanych w formie par klucz-wartość do Twoich zestawów pytań i odpowiedzi. Korzystając z tych informacji, można filtrować wyniki zapytań użytkowników oraz przechowywać dodatkowe informacje, które mogą być używane w konwersacjach z monitami. Aby uzyskać więcej informacji, zobacz [Baza wiedzy](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Przechowywanie pytań i odpowiedzi za pomocą jednostki QnA

Ważne jest, aby zrozumieć, w jaki sposób QnA Maker przechowuje dane pytania i odpowiedzi. Na poniższej ilustracji przedstawiono jednostkę QnA:

![Ilustracja jednostki QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Każda jednostka QnA ma unikatowy i trwały identyfikator. IDENTYFIKATORA można użyć do wprowadzania aktualizacji do określonej jednostki QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Uzyskiwanie prognoz odpowiedzi przy użyciu interfejsu API GenerateAnswer

W celu uzyskania najlepszego dopasowania z zestawów pytań i odpowiedzi korzystasz z [interfejsu API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) w bot lub aplikacji w celu zbadania bazy wiedzy przy użyciu pytania użytkownika.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Opublikuj, aby uzyskać punkt końcowy GenerateAnswer 

Po opublikowaniu bazy wiedzy z poziomu [portalu QNA Maker](https://www.qnamaker.ai)lub przy użyciu [interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)można uzyskać szczegółowe informacje o punkcie końcowym usługi GenerateAnswer.

Aby uzyskać szczegółowe informacje o punkcie końcowym:
1. Zaloguj się do witryny [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. W obszarze **Moje bazy wiedzy**wybierz pozycję **Wyświetl kod** dla bazy wiedzy.
    ![Screenshot z mojej bazy wiedzy @ no__t-1
1. Pobierz szczegóły punktu końcowego GenerateAnswer.

    ![Zrzut ekranu przedstawiający szczegóły punktu końcowego](../media/qnamaker-how-to-metadata-usage/view-code.png)

Możesz również uzyskać szczegółowe informacje o punkcie końcowym z karty **Ustawienia** bazy wiedzy.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfiguracja żądania GenerateAnswer

Wywołanie GenerateAnswer z żądaniem HTTP POST. Przykładowy kod, który pokazuje, jak wywoływać GenerateAnswer, można znaleźć w [przewodnikach szybki start](../quickstarts/create-publish-kb-csharp-sdk.md#generate-an-answer-from-the-knowledge-base). 

Żądanie POST używa:

* Wymagane [Parametry identyfikatora URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Wymagana [Właściwość nagłówka](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, dla zabezpieczeń
* Wymagane [właściwości treści](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

Adres URL GenerateAnswer ma następujący format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Pamiętaj, aby ustawić właściwość nagłówka HTTP `Authorization` z wartością ciągu `EndpointKey` z końcowym miejscem na stronie **Ustawienia** .

Przykładowa treść JSON wygląda następująco:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Poprzedni kod JSON zażądał tylko odpowiedzi o wartości co najmniej 30% lub wyższej. 

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Właściwości odpowiedzi GenerateAnswer

[Odpowiedź](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) to obiekt JSON obejmujący wszystkie informacje potrzebne do wyświetlenia odpowiedzi i następnego włączenia konwersacji, jeśli jest dostępny.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Powyższy kod JSON odpowiedział z odpowiedzią z wynikiem 38,5%. 

## <a name="use-qna-maker-with-a-bot-in-c"></a>Użyj QnA Maker z botą wC#

Bot Framework zapewnia dostęp do właściwości QnA Maker za pomocą [interfejsu API getanswer](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Poprzedni kod JSON zażądał tylko odpowiedzi o wartości co najmniej 30% lub wyższej. 

[Przykładem](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) pomocy technicznej bot jest ten kod.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Używanie QnA Maker z bot w języku Node. js

Bot Framework zapewnia dostęp do właściwości QnA Maker za pomocą [interfejsu API getanswer](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Poprzedni kod JSON zażądał tylko odpowiedzi o wartości co najmniej 30% lub wyższej. 

[Przykładem](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) pomocy technicznej bot jest ten kod.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Korzystanie z metadanych do filtrowania odpowiedzi według niestandardowych tagów metadanych

Dodanie metadanych umożliwia filtrowanie odpowiedzi według tych tagów metadanych. Dodaj kolumnę Metadata z menu **Opcje widoku** . Dodaj metadane do bazy wiedzy, wybierając ikonę metadanych **+** , aby dodać parę metadanych. Ta para składa się z jednego klucza i jednej wartości.

![Zrzut ekranu przedstawiający dodawanie metadanych](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrowanie wyników za pomocą strictFilters dla tagów metadanych

Rozważ pytanie użytkownika "gdy ten hotel zostanie zamknięty?", gdzie zamiar jest implikowany dla restauracji "Paradise".

Ponieważ wyniki są wymagane tylko dla restauracji "Paradise", można ustawić filtr w wywołaniu GenerateAnswer dla metadanych "nazwa restauracji". Poniższy przykład pokazuje:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Użyj wyników pytania i odpowiedzi, aby zachować kontekst konwersacji

Odpowiedź na GenerateAnswer zawiera odpowiednie informacje o metadanych dopasowanego zestawu pytań i odpowiedzi. Możesz użyć tych informacji w aplikacji klienckiej do przechowywania kontekstu poprzedniej konwersacji do użycia w późniejszych konwersacjach. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Dopasuj tylko pytania według tekstu

Domyślnie QnA Maker przeszukuje pytania i odpowiedzi. Jeśli chcesz przeszukiwać tylko pytania, aby wygenerować odpowiedź, użyj `RankerType=QuestionOnly` w treści wpisu żądania GenerateAnswer.

Opublikowaną KB można przeszukiwać przy użyciu `isTest=false` lub w teście KB przy użyciu `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Typowe błędy HTTP

|Kod|Wyjaśnienie|
|:--|--|
|2xx|Powodzenie|
|400|Parametry żądania są niepoprawne, co oznacza, że brakuje wymaganych parametrów, są źle sformułowane lub zbyt duże|
|400|Treść żądania jest niepoprawna, co oznacza, że brakuje pliku JSON, został źle sformułowany lub jest zbyt duży|
|401|Nieprawidłowy klucz|
|403|Zabronione — nie masz poprawnych uprawnień|
|404|KB nie istnieje|
|410|Ten interfejs API jest przestarzały i nie jest już dostępny|

## <a name="next-steps"></a>Następne kroki

Strona **Publikowanie** zawiera również informacje umożliwiające wygenerowanie odpowiedzi przy użyciu elementu [Poster](../Quickstarts/get-answer-from-kb-using-postman.md) i [zwinięcie](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy bot](../tutorials/integrate-qnamaker-luis.md)
