---
title: Mapowania pól w indeksatorach
titleSuffix: Azure Cognitive Search
description: Skonfiguruj mapowania pól w indeksatorze, aby uwzględnić różnice w nazwach pól i reprezentacjach danych.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 17a96479b80410cbfcb2a6061904491f95c45f10
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116268"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapowania pól i przekształcenia przy użyciu indeksatorów usługi Azure Wyszukiwanie poznawcze

W przypadku korzystania z usługi Azure Wyszukiwanie poznawcze indeksatorów czasami okaże się, że dane wejściowe nie są całkowicie zgodne ze schematem docelowego indeksu. W takich przypadkach można użyć **mapowań pól** , aby zmienić kształt danych podczas procesu indeksowania.

Niektóre sytuacje, w których mapowania pól są użyteczne:

* Źródło danych ma pole o nazwie `_id`, ale usługa Azure wyszukiwanie poznawcze nie zezwala na używanie nazw pól, które zaczynają się od znaku podkreślenia. Mapowanie pól pozwala efektywnie zmienić nazwę pola.
* Chcesz wypełnić kilka pól w indeksie z tych samych danych źródła danych. Na przykład możesz chcieć zastosować różne analizatory do tych pól.
* Chcesz wypełnić pole indeksu danymi z więcej niż jednego źródła danych, a źródła danych używają różnych nazw pól.
* Musisz zakodować lub zdekodować dane w formacie base64. Mapowania pól obsługują kilka **funkcji mapowania**, w tym funkcje kodowania i dekodowania Base64.

