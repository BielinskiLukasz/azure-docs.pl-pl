---
title: Zamiana mowy na tekst — usługa mowy
titleSuffix: Azure Cognitive Services
description: Funkcja zamiany mowy na tekst włącza transkrypcję strumieni audio w czasie rzeczywistym do tekstu. Twoje aplikacje, narzędzia lub urządzenia mogą wykorzystywać, wyświetlać i podejmować działania dotyczące tego tekstu. Ta usługa współpracuje bezproblemowo z funkcją zamiany tekstu na mowę (synteza mowy) i funkcją tłumaczenia mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: erhopf
ms.openlocfilehash: 8518f92a4f4df1686d4b338783a93d969e04d219
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388298"
---
# <a name="what-is-speech-to-text"></a>Co to jest zamiana mowy na tekst?

Zamiana mowy na tekst z usługi mowy, znanej również jako rozpoznawanie mowy, umożliwia zapisanie strumieni audio w czasie rzeczywistym do tekstu. Aplikacje, narzędzia lub urządzenia mogą zużywać, wyświetlać i podejmować działania dotyczące tego tekstu jako dane wejściowe polecenia. Ta usługa jest oparta na tej samej technologii rozpoznawania, która jest wykorzystywana przez firmę Microsoft dla Cortany i produktów pakietu Office. Bezproblemowo współpracuje z ofertami usług <a href="./speech-translation.md" target="_blank">tłumaczenia <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i <a href="./text-to-speech.md" target="_blank">zamiany tekstu <span class="docon docon-navigate-external x-hidden-focus"></span> na mowę</a> . Aby uzyskać pełną listę dostępnych języków zamiany mowy na tekst, zobacz [obsługiwane języki](language-support.md#speech-to-text).

Usługa zamiany mowy na tekst domyślnie używa modelu języka uniwersalnego. Ten model został przeszkolony przy użyciu danych firmy Microsoft i jest wdrażany w chmurze. Jest optymalny dla scenariuszy konwersacji i dyktowania. Przy użyciu zamiany mowy na tekst w celu rozpoznawania i transkrypcji w unikatowym środowisku można tworzyć i uczenie niestandardowych modeli akustycznych, językowych i wymowy. Dostosowanie jest pomocne w odniesieniu do hałasu otoczenia lub słownictwa specyficznego dla branży.

> [!NOTE]
> Rozpoznawanie mowy Bing został zlikwidowany 15 października 2019. Jeśli Twoje aplikacje, narzędzia lub produkty używają rozpoznawanie mowy Bing interfejsów API lub Custom Speech, zostały utworzone przewodniki ułatwiające Migrowanie do usługi mowy.
> - [Migrowanie z rozpoznawanie mowy Bing do usługi mowy](how-to-migrate-from-bing-speech.md)
> - [Migrowanie z Custom Speech do usługi mowy](how-to-migrate-from-custom-speech-service.md)

## <a name="get-started-with-speech-to-text"></a>Wprowadzenie do zamiany mowy na tekst

Usługa zamiany mowy na tekst jest dostępna za pośrednictwem [zestawu Speech SDK](speech-sdk.md). Istnieje kilka typowych scenariuszy dostępnych w ramach przewodników szybki start w różnych językach i platformach:

 - [Szybki Start: Rozpoznawanie mowy przy użyciu danych wejściowych mikrofonu](quickstarts/speech-to-text-from-microphone.md)
 - [Szybki Start: Rozpoznawanie mowy z pliku](quickstarts/speech-to-text-from-file.md)
 - [Szybki Start: Rozpoznawanie mowy przechowywanej w usłudze BLOB Storage](quickstarts/from-blob.md)

Jeśli wolisz używać usługi REST do zamiany mowy na tekst, zobacz [interfejsy API REST](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Samouczki i przykładowy kod

Jeśli masz szansę na korzystanie z usługi Speech, wypróbuj nasz samouczek, który uczy się, jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK i LUIS.

- [Samouczek: Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK i LUIS przy użyciuC#](how-to-recognize-intents-from-speech-csharp.md)

Przykładowy kod dla zestawu Speech SDK jest dostępny w witrynie GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi.

- [Przykłady zamiany mowy na tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Dostosowywanie

Oprócz standardowego modelu usługi mowy można tworzyć modele niestandardowe. Dostosowanie pomaga w przezwyciężeniu barier rozpoznawania mowy, takich jak styl mowy, słownictwo i hałas w tle, zobacz [Custom Speech](how-to-custom-speech.md). Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych, zobacz [obsługiwane języki](supported-languages.md) , aby sprawdzić pomoc techniczną.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Następne kroki

- [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
- [Pobieranie zestawu Speech SDK](speech-sdk.md)
