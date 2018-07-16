---
title: Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w języku JavaScript | Dokumentacja firmy Microsoft
description: Użyj interfejsu API rozpoznawania mowy firmy Microsoft w usługach Cognitive Services do tworzenia aplikacji, które stale Konwertuj dźwięk mówiony na tekst.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 12/21/2017
ms.author: zhouwang
ms.openlocfilehash: 04332c453d22122e65a758a65b09e17300e07f02
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040542"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Wprowadzenie do interfejsu API rozpoznawania mowy w języku JavaScript

Można tworzyć aplikacje, które Konwertuj dźwięk mówiony na tekst za pomocą interfejsu API rozpoznawania mowy. Korzysta z biblioteki klienta JavaScript [protokołu WebSocket usługi mowy](../API-Reference-REST/websocketprotocol.md), co pozwala na rozmowy i otrzymywać transkrybowanego jednocześnie tekstu. Ten artykuł ułatwia rozpoczęcie pracy z interfejsu API rozpoznawania mowy w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie do interfejsu API rozpoznawania mowy i Uzyskaj klucz subskrypcji wersji próbnej

Interfejs API mowy jest częścią usług Cognitive Services. Możesz uzyskać bezpłatną subskrypcję próbną kluczy z [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API rozpoznawania mowy, wybierz opcję **Uzyskaj klucz interfejsu API** można pobrać klucza. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tego samego limitu przydziału, aby można było używać żadnego z nich.

> [!IMPORTANT]
> Pobierz klucz subskrypcji. Korzystanie z biblioteki klienta mowy, konieczne jest posiadanie [klucz subskrypcji](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Rozpoczęcie pracy

W tej sekcji przeprowadzimy Cię przez niezbędne kroki, aby załadować przykładową stronę HTML. Przykład znajduje się w naszym [repozytorium github](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Możesz **bezpośrednio otwórz przykładową** z repozytorium lub **Otwórz przykładową z lokalną kopią** repozytorium. 

> [!NOTE]
> Niektóre przeglądarki blokować dostęp do mikrofonu w źródle cofnąć zabezpieczeń. Dlatego zaleca się hosta "przykładowy" / "aplikacja", przy użyciu protokołu https, aby przygotować go do pracy na wszystkich obsługiwanych przeglądarek. 

### <a name="open-the-sample-directly"></a>Otwórz przykładową bezpośrednio

Uzyskać klucz subskrypcji, zgodnie z powyższym opisem. Następnie otwórz [łącze do przykładu](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Spowoduje to załadowanie strony w domyślnej przeglądarce (renderowany przy użyciu [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Otwórz przykładową z lokalną kopią

Aby wypróbować przykładu na maszynę lokalną, sklonuj to repozytorium:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

kompilowanie źródeł TypeScript i powiązać je w jednym pliku JavaScript ([npm](https://www.npmjs.com/) musi być zainstalowany na komputerze). Przejdź do katalogu głównego sklonowanego repozytorium, a następnie uruchom polecenia:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Otwórz `samples\browser\Sample.html` w ulubionej przeglądarce.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat do nich zestawu SDK do swojej własnej stronie sieci Web jest dostępna [tutaj](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Uwagi

- Interfejs API rozpoznawania mowy obsługuje trzy [tryby rozpoznawania](../concepts.md#recognition-modes). Możesz przełączać tryb, aktualizując **Setup()** funkcja znajdującą się w pliku Sample.html. Przykład ustawia tryb `Interactive` domyślnie. Aby zmienić tryb, należy zaktualizować parametru `SR.RecognitionMode.Interactive` do innego trybu. Na przykład zmień parametr `SR.RecognitionMode.Conversation`.
- Aby uzyskać pełną listę obsługiwanych języków, zobacz [obsługiwane języki](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Powiązane tematy

- [Interfejs API rozpoznawania mowy JavaScript przykładowego repozytorium](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Wprowadzenie do interfejsu API REST](GetStartedREST.md)
