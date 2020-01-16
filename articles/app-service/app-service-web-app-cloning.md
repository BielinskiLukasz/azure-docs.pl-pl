---
title: Klonowanie aplikacji za pomocą programu PowerShell
description: Dowiedz się, jak sklonować aplikację App Service do nowej aplikacji przy użyciu programu PowerShell. Omówiono różne scenariusze klonowania, w tym Traffic Manager integrację.
author: ahmedelnably
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 332f7a562fc8c1d7b2bbebb0e596a068797c2ce3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75967223"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service klonowania aplikacji przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dzięki wersji Microsoft Azure PowerShell w wersji 1.1.0 dodano nową opcję do `New-AzWebApp`, która umożliwia klonowanie istniejącej aplikacji App Service do nowo utworzonej aplikacji w innym regionie lub w tym samym regionie. Ta opcja umożliwia klientom szybkie i łatwe wdrażanie wielu aplikacji w różnych regionach.

Klonowanie aplikacji jest obsługiwane w planach usługi App Service w warstwach Standardowa, Premium, Premium v2 i izolowanych. Nowa funkcja używa tych samych ograniczeń co App Service funkcji tworzenia kopii zapasowych, zobacz [wykonywanie kopii zapasowej aplikacji w Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klonowanie istniejącej aplikacji
Scenariusz: istniejąca aplikacja w regionie Południowo-środkowe stany USA i chcesz sklonować zawartość do nowej aplikacji w regionie Północno-środkowe stany USA. Można to zrobić za pomocą Azure Resource Manager wersji polecenia cmdlet programu PowerShell, aby utworzyć nową aplikację z opcją `-SourceWebApp`.

Znając nazwę grupy zasobów, która zawiera aplikację źródłową, możesz użyć następującego polecenia programu PowerShell, aby uzyskać informacje o aplikacji źródłowej (w tym przypadku o nazwie `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Aby utworzyć nowy plan App Service, można użyć polecenia `New-AzAppServicePlan`, jak w poniższym przykładzie.

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Za pomocą polecenia `New-AzWebApp` można utworzyć nową aplikację w regionie Północno-środkowe stany USA i powiązać ją z istniejącym planem App Service. Ponadto możesz użyć tej samej grupy zasobów co aplikacja źródłowa lub zdefiniować nową grupę zasobów, jak pokazano w następującym poleceniu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Aby sklonować istniejącą aplikację wraz ze wszystkimi skojarzonymi miejscami wdrożenia, należy użyć parametru `IncludeSourceWebAppSlots`.  Należy pamiętać, że parametr `IncludeSourceWebAppSlots` jest obsługiwany tylko w przypadku klonowania całej aplikacji, w tym wszystkich miejsc. Następujące polecenie programu PowerShell demonstruje użycie tego parametru za pomocą polecenia `New-AzWebApp`:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Aby sklonować istniejącą aplikację w tym samym regionie, należy utworzyć nową grupę zasobów i nowy plan usługi App Service w tym samym regionie, a następnie użyć następującego polecenia programu PowerShell w celu sklonowania aplikacji:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonowanie istniejącej aplikacji do App Service Environment
Scenariusz: istniejąca aplikacja w regionie Południowo-środkowe stany USA i chcesz sklonować zawartość do nowej aplikacji do istniejącej App Service Environment (ASE).

Znając nazwę grupy zasobów, która zawiera aplikację źródłową, możesz użyć następującego polecenia programu PowerShell, aby uzyskać informacje o aplikacji źródłowej (w tym przypadku o nazwie `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Znajomość nazwy środowiska ASE i nazwy grupy zasobów, do której należy środowisko ASE, można utworzyć nową aplikację w istniejącym środowisku ASE, jak pokazano w następującym poleceniu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Parametr `Location` jest wymagany z powodu starszej przyczyny, ale jest ignorowany podczas tworzenia aplikacji w środowisku ASE. 

## <a name="cloning-an-existing-app-slot"></a>Klonowanie istniejącego miejsca aplikacji
Scenariusz: chcesz sklonować istniejące miejsce wdrożenia aplikacji do nowej aplikacji lub nowego gniazda. Nowa aplikacja może znajdować się w tym samym regionie, w którym znajduje się oryginalne miejsce aplikacji lub w innym regionie.

Znajomość nazwy grupy zasobów, która zawiera aplikację źródłową, można użyć następującego polecenia programu PowerShell, aby uzyskać informacje o gnieździe aplikacji źródłowej (w tym przypadku o nazwie `source-appslot`) powiązane z `source-app`:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Następujące polecenie pokazuje, jak utworzyć klon aplikacji źródłowej do nowej aplikacji:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurowanie Traffic Manager podczas klonowania aplikacji
Tworzenie aplikacji obejmujących wiele regionów i Konfigurowanie usługi Azure Traffic Manager w celu kierowania ruchu do wszystkich tych aplikacji, jest ważnym scenariuszem, aby zapewnić wysoką dostępność aplikacji klientów. Podczas klonowania istniejącej aplikacji istnieje możliwość połączenia obu aplikacji z nowym profilem usługi Traffic Manager lub istniejącym. Obsługiwana jest tylko Azure Resource Manager wersja Traffic Manager.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Tworzenie nowego profilu Traffic Manager podczas klonowania aplikacji
Scenariusz: chcesz sklonować aplikację do innego regionu podczas konfigurowania profilu usługi Azure Resource Manager Traffic Manager zawierającego obie aplikacje. Następujące polecenie pokazuje, jak utworzyć klon aplikacji źródłowej do nowej aplikacji podczas konfigurowania nowego profilu Traffic Manager:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Dodawanie nowej sklonowanej aplikacji do istniejącego profilu Traffic Manager
Scenariusz: masz już profil usługi Azure Resource Manager Traffic Manager i chcesz dodać obie aplikacje jako punkty końcowe. Aby to zrobić, należy najpierw utworzyć istniejący identyfikator profilu Menedżera ruchu. Wymagany jest Identyfikator subskrypcji, nazwa grupy zasobów i istniejąca nazwa profilu usługi Traffic Manager.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po zastosowaniu identyfikatora Traffic Manager następujące polecenie pokazuje, jak utworzyć klon aplikacji źródłowej do nowej aplikacji podczas dodawania ich do istniejącego profilu Traffic Manager:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Bieżące ograniczenia
Poniżej przedstawiono znane ograniczenia klonowania aplikacji:

* Ustawienia automatycznego skalowania nie są klonowane
* Ustawienia harmonogramu kopii zapasowych nie są klonowane
* Ustawienia sieci wirtualnej nie zostały sklonowane
* Usługi App Insights nie są automatycznie konfigurowane w aplikacji docelowej
* Proste ustawienia uwierzytelniania nie są klonowane
* Rozszerzenie kudu nie jest sklonowane
* Reguły TiP nie są klonowane
* Zawartość bazy danych nie jest sklonowana
* Wychodzące adresy IP zmieniają się w przypadku klonowania do innej jednostki skalowania
* Niedostępne dla aplikacji systemu Linux

### <a name="references"></a>Informacje
* [Klonowanie App Service](app-service-web-app-cloning.md)
* [Tworzenie kopii zapasowej aplikacji w Azure App Service](manage-backup.md)
* [Obsługa Azure Resource Manager w wersji zapoznawczej platformy Azure Traffic Manager](../traffic-manager/traffic-manager-powershell-arm.md)
* [Wprowadzenie do usługi App Service Environment](environment/intro.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

