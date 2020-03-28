---
title: 'Szybki start: biblioteka klientów analizy tekstu dla Ruby | Dokumenty firmy Microsoft'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wykryj język przy użyciu biblioteki klienta analizy tekstu Ruby z usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0d4d32a413dd22c55f1b2f01dce3a3df81f5f729
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77919672"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Szybki start: użyj biblioteki klienta analizy tekstu dla Ruby

Wprowadzenie do biblioteki klienta analizy tekstu. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta analizy tekstu, aby wykonać:

* Analiza tonacji
* Wykrywanie języka
* Rozpoznawanie jednostek
* Wyodrębnianie kluczowych fraz

> [!NOTE]
> Ten szybki start dotyczy tylko analizy tekstu w wersji 2.1. Obecnie biblioteka klienta w wersji 3 dla Ruby jest niedostępna.

[Dokumentacja](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [referencyjna Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [(RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [biblioteki](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Aktualna wersja [Ruby](https://www.ruby-lang.org/)
* Po utworzeniu subskrypcji <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="platformy Azure utwórz"  target="_blank">zasób <span class="docon docon-navigate-external x-hidden-focus"></span> </a> analizy tekstu w witrynie Azure portal, aby uzyskać klucz i punkt końcowy. 
    * Potrzebny będzie klucz i punkt końcowy z zasobu, który tworzysz, aby połączyć aplikację z interfejsem API analizy tekstu. Zrobisz to później w przewodniku Szybki start.
    * Można użyć bezpłatnej warstwy cenowej, aby wypróbować usługę i uaktualnić później do warstwy płatnej dla produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-ruby-application"></a>Tworzenie nowej aplikacji Ruby

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego. Następnie utwórz `GemFile`plik o nazwie i plik Ruby dla kodu.

```console
mkdir myapp && cd myapp
```

W `GemFile`programie dodaj następujące wiersze, aby dodać bibliotekę klienta jako zależność.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

W pliku Ruby zaimportuj następujące pakiety.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Tworzenie zmiennych dla punktu końcowego i klucza platformy Azure zasobu. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Model obiektu 

Klient analizy tekstu uwierzytelnia się na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, jako pojedynczy ciąg lub partii. 

Tekst jest wysyłany do interfejsu `documents`API `dictionary` jako lista , `id`które `text`są `language` obiektami zawierającymi kombinację , i atrybutów w zależności od zastosowanej metody. Atrybut `text` przechowuje tekst do analizy w `language`źródle i `id` może być dowolną wartością. 

Obiekt odpowiedzi jest listą zawierającą informacje analityczne dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta analizy tekstu dla języka Python:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek](#entity-recognition)
* [Wyodrębnianie fraz kluczowych](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Tworzenie klasy `TextAnalyticsClient`o nazwie . 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

W tej klasie utwórz `initialize` funkcję wywoływaną do uwierzytelniania klienta przy użyciu klucza i punktu końcowego. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Poza klasą użyj `new()` funkcji klienta, aby utworzyć jej wystąpienie.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analiza tonacji

W obiekcie klienta utwórz `AnalyzeSentiment()` funkcję o nazwie, która przyjmuje listę dokumentów wejściowych, które zostaną utworzone później. Zadzwoń do `sentiment()` funkcji klienta i uzyskać wynik. Następnie iterować przez wyniki i wydrukować identyfikator każdego dokumentu i wynik tonacji. Wynik bliższy 0 wskazuje na negatywne nastroje, podczas gdy wynik bliższy 1 wskazuje na pozytywne nastroje.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Poza funkcją klienta utwórz nową `SentimentAnalysisExample()` funkcję `TextAnalyticsClient` o nazwie, która przyjmuje obiekt utworzony wcześniej. Utwórz listę `MultiLanguageInput` obiektów zawierającą dokumenty, które chcesz analizować. Każdy obiekt będzie `id` `Language` zawierał `text` atrybut i atrybut. Atrybut `text` przechowuje tekst do analizy, `language` jest językiem dokumentu i `id` może być dowolną wartością. Następnie wywołaj `AnalyzeSentiment()` funkcję klienta.

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

W obiekcie klienta utwórz `DetectLanguage()` funkcję o nazwie, która przyjmuje listę dokumentów wejściowych, które zostaną utworzone później. Zadzwoń do `detect_language()` funkcji klienta i uzyskać wynik. Następnie iteruj przez wyniki i wydrukuj identyfikator każdego dokumentu i wykryty język.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Poza funkcją klienta utwórz nową `DetectLanguageExample()` funkcję `TextAnalyticsClient` o nazwie, która przyjmuje obiekt utworzony wcześniej. Utwórz listę `LanguageInput` obiektów zawierającą dokumenty, które chcesz analizować. Każdy obiekt będzie `id`zawierał `text` atrybut i atrybut. Atrybut `text` przechowuje tekst do analizy i `id` może być dowolną wartością. Następnie wywołaj `DetectLanguage()` funkcję klienta.

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

W obiekcie klienta utwórz `RecognizeEntities()` funkcję o nazwie, która przyjmuje listę dokumentów wejściowych, które zostaną utworzone później. Zadzwoń do `entities()` funkcji klienta i uzyskać wynik. Następnie iterować przez wyniki i wydrukować identyfikator każdego dokumentu i rozpoznane jednostki.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Poza funkcją klienta utwórz nową `RecognizeEntitiesExample()` funkcję `TextAnalyticsClient` o nazwie, która przyjmuje obiekt utworzony wcześniej. Utwórz listę `MultiLanguageInput` obiektów zawierającą dokumenty, które chcesz analizować. Każdy obiekt będzie `id`zawierał `language`atrybut `text` , a i atrybut. Atrybut `text` przechowuje tekst do analizy, `language` jest językiem tekstu i `id` może być dowolną wartością. Następnie wywołaj `RecognizeEntities()` funkcję klienta.

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

W obiekcie klienta utwórz `ExtractKeyPhrases()` funkcję o nazwie, która przyjmuje listę dokumentów wejściowych, które zostaną utworzone później. Zadzwoń do `key_phrases()` funkcji klienta i uzyskać wynik. Następnie iterować przez wyniki i wydrukować identyfikator każdego dokumentu i wyodrębnione frazy kluczowe.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Poza funkcją klienta utwórz nową `KeyPhraseExtractionExample()` funkcję `TextAnalyticsClient` o nazwie, która przyjmuje obiekt utworzony wcześniej. Utwórz listę `MultiLanguageInput` obiektów zawierającą dokumenty, które chcesz analizować. Każdy obiekt będzie `id`zawierał `language`atrybut `text` , a i atrybut. Atrybut `text` przechowuje tekst do analizy, `language` jest językiem tekstu i `id` może być dowolną wartością. Następnie wywołaj `ExtractKeyPhrases()` funkcję klienta.

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
