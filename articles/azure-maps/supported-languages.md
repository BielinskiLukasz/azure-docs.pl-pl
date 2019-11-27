---
title: Obsługa lokalizacji w Azure Maps | Microsoft Docs
description: Informacje o obsługiwanych językach dla usług w Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 64197cc85822997926a8011af8cb3b981fa9064d
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286419"
---
# <a name="localization-support-in-azure-maps"></a>Obsługa lokalizacji w Azure Maps

Azure Maps obsługuje różne języki i widoki na podstawie kraju/regionu. W tym artykule przedstawiono obsługiwane języki i widoki ułatwiające implementację Azure Maps.


## <a name="azure-maps-supported-languages"></a>Obsługiwane języki Azure Maps

Azure Maps zostały zlokalizowane w różnych językach w ramach usług. W poniższej tabeli przedstawiono obsługiwane kody języka dla każdej usługi.  
  

| ID         | Nazwa                   |  Maps | Wyszukaj | Routing | Pogoda | Zdarzenia dotyczące ruchu | Formant mapy JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabski                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bengalski (Bangladesz)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bengalski (Indie)         |       |       |         |     ✓    |                   |                |
| BS — BA      | Bośniacki                 |       |       |         |     ✓    |                   |                |
| EU-ES      | Baskijski                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bułgarski              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ES urzędu certyfikacji      | Kataloński                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chiński (uproszczony)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chiński (SRA Hongkong)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chiński (Tajwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Chorwacki               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Czeski                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| Akcelerator deweloperski w wersji DK      | Duński                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Holenderski (Belgia)        |       |    ✓   |         |      ✓    |                   |                |
| NL-NL      | Holenderski (Holandia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-AU      | Angielski (Australia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN NZ      | Angielski (Nowa Zelandia)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Angielski (Wielka Brytania) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Angielski (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estoński               |       |    ✓   |         |      ✓    |         ✓         |                |
| PLI — PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Fiński                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francuski                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francuski (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| GL — ES      | Galicyjski               |       |    ✓   |         |         |                   |                |
| de-DE.      | Niemiecki                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Grecki                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu      | Gudżarati                |       |       |         |     ✓    |                   |                |
| HE-IL      | Hebrajski                 |       |    ✓   |         |     ✓    |         ✓         |                |
| w      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Węgierski              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| IS-IS      | Islandzki              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonezyjski             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| IT-IT      | Włoski                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japoński               |       |        |         |     ✓    |                   |                |
| kn-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazachski                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreański                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Łaciński (Ameryka Łacińska) |       |    ✓   |         |         |                   |                |
| LV — LV      | Łotewski                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litewski             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk — MK      | Macedonii             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malajski (łaciński)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norwegian Bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutralne Języki w języku prawdy dla wszystkich regionów w lokalnych skryptach, jeśli są dostępne |   ✓     |        |         |       |        |      ✓          |
| NGT — Latn   | Neutralna obudowa prawdy-łacińskiego exonyms. Skrypt łaciński zostanie użyty, jeśli jest dostępny |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polski                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugalski (Brazylia)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugalski (Portugalia)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | Pendżabski                 |       |       |         |     ✓    |                   |                |
| RO RO      | Rumuński               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Rosyjski                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbski (Cyrylica)     |       |   Wirtualizacja SR-RS  |         |    Wirtualizacja SR-RS     |                   |                |
| sr-latn-RS | Serbski (łaciński)        |       |       |         |     Wirtualizacja sr-latn    |                   |                |
| sk-SK      | Słowacki             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Słoweński              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Hiszpański                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Hiszpański (Meksyk)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Szwedzki                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| Ta w      | Tamilski (Indie)                 |       |       |         |     ✓    |                   |                |
| Twórz w      | Telugu (Indie)                 |       |       |         |     ✓    |                   |                |
| th TH      | Tajlandzki                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turecki                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukraiński               |       |    ✓   |         |     ✓    |                   |                |
| Twoje — klucz podstawowy      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbek                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Wietnamski             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps obsługiwane widoki

> [!Note]
> Azure Maps zostało wydane w następujących krajach/regionach 1 sierpnia 2019:
>  * Argentyna
>  * Indie
>  * Maroko
>  * Pakistan
>
> Po 1 sierpnia 2019 ustawienie parametru **widoku** spowoduje zdefiniowanie zwróconej zawartości mapy dla nowych regionów/krajów wymienionych powyżej. Zachęcamy do upewnienia się, że skonfigurowano parametr View jako wymagany dla interfejsów API REST i zestawów SDK używanych przez używane usługi.
>  
>
>  **Interfejsy API REST:**
>  
>  Upewnij się, że parametr View został skonfigurowany zgodnie z wymaganiami. Parametr widoku Określa, który zestaw zawartości o treści geopolitycznej jest zwracany przez Azure Maps usług. 
>
>  Azure Maps usługi REST:
>    
>    * Pobierz kafelek mapy
>    * Pobierz obraz mapy 
>    * Pobierz rozmyte wyszukiwanie
>    * Pobierz punkt POI wyszukiwania
>    * Pobierz kategorię wyszukiwania punkt POI
>    * Rozpocznij wyszukiwanie w pobliżu
>    * Pobierz adres wyszukiwania
>    * Pobierz strukturę adresu wyszukiwania
>    * Uzyskaj zwrotny adres wyszukiwania
>    * Pobierz adres wyszukiwania odwrotnie
>    * Opublikuj wyszukiwanie w geometrii
>    * Zapoznawcza partia adresów wyszukiwania
>    * Adres wyszukiwania zwrotnego — wersja zapoznawcza
>    * Publikuj wyszukiwanie wzdłuż trasy
>    * Przeszukiwanie rozmytej partii w wersji zapoznawczej
>
>    
>  **Zestawy**
>
>  Upewnij się, że skonfigurowano parametr View zgodnie z wymaganiami i masz najnowszą wersję zestawu Web SDK i Android SDK. Uwzględnione zestawy SDK:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Parametr **widoku** Azure Maps (określany również jako "parametr regionu użytkownika") to dwuliterowy kod kraju ISO-3166, który będzie pokazywał poprawne mapy dla danego kraju/regionu, określając, który zestaw geopoznawczych treści jest zwracany przez usługi Azure Maps, w tym obramowania i etykiety wyświetlane na mapie. 

Domyślnie parametr widoku jest ustawiony na **ujednolicony**, nawet jeśli nie został zdefiniowany w żądaniu. Jest odpowiedzialny za określenie lokalizacji użytkowników, a następnie ustawienie parametru widoku dla tej lokalizacji. Alternatywnie możesz ustawić opcję "View = Auto", która zwróci dane mapy na podstawie adresu IP żądania.  Parametr widoku w Azure Maps musi być używany zgodnie z obowiązującymi przepisami, włącznie z tymi, które dotyczą mapowania kraju, w którym są dostępne mapy, obrazy i inne dane oraz zawartość innych firm, do których uzyskuje się dostęp za pośrednictwem Azure Maps.


W poniższej tabeli przedstawiono obsługiwane widoki.

| Widok         | Opis                            |  Maps | Wyszukaj | kontrolka mapy JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Zjednoczone Emiraty Arabskie (widok arabski)    |   ✓   |        |     ✓          |
| AR           | Argentyna (widok argentyński)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrajn (widok arabski)                 |   ✓   |        |     ✓          |
| IN           | Indie (widok Indyjski)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (widok arabski)                    |   ✓   |        |     ✓          |
| JO           | Jordania (widok arabski)                  |   ✓   |        |     ✓          |
| KW           | Kuwejt (widok arabski)                  |   ✓   |        |     ✓          |
| LB           | Liban (widok arabski)                 |   ✓   |        |     ✓          |
| MA           | Maroko (widok marokański)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (widok arabski)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (widok pakistański)              |   ✓   |    ✓    |     ✓          |
| PS           | Autonomia Palestyńska (widok arabski)    |   ✓   |        |     ✓          |
| QA           | Katar (widok arabski)                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudyjska (widok arabski)            |   ✓   |        |     ✓          |
| SY           | Syria (widok arabski)                   |   ✓   |        |     ✓          |
| YE           | Jemen (widok arabski)                   |   ✓   |        |     ✓          |
| Automatycznie         | Zwróć dane mapy na podstawie adresu IP żądania.|   ✓   |    ✓   |     ✓          |
| Zunifikowane      | Ujednolicony widok (inne)                  |   ✓   |   ✓     |     ✓          |
