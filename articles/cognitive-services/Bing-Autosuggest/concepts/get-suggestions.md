---
title: Sugerowanie terminów wyszukiwania za pomocą interfejsu API automatycznego sugerowania Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API automatycznego sugerowania Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/06/2019
ms.author: aahi
ms.openlocfilehash: 8460f282a4372b4297b373bad669f56f2932e323
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271617"
---
# <a name="suggesting-query-terms"></a>Sugerowanie terminów zapytania

Interfejs API automatycznego sugerowania Bing jest zwykle wywoływany za każdym razem, kiedy użytkownik wpisuje nowy znak w polu wyszukiwania w aplikacji. Kompletność ciągu zapytania ma wpływ na istotność sugerowanych terminów zapytania zwracanych przez interfejs API. Im bardziej kompletny jest ciąg zapytania, tym bardziej istotne są sugerowane terminy zapytania na wyświetlanej liście. Na przykład sugestie, które może zwrócić interfejs API po wpisaniu litery `s`, będą prawdopodobnie mniej istotne niż zapytania zwrócone dla terminu `sailing dinghies`.

## <a name="example-request"></a>Przykładowe żądanie

W poniższym przykładzie przedstawiono żądanie, które zwraca sugerowane ciągi zapytania dla terminu *sail*. Jeśli ustawiono parametr zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query), należy pamiętać o zakodowaniu w adresie URL częściowego terminu zapytania użytkownika. Na przykład, jeśli użytkownik wprowadził termin *sailing les*, ustaw parametr `q` na wartość `sailing+les` lub `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

W następującej odpowiedzi widać listę obiektów [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction), które zawierają sugerowane terminy zapytania.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Używanie sugerowanych terminów zapytania

Każda sugestia obejmuje pola `displayText`, `query` oraz `url`. Pole `displayText` zawiera sugerowane zapytanie, używane do zapełnienia listy rozwijanej w polu wyszukiwania. Należy wyświetlić wszystkie sugestie, które zawiera odpowiedź, w podanej kolejności.

Poniższy przykład przedstawia pole wyszukiwania z listą rozwijaną sugerowanych terminów zapytania z interfejsu API automatycznego sugerowania Bing.

![Pole wyszukiwania z listą rozwijaną automatycznie sugerowanych terminów](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Jeśli użytkownik wybierze sugerowane zapytanie z listy rozwijanej, termin zapytania z pola `query` zostanie użyty w celu wywołania [interfejsu API wyszukiwania w sieci Web Bing](../../bing-web-search/search-the-web.md) i wyświetlenia wyników. Alternatywnie można też użyć adresu URL z pola `url` w celu przeniesienia użytkownika na stronę wyników wyszukiwania usługi Bing.

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API automatycznego sugerowania Bing?](../get-suggested-search-terms.md)