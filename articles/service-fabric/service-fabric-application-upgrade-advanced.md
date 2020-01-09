---
title: Zaawansowane tematy dotyczące uaktualniania aplikacji
description: W tym artykule omówiono niektóre zaawansowane tematy dotyczące uaktualniania aplikacji Service Fabric.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: bd95d651e02cb61bcbe7a108db92afce8b5484bd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457529"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uaktualnianie aplikacji Service Fabric: Tematy zaawansowane
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Dodawanie lub usuwanie typów usług podczas uaktualniania aplikacji
Jeśli nowy typ usługi zostanie dodany do opublikowanej aplikacji w ramach uaktualnienia, nowy typ usługi zostanie dodany do wdrożonej aplikacji. Takie uaktualnienie nie ma wpływu na żadne wystąpienia usługi, które już należały do aplikacji, ale wystąpienie typu usługi, który został dodany, musi zostać utworzone dla nowego typu usługi, który ma być aktywny (zobacz [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Podobnie typy usług można usunąć z aplikacji w ramach uaktualnienia. Jednak przed kontynuowaniem uaktualniania należy usunąć wszystkie wystąpienia usługi typu, które mają zostać usunięte (zobacz [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Tryb uaktualniania ręcznego
> [!NOTE]
> W przypadku wszystkich uaktualnień Service Fabric zalecany jest *monitorowany* tryb uaktualniania.
> Tryb uaktualniania *UnmonitoredManual* powinien być uwzględniany tylko w przypadku uaktualnień zakończonych niepowodzeniem lub wstrzymane. 
>
>

W trybie *monitorowanym* Service Fabric stosuje zasady kondycji, aby upewnić się, że aplikacja jest w dobrej kondycji w miarę postępu uaktualniania. Jeśli zasady kondycji zostały naruszone, uaktualnienie jest zawieszone lub automatycznie wycofywane w zależności od określonej *FailureAction*.

W trybie *UnmonitoredManual* administrator aplikacji ma całkowitą kontrolę nad postępem uaktualniania. Ten tryb jest przydatny w przypadku stosowania niestandardowych zasad oceny kondycji lub przeprowadzania niekonwencjonalnych uaktualnień w celu obejścia całkowitego monitorowania kondycji (np. aplikacja jest już w utracie danych). Uaktualnienie uruchomione w tym trybie zostanie zawieszone po ukończeniu każdego UDu i musi zostać jawnie wznowione przy użyciu polecenia [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Gdy uaktualnienie zostanie wstrzymane i będzie gotowe do wznowienia przez użytkownika, jego stan uaktualnienia zostanie wyświetlony *RollforwardPending* (zobacz [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Na koniec tryb *UnmonitoredAuto* jest przydatny do przeprowadzania szybkich iteracji uaktualniania podczas tworzenia lub testowania usług, ponieważ nie są wymagane żadne dane wejściowe użytkownika i nie są oceniane żadne zasady dotyczące kondycji aplikacji.

## <a name="upgrade-with-a-diff-package"></a>Uaktualnianie przy użyciu pakietu różnicowego
Zamiast aprowizacji kompletnego pakietu aplikacji, uaktualnienia mogą być również wykonywane przez inicjowanie obsługi pakietów różnicowych, które zawierają tylko zaktualizowany kod/konfiguracja/pakiety danych wraz z kompletnym manifestem aplikacji i kompletnymi manifestami usługi. Kompletne pakiety aplikacji są wymagane tylko w przypadku początkowej instalacji aplikacji w klastrze. Kolejne uaktualnienia mogą pochodzić z pełnych pakietów aplikacji lub pakietów różnicowych.  

Wszelkie odwołania do manifestu aplikacji lub manifestów usługi pakietu różnicowego, których nie można znaleźć w pakiecie aplikacji, są automatycznie zastępowane aktualnie zainicjowaną wersją.

Scenariusze korzystania z pakietu różnicowego są następujące:

* Jeśli masz duży pakiet aplikacji, który odwołuje się do kilku plików manifestu usługi i/lub kilku pakietów kodu, pakietów konfiguracyjnych lub pakietów danych.
* W przypadku systemu wdrażania, który generuje układ kompilacji bezpośrednio z procesu kompilacji aplikacji. W tym przypadku, chociaż kod nie został zmieniony, nowo skompilowane zestawy uzyskują inną sumę kontrolną. Korzystanie z pełnego pakietu aplikacji wymaga zaktualizowania wersji we wszystkich pakietach kodu. Przy użyciu pakietu różnicowego można podać tylko zmienione pliki i pliki manifestu, w których wersja została zmieniona.

Gdy aplikacja zostanie uaktualniona przy użyciu programu Visual Studio, pakiet diff jest publikowany automatycznie. Aby ręcznie utworzyć pakiet diff, należy zaktualizować manifest aplikacji i manifesty usługi, ale tylko zmienione pakiety powinny być zawarte w końcowym pakiecie aplikacji.

Na przykład Zacznijmy od następującej aplikacji (numery wersji podane w celu łatwiejszego poznania):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Załóżmy, że chcemy zaktualizować tylko pakiet kodu Service1 przy użyciu pakietu diff. Zaktualizowana aplikacja ma następujące zmiany w wersji:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

W takim przypadku należy zaktualizować manifest aplikacji do 2.0.0 i manifestu usługi dla Service1 w celu odzwierciedlenia aktualizacji pakietu kodu. Folder pakietu aplikacji będzie miał następującą strukturę:

```text
app1/
  service1/
    code/
```

Innymi słowy, Utwórz cały pakiet aplikacji, a następnie usuń wszystkie foldery kodu/konfiguracji/pakietu danych, dla których wersja nie została zmieniona.

## <a name="upgrade-application-parameters-independently-of-version"></a>Uaktualnij parametry aplikacji niezależnie od wersji

Czasami należy zmienić parametry aplikacji Service Fabric bez zmiany wersji manifestu. Można to zrobić wygodnie przy użyciu flagi **-ApplicationParameter** z poleceniem cmdlet **Start-ServiceFabricApplicationUpgrade** platformy Azure Service Fabric PowerShell. Załóżmy, że aplikacja Service Fabric o następujących właściwościach:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Teraz Uaktualnij aplikację za pomocą polecenia cmdlet **Start-ServiceFabricApplicationUpgrade** . W tym przykładzie przedstawiono monitorowane uaktualnienie, ale można również użyć niemonitorowanego uaktualnienia. Aby wyświetlić pełen opis flag zaakceptowanych przez to polecenie cmdlet, zobacz [informacje dotyczące modułu Azure Service Fabric PowerShell](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Po uaktualnieniu upewnij się, że aplikacja ma zaktualizowane parametry i tę samą wersję:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="rolling-back-application-upgrades"></a>Wycofywanie uaktualnień aplikacji

Podczas gdy uaktualnienia można przekazywać w jednym z trzech trybów (*monitorowane*, *UnmonitoredAuto*lub *UnmonitoredManual*), można je wycofać tylko w trybie *UnmonitoredAuto* lub *UnmonitoredManual* . Wycofywanie w trybie *UnmonitoredAuto* działa tak samo jak w przypadku przenoszonej do przodu z wyjątkiem, że wartość domyślna *UpgradeReplicaSetCheckTimeout* jest różna — zobacz [Parametry uaktualnienia aplikacji](service-fabric-application-upgrade-parameters.md). Wycofanie w trybie *UnmonitoredManual* działa tak samo jak w przypadku przenoszonego do przodu — wycofanie zostanie zawieszone po ukończeniu każdego ud i musi zostać jawnie wznowione przy użyciu [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) , aby kontynuować wycofywanie.

Wycofywanie mogą być wyzwalane automatycznie, gdy zasady kondycji uaktualnienia w *monitorowanym* trybie z *FailureAction* *wycofania* są naruszone (zobacz [Parametry uaktualnienia aplikacji](service-fabric-application-upgrade-parameters.md)) lub jawnie za pomocą polecenia [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

W trakcie wycofywania wartość *UpgradeReplicaSetCheckTimeout* i tryb można nadal zmieniać w dowolnym momencie za pomocą polecenia [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Aby uaktualnić aplikacje, należy się upewnić, jak używać [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Rozwiązywanie typowych problemów dotyczących uaktualnień aplikacji, w odniesieniu do kroków w [temacie Troubleshooting Upgrades Applications](service-fabric-application-upgrade-troubleshooting.md).
