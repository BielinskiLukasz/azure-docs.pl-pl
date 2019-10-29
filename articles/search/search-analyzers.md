---
title: Analizatory dla przetwarzania lingwistycznego i tekstu — Azure Search
description: Przypisz analizatory do pól tekstowych z możliwością wyszukiwania w indeksie, aby zastąpić domyślne standardowe Lucene z niestandardowym, wstępnie zdefiniowanym lub specyficznym dla języka alternatywą.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: heidist
manager: nitinme
author: HeidiSteen
ms.openlocfilehash: 85ebc75a22a4b27803df758d3f411a46a6206eb7
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987617"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analizatory do przetwarzania tekstu w Azure Search

*Analizator* jest składnikiem [aparatu wyszukiwania pełnotekstowego](search-lucene-query-architecture.md) odpowiedzialnego za przetwarzanie tekstu w ciągach zapytań i indeksowanych dokumentach. Różne analizatory manipulują tekstem na różne sposoby w zależności od scenariusza. Analizatory języka przetwarzają tekst przy użyciu reguł lingwistycznych, aby zwiększyć jakość wyszukiwania, podczas gdy inne analizatory wykonują bardziej podstawowe zadania, takie jak konwertowanie znaków na małe litery, na przykład. 

Analizatory języka są najczęściej używane, a dla każdego pola możliwego do przeszukiwania w indeksie Azure Search jest przypisana domyślna analiza języka. Podczas analizy tekstu typowe są następujące przekształcenia języka:

+ Wyrazy nieistotne (Stop-słowa) i interpunkcja są usuwane.
+ Frazy i wyrazy w wyrazach są podzielone na części składnika.
+ Wielkie litery są małymi literami.
+ Wyrazy są skracane do formularzy głównych, dzięki czemu można znaleźć dopasowanie niezależnie od intensywności.

Analizatory języka konwertują wprowadzanie tekstu do formularzy podstawowych lub głównych, które są wydajne w celu przechowywania i pobierania informacji. Konwersja występuje podczas indeksowania, gdy indeks zostanie skompilowany, a następnie ponownie podczas wyszukiwania, gdy indeks zostanie odczytany. Jeśli używasz tego samego analizatora dla obu operacji, możesz uzyskać oczekiwany wynik wyszukiwania.

## <a name="default-analyzer"></a>Analizator domyślny  

