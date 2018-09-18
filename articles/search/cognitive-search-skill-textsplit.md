---
title: Tekst podziału umiejętności wyszukiwanie kognitywne (Azure Search) | Dokumentacja firmy Microsoft
description: Podziel tekst na fragmenty lub strony tekstu, na podstawie długości w usłudze Azure Search wzbogacony potok.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 583d2ac5a8ac4c236612cdfe78595da1812c56fa
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730770"
---
#   <a name="text-split-cognitive-skill"></a>Tekst, Podziel umiejętności cognitive

**Dzielenie tekstu** umiejętności dzieli tekst na fragmenty tekstu. Można określić, czy użytkownik chce podzielić go na zdania lub do stron o określonej długości. To umiejętności jest szczególnie przydatne w przypadku tekstu wymagania dotyczące długości w innych umiejętności podrzędne. 

> [!NOTE]
> Wyszukiwanie poznawcze jest dostępne w publicznej wersji zapoznawczej. Wykonywanie zestawu umiejętności i wyodrębniania obrazu i normalizacji są obecnie oferowane bezpłatnie. W późniejszym czasie cen tych funkcji zostanie ogłoszona. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| textSplitMode      | "Pojęcie strony" lub "zdania" | 
| maximumPageLength | Jeśli textSplitMode "strony" odnosi się to do długość maksymalna strony, gdyż jest mierzone przez `String.Length`. Wartość minimalna wynosi 100. | 
| defaultLanguageCode   | (opcjonalnie) Jedną z następujących kodach języków: `da, de, en, es, fi, fr, it, ko, pt`. Wartością domyślną jest angielski (en). Kwestie do rozważenia:<ul><li>W przypadku przekazania format languagecode countrycode jest używana tylko część languagecode format.</li><li>Jeśli język nie jest na poprzedniej liście, umiejętności podziału dzieli tekst na granicach znaków.</li><li>Dostarczanie kodu języka jest przydatna, aby uniknąć Wycinanie wyrazu w połowie języków innych niż spacja, takich jak języka chińskiego, japońskiego i koreańskiego.</li></ul>  |


## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Nazwa parametru       | Opis      |
|----------------------|------------------|
| tekst  | Tekst, który można podzielić na podciąg. |
| languageCode  | (Opcjonalnie) Kod języka dla danego dokumentu.  |

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności 

| Nazwa parametru     | Opis |
|--------------------|-------------|
| textItems | Tablica podciągi, które zostały wyodrębnione. |


##  <a name="sample-definition"></a>Przykładowa definicja

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>W przypadku wystąpienia błędów
Jeśli język nie jest obsługiwany, generowane jest ostrzeżenie, a tekst jest podzielony na granicach znaków.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
