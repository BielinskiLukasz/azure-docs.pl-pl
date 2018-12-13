---
title: Obsługa języków — interfejs API wyszukiwania wizualnego Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wizualnego Bing. Interfejs API wyszukiwania wizualnego Bing obsługuje więcej niż trzech tuzina kraje/regiony, wiele z więcej niż jednym języku.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 1dd434bbf71e482f5d0a52b3c84e46382079b43a
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886403"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Obsługa języka i regionu dla interfejsu API wyszukiwania wizualnego Bing

Interfejs API wyszukiwania wizualnego Bing obsługuje więcej niż trzech tuzina kraje/regiony, wiele z więcej niż jednym języku. Każde żądanie powinno obejmować kraj/region i język wybranego użytkownika. Wiedząc rynku użytkownika pomaga Bing odpowiednich wyników. Jeśli nie określisz kraj/region i język Bing sprawia, że najlepszy nakład pracy, aby określić kraj/region i język użytkownika. Ponieważ wyniki mogą zawierać łącza do usługi Bing, wiedząc, kraj/region i język może dostarczyć preferowanych Bing zlokalizowane środowisko użytkownika kliknięcie łącza Bing.

Aby określić kraj/region i język, ustaw `mkt` parametr zapytania (rynek) do kodu ze **rynków** w poniższej tabeli. Rynek Określa kraj/region i język. Jeśli użytkownik chce zobaczyć wyświetlania tekstu w innym języku, należy ustawić `setLang` parametru w kodzie odpowiedni język zapytania.

Alternatywnie można określić za pomocą kraju/regionu `cc` parametr zapytania. Jeśli określisz kraju/regionu, należy także określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki zależą od kraju/regionu są one podane dla każdego kraju w tabeli rynkach.



> [!NOTE]
> Obowiązują następujące ograniczenia na rynku:
>
> - Adnotacje rozpoznawania obrazów dostępnych w języku angielskim tylko.
> - Przepisu, zakupy i wgląd w tym strony są dostępne na rynku en US.


## <a name="countries"></a>Kraje

|Kraj/region|Kod|
|-------|----|
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
|Chiny|CN|
|Polska|PL|
|Portugalia|(CZAS PACYFICZNY)|
|Filipiny|PH|
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


## <a name="markets"></a>Rynki

|Kraj/region|Język|Rynek kodu|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES AR|
|Australia|Polski|EN-AU|
|Austria|Niemiecki|de-AT|
|Belgia|Holenderski|Holandia — być|
|Belgia|Francuski|FR — być|
|Brazylia|Portugalski|pt-BR|
|Kanada|Polski|EN-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|ES-CL|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE.|
|SRA Hongkong|Chiński (tradycyjny)|zh-HK|
|Indie|Polski|EN-IN|
|Indonezja|Polski|EN-ID|
|Włochy|Włoski|IT-IT|
|Japonia|Japoński|ja-JP|
|Korea|Koreański|ko-KR|
|Malezja|Polski|Moje en|
|Meksyk|Hiszpański|es-MX|
|Holandia|Holenderski|NL-NL|
|Nowa Zelandia|Polski|EN NZ|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Polski|EN PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Polski|EN ZA|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|FR-CH|
|Szwajcaria|Niemiecki|de-CH|
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Turcja|Turecki|tr-TR|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Stany Zjednoczone|Hiszpański|es-US|