Azure Search używa programu [Apache Lucene Standard (standardowa Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) jako domyślnego, który dzieli tekst na elementy po regułach ["segmentacji tekstu Unicode"](https://unicode.org/reports/tr29/) . Dodatkowo Analizator standardowy konwertuje wszystkie znaki na ich małą literę. Zarówno indeksowane dokumenty, jak i wyszukiwane terminy przechodzą przez analizę podczas indeksowania i przetwarzania zapytań.  

Jest ona używana automatycznie dla każdego pola z możliwością wyszukiwania. Można przesłonić wartość domyślną dla każdego pola. Alternatywni analizatory mogą być [analizatorem języka](index-add-language-analyzers.md), [analizatorem niestandardowym](index-add-custom-analyzers.md)lub predefiniowaną z [listy dostępnych](index-add-custom-analyzers.md#AnalyzerTable)narzędzi do analizowania.


## <a name="types-of-analyzers"></a>Typy analizatorów

Na poniższej liście opisano, które analizatory są dostępne w Azure Search.

| Kategoria | Opis |
|----------|-------------|
| [Standardowy Analizator Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Domyślne. Nie jest wymagana Specyfikacja ani konfiguracja. Ten Analizator ogólnego przeznaczenia dobrze sprawdza się w przypadku większości języków i scenariuszy.|
| Wstępnie zdefiniowane analizatory | Oferowany jako gotowy produkt przeznaczony do użycia w przypadku, gdy jest to możliwe. <br/>Istnieją dwa typy: wyspecjalizowane i językowe. Co sprawia, że "wstępnie zdefiniowane" polega na odwoływaniu się do nich według nazwy, bez konfiguracji ani dostosowywania. <br/><br/>[Analizatory wyspecjalizowane (Language-niezależny od)](index-add-custom-analyzers.md#AnalyzerTable) są używane, gdy dane wejściowe wymagają wyspecjalizowanego przetwarzania lub minimalnego przetwarzania. Analizatory wstępnie zdefiniowane w języku innym niż język to **Asciifolding**, **słowo kluczowe**, **wzorzec**, **prosty**, **stop**, **biały**znak.<br/><br/>[Analizatory języka](index-add-language-analyzers.md) są używane, gdy potrzebna jest zaawansowana obsługa języków w poszczególnych językach. Azure Search obsługuje 35 analizatorów języka (Lucene) i 50 analizatorów przetwarzania języka naturalnego firmy Microsoft. |
|[Analizatory niestandardowe](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Odnosi się do zdefiniowanej przez użytkownika konfiguracji kombinacji istniejących elementów, która składa się z jednego tokenizatora (wymagane) i opcjonalnych filtrów (Char lub token).|

Kilka wstępnie zdefiniowanych analizatorów, takich jak **wzorzec** lub **Zatrzymywanie**, obsługują ograniczony zestaw opcji konfiguracji. Aby ustawić te opcje, można efektywnie utworzyć Analizator niestandardowy składający się z wstępnie zdefiniowanego analizatora i jedną z opcji alternatywnych udokumentowanych w [wstępnie zdefiniowanych odwołaniach analizatora](index-add-custom-analyzers.md#AnalyzerTable). Podobnie jak w przypadku dowolnej konfiguracji niestandardowej, należy podać nową konfigurację przy użyciu nazwy, takiej jak *myPatternAnalyzer* , aby odróżnić ją od analizatora wzorców Lucene.

## <a name="how-to-specify-analyzers"></a>Jak określić analizatory

1. (tylko dla analizatorów niestandardowych) Utwórz sekcję nazwanego **analizatora** w definicji indeksu. Aby uzyskać więcej informacji, zobacz [Tworzenie indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) i [Dodawanie niestandardowych analizatorów](index-add-custom-analyzers.md).

2. Dla [definicji pola](https://docs.microsoft.com/rest/api/searchservice/create-index) w indeksie ustaw właściwość **analizatora** pola na nazwę analizatora docelowego (na przykład `"analyzer" = "keyword"`. Prawidłowe wartości to nazwa wstępnie zdefiniowanego analizatora, analizatora języka lub analizatora niestandardowego zdefiniowanego w schemacie indeksu. Zaplanuj przypisanie analizatora w fazie definicji indeksu przed utworzeniem indeksu w usłudze.

3. Opcjonalnie, zamiast jednej właściwości **analizatora** , można ustawić różne analizy do indeksowania i wykonywania zapytań przy użyciu parametrów pola **indexAnalyzer** i **searchAnalyzer** . Przy przygotowywaniu i pobieraniu danych należy używać różnych analizatorów, jeśli jedno z tych działań wymagało konkretnego przekształcenia, które nie jest potrzebne.

Przypisanie **analizatora** lub **indexAnalyzer** do pola, które zostało już utworzone fizycznie jest niedozwolone. Jeśli którykolwiek z tych elementów jest niejasny, zapoznaj się z poniższą tabelą, aby zapoznać się z tym, które akcje wymagają odbudowania i dlaczego.
 
 | Scenariusz | Wpływ | Kroki |
 |----------|--------|-------|
 | Dodaj nowe pole | Mniejsze | Jeśli pole nie istnieje jeszcze w schemacie, nie istnieje żadna poprawka pola, ponieważ pole nie ma jeszcze fizycznej obecności w indeksie. Możesz użyć [indeksu aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-index) , aby dodać nowe pole do istniejącego indeksu i [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) je wypełnić.|
 | Dodaj **Analizator** lub **indexAnalyzer** do istniejącego pola indeksowanego. | [odtworzyć](search-howto-reindex.md) | Odwrócony indeks dla tego pola musi zostać odtworzony od podstaw, a zawartość tych pól musi być ponownie indeksowana. <br/> <br/>W przypadku indeksów pod aktywnym programowaniem [Usuń](https://docs.microsoft.com/rest/api/searchservice/delete-index) i [Utwórz](https://docs.microsoft.com/rest/api/searchservice/create-index) indeks, aby pobrać nową definicję pola. <br/> <br/>W przypadku indeksów w środowisku produkcyjnym można odroczyć ponowną kompilację, tworząc nowe pole w celu określenia poprawionej definicji i rozpoczęcia korzystania z niej zamiast Starego. Użyj [indeksu aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-index) , aby uwzględnić nowe pole i [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) w celu wypełnienia. Później w ramach planowanej obsługi indeksów można wyczyścić indeks w celu usunięcia przestarzałych pól. |

## <a name="when-to-add-analyzers"></a>Kiedy należy dodawać analizatory

Najlepszy czas dodawania i przypisywania analizatorów odbywa się podczas aktywnego programowania, gdy porzucanie i odtwarzanie indeksów jest rutynowe.

Jako solidifies definicji indeksu można dołączyć nowe konstrukcje analizy do indeksu, ale aby uniknąć tego błędu, należy przekazać flagę **allowIndexDowntime** w celu [zaktualizowania indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) .

*"Aktualizacja indeksu jest niedozwolona, ponieważ spowodowałoby to przestoje. Aby dodać nowe analizatory, tokenizatory, filtry tokenów lub filtry znaków do istniejącego indeksu, ustaw parametr zapytania "allowIndexDowntime" na wartość "true" w żądaniu aktualizacji indeksu. Należy pamiętać, że ta operacja spowoduje przełączenie indeksu w tryb offline przez co najmniej kilka sekund, co spowoduje niepowodzenie żądania indeksowania i zapytań. Dostępność indeksu może być niesparowana przez kilka minut od momentu zaktualizowania indeksu lub dłuższe dla bardzo dużych indeksów.*

Te same wartości są spełnione podczas przypisywania analizatora do pola. Analizator jest integralną częścią definicji pola, więc można ją dodać tylko po utworzeniu pola. Jeśli chcesz dodać analizatory do istniejących pól, musisz [porzucić i ponownie skompilować](search-howto-reindex.md) indeks lub dodać nowe pole z żądanym analizatorze.

Jak wspomniano, wyjątek jest **searchAnalyzer** Variant. Z trzech sposobów określania**analizatorów (analizatorze**, **indexAnalyzer**, **SearchAnalyzer**), tylko atrybut **searchAnalyzer** można zmienić w istniejącym polu.

## <a name="recommendations-for-working-with-analyzers"></a>Zalecenia dotyczące pracy z analizatorami

Ta sekcja zawiera wskazówki dotyczące pracy z analizatorami.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden Analizator do odczytu i zapisu, o ile nie masz określonych wymagań

Azure Search pozwala określić różne analizatory do indeksowania i wyszukiwania za pomocą dodatkowych parametrów pola **indexAnalyzer** i **searchAnalyzer** . Jeśli nie zostanie określony, Analizator zestawu z właściwością **Analizator** jest używany do indeksowania i wyszukiwania. Jeśli `analyzer` jest nieokreślony, używany jest domyślny standardowy Analizator Lucene.

Ogólną zasadą jest użycie tego samego analizatora zarówno dla indeksowania, jak i wykonywania zapytań, chyba że określone wymagania nie wyróżnią się w inny sposób. Pamiętaj o dokładnym przetestowaniu. Gdy przetwarzanie tekstu różni się w czasie wyszukiwania i indeksowania, należy uruchomić ryzyko niezgodności między terminami zapytania a indeksowanymi terminami, kiedy konfiguracje analizatora wyszukiwania i indeksowania nie są wyrównane.

### <a name="test-during-active-development"></a>Testowanie podczas aktywnego programowania

Zastępowanie analizatora standardowego wymaga odbudowania indeksu. Jeśli to możliwe, zdecyduj, które analizatory mają być używane podczas aktywnego programowania, przed przetworzeniem indeksu do produkcji.

### <a name="inspect-tokenized-terms"></a>Sprawdzanie tokenów w terminologii

Jeśli wyszukiwanie nie zwróci oczekiwanych wyników, najbardziej prawdopodobną przyczyną jest niezgodności tokenów między danymi wejściowymi zapytania a terminami tokenów w indeksie. Jeśli tokeny nie są takie same, dopasowań nie powiedzie się zmaterializowania. Aby sprawdzić dane wyjściowe tokenizatora, zalecamy korzystanie z [interfejsu API analizy](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako narzędzia do badania. Odpowiedź składa się z tokenów, zgodnie z wygenerowanym przez określony Analizator.

<a name="examples"></a>

## <a name="rest-examples"></a>Przykłady REST

W poniższych przykładach przedstawiono definicje analizatora dla kilku kluczowych scenariuszy.

+ [Przykład analizatora niestandardowego](#Custom-analyzer-example)
+ [Przypisanie analizatorów do przykładu pola](#Per-field-analyzer-assignment-example)
+ [Mieszanie analizatorów do indeksowania i wyszukiwania](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Przykładowy Analizator języka](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Przykład analizatora niestandardowego

Ten przykład ilustruje definicję analizatora z opcjami niestandardowymi. Opcje niestandardowe filtrów char, tokenizatory i filtrów tokenów są określane oddzielnie jako konstrukcje nazwane, a następnie przywoływane w definicji analizatora. Wstępnie zdefiniowane elementy są używane jako-is i po prostu przywoływane przez nazwę.

Przechodzenie przez ten przykład:

* Analizatory są właściwością klasy Field dla pola z możliwością wyszukiwania.
* Analizator niestandardowy jest częścią definicji indeksu. Może być odpowiednio dostosowany (na przykład dostosowanie pojedynczej opcji w jednym filtrze) lub dostosowany w wielu miejscach.
* W takim przypadku Analizator niestandardowy to "my_analyzer", który z kolei używa niestandardowego standardowego tokenizatora "my_standard_tokenizer" i dwóch filtrów tokenów: małe litery i dostosowany filtr asciifolding "my_asciifolding".
* Definiuje również 2 niestandardowe filtry znaków "map_dash" i "remove_whitespace". Pierwszy z nich zastępuje wszystkie kreski znakami podkreślenia, podczas gdy druga z nich usuwa wszystkie spacje. Spacje muszą być zakodowane w formacie UTF-8 w regułach mapowania. Filtry znaków są stosowane przed tokenizacji i będą miały wpływ na wynikowe tokeny (standardowy tokenizatora jest podzielony na kreski i spacje, ale nie w podkreśleniu).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Przykład przypisywania analizatora poszczególnych pól

Standardowa analizator jest wartością domyślną. Załóżmy, że chcesz zastąpić wartość domyślną przy użyciu innego wstępnie zdefiniowanego analizatora, takiego jak Analizator wzorców. Jeśli nie ustawiasz opcji niestandardowych, wystarczy określić ją tylko według nazwy w definicji pola.

Element "Analyzer" przesłania analizatora standardowego w zależności od pola. Nie istnieje globalne zastąpienie. W tym przykładzie `text1` używa analizatora wzorców i `text2`, który nie określa analizatora, używa domyślnego.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Mieszanie analizatorów dla operacji indeksowania i wyszukiwania

Interfejsy API zawierają dodatkowe atrybuty indeksu do określania różnych analizatorów do indeksowania i wyszukiwania. Atrybuty **searchAnalyzer** i **indexAnalyzer** muszą być określone jako para, zastępując atrybut pojedynczego **analizatora** .


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Przykładowy Analizator języka

Pola zawierające ciągi w różnych językach mogą używać analizatora języka, podczas gdy inne pola zachowują wartość domyślną (lub używają innego wstępnie zdefiniowanego lub niestandardowego analizatora). Jeśli używasz analizatora języka, musi on być używany do operacji indeksowania i wyszukiwania. Pola, które używają analizatora języka, nie mogą mieć różnych analizatorów do indeksowania i wyszukiwania.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C#pokazują

Jeśli używasz przykładów kodu zestawu .NET SDK, możesz dołączyć te przykłady, aby użyć lub skonfigurować analizatory.

+ [Przypisywanie wbudowanej analizatora](#Assign-a-language-analyzer)
+ [Konfigurowanie analizatora](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Przypisywanie analizatora języka

Każdy Analizator, który jest używany jako-is, bez konfiguracji, jest określony w definicji pola. Nie istnieje wymóg tworzenia konstrukcji analizatora. 

W tym przykładzie przypisujemy do pól opisu język angielski i analizatory francuskie firmy Microsoft. Jest to fragment pochodzący z większej definicji indeksu hoteli, który tworzy się przy użyciu klasy hotelu w pliku hotels.cs przykładu [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) .

[Analizator](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)wywołań, określający typ [analizatorzename](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) , który zapewnia analizator tekstu obsługiwany w Azure Search.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definiowanie analizatora niestandardowego

Gdy wymagane jest dostosowanie lub konfiguracja, konieczne będzie dodanie konstrukcji analizatora do indeksu. Po jego zdefiniowaniu można dodać do niego definicję pola, jak pokazano w poprzednim przykładzie.

Utwórz obiekt [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) . Aby uzyskać więcej przykładów, zobacz [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Następne kroki

+ Zapoznaj się z kompleksowym wyjaśnieniem [działania wyszukiwania pełnotekstowego w Azure Search](search-lucene-query-architecture.md). W tym artykule przedstawiono przykłady zachowań, które mogą wydawać się intuicyjne na powierzchni.

+ Wypróbuj dodatkową składnię zapytania z przykładowej sekcji [Wyszukaj dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) lub z [prostej składni zapytania](query-simple-syntax.md) w Eksploratorze wyszukiwania w portalu.

+ Dowiedz się, jak stosować [analizatory leksykalne charakterystyczne dla języka](index-add-language-analyzers.md).

+ [Skonfiguruj Niestandardowe analizatory](index-add-custom-analyzers.md) dla minimalnego przetwarzania lub wyspecjalizowanego przetwarzania dla poszczególnych pól.

## <a name="see-also"></a>Zobacz także

 [Interfejs API REST dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Prosta składnia zapytań](query-simple-syntax.md) 

 [Pełna składnia zapytań Lucene](query-lucene-syntax.md) 
 
 [Obsługa wyników wyszukiwania](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
