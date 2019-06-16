---
title: Konteneryzowanie istniejącej aplikacji .NET dla usługi Service Fabric Mesh | Microsoft Docs
description: Dodawanie obsługi usługi Mesh do istniejącej aplikacji .NET
services: service-fabric-mesh
keywords: konteneryzowanie usługi Service Fabric Mesh
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: cb4e327e1c8c0a653cb94233f568b4847494c439
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419452"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Konteneryzowanie istniejącej aplikacji .NET dla usługi Service Fabric Mesh

W tym artykule przedstawiono sposób dodawania obsługi orkiestracji kontenerów usługi Service Fabric Mesh do istniejącej aplikacji .NET.

W programie Visual Studio 2017 obsługę konteneryzacji można dodać do projektów platformy ASP.NET i konsoli korzystających z pełnej platformy .NET.

> [!NOTE]
> Projekty platformy .NET **Core** nie są obecnie obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Upewnij się, że masz [skonfigurowane środowisko projektowe](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Obejmuje to zainstalowanie środowiska uruchomieniowego usługi Service Fabric, zestawu SDK, platformy Docker i programu Visual Studio 2017 oraz utworzenie lokalnego klastra.

## <a name="open-an-existing-net-app"></a>Otwieranie istniejącej aplikacji platformy .NET

Otwórz aplikację, do której chcesz dodać obsługę orkiestracji kontenerów.

Jeśli chcesz wypróbować przykład, użyj przykładowego kodu [eShop](https://github.com/MikkelHegn/ContainersSFLab). W pozostałej części tego artykułu przyjęto, że jest używany ten projekt, lecz kroki można także zastosować do własnego projektu.

Pobierz kopię projektu **eShop**:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Po jej pobraniu otwórz w programie Visual Studio 2017 plik **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Dodawanie obsługi kontenerów
 
Dodaj obsługę orkiestracji kontenerów do istniejącego projektu platformy ASP.NET lub konsoli przy użyciu narzędzi usługi Service Fabric Mesh w następujący sposób:

W eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy nazwę projektu (w tym przykładzie **eShopLegacyWebForms**), a następnie wybierz pozycję **Dodaj** > **Obsługa orkiestratora kontenerów**.
Zostanie wyświetlone okno dialogowe **Dodawanie obsługi orkiestratora kontenerów**.

![Okno dialogowe dodawania orkiestratora kontenerów w programie Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Wybierz pozycję **Service Fabric Mesh** z listy rozwijanej, a następnie kliknij pozycję **OK**.

Wtedy narzędzie sprawdzi, czy platforma Docker jest zainstalowana, doda plik Dockerfile do projektu i ściągnie obraz platformy Docker dla projektu.  
Projekt aplikacji usługi Service Fabric Mesh został dodany do rozwiązania. Zawiera on profile publikowania usługi Mesh i pliki konfiguracji. Nazwa projektu jest taka sama jak nazwa Twojego projektu z dołączonym słowem „Application” na końcu, na przykład **eShopLegacyWebFormsApplication**. 

W nowym projekcie usługi Mesh zobaczysz dwa foldery, o których warto wiedzieć:
- **App Resources** zawierający pliki YAML, które opisują dodatkowe zasoby usługi Mesh, takie jak sieć.
- **Service Resources** zawierający plik service.yaml, który opisuje, jak należy uruchomić aplikację po wdrożeniu.

Po dodaniu obsługi orkiestracji kontenerów do aplikacji możesz nacisnąć klawisz **F5**, aby debugować aplikację .NET w lokalnym klastrze usługi Service Fabric Mesh. Tutaj przedstawiono aplikację platformy ASP.NET eShop uruchomioną w klastrze usługi Service Fabric Mesh: 

![Aplikacja eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Teraz możesz opublikować aplikację w usłudze Azure Service Fabric Mesh.

## <a name="next-steps"></a>Kolejne kroki

Zobacz, jak opublikować aplikację w usłudze Service Fabric Mesh: [Samouczek — Wdrażanie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)