> [!NOTE]
> Mapowania pól w indeksatorach to prosty sposób mapowania pól danych do pól indeksu, z możliwością konwersji lekkich danych. Bardziej złożone dane mogą wymagać wstępnego przetwarzania, aby przetworzyć je w postaci, która jest w trakcie indeksowania. Jedna z opcji, którą można rozważyć, to [Azure Data Factory](https://docs.microsoft.com/zure/data-factory/).

## <a name="set-up-field-mappings"></a>Skonfiguruj mapowania pól

Mapowanie pola składa się z trzech części:

1. A `sourceFieldName`, który reprezentuje pole w źródle danych. Ta właściwość jest wymagana.
2. Opcjonalna `targetFieldName`, która reprezentuje pole w indeksie wyszukiwania. W przypadku pominięcia zostanie użyta taka sama nazwa jak w źródle danych.
3. Opcjonalna `mappingFunction`, która może przekształcić dane przy użyciu jednej z kilku wstępnie zdefiniowanych funkcji. Pełna lista funkcji znajduje się [poniżej](#mappingFunctions).

Mapowania pól są dodawane do `fieldMappings` tablicy definicji indeksatora.

## <a name="map-fields-using-the-rest-api"></a>Mapowanie pól przy użyciu interfejsu API REST

Można dodać mapowania pól podczas tworzenia nowego indeksatora przy użyciu żądania interfejsu API [tworzenia indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) . Można zarządzać mapowaniami pól istniejącego indeksatora przy użyciu żądania API [Update indeksator](https://docs.microsoft.com/rest/api/searchservice/update-indexer) .

Na przykład poniżej przedstawiono sposób mapowania pola źródłowego do pola docelowego o innej nazwie:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Do pola źródłowego można odwoływać się w wielu mapowaniach pól. Poniższy przykład pokazuje, jak "rozwidlenie" pola, kopiując te same pola źródła do dwóch różnych pól indeksu:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Usługa Azure Wyszukiwanie poznawcze używa porównania bez uwzględniania wielkości liter, aby rozpoznać nazwy pól i funkcji w mapowaniu pól. Jest to wygodne (nie trzeba uzyskać całej wielkości liter), ale oznacza to, że źródło danych lub indeks nie mogą mieć pól, które różnią się tylko wielkością liter.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapowanie pól przy użyciu zestawu .NET SDK

Mapowania pól można definiować w zestawie .NET SDK przy użyciu klasy [fieldmapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , która ma właściwości `SourceFieldName` i `TargetFieldName`i opcjonalne `MappingFunction` odwołanie.

Można określić mapowania pól podczas konstruowania indeksatora lub później przez bezpośrednie ustawienie `Indexer.FieldMappings` właściwości.

W poniższym przykładzie w języku C# są ustawiane mapowania pól podczas konstruowania indeksatora.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funkcje mapowania pól

Funkcja mapowania pól przekształca zawartość pola, zanim zostanie on zapisany w indeksie. Następujące funkcje mapowania są obecnie obsługiwane:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode, funkcja

Wykonuje bezpieczne kodowanie Base64 dla *adresu URL* w ciągu wejściowym. Przyjęto założenie, że dane wejściowe są kodowane w formacie UTF-8.

#### <a name="example---document-key-lookup"></a>Przykład — wyszukiwanie klucza dokumentu

W kluczu dokumentu usługi Azure Wyszukiwanie poznawcze mogą występować tylko bezpieczne znaki w adresie URL (ponieważ klienci muszą mieć możliwość adresowania dokumentu przy użyciu [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Jeśli pole źródłowe klucza zawiera adresy URL-niebezpieczne, można użyć `base64Encode` funkcji, aby skonwertować ją podczas indeksowania. Jednak klucz dokumentu (zarówno przed, jak i po konwersji) nie może być dłuższy niż 1 024 znaków.

Gdy pobierasz zakodowany klucz podczas wyszukiwania, możesz użyć `base64Decode` funkcji, aby uzyskać oryginalną wartość klucza, a następnie użyć jej do pobrania dokumentu źródłowego.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Jeśli nie dołączysz właściwości Parameters dla funkcji mapowania, wartość `{"useHttpServerUtilityUrlTokenEncode" : true}`domyślna to.

Usługa Azure Wyszukiwanie poznawcze obsługuje dwa różne kodowania base64. Należy używać tych samych parametrów podczas kodowania i dekodowania tego samego pola. Aby uzyskać więcej informacji, zobacz [Opcje kodowania base64](#base64details) , aby określić parametry do użycia.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode, funkcja

Wykonuje dekodowanie Base64 ciągu wejściowego. Przyjmuje się, że wartość wejściowa jest *bezpiecznym* ciągiem z kodowaniem Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Przykład — dekodowanie metadanych lub adresów URL obiektu BLOB

Dane źródłowe mogą zawierać ciągi kodowane algorytmem Base64, takie jak ciągi metadanych obiektów blob lub adresy URL sieci Web, które mają być przeszukiwane jako zwykły tekst. Możesz użyć funkcji, `base64Decode` aby przekształcić zakodowane dane z powrotem do zwykłych ciągów podczas wypełniania indeksu wyszukiwania.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Jeśli nie dołączysz właściwości Parameters, wartość `{"useHttpServerUtilityUrlTokenEncode" : true}`domyślna to.

Usługa Azure Wyszukiwanie poznawcze obsługuje dwa różne kodowania base64. Należy używać tych samych parametrów podczas kodowania i dekodowania tego samego pola. Aby uzyskać więcej informacji, zobacz [Opcje kodowania base64](#base64details) , aby określić parametry do użycia.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Opcje kodowania base64

Usługa Azure Wyszukiwanie poznawcze obsługuje kodowanie przy użyciu bezpiecznego adresu URL i standardowego kodowania base64. Ciąg szyfrowany algorytmem Base64 podczas indeksowania powinien zostać zdekodowany później przy użyciu tych samych opcji kodowania lub w przeciwnym razie wynik nie będzie zgodny z oryginałem.

Jeśli `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` lub parametry kodowania i dekodowania odpowiednio są ustawione na, to `true` `base64Encode` zachowuje się jak [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) i `base64Decode` zachowuje się jak [HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Jeśli `base64Encode` jest używany do tworzenia wartości kluczy, `useHttpServerUtilityUrlTokenEncode` należy ustawić wartość true. Dla wartości kluczy można używać tylko bezpiecznego kodowania base64 z adresem URL. Zobacz [reguły nazewnictwa &#40;Azure Wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) , aby uzyskać pełny zestaw ograniczeń dotyczących znaków w wartościach klucza.

W bibliotekach platformy .NET na platformie Azure Wyszukiwanie poznawcze założono pełny .NET Framework, który zapewnia wbudowane kodowanie. Opcje `useHttpServerUtilityUrlTokenEncode` i `useHttpServerUtilityUrlTokenDecode` wykorzystują tę wbudowaną funkcję. W przypadku korzystania z platformy .NET Core lub innej platformy zalecamy ustawienie tych opcji na `false` i wywołanie funkcji kodowania i dekodowania struktury.

Poniższa tabela zawiera porównanie różnych kodowań Base64 ciągu `00>00?00`. Aby określić wymagane dodatkowe przetwarzanie (jeśli istnieje) dla funkcji Base64, zastosuj funkcję kodowania biblioteki w ciągu `00>00?00` i Porównaj dane wyjściowe z oczekiwanymi danymi wyjściowymi. `MDA-MDA_MDA`

| Kodowanie | Dane wyjściowe kodowania base64 | Dodatkowe przetwarzanie po kodowaniu biblioteki | Dodatkowe przetwarzanie przed dekodowaniem biblioteki |
| --- | --- | --- | --- |
| Base64 z uzupełnieniem | `MDA+MDA/MDA=` | Użyj znaków bezpiecznych dla adresu URL i Usuń uzupełnienie | Użyj standardowych znaków base64 i Dodaj uzupełnienie |
| Bezuzupełnienie Base64 | `MDA+MDA/MDA` | Użyj znaków bezpiecznych dla adresu URL | Używaj standardowych znaków Base64 |
| Bezpieczny algorytm Base64 z użyciem adresu URL | `MDA-MDA_MDA=` | Usuń uzupełnienie | Dodaj uzupełnienie |
| Bezpieczny dla adresu URL algorytm Base64 bez dopełnienia | `MDA-MDA_MDA` | Brak | Brak |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition, funkcja

Dzieli pole ciągu przy użyciu określonego ogranicznika i wybiera token w określonym położeniu w wyniku podziału.

Ta funkcja używa następujących parametrów:

* `delimiter`: ciąg, który ma być używany jako separator podczas dzielenia ciągu wejściowego.
* `position`: liczba całkowita na początku tokenu do wybrania po podzieleniu ciągu wejściowego.

Na przykład, jeśli dane wejściowe to `Jane Doe`, `delimiter` is `" "`(Space) i `position` is 0, wynikiem jest; `Jane` Jeśli wartość `position` to 1, wynik jest `Doe`. Jeśli pozycja odwołuje się do nieistniejącego tokenu, zwracany jest błąd.

#### <a name="example---extract-a-name"></a>Przykład — Wyodrębnij nazwę

Źródło danych zawiera `PersonName` pole i chcesz zindeksować je jako dwie osobne `FirstName` i `LastName` pola. Za pomocą tej funkcji można podzielić dane wejściowe za pomocą znaku spacji jako ogranicznika.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection, funkcja

Transformuje ciąg sformatowany jako tablicę JSON ciągów do tablicy ciągów, która może służyć do wypełniania `Collection(Edm.String)` pola w indeksie.

Na przykład, jeśli ciąg wejściowy to `["red", "white", "blue"]`, pole docelowe `Collection(Edm.String)` typu zostanie wypełnione trzema wartościami `red`, `white`i. `blue` W przypadku wartości wejściowych, których nie można analizować jako tablic ciągów JSON, zwracany jest błąd.

#### <a name="example---populate-collection-from-relational-data"></a>Przykład — wypełnienie kolekcji z danych relacyjnych

Azure SQL Database nie ma wbudowanego typu danych, który naturalnie mapuje do `Collection(Edm.String)` pól w usłudze Azure wyszukiwanie poznawcze. Aby wypełnić pola kolekcji ciągów, możesz wstępnie przetworzyć dane źródłowe jako tablicę ciągów JSON, a następnie użyć `jsonArrayToStringCollection` funkcji mapowania.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode, funkcja

Ta funkcja może służyć do kodowania ciągu w taki sposób, że jest to "bezpieczny adres URL". W przypadku użycia z ciągiem zawierającym znaki, które nie są dozwolone w adresie URL, ta funkcja przekonwertuje te "niebezpieczne" znaków na odpowiedniki jednostek znakowych. Ta funkcja używa formatu kodowania UTF-8.

#### <a name="example---document-key-lookup"></a>Przykład — wyszukiwanie klucza dokumentu

`urlEncode`funkcja może być używana jako alternatywa dla `base64Encode` funkcji, jeśli tylko niebezpieczne znaki adresów URL mają być konwertowane, przy czym inne znaki są inne.

Załóżmy, że ciąg wejściowy `<hello>` jest-następnie pole docelowe typu `(Edm.String)` zostanie wypełnione wartością`%3chello%3e`

Gdy pobierasz zakodowany klucz podczas wyszukiwania, możesz użyć `urlDecode` funkcji, aby uzyskać oryginalną wartość klucza, a następnie użyć jej do pobrania dokumentu źródłowego.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode, funkcja

 Ta funkcja konwertuje ciąg zakodowany w adresie URL na zdekodowany ciąg przy użyciu formatu kodowania UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Przykład — dekodowanie metadanych obiektu BLOB

 Niektórzy klienci usługi Azure Storage automatycznie zakodują metadane obiektu BLOB, jeśli zawiera znaki inne niż ASCII. Jeśli jednak chcesz przeszukiwać takie metadane (jako zwykły tekst), możesz użyć `urlDecode` funkcji, aby przekształcić zakodowane dane z powrotem do zwykłych ciągów podczas wypełniania indeksu wyszukiwania.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode, funkcja
 
 Ta funkcja konwertuje ciąg o dowolnej długości na ciąg o stałej długości.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Przykład — Mapuj klucze dokumentów, które są zbyt długie
 
W przypadku wystąpienia błędów ponoszących informacje o kluczu dokumentu dłuższym niż 1024 znaków, ta funkcja może być stosowana w celu skrócenia długości klucza dokumentu.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
