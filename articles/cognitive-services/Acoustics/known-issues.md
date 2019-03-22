---
title: Znane problemy z wtyczką Akustyka projektu
titlesuffix: Azure Cognitive Services
description: W przypadku używania projektanta w wersji zapoznawczej dla Akustyka projektu, mogą wystąpić następujące znane problemy.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 50de4d983ed24440d655cf5b9ba3fb5e33d8d7cd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309949"
---
# <a name="project-acoustics-known-issues"></a>Projekt Akustyka znane problemy
W przypadku używania projektanta w wersji zapoznawczej dla Akustyka projektu, mogą wystąpić następujące znane problemy.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parametrów akustycznych zostaną utracone po zmianie nazwy sceny

Jeśli zmienisz scenę, wszystko, czego akustyczny parametry, które należą do tej sceny nie będzie automatycznie przenieść do sceny nowy. Będzie nadal istnieją one w starym pliku zasobów jednak. Wyszukaj **SceneName_AcousticParameters.asset** pliku wewnątrz **edytora** katalogu obok pliku sceny. Zmień nazwę pliku, aby odzwierciedlały nową nazwę sceny.

## <a name="unity-crashes-when-closing-project"></a>Unity ulega awarii podczas zamykania projektu

Na najnowsze wersje aparatu Unity (2018.2 +) jest to znana usterka, w którym Unity ulegnie awarii po zamknięciu projektu. To jest śledzona przez [ten problem Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Wdrożenia w systemie Android z niektórych wersji aparatu Unity

Niektóre wersje programu Unity mają usterkę przy użyciu wdrożenia wtyczek audio w systemie Android. Upewnij się, że nie używasz wersji dotyczy [tej usterki](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Otrzymuję komunikat o błędzie tego "nie można znaleźć pliku metadanych System.Security.dll"

Upewnij się, jest ustawiona wersja środowiska uruchomieniowego skryptów w ustawieniach Player **.NET 4.x odpowiednik**i ponownie uruchomić aparatu Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Występują problemy z uwierzytelniania podczas nawiązywania połączenia z platformą Azure

Dokładnie sprawdź wykorzystano poprawne poświadczenia dla konta platformy Azure, że Twoje konto obsługuje typ węzła w tworzenie i czy zegar systemowy jest prawidłowa.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Trwa anulowanie tworzenie pozostawia kartę tworzenie w stanie "usuwania"
Akustyka projektu spowoduje oczyszczenie wszystkich zasobów platformy Azure dla zadania o pomyślnym zakończeniu lub anulowaniu. Może to potrwać do 5 minut.

## <a name="next-steps"></a>Kolejne kroki
* Spróbuj [Unity](unity-quickstart.md) lub [Unreal](unreal-quickstart.md) zawartości

