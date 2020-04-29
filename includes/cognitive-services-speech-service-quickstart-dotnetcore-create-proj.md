---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66482445"
---
1. Uruchom program Visual Studio 2019.

1. Upewnij się, że jest dostępny pakiet roboczy do **tworzenia aplikacji na wiele platform dla platformy .NET**. Wybierz pozycję **Narzędzia** > **Pobierz narzędzia i funkcje** z paska menu programu Visual Studio, aby otworzyć Instalatora programu Visual Studio. Jeśli ten pakiet roboczy jest już włączony, zamknij okno dialogowe.

   ![Zrzut ekranu Instalatora programu Visual Studio z wyróżnioną kartą Pakiety robocze](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   W przeciwnym razie zaznacz pole obok pozycji **Tworzenie aplikacji na wiele platform dla platformy .NET Core** i wybierz opcję **Modyfikuj** w prawym dolnym rogu okna dialogowego. Instalowanie nowej funkcji chwilę potrwa.

1. Utwórz nową aplikację konsolową w języku Visual C# dla platformy .NET Core. W oknie dialogowym **Nowy projekt** w lewym okienku rozwiń pozycje **Zainstalowane** > **Visual C#** > **.NET Core**. Następnie wybierz pozycję **Aplikacja konsolowa (.NET Core)**. Jako nazwę projektu podaj *helloworld*.

   ![Zrzut ekranu przedstawiający okno dialogowe nowego projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Tworzenie aplikacji konsolowej Visual C# (.NET Core)")

1. Instalowanie i przywoływanie [zestawu Speech SDK narzędzia NuGet](https://aka.ms/csspeech/nuget). W Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

   ![Zrzut ekranu przedstawiający Eksplorator rozwiązań z opcją Zarządzaj pakietami NuGet dla rozwiązania wyróżnionego](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Zarządzanie pakietami NuGet dla rozwiązania")

1. W prawym górnym rogu w polu **Źródło pakietu** wybierz pozycję **NuGet.org**. Wyszukaj `Microsoft.CognitiveServices.Speech` pakiet i zainstaluj go w projekcie **HelloWorld** .

   ![Zrzut ekranu okna dialogowego Zarządzaj pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Zainstaluj pakiet NuGet")

1. Zaakceptuj wyświetloną licencję, aby rozpocząć instalowanie pakietu NuGet.

   ![Zrzut ekranu okna dialogowego Akceptacja licencji](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Zaakceptuj licencję")

Po zainstalowaniu pakietu zostanie wyświetlone potwierdzenie w konsoli Menedżera pakietów.
