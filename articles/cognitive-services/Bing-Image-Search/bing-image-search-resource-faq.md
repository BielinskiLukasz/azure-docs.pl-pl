---
title: Często zadawane pytania — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejs API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 2d919640431ce014d34372812d9c07bb84d750b2
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184059"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Często zadawane pytania dotyczące interfejs API wyszukiwania obrazów Bing

Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejs API wyszukiwania obrazów Bing Microsoft Cognitive Services na platformie Azure.

## <a name="response-headers-in-javascript"></a>Nagłówki odpowiedzi w języku JavaScript

Poniższe nagłówki mogą wystąpić w odpowiedziach z interfejs API wyszukiwania obrazów Bing.

| Atrybut           | Opis   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Unikatowy identyfikator, który jest przypisany do użytkownika usługi Bing |
| `BingAPIs-Market`   |Rynek używany do realizacji żądania |
| `BingAPIs-TraceId`  |Wpis dziennika na serwerze interfejsu API Bing dla tego żądania (na potrzeby obsługi) |

Jest to szczególnie ważne, aby zachować identyfikator klienta i zwrócić go na kolejne żądania. Gdy to zrobisz, wyszukiwanie będzie używać przeszłego kontekstu w celu określania rankingu wyników wyszukiwania, a także zapewnienia spójnego środowiska użytkownika.

Jednak po wywołaniu interfejs API wyszukiwania obrazów Bing w języku JavaScript wbudowane funkcje zabezpieczeń (CORS) przeglądarki mogą uniemożliwiać dostęp do wartości tych nagłówków.

Aby uzyskać dostęp do nagłówków, można wykonać żądanie interfejs API wyszukiwania obrazów Bing za pomocą serwera proxy CORS. Odpowiedź z tego serwera proxy zawiera nagłówek `Access-Control-Expose-Headers`, który zezwala na nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

Można łatwo zainstalować serwer proxy CORS, aby umożliwić naszej [aplikacji samouczka](tutorial-bing-image-search-single-page-app.md) dostęp do opcjonalnych nagłówków klienta. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Potem wprowadź poniższe polecenie w wierszu polecenia.

```console
npm install -g cors-proxy-server
```

Następnie zmień interfejs API wyszukiwania obrazów Bing punkt końcowy w pliku HTML na: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

```console
cors-proxy-server
```

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w środowisku produkcyjnym

Podejście proxy CORS opisane w poprzedniej odpowiedzi jest odpowiednie do programowania, testowania i uczenia się.

W środowisku produkcyjnym należy jednak hostować skrypt po stronie serwera w tej samej domenie, w której znajduje się Strona sieci Web, która używa interfejs API wyszukiwania w sieci Web Bing. Ten skrypt powinien w rzeczywistości wykonać wywołania interfejsu API na żądanie ze strony sieci Web JavaScript i przekazać wszystkie wyniki, włącznie z nagłówkami, z powrotem do klienta. Ponieważ dwa zasoby (strona i skrypt) współużytkują źródło, CORS nie są odtwarzane, a specjalne nagłówki są dostępne dla języka JavaScript na stronie sieci Web.

Takie podejście umożliwia również ochronę klucza interfejsu API przed ekspozycją publiczną, ponieważ tylko skrypt po stronie serwera wymaga tego. Skrypt może użyć innej metody (na przykład odwołującej HTTP), aby upewnić się, że żądanie jest autoryzowane.

## <a name="next-steps"></a>Następne kroki

Czy masz pytanie dotyczące brakującej funkcji lub funkcji? Rozważ zażądanie lub odgłosowanie na ten temat w naszej [witrynie sieci Web głosu użytkownika](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Zobacz też

 [Stack Overflow: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)
