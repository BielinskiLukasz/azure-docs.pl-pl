---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: 9c20726ce412e651aba0fdd202031d342350c922
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971488"
---
Wybierz pozycję **kompilacja**. W otwartym okienku wybierz folder, do którego chcesz wyeksportować projekt Xcode.

   Po zakończeniu eksportowania zostanie wyświetlony folder zawierający wyeksportowany projekt Xcode.

   > [!NOTE]
   > Jeśli zostanie wyświetlone okno z komunikatem z pytaniem, czy chcesz zamienić lub dołączyć, zalecamy wybranie opcji **Dołącz**, ponieważ jest ona szybsza. Należy zaznaczyć opcję **Zamień** tylko wtedy, gdy zmieniasz elementy zawartości w scenie. Można na przykład dodać, usunąć lub zmienić relacje nadrzędny/podrzędny lub dodać, usunąć lub zmienić właściwości. Jeśli wprowadzasz tylko zmiany w kodzie źródłowym, **dołączenie** powinno być wystarczające.

## <a name="open-the-xcode-project"></a>Otwieranie projektu Xcode

Teraz możesz otworzyć `Unity-iPhone.xcodeproj` projekt w Xcode. 

Można uruchomić Xcode i otworzyć wyeksportowany `Unity-iPhone.xcodeproj` projekt lub uruchomić projekt w programie Xcode, uruchamiając następujące polecenie w lokalizacji, w której został wyeksportowany projekt:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Wybierz węzeł główny **Unity-iPhone** , aby wyświetlić ustawienia projektu, a następnie wybierz kartę **Ogólne** .

W obszarze **podpisywanie**upewnij się, że funkcja **automatycznego zarządzania podpisywaniem** jest włączona. Jeśli tak nie jest, włącz ją, a następnie Zresetuj ustawienia kompilacji, wybierając opcję **Włącz automatyczne** w wyświetlonym okienku.

W obszarze **Informacje o wdrożeniu**upewnij się, że **element docelowy wdrożenia** ma wartość **11,0**.

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzeniu z systemem iOS.

   ![Zrzut ekranu przedstawiający przycisk My iPhone służący do wybierania urządzenia.](./media/spatial-anchors-unity/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

   ![Zrzut ekranu przedstawiający przycisk strzałki "wdróż i uruchom".](./media/spatial-anchors-unity/deploy-run.png)
