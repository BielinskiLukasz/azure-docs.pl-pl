---
title: Zasady międzydomenowe platformy Azure API Management | Microsoft Docs
description: Dowiedz się więcej o zasadach międzydomenowych dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 86c61679a73f03f7e54bba746107685796ec07c9
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442313"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (Zasady usługi API Management obejmujące różne domeny)
Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a>Zasady międzydomenowe

- [Zezwalaj na wywołania](api-management-cross-domain-policies.md#AllowCrossDomainCalls) międzydomenowe — umożliwia dostęp do interfejsu API z poziomu klientów opartych na przeglądarce Adobe Flash i Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) — dodaje obsługę funkcji udostępniania zasobów między źródłami (CORS) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce.
- [JSONP](api-management-cross-domain-policies.md#JSONP) — dodaje kod JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce JavaScript.

## <a name="AllowCrossDomainCalls"></a>Zezwalaj na wywołania między domenami
`cross-domain` Użyj zasad, aby udostępnić interfejs API z klientów Adobe Flash i Microsoft Silverlight opartych na przeglądarce.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Przykład

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|między domenami|Element główny. Elementy podrzędne muszą być zgodne ze [specyfikacją pliku zasad między domenami Adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Tak|

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="CORS"></a>SPECYFIKACJI
`cors` Zasada dodaje obsługę funkcji udostępniania zasobów między źródłami (CORS) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce.

Mechanizm CORS pozwala przeglądarce i serwerowi na współpracujące i określać, czy zezwolić na określone żądania między źródłami (tj. wywołania XMLHttpRequest wykonane ze skryptu JavaScript na stronie sieci Web do innych domen). Zapewnia to większą elastyczność niż Zezwalanie na żądania tego samego źródła, ale jest bezpieczniejsze niż Zezwalanie na wszystkie żądania między źródłami.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Przykład
W tym przykładzie pokazano, jak obsługiwać żądania przed inspekcją, takie jak te z niestandardowymi nagłówkami lub metodami innymi niż GET i POST. Aby obsługiwać niestandardowe nagłówki i dodatkowe zlecenia http, użyj `allowed-methods` sekcji i `allowed-headers` , jak pokazano w poniższym przykładzie.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|Domyślny|
|----------|-----------------|--------------|-------------|
|specyfikacji|Element główny.|Yes|ND|
|dozwolone źródła|Zawiera `origin` elementy, które opisują dozwolone źródła dla żądań międzydomenowych. `allowed-origins`może zawierać pojedynczy `origin` element, który określa `*` , aby zezwolić na dowolne Źródło lub jeden lub więcej `origin` elementów, które zawierają identyfikator URI.|Tak|ND|
|źródło|Wartość może być `*` równa, aby zezwolić na wszystkie źródła lub identyfikator URI, który określa pojedyncze źródło. Identyfikator URI musi zawierać schemat, hosta i port.|Yes|Jeśli port zostanie pominięty w identyfikatorze URI, port 80 jest używany dla protokołu HTTP, a port 443 jest używany w przypadku protokołu HTTPS.|
|dozwolone — metody|Ten element jest wymagany, jeśli dozwolone są metody inne niż GET lub POST. Zawiera `method` elementy, które określają obsługiwane zlecenia http.|Nie|Jeśli ta sekcja nie jest obecna, obsługiwane są pozycje GET i POST.|
|— metoda|Określa czasownik HTTP.|Jeśli sekcja jest `method` obecna, `allowed-methods` wymagany jest co najmniej jeden element.|ND|
|dozwolone — nagłówki|Ten element zawiera `header` elementy określające nazwy nagłówków, które mogą zostać uwzględnione w żądaniu.|Nie|ND|
|Uwidacznianie — nagłówki|Ten element zawiera `header` elementy określające nazwy nagłówków, które będą dostępne dla klienta.|Nie|ND|
|nagłówek|Określa nazwę nagłówka.|Co najmniej jeden `header` element jest wymagany w `allowed-headers` lub `expose-headers` Jeśli sekcja jest obecna.|ND|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagane|Domyślny|
|----------|-----------------|--------------|-------------|
|Zezwalaj — poświadczenia|W `Access-Control-Allow-Credentials` nagłówku odpowiedzi na inspekcję wstępną zostanie ustawiona wartość tego atrybutu i będzie ona mieć wpływ na zdolność klienta do przesyłania poświadczeń w żądaniach międzydomenowych.|Nie|false|
|preflight-result-max-age|`Access-Control-Max-Age` Nagłówek w odpowiedzi na inspekcję wstępną zostanie ustawiony na wartość tego atrybutu i będzie miał wpływ na zdolność agenta użytkownika do buforowania przed lotem odpowiedzi.|Nie|0|

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="JSONP"></a>JSONP
`jsonp` Zasada dodaje kod JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce JavaScript. JSONP to metoda używana w programach JavaScript do żądania danych z serwera w innej domenie. JSONP pomija ograniczenia wymuszane przez większość przeglądarek sieci Web, w których dostęp do stron sieci Web musi znajdować się w tej samej domenie.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Przykład

```xml
<jsonp callback-parameter-name="cb" />
```

Jeśli wywołasz metodę bez parametru wywołania zwrotnego? CB = XXX, zwróci on zwykły kod JSON (bez otoki wywołania funkcji).

Dodanie parametru `?cb=XXX` wywołania zwrotnego spowoduje zwrócenie wyniku JSONP, zawijanie oryginalnych wyników JSON wokół funkcji wywołania zwrotnego, takiej jak`XYZ('<json result goes here>');`

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|JSONP|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagane|Domyślny|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|Międzydomenowe wywołanie funkcji JavaScript poprzedzone prefiksem w pełni kwalifikowanej nazwy domeny, w której znajduje się funkcja.|Tak|ND|

### <a name="usage"></a>Użycie
Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** wychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](api-management-policy-reference.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
