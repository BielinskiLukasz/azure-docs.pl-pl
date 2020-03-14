---
title: 'Szybki Start: analiza tekstua Biblioteka kliencka v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby połączyć aplikacje z interfejs API analizy tekstu z poziomu platformy Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 5a0856bb2fc73b7095b0b2552efe0ccc91ae3f59
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241724"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Szybki Start: korzystanie z biblioteki klienta analiza tekstu

Rozpocznij pracę z biblioteką klienta analiza tekstu. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta analiza tekstu do wykonania:

* Analiza tonacji
* Wykrywanie języka
* Rozpoznawanie jednostek
* Wyodrębnianie kluczowych fraz

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Najnowsza wersja zapoznawcza interfejs API analizy tekstu jest `3.0-preview`, która obejmuje publiczną wersję zapoznawczą dla ulepszonej [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [nazwanego rozpoznawania jednostek (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Najnowsza stabilna wersja jest `2.1`.
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Ten przewodnik Szybki Start dotyczy tylko wersji `3.0-preview` biblioteki klienckiej analiza tekstu, która obejmuje publiczną wersję zapoznawczą dla ulepszonego [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [nazwanego rozpoznawania jednostek (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Najnowsza wersja zapoznawcza interfejs API analizy tekstu jest `3.0-preview`, która obejmuje publiczną wersję zapoznawczą dla ulepszonej [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [nazwanego rozpoznawania jednostek (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Najnowsza stabilna wersja jest `2.1`.
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej.
> * Tę wersję biblioteki klienta analiza tekstu można również uruchomić [w przeglądarce](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Najnowsza wersja zapoznawcza interfejs API analizy tekstu jest `3.0-preview`, która obejmuje publiczną wersję zapoznawczą dla ulepszonej [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [nazwanego rozpoznawania jednostek (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Najnowsza stabilna wersja jest `2.1`.
>    * Pamiętaj, aby postępować zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Zapoznaj się z dokumentacją referencyjną poniżej. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Obsługa dodatkowych języków

Jeśli kliknięto tę kartę, prawdopodobnie nie widzisz przewodnika Szybki Start w ulubionym języku programowania. Nie martw się, możemy korzystać z dodatkowych przewodników Szybki Start. Użyj tabeli, aby znaleźć odpowiedni przykład dla języka programowania.

| Język | Dostępna wersja | 
|----------|------------------------|
| Ruby     | [Wersja 2,1](ruby-sdk.md) | 
| Przejdź       | [Wersja 2,1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Eksplorowanie rozwiązania](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
* [Wykryj język](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)
