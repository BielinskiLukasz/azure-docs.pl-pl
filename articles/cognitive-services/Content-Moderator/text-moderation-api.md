---
title: Moderowanie tekstu — Content Moderator
titleSuffix: Azure Cognitive Services
description: Użyj moderowania tekstu, aby uzyskać niechciany tekst, dane OSOBowe i niestandardowe listy warunków.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e1d5224d8dc86c82624613b0d2a984ceef3ae5bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564379"
---
# <a name="learn-text-moderation-concepts"></a>Poznaj pojęcia związane z moderowaniem tekstu

W celu zapewnienia umiarkowanej zawartości tekstu użyj funkcji moderowania tekstu wspomaganego przez Content Moderator i [kontroli ludzkich](Review-Tool-User-Guide/human-in-the-loop.md) .

Użytkownik może zablokować, zatwierdzać lub przeglądać zawartość na podstawie zasad i progów. Służy do rozszerzonego moderowania środowisk, w których partnerzy, pracownicy i konsumenci generują zawartość tekstową. Obejmują one pokoje rozmów, Tablice dyskusyjne, rozszerzenie czatbotów, katalogi handlu elektronicznego i dokumenty. 

Odpowiedź usługi zawiera następujące informacje:

- Niewulgarność: oparta na terminach zgodność z wbudowaną listą niezgodnych terminów w różnych językach
- Klasyfikacja: Klasyfikacja z pomocą maszynową do trzech kategorii
- Dane osobowe
- Tekst skorygowany automatycznie
- Oryginalny tekst
- Język

## <a name="profanity"></a>Wulgaryzmów

Jeśli interfejs API wykryje wszelkie nieodpowiednie warunki w żadnym z [obsługiwanych języków](Text-Moderation-API-Languages.md), te warunki są zawarte w odpowiedzi. Odpowiedź zawiera również lokalizację (`Index`) w oryginalnym tekście. W poniższym przykładowym kodzie JSON odwołują się do warunków znalezionych w [niestandardowych listach terminów](try-terms-list-api.md) , jeśli są dostępne. `ListId`

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> W polu parametr **języka** Przypisz `eng` lub pozostaw to pole puste, aby wyświetlić odpowiedź **klasyfikacji** z obsługą maszyn (funkcja w wersji zapoznawczej). **Ta funkcja obsługuje tylko język angielski**.
>
> W przypadku wykrywania **warunków wulgarnych** Użyj [kodu ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) dla obsługiwanych języków wymienionych w tym artykule lub pozostaw to pole puste.

## <a name="classification"></a>Klasyfikacja

**Funkcja klasyfikacji tekstu** wspierana przez Content moderator jest obsługiwana **tylko w języku angielskim**i pomaga wykrywać potencjalnie niepożądaną zawartość. Oflagowana zawartość może zostać oceniona jako nieodpowiednia w zależności od kontekstu. Przekazuje prawdopodobieństwo każdej kategorii i może zalecić przegląd ludzkich. Funkcja używa przeszkolonego modelu do identyfikowania możliwego użycia, derogacyjnego lub dyskryminacyjnego języka. Dotyczy to żargonu, skróconych wyrazów, obraźliwych i celowo błędnie napisanych wyrazów do przeglądu. 

Poniższy wyodrębnienie w ekstrakcie JSON pokazuje przykładowe dane wyjściowe:

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Wyjaśnienie

- `Category1`odnosi się do potencjalnej obecności języka, który może być uważany za jawny lub dorosły w pewnych sytuacjach.
- `Category2`odnosi się do potencjalnej obecności języka, który może być uważany za seksowo lub dojrzały w niektórych sytuacjach.
- `Category3`odnosi się do potencjalnej obecności języka, który może być uznany za obraźliwy w pewnych sytuacjach.
- `Score`jest z zakresu od 0 do 1. Im wyższy wynik, tym większy jest przewidywanie, że Kategoria może być stosowana. Ta funkcja opiera się na modelu statystycznym zamiast ręcznie zakodowanych wyników. Zalecamy testowanie przy użyciu własnej zawartości, aby określić, jak każda kategoria jest wyrównana do wymagań.
- `ReviewRecommended`ma wartość true lub false, w zależności od wewnętrznych progów wyniku. Klienci powinni ocenić, czy należy używać tej wartości, czy też decydować o niestandardowych progach na podstawie ich zasad dotyczących zawartości.

## <a name="personal-data"></a>Dane osobowe

Funkcja dane OSOBowe wykrywa potencjalną obecność tych informacji:

- Adres e-mail
- Adres wysyłkowy nas
- Adres IP
- Numer telefonu USA
- BRYTYJSKI numer telefonu
- Numer ubezpieczenia społecznego (SSN)

W poniższym przykładzie pokazano przykładową odpowiedź:

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Funkcja Autokorekty

Załóżmy, że tekst wejściowy jest ("lzay" i "f0x" zamierzone):

    The qu!ck brown f0x jumps over the lzay dog.

W przypadku poproszenia o korektę automatyczną odpowiedź zawiera poprawioną wersję tekstu:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Tworzenie niestandardowych list terminów i zarządzanie nimi

Mimo że globalna lista warunków jest bardzo wysoka w większości przypadków, warto zapoznać się z warunkami, które są specyficzne dla potrzeb Twojej firmy. Na przykład możesz chcieć odfiltrować wszystkie konkurencyjne nazwy marek od wpisów przez użytkowników.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list terminów**, a poszczególne listy **nie mogą przekraczać 10 000 terminów**.
>

Poniższy przykład pokazuje identyfikator pasującej listy:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator udostępnia [interfejs API listy terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) z operacjami do zarządzania listami terminów niestandardowych. Rozpocznij od [terminu listy konsoli interfejsu](try-terms-list-api.md) API i użyj przykładów kodu interfejsu API REST. Zapoznaj się również z tematem [przewodnika Szybki Start dla programu .NET](term-lists-quickstart-dotnet.md) , jeśli C#znasz program Visual Studio i.

## <a name="next-steps"></a>Następne kroki

Przetestuj [konsolę interfejsu API moderowania tekstu](try-text-api.md) i użyj przykładów kodu interfejsu API REST. Zapoznaj się również z tematem [przewodnika Szybki Start](text-moderation-quickstart-dotnet.md) dotyczącego moderowania tekstu w C#przypadku znajomości programów Visual Studio i.
