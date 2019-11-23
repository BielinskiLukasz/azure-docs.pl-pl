---
title: 'Create a Kubernetes dev space: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: bd59046af58f63c974d5c8d6a97a372f63b848ae
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327321"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-and-net-core-with-azure-dev-spaces"></a>Create a Kubernetes dev space: Visual Studio and .NET Core with Azure Dev Spaces

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu Visual Studio.
- Niezależne tworzenie dwóch oddzielnych usług i wywoływanie innej usługi przy użyciu funkcji odnajdywania usług DNS w środowisku Kubernetes.
- Efektywne tworzenie i testowanie kodu w środowisku zespołu.

> [!Note]
> **If you get stuck** at any time, see the [Troubleshooting](troubleshooting.md) section.


## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Tworzenie klastra Kubernetes obsługującego usługę Azure Dev Spaces

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Wybierz pozycję **Utwórz zasób** > wyszukaj pozycję **Kubernetes** > wybierz pozycję **Kubernetes Service** > **Utwórz**.

   Complete the following steps under each heading of the *Create Kubernetes cluster* form and verify your selected [region supports Azure Dev Spaces][supported-regions].

   - **PROJECT DETAILS**: select an Azure subscription and a new or existing Azure resource group.
   - **SZCZEGÓŁY KLASTRA**: wprowadź nazwę, region, wersję i prefiks nazwy DNS dla klastra AKS.
   - **SKALA**: wybierz rozmiar maszyny wirtualnej dla węzłów agenta AKS i liczbę węzłów. Jeśli rozpoczynasz pracę z usługą Azure Dev Spaces, jeden węzeł jest wystarczający, aby zapoznać się z wszystkimi funkcjami. Liczbę węzłów można łatwo dostosować w dowolnym momencie po wdrożeniu klastra. Pamiętaj, że rozmiaru maszyny wirtualnej nie można zmienić po utworzeniu klastra usługi AKS. Jednak w razie potrzeby skalowania w górę po wdrożeniu klastra usługi AKS możesz łatwo utworzyć nowy klaster usługi AKS z większymi maszynami wirtualnymi i przeprowadzić ponowne wdrożenie na tym większym klastrze za pomocą usługi Dev Spaces.

   ![Ustawienia konfiguracji platformy Kubernetes](media/common/Kubernetes-Create-Cluster-2.PNG)


   Wybierz pozycję **Dalej: uwierzytelnianie** po zakończeniu.

1. Wybierz żądane ustawienie kontroli dostępu na podstawie ról (RBAC, role-based access control). Usługa Azure Dev Spaces obsługuje klastry z włączoną lub wyłączoną kontrolą RBAC.

    ![Ustawienie kontroli RBAC](media/common/k8s-RBAC.PNG)

1. Wybierz pozycje **Przegląd + utwórz**, a następnie po zakończeniu wybierz pozycję **Utwórz**.

