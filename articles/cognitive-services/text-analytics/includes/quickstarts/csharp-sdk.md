---
title: 'Szybki Start: analiza tekstua Biblioteka kliencka dla języka C# | Microsoft Docs'
description: 'Wprowadzenie do biblioteki klienta analiza tekstu dla języka C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 979c714ca5446d3f1eabb76e97535ee5ffa2e359
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140748"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Dokumentacja referencyjna [v3](https://aka.ms/azsdk-net-textanalytics-ref-docs)  |  kod źródłowy biblioteki [v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics)  |  [pakiet v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [przykłady wersji 3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Dokumentacja referencyjna w [wersji 2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview)  |  kod źródłowy biblioteki [v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics)  |  [Pakiet v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/)  |  [przykłady w wersji 2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/)
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Utwórz zasób analiza tekstu "  target="_blank"> utwórz zasób analiza tekstu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy.  Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API analizy tekstu. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji platformy .NET Core

Za pomocą środowiska IDE programu Visual Studio Utwórz nową aplikację konsolową platformy .NET Core. Spowoduje to utworzenie projektu "Hello world" z pojedynczym plikiem źródłowym C#: *program.cs*.

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj**, zaznacz pozycję **Uwzględnij wersję wstępną**i Wyszukaj `Azure.AI.TextAnalytics` . Wybierz wersję `1.0.0-preview.5` , a następnie **Zainstaluj**. Można również użyć [konsoli Menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć [w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start. 

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który zostanie otwarty, wybierz pozycję **Przeglądaj** i Wyszukaj `Microsoft.Azure.CognitiveServices.Language.TextAnalytics` . Kliknij go, a następnie **Zainstaluj**. Można również użyć [konsoli Menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć [w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start. 

---

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Otwórz plik *program.cs* i Dodaj następujące `using` dyrektywy:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

W `Program` klasie aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Zastąp metodę aplikacji `Main` . Metody wywoływane tutaj zostaną zdefiniowane później.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Otwórz plik *program.cs* i Dodaj następujące `using` dyrektywy:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

W `Program` klasie aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Zastąp metodę aplikacji `Main` . Metody wywoływane tutaj zostaną zdefiniowane później.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest `TextAnalyticsClient` obiektem, który jest uwierzytelniany na platformie Azure przy użyciu klucza, i udostępnia funkcje do akceptowania tekstu jako pojedyncze ciągi lub jako partii. Możesz wysyłać tekst do interfejsu API synchronicznie lub asynchronicznie. Obiekt Response będzie zawierać informacje o analizie dla każdego wysyłanego dokumentu. 

Jeśli używasz wersji `3.0` usługi, możesz użyć opcjonalnego `TextAnalyticsClientOptions` wystąpienia, aby zainicjować klienta z różnymi ustawieniami domyślnymi (na przykład domyślny język lub wskazówkę kraju/regionu). Uwierzytelnianie można także przeprowadzić przy użyciu tokenu Azure Active Directory. 

## <a name="code-examples"></a>Przykłady kodu

* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Upewnij się, że główna Metoda z wcześniej tworzy nowy obiekt klienta z punktem końcowym i poświadczeniami.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nową `ApiKeyServiceClientCredentials` klasę do przechowywania poświadczeń i Dodaj je do żądań klienta. W tym celu należy utworzyć przesłonięcie, `ProcessHttpRequestAsync()` które dodaje klucz do `Ocp-Apim-Subscription-Key` nagłówka.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Utwórz metodę, aby utworzyć wystąpienie obiektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) z punktem końcowym i `ApiKeyServiceClientCredentials` obiektem zawierającym klucz.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Utwórz nową funkcję o nazwie `SentimentAnalysisExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `AnalyzeSentiment()` funkcję. Zwrócony `Response<DocumentSentiment>` obiekt będzie zawierać etykietę tonacji i ocenę całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania, jeśli to się powiedzie. Jeśli wystąpił błąd, wygeneruje `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nową funkcję o nazwie `SentimentAnalysisExample()` , która przybiera wcześniej utworzoną klienta i wywołaj funkcję [tonacji ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) . Zwrócony obiekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) będzie zawierać tonacji w `Score` przypadku powodzenia, a `errorMessage` Jeśli nie. 

Wynik zbliżony do 0 wskazuje negatywną tonacji, podczas gdy wynik zbliżony do 1 wskazuje pozytywną tonacji.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)


Utwórz nową funkcję o nazwie `LanguageDetectionExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `DetectLanguage()` funkcję. Zwrócony `Response<DetectedLanguage>` obiekt będzie zawierać wykryty język wraz z jego nazwą i kodem ISO-6391. Jeśli wystąpił błąd, wygeneruje `RequestFailedException` .

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Możesz użyć parametru, `countryHint` Aby określić 2-literowy kod kraju/regionu. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` . Aby ustawić inne ustawienia domyślne, należy ustawić `TextAnalyticsClientOptions.DefaultCountryHint` Właściwość i przekazać ją podczas inicjowania klienta.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Dane wyjściowe

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nową funkcję o nazwie `languageDetectionExample()` , która przybiera wcześniej utworzoną klienta i wywołaj funkcję [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Zwrócony obiekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) będzie zawierać listę wykrytych języków w `DetectedLanguages` przypadku powodzenia, a `errorMessage` Jeśli nie. Drukowanie pierwszego zwracanego języka.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Możesz użyć parametru, `countryHint` Aby określić 2-literowy kod kraju/regionu. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Dane wyjściowe

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)


