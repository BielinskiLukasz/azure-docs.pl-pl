---
title: 'Szybki start: uzyskiwanie długości zdań, Node.js — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak określać długość zdań (w znakach) przy użyciu środowiska Node.js i interfejsu API REST tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 10/29/2018
ms.author: erhopf
ms.openlocfilehash: 09d0c855aada753b0a3e594055ec188c2698e677
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999009"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-with-nodejs"></a>Szybki start: korzystanie z interfejsu API tłumaczenia tekstu w usłudze Translator do określania długości zdań przy użyciu środowiska Node.js

W tym przewodniku Szybki start dowiesz się, jak określać długość zdań (w znakach) przy użyciu środowiska Node.js i interfejsu API REST tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko [Node w wersji 8.12.x lub nowszej](https://nodejs.org/en/)
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `sentence-length.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `npm install request uuidv4`.

Te moduły są wymagane do utworzenia żądania HTTP i unikatowego identyfikatora dla nagłówka `'X-ClientTraceId'`.

## <a name="set-the-subscription-key"></a>Ustawianie klucza subskrypcji

Ten kod spróbuje odczytać klucz subskrypcji na potrzeby tłumaczenia tekstu w usłudze Translator ze zmiennej środowiskowej `TRANSLATOR_TEXT_KEY`. Jeśli nie chcesz korzystać ze zmiennych środowiskowych, możesz ustawić element `subscriptionKey` jako ciąg i oznaczyć instrukcję warunkową jako komentarz.

Skopiuj ten kod do projektu:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>Konfigurowanie żądania

Metoda `request()`, udostępniona przez moduł żądania, umożliwia nam przekazanie metody HTTP, parametrów żądania adresu URL, nagłówków i treści w formacie JSON w postaci obiektu `options`. W poniższym fragmencie kodu skonfigurujemy żądanie:

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator Text API 3.0: Break Sentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) (Interfejs API 3.0 tłumaczenia tekstu w usłudze Translator: podział zdania).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'breaksentence',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'How are you? I am fine. What did you do today?'
    }],
    json: true,
};
```

### <a name="authentication"></a>Authentication

Najprostszym sposobem uwierzytelniania żądania jest przekazanie klucza subskrypcji jako nagłówka `Ocp-Apim-Subscription-Key`. Ta metoda jest używana w tym przykładzie. Alternatywnie można wymienić klucz subskrypcji na token dostępu i przekazać go dalej jako nagłówek `Authorization` w celu zweryfikowania żądania. Aby uzyskać więcej informacji, zobacz [Authentication](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) (Uwierzytelnianie).

## <a name="make-the-request-and-print-the-response"></a>Wysyłanie żądania i wyświetlanie odpowiedzi

Następnie utworzymy żądanie przy użyciu metody `request()`. Pobiera ona obiekt `options` utworzony w poprzedniej sekcji jako pierwszy argument, a następnie wyświetla ulepszoną odpowiedź w formacie JSON.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> W tym przykładzie definiujemy żądanie HTTP w obiekcie `options`. Jednak moduł żądania obsługuje również metody ułatwiające, takie jak `.post` i `.get`. Aby uzyskać więcej informacji, zobacz [Convenience methods](https://github.com/request/request#convenience-methods) (Metody ułatwiające).

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To wszystko. Utworzono prosty program, który będzie wywoływał interfejs API tłumaczenia tekstu w usłudze Translator i zwracał odpowiedź w formacie JSON. Teraz nadszedł czas, aby uruchomić program:

```console
node sentence-length.js
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Przykładowa odpowiedź

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli klucz subskrypcji umieszczono na stałe w kodzie programu, pamiętaj, aby usunąć ten klucz subskrypcji po zakończeniu pracy z przewodnikiem Szybki start.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla środowiska Node.js w usłudze GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>Zobacz też

Oprócz wykrywania języka interfejs API tłumaczenia tekstu w usłudze Translator może wykonywać następujące zadania:

* [Tłumaczenie tekstu](quickstart-nodejs-translate.md)
* [Transliteracja tekstu](quickstart-nodejs-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-nodejs-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-nodejs-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-nodejs-languages.md)
