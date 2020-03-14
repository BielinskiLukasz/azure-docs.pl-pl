---
title: Zasady buforowania API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat zasad buforowania dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280302"
---
# <a name="api-management-caching-policies"></a>Zasady buforowania API Management
Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a>Zasady buforowania

- Zasady buforowania odpowiedzi
    - [Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wykonaj wyszukiwanie w pamięci podręcznej i zwróć poprawne buforowane odpowiedzi, jeśli są dostępne.
    - [Przechowywanie w pamięci](api-management-caching-policies.md#StoreToCache) podręcznej w pamięci podręcznej odpowiedzi zgodnie z określoną konfiguracją kontroli pamięci podręcznej.
- Zasady buforowania wartości
    - [Pobierz wartość z pamięci podręcznej](#GetFromCacheByKey) — Pobierz zbuforowany element według klucza.
    - [Wartość magazynu w pamięci podręcznej](#StoreToCacheByKey) — przechowywanie elementu w pamięci podręcznej według klucza.
    - [Usuń wartość z pamięci podręcznej](#RemoveCacheByKey) — Usuń element z pamięci podręcznej według klucza.

## <a name="GetFromCache"></a>Pobierz z pamięci podręcznej
Użyj zasad `cache-lookup` w celu przeprowadzenia wyszukiwania w pamięci podręcznej i zwrócenia prawidłowej pamięci podręcznej, jeśli jest dostępna. Te zasady mogą być stosowane w przypadkach, w których zawartość odpowiedzi pozostaje statyczna w danym okresie czasu. Buforowanie odpowiedzi zmniejsza wymagania dotyczące przepustowości i przetwarzania narzucone na serwerze sieci Web zaplecza i obniża opóźnienia postrzegane przez klientów interfejsu API.

> [!NOTE]
> Te zasady muszą mieć odpowiedni [Magazyn do zasad pamięci podręcznej](api-management-caching-policies.md#StoreToCache) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Przykład użycia wyrażeń zasad
Ten przykład pokazuje, jak API Management skonfigurować czas trwania buforowania odpowiedzi, który jest zgodny z buforowaniem odpowiedzi usługi wewnętrznej bazy danych zgodnie z dyrektywą `Cache-Control` usługi kopii zapasowej. Aby zapoznać się z prezentacją konfigurowania i korzystania z tych zasad, zobacz temat [Cloud okładki epizod 177: więcej API Management funkcji z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przekazanie do 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|pamięć podręczna — wyszukiwanie|Element główny.|Tak|
|Zróżnicuj według nagłówka|Rozpocznij buforowanie odpowiedzi na wartość określonego nagłówka, na przykład Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, from, host, if-Match.|Nie|
|Zróżnicuj według zapytania — parametr|Rozpocznij buforowanie odpowiedzi na wartość określonych parametrów zapytania. Wprowadź jeden lub wiele parametrów. Użyj średnika jako separatora. Jeśli żaden nie zostanie określony, używane są wszystkie parametry zapytania.|Nie|

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)                           | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Ustawienie wartości `true`umożliwia buforowanie żądań zawierających nagłówek autoryzacji.                                                                                                                                                                                                                                                                        | Nie       | {1&gt;false&lt;1}             |
| Typ buforowania               | Wybierz między następującymi wartościami atrybutu:<br />- `internal`, aby użyć wbudowanej pamięci podręcznej API Management<br />- `external` do korzystania z zewnętrznej pamięci podręcznej, zgodnie z opisem w temacie [Korzystanie z zewnętrznej pamięci podręcznej platformy Azure dla Redis na platformie azure API Management](api-management-howto-cache-external.md)<br />- `prefer-external` do używania zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrzna pamięć podręczna w przeciwnym razie. | Nie       | `prefer-external` |
| buforowanie podrzędne-Type        | Ten atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> -None — buforowanie podrzędne nie jest dozwolone.<br />— prywatne buforowanie w trybie podrzędnym jest dozwolone.<br />-publiczne i udostępnione buforowanie podrzędne jest dozwolone.                                                                                                          | Nie       | brak              |
| must-revalidate                | Gdy buforowanie podrzędne jest włączone, ten atrybut włącza lub wyłącza dyrektywę kontroli pamięci podręcznej `must-revalidate` w odpowiedziach bramy.                                                                                                                                                                                                                      | Nie       | {1&gt;true&lt;1}              |
| Zróżnicuj według deweloperów              | Ustaw, aby `true` do buforowania odpowiedzi na [klucz subskrypcji](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Tak      |         Fałsz          |
| różne grupy deweloperów       | Ustaw na `true` do buforowania odpowiedzi dla każdej [grupy użytkowników](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Tak      |       Fałsz            |

### <a name="usage"></a>Sposób użycia
Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="StoreToCache"></a>Przechowywanie w pamięci podręcznej
Zasady `cache-store` buforują odpowiedzi zgodnie z określonymi ustawieniami pamięci podręcznej. Te zasady mogą być stosowane w przypadkach, w których zawartość odpowiedzi pozostaje statyczna w danym okresie czasu. Buforowanie odpowiedzi zmniejsza wymagania dotyczące przepustowości i przetwarzania narzucone na serwerze sieci Web zaplecza i obniża opóźnienia postrzegane przez klientów interfejsu API.

> [!NOTE]
> Te zasady muszą mieć odpowiednie zasady [Get z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Przykład użycia wyrażeń zasad
Ten przykład pokazuje, jak API Management skonfigurować czas trwania buforowania odpowiedzi, który jest zgodny z buforowaniem odpowiedzi usługi wewnętrznej bazy danych zgodnie z dyrektywą `Cache-Control` usługi kopii zapasowej. Aby zapoznać się z prezentacją konfigurowania i korzystania z tych zasad, zobacz temat [Cloud okładki epizod 177: więcej API Management funkcji z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przekazanie do 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|Magazyn pamięci podręcznej|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | Czas wygaśnięcia wpisów w pamięci podręcznej (w sekundach).                                                                                                                                                                                                                                                                                                   | Tak      | N/D               |

### <a name="usage"></a>Sposób użycia
Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** wychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="GetFromCacheByKey"></a>Pobierz wartość z pamięci podręcznej
Użyj zasad `cache-lookup-value` w celu przeprowadzenia wyszukiwania w pamięci podręcznej według klucza i zwrócenia wartości w pamięci podręcznej. Klucz może mieć dowolną wartość ciągu i jest zwykle dostarczany przy użyciu wyrażenia zasad.

> [!NOTE]
> Te zasady muszą mieć odpowiadającą [wartość magazynu w zasadach pamięci podręcznej](#StoreToCacheByKey) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Przykład
Aby uzyskać więcej informacji i przykłady tych zasad, zobacz [niestandardowe buforowanie na platformie Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|pamięć podręczna — wyszukiwanie-wartość|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybierz między następującymi wartościami atrybutu:<br />- `internal`, aby użyć wbudowanej pamięci podręcznej API Management<br />- `external` do korzystania z zewnętrznej pamięci podręcznej, zgodnie z opisem w temacie [Korzystanie z zewnętrznej pamięci podręcznej platformy Azure dla Redis na platformie azure API Management](api-management-howto-cache-external.md)<br />- `prefer-external` do używania zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrzna pamięć podręczna w przeciwnym razie. | Nie       | `prefer-external` |
| wartość domyślna    | Wartość, która zostanie przypisana do zmiennej, jeśli wyszukiwanie klucza pamięci podręcznej spowodowało brak. Jeśli ten atrybut nie jest określony, `null` jest przypisany.                                                                                                                                                                                                           | Nie       | `null`            |
| key              | Wartość klucza pamięci podręcznej do użycia w wyszukiwaniu.                                                                                                                                                                                                                                                                                                                       | Tak      | N/D               |
| Nazwa zmiennej    | Nazwa [zmiennej kontekstowej](api-management-policy-expressions.md#ContextVariables) , do której zostanie przypisana wartość, jeśli wyszukiwanie zakończyło się pomyślnie. Jeśli odszukanie spowoduje odrzucenie, zmienna zostanie przypisana wartość atrybutu `default-value` lub `null`, jeśli atrybut `default-value` został pominięty.                                       | Tak      | N/D               |

### <a name="usage"></a>Sposób użycia
Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu
- **Zakresy zasad:** wszystkie zakresy

## <a name="StoreToCacheByKey"></a>Wartość magazynu w pamięci podręcznej
`cache-store-value` wykonuje Magazyn pamięci podręcznej według klucza. Klucz może mieć dowolną wartość ciągu i jest zwykle dostarczany przy użyciu wyrażenia zasad.

> [!NOTE]
> Te zasady muszą mieć odpowiednie [wartości Get z zasad pamięci podręcznej](#GetFromCacheByKey) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Przykład
Aby uzyskać więcej informacji i przykłady tych zasad, zobacz [niestandardowe buforowanie na platformie Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|pamięć podręczna-magazyn-wartość|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybierz między następującymi wartościami atrybutu:<br />- `internal`, aby użyć wbudowanej pamięci podręcznej API Management<br />- `external` do korzystania z zewnętrznej pamięci podręcznej, zgodnie z opisem w temacie [Korzystanie z zewnętrznej pamięci podręcznej platformy Azure dla Redis na platformie azure API Management](api-management-howto-cache-external.md)<br />- `prefer-external` do używania zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrzna pamięć podręczna w przeciwnym razie. | Nie       | `prefer-external` |
| duration         | Wartość zostanie zbuforowana dla podanej wartości czasu trwania określonej w sekundach.                                                                                                                                                                                                                                                                                 | Tak      | N/D               |
| key              | Klucz pamięci podręcznej, w której będzie przechowywana wartość.                                                                                                                                                                                                                                                                                                                   | Tak      | N/D               |
| {1&gt;value&lt;1}            | Wartość do buforowania.                                                                                                                                                                                                                                                                                                                                     | Tak      | N/D               |
### <a name="usage"></a>Sposób użycia
Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu
- **Zakresy zasad:** wszystkie zakresy

### <a name="RemoveCacheByKey"></a>Usuń wartość z pamięci podręcznej
`cache-remove-value` usuwa buforowany element identyfikowany przez jego klucz. Klucz może mieć dowolną wartość ciągu i jest zwykle dostarczany przy użyciu wyrażenia zasad.

#### <a name="policy-statement"></a>Instrukcja zasad

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Przykład

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|cache-remove-value|Element główny.|Tak|

#### <a name="attributes"></a>Atrybuty

| Name (Nazwa)             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybierz między następującymi wartościami atrybutu:<br />- `internal`, aby użyć wbudowanej pamięci podręcznej API Management<br />- `external` do korzystania z zewnętrznej pamięci podręcznej, zgodnie z opisem w temacie [Korzystanie z zewnętrznej pamięci podręcznej platformy Azure dla Redis na platformie azure API Management](api-management-howto-cache-external.md)<br />- `prefer-external` do używania zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrzna pamięć podręczna w przeciwnym razie. | Nie       | `prefer-external` |
| key              | Klucz poprzednio buforowanej wartości, który ma zostać usunięty z pamięci podręcznej.                                                                                                                                                                                                                                                                                        | Tak      | N/D               |

#### <a name="usage"></a>Sposób użycia
Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) zasad.

- **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu
- **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](api-management-policy-reference.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
