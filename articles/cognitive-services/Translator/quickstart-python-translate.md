---
title: 'Szybki start: tłumaczenie tekstu, Python — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start przetłumaczysz tekst z jednego języka na inny przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka Python w mniej niż 10 minut.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 10/17/2018
ms.author: erhopf
ms.openlocfilehash: db2328d09f9e06ccabdc2408327a342fe94fefe0
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999586"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-python"></a>Szybki start: tłumaczenie ciągu za pomocą interfejsu API tłumaczenia tekstu w usłudze Translator oraz języka Python

W tym przewodniku Szybki start dowiesz się, jak przetłumaczyć ciąg tekstowy z języka angielskiego na włoski i niemiecki przy użyciu języka Python i interfejsu API REST tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko Python 2.7.x lub 3.x
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `translate-text.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests uuid`.

Pierwszy komentarz informuje interpreter języka Python, aby używać kodowania UTF-8. Następnie wymagane moduły są importowane w celu odczytania klucza subskrypcji ze zmiennej środowiskowej, skonstruowania żądania http, utworzenia unikatowego identyfikatora i obsłużenia odpowiedzi JSON zwracanej przez interfejs API tłumaczenia tekstu w usłudze Translator.

## <a name="set-the-subscription-key-base-url-and-path"></a>Ustawianie klucza subskrypcji, podstawowego adresu URL i ścieżki

Ten przykładowy kod spróbuje odczytać klucz subskrypcji na potrzeby tłumaczenia tekstu w usłudze Translator ze zmiennej środowiskowej `TRANSLATOR_TEXT_KEY`. Jeśli nie chcesz korzystać ze zmiennych środowiskowych, możesz ustawić element `subscriptionKey` jako ciąg i oznaczyć instrukcję warunkową jako komentarz.

Skopiuj ten kod do projektu:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

Obecnie na potrzeby tłumaczenia tekstu w usłudze Translator jest dostępny jeden punkt końcowy ustawiony jako `base_url`. Element `path` ustawia trasę `translate` i określa, że chcemy korzystać z wersji 3 interfejsu API.

Elementy `params` służą do ustawiania języków wyjściowych. W tym przykładzie tłumaczymy z języka angielskiego na włoski i niemiecki: `it` i `de`.

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator Text API 3.0: Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) (Interfejs API 3.0 tłumaczenia tekstu w usłudze Translator: tłumaczenie).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/translate?api-version=3.0'
params = '&to=de&to=it'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Dodawanie nagłówków

Najprostszym sposobem uwierzytelniania żądania jest przekazanie klucza subskrypcji jako nagłówka `Ocp-Apim-Subscription-Key`. Ta metoda jest używana w tym przykładzie. Alternatywnie można wymienić klucz subskrypcji na token dostępu i przekazać go dalej jako nagłówek `Authorization` w celu zweryfikowania żądania. Aby uzyskać więcej informacji, zobacz [Authentication](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) (Uwierzytelnianie).

Skopiuj ten fragment kod do projektu:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-translate-text"></a>Tworzenie żądania na potrzeby tłumaczenia tekstu

Zdefiniuj ciąg (lub ciągi), który ma zostać przetłumaczony:

```python
body = [{
    'text' : 'Hello World!'
}]
```

Następnie utworzymy żądanie POST przy użyciu modułu `requests`. Przyjmuje on trzy argumenty: połączony adres URL, nagłówki żądania i treść żądania:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Wyświetlanie odpowiedzi

Ostatnim krokiem jest wyświetlenie wyników. Ten fragment kodu ulepsza wyniki, sortując klucze, ustawiając wcięcia i deklarując separatory elementów i kluczy.

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To wszystko. Utworzono prosty program, który będzie wywoływał interfejs API tłumaczenia tekstu w usłudze Translator i zwracał odpowiedź w formacie JSON. Teraz nadszedł czas, aby uruchomić program:

```console
python translate-text.py
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Przykładowa odpowiedź

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli klucz subskrypcji umieszczono na stałe w kodzie programu, pamiętaj, aby usunąć ten klucz subskrypcji po zakończeniu pracy z przewodnikiem Szybki start.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w usłudze GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Zobacz też

Dowiedz się, jak używać interfejsu API tłumaczenia tekstu w usłudze Translator w następujących celach:

* [Transliteracja tekstu](quickstart-python-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-python-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-python-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-python-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-python-sentences.md)
