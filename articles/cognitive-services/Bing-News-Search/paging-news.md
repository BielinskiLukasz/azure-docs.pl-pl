---
title: Jak stronicować za pośrednictwem wyników wyszukiwania wiadomości Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak stronicować za pośrednictwem artykuły z wiadomościami, które zwraca interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 1d344f388b03acb3a81fcfde0e214eb7d82dc9b9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885076"
---
# <a name="how-to-page-through-news-search-results"></a>Jak stronicować za pośrednictwem wyników wyszukiwania wiadomości

Po wywołaniu interfejsu API wyszukiwania wiadomości Bing zwraca listę wyników, które są istotne dla kwerendy. Aby uzyskać szacowana liczba dostępnych wyników, dostęp do obiektu odpowiedzi [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) pola.  
  
W poniższym przykładzie przedstawiono `totalEstimatedMatches` pola, który zawiera odpowiedź wiadomości.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Na stronie przy użyciu dostępnych artykułów, użyj [liczba](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) i [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) parametry zapytania.  
 

|Parametr  |Opis  |
|---------|---------|
|`count`     | Określa liczbę wyników do zwrócenia w odpowiedzi. Maksymalna liczba wyników, których może żądać w odpowiedzi to 100. Wartość domyślna wynosi 10. Rzeczywista liczba dostarczonych może być mniejsza niż żądana.        |
|`offset`     | Określa liczbę wyników do pominięcia. `offset` Jest liczony od zera i powinna być mniejsza niż (`totalEstimatedMatches` - `count`).          |

Na przykład, jeśli chcesz wyświetlić 20 artykułów na stronę, należy ustawić `count` do 20 i `offset` na 0, aby pobrać pierwszej strony wyników. Na kolejnych stronach, możesz zwiększyć `offset` 20 (na przykład 20, 40).  
  
Poniższy przykład żądania 20 nowych artykułów, rozpoczynając od przesunięcia 40.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Jeśli wartość domyślna `count` wartość działa w przypadku implementacji, należy określić tylko `offset` parametr zapytania, jak pokazano w poniższym przykładzie:  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Stronicowanie ma zastosowanie tylko do wyszukiwania wiadomości (/ wiadomości/wyszukiwania), a nie popularne tematy (/ wiadomości/trendingtopics) lub kategorii wiadomości (/ grup dyskusyjnych).

> [!NOTE]
> `TotalEstimatedAnswers` Pole jest szacunkową liczbę całkowitą liczbę wyników wyszukiwania, możesz pobrać dla bieżącego zapytania.  Po ustawieniu `count` i `offset` parametrów, `TotalEstimatedAnswers` liczba mogą ulec zmianie. 