> [!NOTE]
> Nowość w wersji `3.0` :
> * Łączenie jednostek jest teraz oddzielone od rozpoznawania jednostek.


Utwórz nową funkcję o nazwie `EntityRecognitionExample()` , która przyjmuje wcześniej utworzony klient, wywołaj jego `RecognizeEntities()` funkcję i wykonaj iterację w wynikach. Zwrócony `Response<IReadOnlyCollection<CategorizedEntity>>` obiekt będzie zawierać listę wykrytych jednostek. Jeśli wystąpił błąd, wygeneruje `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie `EntityLinkingExample()` , która przyjmuje wcześniej utworzony klient, wywołaj jego `RecognizeLinkedEntities()` funkcję i wykonaj iterację w wynikach. Zwrócony element `Response<IReadOnlyCollection<LinkedEntity>>` reprezentuje listę wykrytych jednostek. Jeśli wystąpił błąd, wygeneruje `RequestFailedException` . Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są pogrupowane pod `LinkedEntity` obiektem jako lista `LinkedEntityMatch` obiektów.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

> [!NOTE]
> W wersji 2,1, konsolidacja jednostki jest uwzględniona w odpowiedzi NER.

Utwórz nową funkcję o nazwie `RecognizeEntitiesExample()` , która przybiera wcześniej utworzoną klienta i wywołaj funkcję [Entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Wykonaj iterację w wynikach. Zwrócony obiekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) będzie zawierać listę wykrytych jednostek w `Entities` przypadku powodzenia, a `errorMessage` Jeśli nie. Dla każdej wykrytej jednostki Wydrukuj jej typ, podtyp, nazwę witryny Wikipedia (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

#### <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `ExtractKeyPhrases()` funkcję. Zwrócony `<Response<IReadOnlyCollection<string>>` obiekt będzie zawierać listę wykrytych fraz kluczy. Jeśli wystąpił błąd, wygeneruje `RequestFailedException` .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()` , która powoduje, że klient został utworzony wcześniej i wywołaj funkcję [frazy kluczowej ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Wynik będzie zawierać listę wykrytych fraz kluczy w `KeyPhrases` przypadku pomyślnego, a `errorMessage` Jeśli nie. Drukuj wszystkie wykryte frazy kluczy.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

---
