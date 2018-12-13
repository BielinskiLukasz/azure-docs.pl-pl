---
title: Usługa cognitive search zasoby dokumentacji — usługa Azure Search
description: Adnotacjami listę artykułów, samouczki, przykłady i blog publikuje obciążeń związanych z cognitive wyszukiwania w usłudze Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 609b5d990cffce10733f6fc82e6b1032ad0f06bb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314605"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Zasoby dokumentacji dla obciążeń wyszukiwania kognitywnego

Usługa cognitive search teraz w publicznej wersji zapoznawczej, jest nową wzbogacania warstwę usługi Azure Search indeksowanie, odnajduje informacje ukryte w źródłach innych niż tekst i tekstu takimi samymi, przekształcania go w zawartość do przeszukiwania pełnotekstowego w usłudze Azure Search.

Pełną dokumentację usługi cognitive search są następujące artykuły.

## <a name="getting-started"></a>Wprowadzenie
+ [Co to jest usługa cognitive search?](cognitive-search-concept-intro.md)
+ [Szybki Start: Wypróbuj wyszukiwanie kognitywne w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Dowiedz się, usługa cognitive search interfejsów API](cognitive-search-tutorial-blob.md)
+ [Przykład: tworzenie umiejętności niestandardowej](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Wskazówki dotyczące wykonywania określonych zadań
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak odwoływać się do adnotacji w zestawu umiejętności](cognitive-search-concept-annotations-syntax.md)
+ [Sposób mapowania pól do indeksu](cognitive-search-output-field-mapping.md)
+ [Jak przetwarzanie i wyodrębnianie informacji z obrazów](cognitive-search-concept-image-scenarios.md)
+ [Jak odbudować indeksu usługi Azure Search](search-howto-reindex.md)
+ [Jak zdefiniować interfejs umiejętności niestandardowe](cognitive-search-custom-skill-interface.md)
+ [Wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Informacje ogólne

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [Interfejs API REST (wersja zapoznawcza)](search-api-2017-11-11-preview.md)
  + [Tworzenie zestawu umiejętności (wersja api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Tworzenie indeksatora (wersja api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zobacz także

+ [Interfejs API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indeksatory w usłudze Azure Search](search-indexer-overview.md)
+ [Co to jest usługa Azure Search?](search-what-is-azure-search.md)
