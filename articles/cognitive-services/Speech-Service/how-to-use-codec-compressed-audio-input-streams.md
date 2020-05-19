---
title: Koder-dekoder audio skompresowany dźwięk przy użyciu zestawu mowy SDK-Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowane audio do usługi mowy przy użyciu zestawu Speech SDK. Dostępne dla języków C++, C# i Java dla systemu Linux i języka Java w systemie Android i w systemie iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 13cb35ffa650661da2855787279c4bdc37126ac9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585027"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Korzystanie z kodera-dekoder skompresowanego audio przy użyciu zestawu Speech SDK

Interfejs API **strumienia danych wejściowych audio** usługi Speech Service SDK umożliwia przesyłanie strumieniowe skompresowanego dźwięku do usługi mowy przy użyciu `PullStream` lub `PushStream` .

Przesyłane strumieniowo skompresowane audio wejściowe jest obecnie obsługiwane dla języków C#, C++, Java w systemie Windows (platformy UWP aplikacje nie są obsługiwane) i Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8). Jest ona również obsługiwana w przypadku języka Java w systemach Android i C na platformie iOS.
* Zestaw Speech SDK w wersji 1.10.0 lub nowszej jest wymagany dla RHEL 8 i CentOS 8
* Dla systemu Windows wymagany jest pakiet Speech SDK w wersji 1.11.0 lub nowszej.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu kodera skompresowanego sygnału audio

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak rozpoznać mowę](quickstarts/speech-to-text-from-microphone.md)
