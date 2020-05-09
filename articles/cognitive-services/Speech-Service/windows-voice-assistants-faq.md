---
title: Asystenci głosu w systemie Windows — często zadawane pytania
titleSuffix: Azure Cognitive Services
description: Często zadawane pytania podczas opracowywania agenta głosu systemu Windows.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: aa71057d3819acb335153ee5b4b65960320405be
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997415"
---
# <a name="samples-and-faqs"></a>Przykłady i często zadawane pytania

## <a name="the-uwp-voice-assistant-sample"></a>Przykład asystenta głosowego platformy UWP

Przykładem asystenta głosowego platformy UWP jest asystent głosowy w systemie Windows, który służy do

- zademonstrowanie używania interfejsów API ConversationalAgent systemu Windows
- Wyświetlanie najlepszych rozwiązań dla agenta głosowego w systemie Windows
- zapewnianie dostosowywalnej aplikacji i składników wielokrotnego użytku dla aplikacji Agent MVP
- Pokaż, jak Direct Speech line, wraz z bot Framework lub poleceniami niestandardowymi, może być używany razem z interfejsami API ConversationalAgent systemu Windows w celu kompleksowego korzystania z agenta głosowego

Dokumentacja dostarczana z przykładową aplikacją zawiera szczegółowe omówienie funkcji aktywacji głosu i zarządzania agentami zgodnie z wymaganiami wstępnymi dotyczącymi uruchamiania przez właściwe oczyszczanie.

> [!div class="nextstepaction"]
> [Odwiedź repozytorium GitHub dla przykładu platformy UWP](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Jak mogę skontaktować się z firmą Microsoft w zakresie zasobów, takich jak ograniczone tokeny funkcji dostępu i pliki modelu słów kluczowych?

Skontaktuj winvoiceassistants@microsoft.com się z pomocą techniczną, aby zażądać tych zasobów.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>Moja aplikacja jest wyświetlana w małym oknie, gdy aktywujem ją za pomocą głosu. Jak przejść z widoku kompaktowego do pełnego okna aplikacji?

Gdy aplikacja jest uruchamiana po raz pierwszy, zostanie uruchomiona w widoku kompaktowym. Zapoznaj się z tematem [wskazówki dotyczące projektowania w wersji zapoznawczej aktywacji głosu](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) , aby uzyskać wskazówki dotyczące różnych widoków i przejść między nimi w przypadku asystentów głosowych w systemie Windows.

Aby przejść z widoku zwartego do pełnego widoku aplikacji, użyj interfejsu API `TryEnterViewModeAsync`appView:

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Dlaczego funkcje asystenta głosowego w systemie Windows są włączone tylko dla aplikacji platformy UWP?

Uwzględniając zagrożenia dla prywatności związane z funkcjami takimi jak Aktywacja głosowa, funkcje platformy platformy UWP są niezbędne, aby funkcje asystenta głosowego w systemie Windows były wystarczająco bezpieczne.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>W przykładzie asystenta głosowego platformy UWP jest stosowana funkcja Direct line Speech. Czy muszę używać bezpośredniego rozpoznawania mowy dla asystenta głosowego w systemie Windows?

Przykładowa aplikacja platformy UWP została opracowana przy użyciu bezpośredniej mowy liniowej i zestawu SDK usługi Speech Services w celu pokazania sposobu korzystania z usługi dialogu z funkcją agenta konwersacji systemu Windows. Można jednak użyć dowolnej usługi do weryfikacji słów kluczowych lokalnych i w chmurze, konwersji zamiany mowy na tekst, okna dialogowego bot oraz konwersji zamiany tekstu na mowę.