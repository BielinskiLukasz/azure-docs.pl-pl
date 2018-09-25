---
title: Analizy bazy wiedzy
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker przechowuje wszystkie dzienniki czatu i innych telemetrii po włączeniu usługi App Insights podczas tworzenia usługi QnA Maker. Uruchom przykładowe zapytania można pobrać dzienniki czatu z usługi App Insights.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim88
ms.openlocfilehash: dc363a3ba0d809a3307a6803993bdf500da45f1b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035423"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy

Usługa QnA Maker przechowuje wszystkie dzienniki czatu i innych telemetrii po włączeniu usługi App Insights podczas [tworzenia usługi QnA Maker](./set-up-qnamaker-service-azure.md). Uruchom przykładowe zapytania można pobrać dzienniki czatu z usługi App Insights.

1. Przejdź do zasobu usługi App Insights.

    ![Wybierz zasób usługi application insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Wybierz **analizy**. Nowe otworzyć okno, gdzie można tworzyć zapytania telemetrii usługi QnA Maker.

    ![Wybierz usługi Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Wklej poniższe zapytanie i uruchom go.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Wybierz **Uruchom** Aby uruchomić zapytanie.

    ![Uruchamianie zapytania](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Uruchamianie zapytań dla innych analiz dotyczących wiedzy usługi QnA Maker

### <a name="total-90-day-traffic"></a>Całkowity ruch 90 dni

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>Łączna liczba pytanie ruchu w danym okresie czasu

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Użytkownik — ruch

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Rozkład opóźnienia pytań

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zarządzanie kluczami](./key-management.md)
