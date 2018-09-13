---
title: Kluczowe frazy wyodrębniania wyszukiwania kognitywnego umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Ocenia tekstu bez struktury i dla każdego rekordu zwraca listę kluczowych fraz w usłudze Azure Search wzbogacony potok.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: b1da16269a1cbe83c6c0c625aba13026b6a462d6
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35942584"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Klucz frazy umiejętności cognitive

**Klucz frazy** umiejętności ocenia tekstu bez struktury, a następnie dla każdego rekordu zwraca listę fraz kluczowych.

Ta możliwość jest przydatna, jeśli chcesz szybko identyfikować jego główne punkty najważniejsze w rekordzie. Na przykład dany tekst wejściowy "żywności został wyborna i wystąpiły personelu wspaniała", usługa zwraca "jedzenie" i "personel wspaniała".

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekord powinien być 50 000 znaków, gdyż jest mierzone przez `String.Length`. Jeśli zachodzi potrzeba Podziel swoje dane przed wysłaniem ich do wyodrębnianie kluczowych fraz, rozważ użycie [umiejętności dzielenie tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.
| Dane wejściowe                | Opis |
|---------------------|-------------|
| defaultLanguageCode | (Opcjonalnie) Kod języka stosowane do dokumentów, które jawnie określać języka.  Jeśli kod języka domyślnego nie jest określony, angielskie (en) będzie służyć jako kod języka domyślnego. <br/> Zobacz [pełną listę obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Opcjonalnie) Maksymalna liczba frazy kluczowe do produkcji. |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności
| Dane wejściowe     | Opis |
|--------------------|-------------|
| tekst | Tekst do analizy.|
| languageCode  |  Ciąg wskazujący język rekordów. Jeśli nie określono tego parametru, kod języka domyślnego będzie służyć do analizowania rekordów. <br/>Zobacz [pełną listę obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Przykładowa definicja

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia
Jeśli podasz kod nieobsługiwany język, zostanie wygenerowany błąd i frazy kluczowe nie zostały wyodrębnione.
Jeśli tekst jest pusta, zostaną wygenerowane ostrzeżenie.
Jeśli tekst jest większe niż 50 000 znaków, tylko pierwsze 50 000 znaków zostanie przeanalizowany i pojawi się ostrzeżenie.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)