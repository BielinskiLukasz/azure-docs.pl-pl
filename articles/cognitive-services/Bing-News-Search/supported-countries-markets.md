---
title: Obsługiwane kraje/regiony i języki dla interfejsu API wyszukiwania wiadomości Bing na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, które kraje/regiony i języki są obsługiwane przez interfejs API wyszukiwania obrazów Bing.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 12/20/2017
ms.author: v-gedod
ms.openlocfilehash: 1cdacc82b680407814ff2d88e8ed43deacfb17d0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001664"
---
# <a name="bing-news-search-countriesregions-and-languages"></a>Wyszukiwanie wiadomości Bing kraje/regiony i języki

Interfejs API wyszukiwania wiadomości Bing obsługuje wiele kraje/regiony, wiele z więcej niż jednym języku. Określanie kraj/region z zapytaniem służy przede wszystkim, aby zawęzić wyniki wyszukiwania, w oparciu o zainteresowania tego kraju/regionu. Ponadto wyniki mogą zawierać łącza do usługi Bing, a te linki mogą lokalizować Bing środowiska użytkownika zgodnie z określonego kraju/regionu lub języka.

Można określić kraj/region, w którym używana jest `cc` parametr zapytania. Jeśli określisz kraju/regionu, należy także określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki zależą od countr/region są one podane dla każdego kraju/regionu w tabeli rynkach.

Alternatywnie, można określić przy użyciu rynku `mkt` parametr zapytania i kod z **rynków** tabeli. Określanie na rynek, jednocześnie Określa kraj/region i język preferowany. `setLang` Parametr zapytania może być ustawiona na kod języka w tym przypadku; zazwyczaj jest to ten sam język określony przez `mkt` , chyba że użytkownik chce zobaczyć Bing w innym języku.

## <a name="supported-markets-for-news-search-endpoint"></a>Obsługiwane rynki dla punkt końcowy wyszukiwania wiadomości

Aby uzyskać `/news/search` punktu końcowego, w poniższej tabeli wymieniono wartości kodu na rynku, które umożliwia określenie `mkt` parametr zapytania. Wyszukiwarka Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  
  
Kodów listę kraj/region, który można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych krajów](#countrycodes).  
  
|Kraj/region|Język|Rynek kodu|  
|---------------------|--------------|-----------------| 
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Austria|Niemiecki|de-AT| 
|Szwajcaria|Niemiecki|de-CH|
|Niemcy|Niemiecki|de-DE.|
|Australia|Polski|EN-AU|
|Kanada|Polski|EN-CA|
|Wielka Brytania|Polski|en-GB|
|Indonezja|Polski|EN-ID|
|Irlandia|Polski|EN-IE|
|Indie|Polski|EN-IN|
|Malezja|Polski|Moje en|
|Nowa Zelandia|Polski|EN NZ|
|Republika Filipin|Polski|EN PH|
|Singapur|Polski|EN SG|
|Stany Zjednoczone|Polski|pl-PL|
|Polski|Ogólne|EN-WW|
|Polski|Ogólne|EN XA|
|Republika Południowej Afryki|Polski|EN ZA|
|Argentyna|Hiszpański|ES AR|
|Chile|Hiszpański|ES-CL|
|Hiszpania|Hiszpański|es-ES|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone|Hiszpański|es-US| 
|Hiszpański|Ogólne|ES XL|
|Finlandia|Fiński|fi-FI|  
|Francja|Francuski|FR — być|
|Kanada|Francuski|fr-CA| 
|Belgia|Holenderski|Holandia — być|
|Szwajcaria|Francuski|FR-CH|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|IT-IT| 
|SRA Hongkong|Chiński (tradycyjny)|zh-HK|  
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Japonia|Japoński|ja-JP|  
|Korea|Koreański|ko-KR|  
|Holandia|Holenderski|NL-NL|  
|Chińska Republika Ludowa|Chiński|zh-CN|  
|Brazylia|Portugalski|pt-BR|
|Rosja|Rosyjski|ru-RU|  
|Szwecja|Szwedzki|sv-SE|  
|Turcja|Turecki|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Obsługiwane rynki dla punktu końcowego wiadomości
Aby uzyskać `/news` punktu końcowego, w poniższej tabeli wymieniono wartości kodu na rynku, które umożliwia określenie `mkt` parametr zapytania. Wyszukiwarka Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  
  
Kodów listę kraj/region, który można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych krajów](#countrycodes).  
  
|Kraj/region|Język|Rynek kodu|  
|---------------------|--------------|-----------------| 
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Niemcy|Niemiecki|de-DE.|
|Australia|Polski|EN-AU|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Polski|Ogólne|EN-WW|
|Chile|Hiszpański|ES-CL|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone|Hiszpański|es-US| 
|Finlandia|Fiński|fi-FI|  
|Kanada|Francuski|fr-CA|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|IT-IT| 
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Obsługiwane rynki dla punktu końcowego popularne wiadomości
Aby uzyskać `/news/trendingtopics` punktu końcowego, w poniższej tabeli wymieniono wartości kodu na rynku, które umożliwia określenie `mkt` parametr zapytania. Wyszukiwarka Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  
  
Kodów listę kraj/region, który można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych krajów](#countrycodes).  
  
|Kraj/region|Język|Rynek kodu|  
|---------------------|--------------|-----------------| 
|Niemcy|Niemiecki|de-DE.|
|Australia|Polski|EN-AU|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Kanada|Polski|EN-CA|
|Indie|Polski|EN-IN|
|Francja|Francuski|fr-FR|
|Kanada|Francuski|fr-CA|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kody krajów  

Poniżej przedstawiono kodów krajów/regionów, które mogą być określone w `cc` parametr zapytania. Lista może ulec zmianie.  
  
|Kraj/region|Kod kraju|  
|---------------------|------------------|  
|Argentyna|AR|  
|Australia|AU|  
|Austria|AT|  
|Belgia|MOŻNA|  
|Brazylia|BRAZYLIA|  
|Kanada|Urząd certyfikacji|  
|Chile|CL|  
|Dania|DK|  
|Finlandia|FI|  
|Francja|PW|  
|Niemcy|DE|  
|SRA Hongkong|HK|  
|Indie|INDIE|  
|Indonezja|ID|  
|Włochy|IT|  
|Japonia|JP|  
|Korea|KR|  
|Malezja|MOJE|  
|Meksyk|MX|  
|Holandia|HOLANDIA|  
|Nowa Zelandia|NZ|  
|Norwegia|NIE|  
|Chińska Republika Ludowa|CN|  
|Polska|PL|  
|Portugalia|(CZAS PACYFICZNY)|  
|Republika Filipin|PH|  
|Rosja|JEDNOSTKA ŻĄDANIA|  
|Arabia Saudyjska|AMERYKA POŁUDNIOWA|  
|Republika Południowej Afryki|ZA|  
|Hiszpania|ES|  
|Szwecja|SE|  
|Szwajcaria|CH|  
|Tajwan|TW|  
|Turcja|TR|  
|Wielka Brytania|GB|  
|Stany Zjednoczone|USA|

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat punktów końcowych wyszukiwania wiadomości Bing, zobacz [odwołanie do interfejsu API wyszukiwania wiadomości w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).