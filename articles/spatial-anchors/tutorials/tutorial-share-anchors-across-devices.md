---
title: 'Samouczek: udostępnianie kotwic między sesjami i urządzeniami'
description: W tym samouczku dowiesz się, jak udostępnić identyfikatory kotwic Azure w środowisku z systemem Android/iOS w środowisku Unity przy użyciu usługi zaplecza.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0029fcf8e9efdea529212a7cca49cc8660c623f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276963"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Samouczek: udostępnianie kotwic przestrzennych platformy Azure między sesjami i urządzeniami

W ramach tego samouczka nauczysz się używać [kotwic przestrzennych platformy Azure](../overview.md) do tworzenia kotwic podczas jednej sesji, a następnie znajdowania ich na tym samym urządzeniu lub na innym. Te same kotwice mogą również znajdować się na wielu urządzeniach w tym samym miejscu i w tym samym czasie.

![Trwałość](./media/persistence.gif)

Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację, którą można wdrożyć na dwóch urządzeniach lub więcej. Kotwice usługi Azure Spatial Anchors utworzone przez jedno wystąpienie mogą być udostępniane innym wystąpieniom.

Omawiane tematy:

> [!div class="checklist"]
> * Wdrażanie na platformie Azure aplikacji internetowej platformy ASP.NET Core, która może służyć do udostępniania kotwic i przechowywania ich w pamięci przez pewien czas.
> * Konfigurowanie sceny AzureSpatialAnchorsLocalSharedDemo w ramach przykładu aparatu Unity z naszych przewodników Szybki start, aby skorzystać z aplikacji internetowej do udostępniania kotwic.
> * Wdrażanie i uruchamianie na co najmniej jednym urządzeniu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Warto zauważyć, że chociaż w tym samouczku będzie używany aparat Unity i aplikacja internetowa platformy ASP.NET Core, ma to na celu jedynie zaprezentowanie przykładu udostępniania identyfikatorów kotwic usługi Azure Spatial Anchors innym urządzeniom. Możesz użyć innych języków i technologii zaplecza, aby osiągnąć ten sam cel. Ponadto aplikacja internetowa platformy ASP.NET Core używana w tym samouczku zależy od zestawu .NET Core 2.2 SDK. Działa poprawnie w normalnej usłudze Azure Web Apps (dla systemu Windows), ale obecnie nie będzie działać w usłudze Azure Web Apps dla systemu Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Pobierz przykładowy projekt

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Wdrażanie usługi do udostępniania kotwic

## <a name="visual-studiotabvs"></a>[Program Visual Studio](#tab/VS)

Otwórz program Visual Studio i otwórz projekt w folderze `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-codetabvsc"></a>[Visual Studio Code](#tab/VSC)

Przed wdrożeniem usługi w programie VS Code należy utworzyć grupę zasobów i plan App Service.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Przejdź do <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> i zaloguj się do subskrypcji platformy Azure.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Obok pozycji **Grupa zasobów** wybierz pozycję **Nowa**.

Nadaj grupie zasobów nazwę **myResourceGroup**, a następnie kliknij przycisk **OK**.

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Obok pozycji **Plan hostingu** wybierz pozycję **Nowy**.

W oknie dialogowym **Konfiguruj plan hostingu** Użyj następujących ustawień:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| MySharingServicePlan | Nazwa planu usługi App Service. |
| Lokalizacja | Zachodnie stany USA | Centrum danych, w którym hostowana jest aplikacja internetowa. |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , która określa funkcje hostingu. |

Wybierz **OK**.

Otwórz Visual Studio Code i Otwórz projekt w folderze `Sharing\SharingServiceSample`. Postępuj zgodnie z <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">tym samouczkiem</a> , aby wdrożyć usługę udostępniania za pomocą Visual Studio Code. Wykonaj kroki opisane w sekcji "Otwórz za pomocą Visual Studio Code". Nie należy tworzyć innego projektu MVC, jak wyjaśniono w powyższym kroku, ponieważ istnieje już projekt, który należy wdrożyć i opublikować — SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Wdrażanie przykładowej aplikacji

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku została wdrożona aplikacja internetowa platformy ASP.NET Core na platformie Azure, a następnie została skonfigurowana i wdrożona aplikacja aparatu Unity. W aplikacji zostały utworzone kotwice przestrzenne i udostępniono je innym urządzeniom za pomocą aplikacji internetowej platformy ASP.NET Core.

Możesz ulepszyć ASP.NET Core aplikację sieci Web, tak aby używała Azure Cosmos DB do utrwalania magazynu udostępnionych identyfikatorów zakotwiczenia przestrzennego. Dodanie obsługi Azure Cosmos DB umożliwi aplikacji sieci Web ASP.NET Core utworzenie zakotwiczenia dzisiaj i powracanie później w przyszłości, aby można było je ponownie zlokalizować przy użyciu identyfikatora zakotwiczenia przechowywanego w aplikacji sieci Web.

> [!div class="nextstepaction"]
> [Przechowywanie kotwic przy użyciu usługi Azure Cosmo DB](./tutorial-use-cosmos-db-to-store-anchors.md)

