---
title: 'Szybki start: tłumaczenie tekstu, Go — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start przetłumaczysz tekst z jednego języka na inny przy użyciu interfejsu API tłumaczenia tekstu w usłudze Translator i języka Go w mniej niż 10 minut.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: a1218442aedf94028fe1e08f02f13ce1c0d05008
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738117"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-go"></a>Szybki start: tłumaczenie ciągu za pomocą interfejsu API tłumaczenia tekstu w usłudze Translator oraz języka Go

W tym przewodniku Szybki start dowiesz się, jak przetłumaczyć ciąg tekstowy z języka angielskiego na włoski i niemiecki przy użyciu języka Go i interfejsu API REST tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Go](https://golang.org/doc/install)
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt w języku Go przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `translate-text.go`.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Tworzenie funkcji main

Ten przykładowy kod spróbuje odczytać klucz subskrypcji na potrzeby tłumaczenia tekstu w usłudze Translator ze zmiennej środowiskowej `TRANSLATOR_TEXT_KEY`. Jeśli nie chcesz korzystać ze zmiennych środowiskowych, możesz ustawić element `subscriptionKey` jako ciąg i oznaczyć instrukcję warunkową jako komentarz.

Skopiuj ten kod do projektu:

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our translate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    translate(subscriptionKey)
}
```

## <a name="create-a-function-to-translate-text"></a>Tworzenie funkcji na potrzeby tłumaczenia tekstu

Utwórzmy funkcję na potrzeby tłumaczenia tekstu. Ta funkcja będzie przyjmować jeden argument — Twój klucz subskrypcji na potrzeby tłumaczenia tekstu w usłudze Translator.

```go
func translate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Następnie utwórzmy adres URL. Adres URL jest tworzony przy użyciu metod `Parse()` i `Query()`. Można zauważyć, że parametry są dodawane przy użyciu metody `Add()`. W tym przykładzie tłumaczymy z języka angielskiego na włoski i niemiecki: `de` i `it`.

Skopiuj ten kod do funkcji `translate`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/translate?api-version=3.0")
q := u.Query()
q.Add("to", "de")
q.Add("to", "it")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator Text API 3.0: Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) (Interfejs API 3.0 tłumaczenia tekstu w usłudze Translator: tłumaczenie).

## <a name="create-a-struct-for-your-request-body"></a>Tworzenie struktury treści żądania

Następnie utwórz anonimową strukturę treści żądania i zakoduj ją jako dane JSON przy użyciu metody `json.Marshal()`. Dodaj ten kod do funkcji `translate`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Hello, world!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Tworzenie żądania

Teraz, gdy treść żądania została zakodowana jako dane JSON, możesz utworzyć żądanie POST i wywołać interfejs API tłumaczenia tekstu w usłudze Translator.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>Obsługa i wyświetlanie odpowiedzi

Dodaj następujący kod do funkcji `translate`, aby zdekodować odpowiedź JSON, a następnie sformatować i wyświetlić wynik.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To wszystko. Utworzono prosty program, który będzie wywoływał interfejs API tłumaczenia tekstu w usłudze Translator i zwracał odpowiedź w formacie JSON. Teraz nadszedł czas, aby uruchomić program:

```console
go run translate-text.go
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Przykładowa odpowiedź

Po pomyślnym przetworzeniu żądania jest zwracana odpowiedź w formacie JSON, jak pokazano na poniższym przykładzie:

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

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pakietami w języku Go dla interfejsów API usług Cognitive Services na stronie [Azure SDK for Go (Zestaw Azure SDK dla języka Go)](https://github.com/Azure/azure-sdk-for-go) w repozytorium GitHub.

> [!div class="nextstepaction"]
> [Explore Go packages on GitHub (Zapoznaj się z pakietami dla języka Go w repozytorium GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Zobacz też

Dowiedz się, jak używać interfejsu API tłumaczenia tekstu w usłudze Translator w następujących celach:

* [Transliteracja tekstu](quickstart-go-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-go-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-go-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-go-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-go-sentences.md)
