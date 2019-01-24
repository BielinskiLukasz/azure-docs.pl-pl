---
title: Przestarzałe umiejętności poznawcze — usługa Azure Search
description: Ta strona zawiera listę wyszukiwania kognitywnego umiejętności, które są traktowane jako przestarzałe i nie będą obsługiwane w najbliższej przyszłości.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c35e4253858d6820d86d7d3e0763a3dcc577d09d
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54387937"
---
# <a name="deprecated-cognitive-search-skills"></a>Umiejętności przestarzałe wyszukiwania kognitywnego

W tym dokumencie opisano umiejętności poznawcze, które są uznawane za przestarzałe. Użyj następującymi wskazówkami dla zawartości:

* Nazwa umiejętności: Nazwa umiejętności, które zostaną wycofane, jest on mapowany @odata.type atrybutu.
* Ostatnie dostępna wersja interfejsu api: Najnowszej wersji platformy Azure, wyszukaj publicznego interfejsu API za pomocą których dokładniejsze zawierającego odpowiednie umiejętności przestarzałe można utworzone/zaktualizowane.
* Zakończenie pomocy technicznej: Ostatni dzień po upływie którego jest uznawany za odpowiednie umiejętności nieobsługiwany. Utworzone wcześniej dokładniejsze powinny nadal działać, ale zaleca się użytkownicy, aby uniknąć przestarzałe umiejętności.
* Rekomendacje: Ścieżki migracji do przodu do użycia w obsługiwanych umiejętności. Aby postępować zgodnie z zaleceniami, aby kontynuować do otrzymania pomocy technicznej doradza się użytkownicy.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Ostatnie dostępna wersja interfejsu api

2017-11-11-preview

### <a name="end-of-support"></a>Zakończenie obsługi

15 lutego 2019 r.

### <a name="recommendations"></a>Zalecenia 

Use [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) instead. Zapewnia większość funkcjonalności NamedEntityRecognitionSkill wyższej jakości. Zawiera również bogatsze informacje w jej pola złożone dane wyjściowe.

Aby przeprowadzić migrację do [umiejętności rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md), trzeba będzie wykonać co najmniej jedną z następujących zmian do definicji umiejętności. Można zaktualizować przy użyciu definicji umiejętności [aktualizacji interfejsu API zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

_Uwaga_: Obecnie współczynnik ufności jako koncepcja jest nieobsługiwana. Jego będą obsługiwane w najbliższej przyszłości. `minimumPrecision` Parametr istnieje w `EntityRecognitionSkill` do użytku w przyszłości, jak i dla zapewnienia zgodności.

1. *(Wymagane)*  Zmiany `@odata.type` z `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` do `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Opcjonalnie)*  Jeśli wykonujesz użytkowania `entities` danych wyjściowych, należy użyć `namedEntities` złożonych kolekcji danych wyjściowych `EntityRecognitionSkill` zamiast tego. Możesz użyć `targetName` w umiejętnościach definicji w celu zamapowania go do adnotacji o nazwie `entities`.

3. *(Opcjonalnie)*  , Jeśli nie zostanie jawnie określony `categories`, `EntityRecognitionSkill` może zwrócić inny rodzaj kategorii, oprócz tych, które były obsługiwane przez `NamedEntityRecognitionSkill`. Jeśli to zachowanie jest niepożądany, upewnij się, że jawnie ustawić `categories` parametr `["Person", "Location", "Organization"]`.

    _Definicje migracji_

    * Proste migracji

        _(Przed) Definicja umiejętności NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Po) Definicja umiejętności EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Nieco skomplikowane migracji

        _(Przed) Definicja umiejętności NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Po) Definicja umiejętności EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętności rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md)
