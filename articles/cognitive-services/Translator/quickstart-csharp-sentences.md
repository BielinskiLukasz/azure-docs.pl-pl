---
title: 'Szybki start: Uzyskiwanie długości zdań, C# — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak określać długość zdań przy użyciu platformy .NET Core i interfejsu API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: c51db283a03c0259a68cc59e4c38abf6b47bef58
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732009"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>Szybki start: Korzystanie z interfejsu API tłumaczenia tekstu w usłudze Translator do określania długości zdań przy użyciu języka C#

W tym przewodniku Szybki start dowiesz się, jak określać długości zdań przy użyciu platformy .NET Core i interfejsu API tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw SDK platformy .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Pakiet NuGet platformy Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="create-a-net-core-project"></a>Tworzenie projektu platformy .NET Core

Otwórz nowy wiersz polecenia (lub sesję terminalową), a następnie uruchom następujące polecenia:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

Pierwsze polecenie wykonuje dwie czynności. Tworzy nową aplikację konsolową platformy .NET i katalog o nazwie `sentences-sample`. Drugie polecenie zmienia katalog na katalog projektu.

Następnie należy zainstalować element Json.Net. Z poziomu katalogu projektu uruchom:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Dodawanie wymaganych obszarów nazw do projektu

Uruchomione wcześnie polecenie `dotnet new console` spowodowało utworzenie projektu obejmującego plik `Program.cs`. W tym pliku umieścisz kod aplikacji. Otwórz plik `Program.cs` i zastąp istniejące instrukcje using. Te instrukcje gwarantują dostęp do wszystkich typów wymaganych do skompilowania i uruchomienia przykładowej aplikacji.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-determine-sentence-length"></a>Tworzenie funkcji w celu określenia długości zdania

W klasie `Program` utwórz funkcję o nazwie `BreakSentence`. Ta klasa hermetyzuje kod używany do wywołania zasobu BreakSentence i wyświetla wynik w konsoli.

```csharp
static void BreakSentence()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Ustawianie klucza subskrypcji, nazwy hosta i ścieżki

Dodaj następujące wiersze do funkcji `BreakSentence`. Zauważysz, że razem z wersją `api-version` można zdefiniować język wejściowy. W tym przykładzie jest to język angielski.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/breaksentence?api-version=3.0&language=en";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

Następnie należy utworzyć i serializować obiekt JSON obejmujący tekst. Pamiętaj, że możesz przekazać więcej niż jeden obiekt w tablicy `body`.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"How are you? I am fine. What did you do today?" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Tworzenie wystąpienia klienta i wysyłanie żądania

W tych wierszach są tworzone wystąpienia elementów `HttpClient` i `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Konstruowanie żądania i wyświetlanie odpowiedzi

Wewnątrz elementu `HttpRequestMessage` wykonasz następujące czynności:

* Deklarowanie metody HTTP
* Konstruowanie identyfikatora URI żądania
* Wstawianie treści żądania (serializowanego obiektu JSON)
* Dodawanie wymaganych nagłówków
* Wykonywanie żądania asynchronicznego
* Wyświetlanie odpowiedzi

Dodaj następujący kod do elementu `HttpRequestMessage`:

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wywołanie instrukcji `BreakSentence()` w funkcji `Main`. Znajdź kod `static void Main(string[] args)` i dodaj następujące wiersze:

```csharp
BreakSentence();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
dotnet run
```

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

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z kodem przykładowym z tego przewodnika Szybki start i innych, dotyczącym między innymi transliteracji i rozpoznawania języka, a także z innymi projektami przykładowymi dotyczącymi tłumaczenia tekstów w usłudze Translator w repozytorium GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Zobacz też

* [Tłumaczenie tekstu](quickstart-csharp-translate.md)
* [Transliteracja tekstu](quickstart-csharp-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-csharp-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-csharp-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-csharp-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-csharp-sentences.md)
