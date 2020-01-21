---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/02/2019
ms.author: aahi
ms.openlocfilehash: 637288933037b10aab8c22f7c629e52751057ad6
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281404"
---
[Dokumentacja referencyjna](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) |  | pakietu [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) [(RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) [ | ](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja języka [Ruby](https://www.ruby-lang.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-text-analytics-azure-resource"></a>Tworzenie zasobu analiza tekstu platformy Azure 

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-ruby-application"></a>Tworzenie nowej aplikacji Ruby

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. Następnie utwórz plik o nazwie `GemFile`i plik Ruby dla kodu.

```console
mkdir myapp && cd myapp
```

W `GemFile`Dodaj następujące wiersze, aby dodać bibliotekę klienta jako zależność.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

W pliku Ruby Zaimportuj następujące pakiety.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. 

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info-v2.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Model obiektów 

Klient analiza tekstu jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, w postaci pojedynczego ciągu lub partii. 

Tekst jest wysyłany do interfejsu API jako lista `documents`, które są `dictionary` obiektów zawierających kombinację atrybutów `id`, `text`i `language` w zależności od używanej metody. Atrybut `text` przechowuje tekst, który ma być analizowany w `language`źródłowym, a `id` może być dowolną wartością. 

Obiekt Response jest listą zawierającą informacje o analizie dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta analiza tekstu dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek](#entity-recognition)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz klasę o nazwie `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

W tej klasie Utwórz funkcję o nazwie `initialize`, aby uwierzytelnić klienta przy użyciu klucza i punktu końcowego. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Poza klasą Użyj funkcji `new()` klienta, aby ją utworzyć.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analiza opinii

W obiekcie Client Utwórz funkcję o nazwie `AnalyzeSentiment()`, która pobiera listę dokumentów wejściowych, które zostaną utworzone później. Wywołaj funkcję `sentiment()` klienta i uzyskaj wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz tonacji ocenę. Wynik zbliżony do 0 wskazuje negatywną tonacji, natomiast wynik zbliżony do 1 wskazuje pozytywny tonacji.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Poza funkcją klienta Utwórz nową funkcję o nazwie `SentimentAnalysisExample()`, która przyjmuje wcześniej utworzony obiekt `TextAnalyticsClient`. Utwórz listę obiektów `MultiLanguageInput` zawierających dokumenty, które chcesz przeanalizować. Każdy obiekt będzie zawierać `id`, `Language` i `text` atrybut. Atrybut `text` przechowuje tekst do przeanalizowania, `language` jest językiem dokumentu, a `id` może być dowolną wartością. Następnie wywołaj funkcję `AnalyzeSentiment()` klienta.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Wywołaj funkcję `SentimentAnalysisExample()`.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Dane wyjściowe

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Wykrywanie języka

W obiekcie Client Utwórz funkcję o nazwie `DetectLanguage()`, która pobiera listę dokumentów wejściowych, które zostaną utworzone później. Wywołaj funkcję `detect_language()` klienta i uzyskaj wynik. Następnie można wykonać iterację w wynikach i wydrukować każdy dokument o IDENTYFIKATORze oraz wykrytym języku.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Poza funkcją klienta Utwórz nową funkcję o nazwie `DetectLanguageExample()`, która przyjmuje wcześniej utworzony obiekt `TextAnalyticsClient`. Utwórz listę obiektów `LanguageInput` zawierających dokumenty, które chcesz przeanalizować. Każdy obiekt będzie zawierać `id`i atrybut `text`. Atrybut `text` przechowuje tekst do przeanalizowania, a `id` może być dowolną wartością. Następnie wywołaj funkcję `DetectLanguage()` klienta.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Wywołaj funkcję `DetectLanguageExample()`.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Dane wyjściowe

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

W obiekcie Client Utwórz funkcję o nazwie `RecognizeEntities()`, która pobiera listę dokumentów wejściowych, które zostaną utworzone później. Wywołaj funkcję `entities()` klienta i uzyskaj wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz rozpoznane jednostki.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Poza funkcją klienta Utwórz nową funkcję o nazwie `RecognizeEntitiesExample()`, która przyjmuje wcześniej utworzony obiekt `TextAnalyticsClient`. Utwórz listę obiektów `MultiLanguageInput` zawierających dokumenty, które chcesz przeanalizować. Każdy obiekt będzie zawierać `id`, `language`i atrybut `text`. Atrybut `text` przechowuje tekst do przeanalizowania, `language` jest językiem tekstu, a `id` może być dowolną wartością. Następnie wywołaj funkcję `RecognizeEntities()` klienta.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Wywołaj funkcję `RecognizeEntitiesExample()`.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Dane wyjściowe

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

W obiekcie Client Utwórz funkcję o nazwie `ExtractKeyPhrases()`, która pobiera listę dokumentów wejściowych, które zostaną utworzone później. Wywołaj funkcję `key_phrases()` klienta i uzyskaj wynik. Następnie wykonuje iterację w wynikach i drukuje identyfikator każdego dokumentu oraz wyodrębnione kluczowe frazy.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Poza funkcją klienta Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()`, która przyjmuje wcześniej utworzony obiekt `TextAnalyticsClient`. Utwórz listę obiektów `MultiLanguageInput` zawierających dokumenty, które chcesz przeanalizować. Każdy obiekt będzie zawierać `id`, `language`i atrybut `text`. Atrybut `text` przechowuje tekst do przeanalizowania, `language` jest językiem tekstu, a `id` może być dowolną wartością. Następnie wywołaj funkcję `ExtractKeyPhrases()` klienta.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Wywołaj funkcję `KeyPhraseExtractionExample()`.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```
