---
title: Konfigurowanie środowiska deweloperskiego w systemie Windows dla mikrousług platformy Azure | Microsoft Docs
description: Zainstaluj środowisko uruchomieniowe, zestaw SDK i narzędzia oraz utwórz lokalny klaster projektowy. Po ukończeniu tej konfiguracji wszystko będzie gotowe do kompilowania aplikacji w systemie Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2018
ms.author: ryanwi
ms.openlocfilehash: 968c9f2455269cf41a701de685e4f47255b46847
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386432"
---
# <a name="prepare-your-development-environment-on-windows"></a>Przygotowywanie środowiska deweloperskiego w systemie Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Aby kompilować i uruchamiać [aplikacje usługi Azure Service Fabric][1] na maszynie deweloperskiej z systemem Windows, należy zainstalować środowisko uruchomieniowe, zestaw SDK oraz narzędzia usługi Service Fabric. Należy również [włączyć wykonywanie skryptów programu Windows PowerShell](#enable-powershell-script-execution) zawartych w zestawie SDK.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="supported-operating-system-versions"></a>Obsługiwane wersje systemu operacyjnego
Na potrzeby tworzenia aplikacji obsługiwane są następujące wersje systemu operacyjnego:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Obsługa systemu Windows 7:
> - System Windows 7 domyślnie zawiera program Windows PowerShell wyłącznie w wersji 2.0. Polecenia cmdlet programu PowerShell usługi Service Fabric wymagają programu PowerShell w wersji 3.0 lub nowszej. Możesz [pobrać program Windows PowerShell 5.0][powershell5-download] w Centrum pobierania Microsoft.
> - Serwer proxy usługi Service Fabric nie jest dostępny w systemie Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Instalowanie zestawu SDK i narzędzi
Instalator platformy sieci Web (WebPI) jest zalecanym sposobem instalowania zestawu SDK i narzędzi. Jeśli otrzymujesz błędy w czasie wykonywania za pomocą Instalatora WebPI, można również znaleźć bezpośrednie linki do instalatory w informacjach o wersji dla określonej wersji usługi Service Fabric. Informacje o wersji można znaleźć w różnych anonse wersji na [blog zespołu usługi Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

### <a name="to-use-visual-studio-2017"></a>Używanie programu Visual Studio 2017
Narzędzia Service Fabric Tools są częścią pakietu roboczego Programowanie na platformie Azure w programie Visual Studio 2017. Włącz to obciążenie w ramach instalacji programu Visual Studio.
Ponadto należy zainstalować zestaw SDK i środowisko uruchomieniowe usługi Microsoft Azure Service Fabric przy użyciu Instalatora platformy sieci Web.

* [Instalowanie zestawu SDK usługi Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Używanie programu Visual Studio 2015 (wymaga programu Visual Studio 2015 Update 2 lub nowszego)
W przypadku programu Visual Studio 2015 narzędzia usługi Service Fabric są instalowane razem z zestawem SDK i środowiskiem uruchomieniowym przy użyciu Instalatora platformy sieci Web:

* [Instalowanie zestawu SDK i narzędzi usługi Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Instalowanie samego zestawu SDK
Jeśli potrzebujesz tylko zestawu SDK, możesz zainstalować ten pakiet:
* [Instalowanie zestawu SDK usługi Microsoft Azure Service Fabric][core-sdk]

Bieżące wersje:
* Service Fabric SDK i narzędzia 3.3.617
* Środowisko uruchomieniowe usługi Service Fabric 6.4.617
* Narzędzia usługi Service Fabric dla programu Visual Studio 2015 2.4.11116.1
* Visual Studio 2017 15.9 obejmuje narzędzia usługi Service Fabric dla programu Visual Studio 2.4.11024.1 

Listę obsługiwanych wersji można znaleźć na stronie [pomocy technicznej usługi Service Fabric](service-fabric-support.md)

> [!NOTE]
> Uaktualnia jednej maszynie klastry (OneBox) nie są obsługiwane dla aplikacji lub klastra; Usuwanie klastra OneBox i utworzyć ją ponownie, jeśli chcesz przeprowadzić uaktualnienie klastra lub jakichkolwiek problemów w przypadku uaktualniania aplikacji. 

## <a name="enable-powershell-script-execution"></a>Włączanie wykonywania skryptów programu PowerShell
Platforma Service Fabric korzysta ze skryptów programu Windows PowerShell do tworzenia lokalnego klastra projektowego i do wdrażania aplikacji z programu Visual Studio. Domyślnie system Windows blokuje uruchamianie tych skryptów. Aby je włączyć, należy zmienić zasady wykonywania w programie PowerShell. Uruchom program PowerShell jako administrator i wprowadź następujące polecenie:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Zainstalowanie platformy Docker (opcjonalnie)
[Usługa Service Fabric to orkiestrator kontenerów](service-fabric-containers-overview.md) do wdrażania mikrousług w klastrze maszyn. Aby uruchomić aplikacje kontenera Windows na lokalnego klastra projektowego, należy najpierw zainstalować Docker for Windows. Pobierz [Docker CE dla Windows (wersja stabilna)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Po zainstalowaniu i uruchomieniu programu Docker kliknij prawym przyciskiem myszy jego ikonę na pasku zadań i wybierz pozycję **Switch to Windows containers** (Przełącz na kontenery systemu Windows). Ten krok jest wymagany do uruchomienia obrazów platformy Docker opartych na systemie Windows.

## <a name="next-steps"></a>Kolejne kroki
Po skonfigurowaniu środowiska projektowego możesz zacząć kompilować i uruchamiać aplikacje.

* [Tworzenie pierwszej aplikacji platformy Service Fabric w programie Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Informacje o wdrażaniu aplikacji i zarządzaniu nimi w klastrze lokalnym](service-fabric-get-started-with-a-local-cluster.md)
* [Więcej informacji o modelach programowania: Usług Reliable Services i Reliable Actors](service-fabric-choose-framework.md)
* [Przykłady kodu platformy Service Fabric w witrynie GitHub](https://aka.ms/servicefabricsamples)
* [Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Strona kampanii usługi Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI — link"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI — link"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI — link"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
