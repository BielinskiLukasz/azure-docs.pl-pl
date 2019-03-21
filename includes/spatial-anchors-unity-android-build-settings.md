---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 53f480b8858e2bbe7d4699d8637ecaa5ab0c08a3
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305181"
---
Na platformie Unity, otwórz projekt w `Unity` folderu.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W sekcji **Platform (Platforma)** wybierz pozycję **Android**. Zmiana **System kompilacji** do **Gradle** i wybierz **Eksportowanie projektu**.

Wybierz pozycję **Switch Platform (Przełącz platformę)**, aby zmienić platformę na **Android**. Unity może spowodować wyświetlenie monitu do zainstalowania składników obsługi systemu Android, jeśli są one Brak.

![Okno ustawień kompilacji platformy Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zamknij okno **Build Settings (Ustawienia kompilacji)**.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Pobierz i zaimportuj zestaw ARCore SDK dla aparatu Unity

Pobierz plik `unitypackage` z sekcji [wersji zestawu ARCore SDK dla aparatu Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). W projekcie Unity, wybierz **zasoby** > **Importuj pakiet** > **niestandardowy pakiet** , a następnie wybierz `unitypackage` plik wcześniej pobrane. W **Importowanie pakietu Unity** okno dialogowe, upewnij się, że zaznaczone są wszystkie pliki, a następnie wybierz pozycję **importu**.
