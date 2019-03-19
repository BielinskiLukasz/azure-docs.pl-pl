---
title: Projekt Akustyka Szybki Start — Unreal
titlesuffix: Azure Cognitive Services
description: Za pomocą przykładowej zawartości, eksperymentować Akustyka projektu projektowania kontrolek w Unreal i Wwise i wdrażanie pulpitu Windows.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 1314e393d292145ef112e700abf6ab1ef199db7d
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138187"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Szybki Start Unreal/Wwise Akustyka projektu
W tym przewodniku Szybki Start będziesz eksperymentować z Akustyka projektu kontrolki projektu przy użyciu podanych przykładowych zawartości dla aparatu Unreal Engine i Wwise.

Wymagania dotyczące oprogramowania:
* [Aparatu unreal Engine 4.21](https://www.unrealengine.com/)
* [Wwise 2018.1.6](https://www.audiokinetic.com/products/wwise/)

## <a name="download-the-sample-package"></a>Pobierz pakiet przykładowych
Pobierz [projektu Akustyka Unreal + Wwise przykładowy pakiet](http://www.microsoft.com/downloads/details.aspx?FamilyID=f03dff5a-5780-462e-87ef-e6d039d0748d). Przykładowego pakietu zawiera projekt aparatu Unreal Engine, projekt Wwise Unreal projektu oraz wtyczki Wwise Akustyka projektu.

## <a name="set-up-the-project-acoustics-sample-project"></a>Konfigurowanie projektu Akustyka przykładowego projektu
Aby skonfigurować projekt Akustyka Unreal/Wwise przykładowy projekt, należy najpierw zainstalować wtyczkę Akustyka projektu do Wwise. Wdrożenie plików binarnych Wwise Unreal projektu i dostosowanie wtyczki Unreal Wwise do obsługi Akustyka projektu.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Instalowanie wtyczki Wwise Akustyka projektu
Następnie otworzyć uruchamiania Wwise w **wtyczek** , w obszarze **instalowanie nowych wtyczek**, wybierz opcję **dodawanie z katalogu**. Wybierz `AcousticsWwisePlugin\ProjectAcoustics` katalogu, która została uwzględniona w pakiecie, który został pobrany.

![Instalowanie wtyczki Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Dodaj pliki binarne Wwise do projektu Akustyka Unreal przykładowego projektu
Z obszaru uruchamiania Wwise, kliknij przycisk **Unreal Engine** kartę, a następnie kliknij menu "hamburger" **ostatnie projekty systemu Unreal Engine** i wybierz **Przeglądaj w poszukiwaniu projektu**. Otwórz przykładowy projekt Unreal `.uproject` plików w pakiecie `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Karta Wwise Unreal](media/wwise-unreal-tab.png)

Obok Akustyka projekt przykładowy projekt, kliknij przycisk **integracja Wwise w projekcie**.

![Projekt gry Unreal Akustyka Wwise](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Rozszerzanie funkcji wtyczki unreal Engine firmy Wwise
Projekt Akustyka Unreal wtyczka wymaga dodatkowych zachowanie ujawnianie z wtyczki Wwise Unreal interfejsu API. Uruchamianie pliku wsadowego, wyposażone w projekcie Akustyka Unreal dodatek plug-in, aby zautomatyzować te modyfikacje:
* Wewnątrz `AcousticsGame\Plugins\ProjectAcoustics\Resources`Uruchom `PatchWwise.bat`.

    ![Poprawka Wwise skryptu](media/patch-wwise-script.png)

* Jeśli masz zainstalowany zestaw SDK programu DirectX, należy przekształcić w komentarz wiersz zawierający DXSDK_DIR w `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![DXSDK komentarz](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Otwórz projekt unreal Engine. 
Poprosi użytkownika o odbudować modułów; Kliknij przycisk Tak.

Jeśli otwarcie projektu zakończy się niepowodzeniem na błędy kompilacji, sprawdź zainstalowaną wtyczkę Wwise Akustyka projektu do tej samej wersji Wwise używane w przykładowym projekcie Akustyka projektu.

## <a name="experiment-with-project-acoustics-design-controls"></a>Eksperymentowanie z kontrolkami projektowania Akustyka projektu
Posłuchaj, jak brzmi sceny, klikając przycisk odtwarzania w edytorze unreal Engine. Na komputerze stacjonarnym, należy użyć W, A, S, D i myszy, aby poruszać się. Aby wyświetlić skróty klawiaturowe zapewniające większą liczbę kontrolek, naciśnij klawisz **F1**. Poniżej przedstawiono niektóre działania projektu do wypróbowania:

### <a name="modify-occlusion-and-transmission"></a>Modyfikowanie zamknięcia i transmisji
Istnieją-source Akustyka projektu projektowania kontrolek w każdego Unreal aktora dźwięku:

![DemoSceneSoundSourceDesignControls](media/demo-scene-sound-source-design-controls.png)

Jeśli **zamknięcia** mnożnik jest większa niż 1 (wartość domyślna to 1), zamknięcia będzie exaggerated. Ustawienie sprawia, że mniej niż 1 zamknięcia efektu bardziej subtelny.

Aby włączyć przekazywanie za pośrednictwem tablicy, Przenieś **transmisji (baza danych)** suwaka off najniższy poziom. 

### <a name="modify-wetness-for-a-source"></a>Modyfikowanie wetness źródła
Aby zmienić, jak szybko wetness zmieniają się w odległości, użyj **Percepcyjna Warp odległość**. Akustyka projektu oblicza mokrą poziomów w całej przestrzeni z symulacji, które różnią się płynnie z odległości i podaj odległość Percepcyjna podpowiedzi. Zwiększenie warp odległość exaggerates ten efekt, zwiększając powiązane odległość mokrą poziomów. Zniekształcania wartości poniżej 1 należy na podstawie odległości reverberation, zmień bardziej subtelny. Ten efekt można również dostosować szczegółowo bardziej szczegółowej, dostosowując **Wetness (baza danych)**.

Wydłużenie czasu zanikający w całej przestrzeni przez dostosowanie **Skala czasu zanikania**. Należy wziąć pod uwagę w przypadku, gdy wynik symulacji jest czas zanikania 1,5 s. Ustawienie **Skala czasu zanikania** 2 spowoduje w czasie zanikania stosowane do źródła 3 s.

### <a name="modify-distance-based-attenuation"></a>Modyfikowanie tłumienie na podstawie odległości
Wtyczka mixer Wwise Akustyka projektu szanuje wbudowane Wwise na podstawie odległości tłumienie-source. Krzywej zmiana będzie poziom próbnego ścieżki. Wtyczka Akustyka projektu zostanie dostosowana mokrą poziomu do obsługi różnych mokro próbnego, określony przez formanty symulacji i projektowania.

![DemoSoundsAttenuation](media/demo-sounds-attenuation.png)

Projekt Akustyka wykonuje obliczeń w polu "symulacji region" skupia się wokół każdej lokalizacji symulowane odtwarzacza. Zasoby Akustyka w pakiecie przykładowych zostały wbudowanymi z protokołem radius region symulacji 45 m i wygaszenia zostały zaprojektowane do należą do 0 przed 45 m. Podczas tego zaniku nie ma rygorystyczne wymagania, niesie ze sobą ale należy pamiętać, że tylko geometrii w ciągu 45 m odbiornika zostanie occlude dźwięki.

## <a name="next-steps"></a>Kolejne kroki
* [Integrowanie Akustyka projektu](unreal-integration.md) wtyczki do projektu Unreal
* [Utwórz konto platformy Azure](create-azure-account.md) dla własnych przypadków tworzenia