## <a name="get-the-visual-studio-tools"></a>Pobieranie narzędzi Visual Studio
Install the latest version of [Visual Studio](https://www.visualstudio.com/vs/). For Visual Studio 2019 on Windows you need to install the Azure Development workload. For Visual Studio 2017 on Windows you need to install the ASP.NET and web development workload as well as [Visual Studio Tools for Kubernetes](https://aka.ms/get-azds-visualstudio).

## <a name="create-a-web-app-running-in-a-container"></a>Tworzenie aplikacji internetowej uruchomionej w kontenerze

In this section, you'll create an ASP.NET Core web app and get it running in a container in Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji internetowej ASP.NET

From within Visual Studio, create a new project. Obecnie projekt musi być **aplikacją internetową ASP.NET Core**. Nazwij projekt „**webfrontend**”.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Wybierz szablon **Aplikacja internetowa (Model-View-Controller)** i upewnij się, że na dwóch listach rozwijanych w górnej części okna dialogowego zostały wybrane pozycje **.NET Core** i **ASP.NET Core 2.0**. Kliknij przycisk **OK**, aby utworzyć projekt.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Włączanie usługi Dev Spaces dla klastra usługi AKS

Po otwarciu utworzonego przed chwilą projektu wybierz usługę **Azure Dev Spaces** z listy rozwijanej ustawień uruchamiania, jak pokazano poniżej.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

W wyświetlonym następnie oknie dialogowym upewnij się, że logujesz się przy użyciu odpowiedniego konta, a następnie wybierz istniejący klaster Kubernetes.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Na liście rozwijanej **Miejsce** zostaw na razie ustawienie domyślne `default`. Później dowiesz się więcej na temat tej opcji. Zaznacz pole wyboru **Publicznie dostępne**, aby aplikacja internetowa była dostępna za pośrednictwem publicznego punktu końcowego. To ustawienie nie jest wymagane, ale przyda się do zademonstrowania pewnych pojęć w dalszej części tego przewodnika. Nie przejmuj się — w każdym przypadku będzie można debugować witrynę internetową przy użyciu programu Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Kliknij przycisk **OK**, aby wybrać lub utworzyć klaster.

Jeśli wybierzesz klaster, który nie został aktywowany do pracy z usługą Azure Dev Spaces, zobaczysz komunikat z pytaniem, czy chcesz go skonfigurować.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Wybierz pozycję **OK**.

> [!IMPORTANT]
> Proces konfiguracji usługi Azure Dev Spaces spowoduje usunięcie przestrzeni nazw `azds` w klastrze, jeśli taka istnieje.

 W celu wykonania tej czynności zostanie uruchomione zadanie w tle. Ukończenie procedury zajmie kilka minut. Aby zobaczyć, czy zadanie jest w toku, umieść kursor myszy na ikonie **Zadania w tle** w lewym dolnym rogu paska stanu, jak pokazano na poniższej ilustracji.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Dopóki obszar deweloperski nie zostanie pomyślnie utworzony, nie można debugować aplikacji.

### <a name="look-at-the-files-added-to-project"></a>Przyglądanie się plikom dodanym do projektu
Czekając na utworzenie obszaru deweloperskiego, przyjrzyj się plikom, które zostały dodane do projektu po wybraniu opcji korzystania z obszaru deweloperskiego.

Najpierw zobaczysz, że został dodany folder o nazwie `charts`, a w tym folderze został utworzony szkielet [planu narzędzia Helm](https://docs.helm.sh) dla Twojej aplikacji. Te pliki służą do wdrażania aplikacji w obszarze deweloperskim.

Zobaczysz, że został dodany plik o nazwie `Dockerfile`. Ten plik zawiera informacje potrzebne do spakowania aplikacji w standardowym formacie Docker.

Ponadto zobaczysz plik o nazwie `azds.yaml`, zawierający konfigurację w czasie programowania, która jest wymagana przez obszar deweloperski.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po pomyślnym utworzeniu obszaru deweloperskiego możesz debugować aplikację. Ustaw punkt przerwania w kodzie, na przykład w wierszu 20 pliku `HomeController.cs`, w którym jest ustawiona zmienna `Message`. Naciśnij klawisz **F5**, aby rozpocząć debugowanie. 

Program Visual Studio będzie komunikować się z obszarem deweloperskim w celu skompilowania i wdrożenia aplikacji, a następnie otworzy przeglądarkę z uruchomioną aplikacją internetową. Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w obszarze deweloperskim na platformie Azure. Przyczyną utworzenia adresu hosta lokalnego jest utworzenie przez usługę Azure Dev Spaces tymczasowego tunelu SSH w kontenerze uruchomionym w usłudze AKS.

Kliknij link **Informacje** w górnej części strony, aby wyzwolić punkt przerwania. Masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne, informacje o wyjątkach itd., zupełnie jakby kod był wykonywany lokalnie.

## <a name="iteratively-develop-code"></a>Iteracyjne programowanie kodu

Usługa Azure Dev Spaces umożliwia nie tylko uruchamianie kodu w środowisku Kubernetes — pozwala też szybko i wielokrotnie wyświetlać efekt zmian wprowadzonych w kodzie w środowisku Kubernetes w chmurze.

### <a name="update-a-content-file"></a>Aktualizowanie pliku zawartości


1. Znajdź plik `./Views/Home/Index.cshtml` i zmień kod HTML. For example, change [line 73 that reads `<h2>Application uses</h2>`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) to something like: 
  
    ```html
    <h2>Hello k8s in Azure!</h2>`
    ```

2. Zapisz plik.
3. Przejdź do przeglądarki i odśwież stronę. Na stronie powinien być widoczny zaktualizowany kod HTML.

Co się stało? Modyfikacje plików zawartości, takich jak HTML i CSS, nie wymagają ponownej kompilacji w aplikacji internetowej .NET Core. Z tego względu aktywna sesja F5 automatycznie synchronizuje wszystkie zmodyfikowane pliki zawartości z uruchomionym kontenerem w usłudze AKS, co umożliwia błyskawiczny podgląd zmian zawartości.

### <a name="update-a-code-file"></a>Aktualizowanie pliku kodu
Aktualizacja plików kodu wymaga nieco więcej pracy, ponieważ trzeba ponownie skompilować aplikację .NET Core i utworzyć zaktualizowane pliki binarne aplikacji.

1. Zatrzymaj debuger w programie Visual Studio.
1. Otwórz plik kodu o nazwie `Controllers/HomeController.cs` i zmień komunikat wyświetlany na stronie Informacje: `ViewData["Message"] = "Your application description page.";`
1. Zapisz plik.
1. Naciśnij klawisz **F5**, aby ponownie uruchomić debugowanie. 

Zamiast ponownego kompilowania i wdrażania nowego obrazu kontenera przy każdej zmianie kodu, co często zajmuje dużo czasu, usługa Azure Dev Spaces przyrostowo ponownie kompiluje kod w istniejącym kontenerze, co przyspiesza działanie pętli edytowania/debugowania.

Odśwież aplikację internetową w przeglądarce, a następnie otwórz stronę Informacje. Twój komunikat powinien zostać wyświetlony w interfejsie użytkownika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o opracowywaniu dla wielu usług](multi-service-netcore-visualstudio.md)


[supported-regions]: about.md#supported-regions-and-configurations