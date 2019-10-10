---
title: Samouczek dotyczący uaktualniania aplikacji Service Fabric | Microsoft Docs
description: W tym artykule omówiono środowisko wdrażania aplikacji Service Fabric, zmiany kodu i wdrażania uaktualnienia przy użyciu programu Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 5e693a219c4a430f742ebd27878518ebb99ce5da
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167373"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Samouczek uaktualniania aplikacji Service Fabric przy użyciu programu Visual Studio
> [!div class="op_single_selector"]
> * [Program PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Usługa Azure Service Fabric upraszcza proces uaktualniania aplikacji w chmurze, zapewniając uaktualnienie tylko zmienionych usług, a kondycja aplikacji jest monitorowana przez cały proces uaktualniania. Powoduje również automatyczne wycofanie aplikacji do poprzedniej wersji w przypadku wystąpienia problemów. Service Fabric uaktualnienia aplikacji mają *zero przestojów*, ponieważ aplikacja może zostać uaktualniona bez przestojów. W tym samouczku opisano, jak przeprowadzić uaktualnienie stopniowe z programu Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Krok 1. Kompilowanie i publikowanie przykładowych obiektów wizualnych
Najpierw pobierz aplikację [obiekty wizualne](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) z usługi GitHub. Następnie Skompiluj i Opublikuj aplikację, klikając prawym przyciskiem myszy projekt aplikacji, **VisualObjects**i wybierając polecenie **Publikuj** w elemencie menu Service Fabric.

![Menu kontekstowe dla aplikacji Service Fabric][image1]

Wybranie pozycji **Publikuj** powoduje wyświetlenie okna podręcznego i można ustawić **profil docelowy** na **PublishProfiles\Local.XML**. Okno powinno wyglądać podobnie do poniższego przed kliknięciem przycisku **Publikuj**.

![Publikowanie aplikacji Service Fabric][image2]

Teraz możesz kliknąć przycisk **Publikuj** w oknie dialogowym. [Aby wyświetlić klaster i aplikację](service-fabric-visualizing-your-cluster.md), można użyć Service Fabric Explorer. Aplikacja obiektów wizualnych ma usługę sieci Web, do której można przejść, wpisując [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) na pasku adresu przeglądarki.  Powinny być widoczne 10 obiektów wizualizacji przenoszonych wokół ekranu.

**Uwaga:** W przypadku wdrażania w profilu `Cloud.xml` (Azure Service Fabric) aplikacja powinna następnie być dostępna przy użyciu **protokołu http://{Servicefabricname}. { Region}. cloudapp. Azure. com: 8081/visualobjects/** . Upewnij się, że w Load Balancer skonfigurowano `8081/TCP` (Znajdź Load Balancer w tej samej grupie zasobów co wystąpienie Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2. Aktualizacja przykładu obiektów wizualnych
Można zauważyć, że w wersji, która została wdrożona w kroku 1, obiekty wizualne nie są obracane. Uaktualnimy tę aplikację do jednego miejsca, w którym obiekty wizualne również są obracane.

Wybierz projekt VisualObjects. ActorService w ramach rozwiązania VisualObjects i Otwórz plik **VisualObjectActor.cs** . W tym pliku przejdź do metody `MoveObject`, Dodaj komentarz do `visualObject.Move(false)` i Usuń komentarz `visualObject.Move(true)`. Ta zmiana kodu powoduje obrócenie obiektów po uaktualnieniu usługi.  **Teraz można skompilować (nie ponownie skompilować) rozwiązanie**, które kompiluje zmodyfikowane projekty. W przypadku wybrania opcji *Kompiluj ponownie wszystkie*należy zaktualizować wersje dla wszystkich projektów.

Potrzebujemy również wersji naszej aplikacji. Aby zmienić wersję po kliknięciu prawym przyciskiem myszy projektu **VisualObjects** , można użyć opcji **Edytuj wersje manifestu** programu Visual Studio. Wybranie tej opcji powoduje wyświetlenie okna dialogowego z wersjami w następujący sposób:

![Okno dialogowe obsługa wersji][image3]

Zaktualizuj wersje zmodyfikowanych projektów i ich pakietów kodu wraz z aplikacją do wersji 2.0.0. Po wprowadzeniu zmian manifest powinien wyglądać podobnie do następującego (pogrubione fragmenty pokazują zmiany):

![Aktualizowanie wersji][image4]

Narzędzia Visual Studio Tools mogą wykonywać automatyczne pakiety zbiorcze wersji, wybierając opcję **automatycznie Aktualizuj wersje aplikacji i usług**. Jeśli używasz [SemVer](http://www.semver.org), musisz zaktualizować kod i/lub wersję pakietu konfiguracyjnego, jeśli ta opcja jest zaznaczona.

Zapisz zmiany i sprawdź, czy jest to pole **Uaktualnij aplikację** .

## <a name="step-3--upgrade-your-application"></a>Krok 3. Uaktualnianie aplikacji
Zapoznaj się z [parametrami uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) i [procesem uaktualniania](service-fabric-application-upgrade.md) , aby poznać różne parametry uaktualniania, limity czasu i kryterium kondycji, które mogą być stosowane. W tym instruktażu kryterium oceny kondycji usługi jest ustawione na wartość domyślną (niemonitorowany tryb). Te ustawienia można skonfigurować, wybierając pozycję **Konfiguruj ustawienia uaktualnienia** , a następnie modyfikując parametry zgodnie z potrzebami.

Teraz wszystko jest ustawione, aby rozpocząć uaktualnianie aplikacji, wybierając pozycję **Publikuj**. Ta opcja uaktualnia aplikację do wersji 2.0.0, w której obiekty są obracane. Service Fabric uaktualnia jedną domenę aktualizacji w danym momencie (niektóre obiekty są aktualizowane jako pierwsze, a następnie inne), a usługa pozostanie dostępna podczas uaktualniania. Dostęp do usługi można sprawdzić za pomocą klienta (przeglądarki).  

Teraz, gdy uaktualnienie aplikacji będzie kontynuowane, można je monitorować za pomocą Service Fabric Explorer przy użyciu karty **uaktualnienia w toku** w aplikacjach.

W ciągu kilku minut wszystkie domeny aktualizacji powinny zostać uaktualnione (ukończono), a okno danych wyjściowych programu Visual Studio powinno również spowodować ukończenie uaktualnienia. I należy się dowiedzieć, że *wszystkie* obiekty wizualizacji w oknie przeglądarki są teraz obracane!

Możesz spróbować zmienić wersje i przenieść z wersji 2.0.0 do wersji 3.0.0 jako ćwiczenie, a nawet z wersji 2.0.0 z powrotem do wersji 1.0.0. Twórz aplikacje, korzystając z limitów czasu i zasad dotyczących kondycji, aby je znać. W przypadku wdrażania w klastrze platformy Azure, w przeciwieństwie do klastra lokalnego, używane parametry mogą być różne. Zalecane jest, aby ustawić limity czasu.

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Aby uaktualnić aplikacje, należy się upewnić, jak używać [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [zaawansowanych tematów](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów dotyczących uaktualnień aplikacji, w odniesieniu do kroków w [temacie Troubleshooting Upgrades Applications](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
