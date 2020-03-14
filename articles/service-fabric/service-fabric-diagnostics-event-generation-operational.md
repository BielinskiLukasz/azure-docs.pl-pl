---
title: Lista zdarzeń Service Fabric platformy Azure
description: Kompleksowa lista zdarzeń zapewnianych przez usługę Azure Service Fabric w celu ułatwienia monitorowania klastrów.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258540"
---
# <a name="list-of-service-fabric-events"></a>Lista zdarzeń Service Fabric 

Service Fabric uwidacznia podstawowy zestaw zdarzeń klastra w celu poinformowania o stanie klastra jako [zdarzenia Service Fabric](service-fabric-diagnostics-events.md). Są one oparte na akcjach wykonywanych przez Service Fabric w węzłach i w klastrze lub decyzje dotyczące zarządzania wykonywane przez właściciela/operatora klastra. Dostęp do tych zdarzeń można uzyskać przez skonfigurowanie na wiele sposobów, w tym Konfigurowanie [dzienników Azure monitor z klastrem](service-fabric-diagnostics-oms-setup.md)lub wykonywanie zapytań dotyczących [EventStore](service-fabric-diagnostics-eventstore.md). Na maszynach z systemem Windows te zdarzenia są przekazywane do dziennika zdarzeń, co umożliwia wyświetlanie Service Fabric zdarzeń w Podgląd zdarzeń. 

Poniżej przedstawiono niektóre cechy tych zdarzeń
* Każde zdarzenie jest powiązane z konkretną jednostką w klastrze, na przykład aplikacja, usługa, węzeł, replika.
* Każde zdarzenie zawiera zestaw wspólnych pól: EventInstanceId, EventName i Category.
* Każde zdarzenie zawiera pola, które wiążą zdarzenie z powrotem z jednostką, z którą jest skojarzona. Na przykład zdarzenie ApplicationCreated będzie zawierało pola, które identyfikują nazwę utworzonej aplikacji.
* Zdarzenia są uporządkowane w taki sposób, że mogą być używane w różnych narzędziach w celu przeprowadzenia dalszej analizy. Ponadto odpowiednie szczegóły dotyczące zdarzenia są definiowane jako osobne właściwości, a nie jako długie ciągi. 
* Zdarzenia są zapisywane przez różne podsystemy w Service Fabric są identyfikowane przez źródło (zadanie) poniżej. Więcej informacji jest dostępnych w tych podsystemach w [architekturze Service Fabric](service-fabric-architecture.md) i [Service Fabric omówienie techniczne](service-fabric-technical-overview.md).

Poniżej znajduje się lista tych zdarzeń Service Fabric zorganizowanych według jednostek.

## <a name="cluster-events"></a>Zdarzenia klastra

**Zdarzenia uaktualniania klastra**

Więcej szczegółów na temat uaktualniania klastra można znaleźć [tutaj](service-fabric-cluster-upgrade-windows-server.md).

| Identyfikator zdarzenia | Name (Nazwa) | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Uaktualnienie | Rozpoczęto Uaktualnianie klastra | CM | Informacyjne |
| 29628 | ClusterUpgradeCompleted | Uaktualnienie | Ukończono Uaktualnianie klastra | CM | Informacyjne | 
| 29629 | ClusterUpgradeRollbackStarted | Uaktualnienie | Rozpoczęto wycofywanie uaktualnienia klastra  | CM | Ostrzeżenie | 
| 29630 | ClusterUpgradeRollbackCompleted | Uaktualnienie | Uaktualnianie klastra zostało zakończone. | CM | Ostrzeżenie | 
| 29631 | ClusterUpgradeDomainCompleted | Uaktualnienie | Uaktualnianie domeny uaktualnienia podczas uaktualniania klastra zakończyło się | CM | Informacyjne | 

## <a name="node-events"></a>Zdarzenia węzła

**Zdarzenia cyklu życia węzła** 

| Identyfikator zdarzenia | Name (Nazwa) | Kategoria | Opis |Źródło (zadanie) | Poziom |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | Przejścia stanu | Dezaktywacja węzła zakończyła się | FM | Informacyjne | 
| 18603 | NodeUp | Przejścia stanu | Klaster wykrył, że uruchomiono węzeł | FM | Informacyjne | 
| 18604 | NodeDown | Przejścia stanu | Klaster wykrył, że węzeł został zamknięty. Podczas ponownego uruchamiania węzła zobaczysz zdarzenie NodeDown, po którym następuje zdarzenie NodeUp |  FM | Błąd | 
| 18605 | NodeAddedToCluster | Przejścia stanu |  Do klastra został dodany nowy węzeł i Service Fabric można wdrożyć aplikacje w tym węźle | FM | Informacyjne | 
| 18606 | NodeRemovedFromCluster | Przejścia stanu |  Węzeł został usunięty z klastra. Service Fabric nie będzie już wdrażać aplikacji w tym węźle | FM | Informacyjne | 
| 18607 | NodeDeactivateStarted | Przejścia stanu |  Rozpoczęto dezaktywowanie węzła | FM | Informacyjne | 
| 25621 | NodeOpenSucceeded | Przejścia stanu |  Węzeł został pomyślnie uruchomiony | FabricNode | Informacyjne | 
| 25622 | NodeOpenFailed | Przejścia stanu |  Nie można uruchomić węzła i połączyć się z pierścieniem | FabricNode | Błąd | 
| 25624 | NodeClosed | Przejścia stanu |  Węzeł zostanie zamknięty pomyślnie | FabricNode | Informacyjne | 
| 25626 | NodeAborted | Przejścia stanu |  Węzeł jest niebezpiecznie zamknięty | FabricNode | Błąd | 

## <a name="application-events"></a>Zdarzenia aplikacji

**Zdarzenia cyklu życia aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Witrynę | Utworzono nową aplikację | CM | Informacyjne | 
| 29625 | ApplicationDeleted | Witrynę | Istniejąca aplikacja została usunięta | CM | Informacyjne | 
| 23083 | ApplicationProcessExited | Witrynę | Proces w aplikacji zakończył działanie | Hosting | Informacyjne | 

**Zdarzenia uaktualniania aplikacji**

Więcej informacji o uaktualnieniach aplikacji można znaleźć [tutaj](service-fabric-application-upgrade.md).

| Identyfikator zdarzenia | Name (Nazwa) | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Uaktualnienie | Rozpoczęto Uaktualnianie aplikacji | CM | Informacyjne | 
| 29622 | ApplicationUpgradeCompleted | Uaktualnienie | Ukończono Uaktualnianie aplikacji | CM | Informacyjne | 
| 29623 | ApplicationUpgradeRollbackStarted | Uaktualnienie | Rozpoczęto Przywracanie aplikacji |CM | Ostrzeżenie | 
| 29624 | ApplicationUpgradeRollbackCompleted | Uaktualnienie | Uaktualnianie aplikacji zakończyło się wycofywaniem | CM | Ostrzeżenie | 
| 29626 | ApplicationUpgradeDomainCompleted | Uaktualnienie | Uaktualnianie domeny uaktualnienia podczas uaktualniania aplikacji zakończyło się | CM | Informacyjne | 

## <a name="service-events"></a>Zdarzenia usługi

**Zdarzenia cyklu życia usługi**

| Identyfikator zdarzenia | Name (Nazwa) | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- |
| 18657 | Servicecreated | Witrynę | Utworzono nową usługę | FM | Informacyjne | 
| 18658 | Nie usunięto | Witrynę | Istniejąca usługa została usunięta | FM | Informacyjne | 

## <a name="partition-events"></a>Zdarzenia partycji

**Zdarzenia przenoszenia partycji**

| Identyfikator zdarzenia | Name (Nazwa) | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Witrynę | Ukończono ponowną konfigurację partycji | ZDALNEJ | Informacyjne | 

## <a name="replica-events"></a>Zdarzenia repliki

**Zdarzenia cyklu życia repliki**

| Identyfikator zdarzenia | Name (Nazwa) | Kategoria | Opis |Źródło (zadanie) | Poziom |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Witrynę | Otwarto niezawodny słownik | DistributedDictionary | Informacyjne |
| 61702 | ReliableDictionaryClosed | Witrynę | Niezawodny słownik został zamknięty | DistributedDictionary | Informacyjne |
| 61703 | ReliableDictionaryCheckpointRecovered | Witrynę | Niezawodny słownik odzyskał swój punkt kontrolny | DistributedDictionary | Informacyjne |
| 61704 | ReliableDictionaryCheckpointFilesSent | Witrynę | Replika wysłała pliki punktów kontrolnych niezawodnych słowników | DistributedDictionary | Informacyjne |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Witrynę | Replika otrzymała niezawodne pliki punktów kontrolnych słownika | DistributedDictionary | Informacyjne |
| 61963 | ReliableQueueOpened | Witrynę | Otwarto niezawodną kolejkę | DistributedQueue | Informacyjne |
| 61964 | ReliableQueueClosed | Witrynę | Niezawodna kolejka została ZAMKNIĘTA | DistributedQueue | Informacyjne |
| 61965 | ReliableQueueCheckpointRecovered | Witrynę | Niezawodna Kolejka odzyska swój punkt kontrolny | DistributedQueue | Informacyjne |
| 61966 | ReliableQueueCheckpointFilesSent | Witrynę | Replika wysłała pliki punktów kontrolnych niezawodnej kolejki | DistributedQueue | Informacyjne |
| 63647 | ReliableQueueCheckpointFilesReceived | Witrynę | Replika otrzymała niezawodne pliki punktów kontrolnych kolejki | DistributedQueue | Informacyjne |
| 63648 | ReliableConcurrentQueueOpened | Witrynę | Otwarto niezawodną kolejkę współbieżną | ReliableConcurrentQueue | Informacyjne |
| 63649 | ReliableConcurrentQueueClosed | Witrynę | Zamknięto niezawodną kolejkę współbieżną | ReliableConcurrentQueue | Informacyjne |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Witrynę | Niezawodna Kolejka współbieżna odzyska swój punkt kontrolny | ReliableConcurrentQueue | Informacyjne |
| 61687 | TStoreError | Niepowodzenie | Niezawodna kolekcja odebrała nieoczekiwany błąd | TStore | Błąd |
| 63831 | PrimaryFullCopyInitiated | Witrynę | Replika podstawowa zainicjowała pełną kopię | TReplicator | Informacyjne |
| 63832 | PrimaryPartialCopyInitiated | Witrynę | Replika podstawowa zainicjowała kopię częściową | TReplicator | Informacyjne |
| 16831 | BuildIdleReplicaStarted | Witrynę | Replika podstawowa rozpoczęła tworzenie repliki bezczynnej | Replikacja | Informacyjne |
| 16832 | BuildIdleReplicaCompleted | Witrynę | Replika podstawowa ukończyła tworzenie repliki bezczynnej | Replikacja | Informacyjne |
| 16833 | BuildIdleReplicaFailed | Witrynę | Replika podstawowa nie powiodła się podczas tworzenia repliki bezczynnej | Replikacja | Ostrzeżenie |
| 16834 | PrimaryReplicationQueueFull | Health | Kolejka replikacji repliki podstawowej jest pełna | Replikacja | Ostrzeżenie |
| 16835 | PrimaryReplicationQueueWarning | Health | Kolejka replikacji repliki podstawowej jest blisko pełna | Replikacja | Ostrzeżenie |
| 16836 | PrimaryReplicationQueueWarningMitigated | Health | Kolejka replikacji repliki podstawowej jest niedobrana | Replikacja | Informacyjne |
| 16837 | SecondaryReplicationQueueFull | Health | Kolejka replikacji pomocniczej repliki jest pełna | Replikacja | Ostrzeżenie |
| 16838 | SecondaryReplicationQueueWarning | Health | Kolejka replikacji pomocniczej repliki jest blisko pełna | Replikacja | Ostrzeżenie |
| 16839 | SecondaryReplicationQueueWarningMitigated | Health | Kolejka replikacji pomocniczej repliki jest niedobrana | Replikacja | Informacyjne |
| 16840 | PrimaryFaultedSlowSecondary | Health | Replika podstawowa spowodowała awarię wolnej repliki pomocniczej | Replikacja | Ostrzeżenie |
| 16841 | ReplicatorFaulted | Health | Replika została uszkodzona | Replikacja | Ostrzeżenie |

## <a name="container-events"></a>Zdarzenia kontenera

**Zdarzenia cyklu życia kontenera** 

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Kontener został uruchomiony | Hosting | Informacyjne | 1 |
| 23075 | ContainerDeactivated | Kontener został zatrzymany | Hosting | Informacyjne | 1 |
| 23082 | ContainerExited | Kontener został zakończony — Sprawdź flagę UnexpectedTermination | Hosting | Informacyjne | 1 |

## <a name="health-reports"></a>Raporty dotyczące kondycji

[Model kondycji Service Fabric](service-fabric-health-introduction.md) zapewnia rozbudowane, elastyczne i rozszerzalne oceny kondycji oraz raportowanie. Począwszy od Service Fabric w wersji 6,2, dane dotyczące kondycji są zapisywane jako zdarzenia platformy, aby zapewnić historyczne rekordy kondycji. Aby zachować ilość zdarzeń dotyczących kondycji, należy napisać tylko następujące zdarzenia Service Fabric:

* Wszystkie raporty dotyczące kondycji `Error` lub `Warning`
* `Ok` raporty kondycji podczas przejść
* Po wygaśnięciu `Error` lub `Warning` zdarzenia dotyczącego kondycji. Pozwala to określić czas złej kondycji jednostki

**Zdarzenia raportów kondycji klastra**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Dostępny jest nowy raport kondycji klastra | HM | Informacyjne | 1 |
| 54437 | ClusterHealthReportExpired | Istniejący raport kondycji klastra wygasł | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji węzłów**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Dostępny jest nowy raport kondycji węzła | HM | Informacyjne | 1 |
| 54432 | NodeHealthReportExpired | Istniejący raport kondycji węzła wygasł | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Utworzono nowy raport kondycji aplikacji. Dotyczy to niewdrożonych aplikacji. | HM | Informacyjne | 1 |
| 54426 | DeployedApplicationNewHealthReport | Utworzono nowy wdrożony Raport kondycji aplikacji | HM | Informacyjne | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Utworzono nowy wdrożony Raport kondycji usługi | HM | Informacyjne | 1 |
| 54434 | ApplicationHealthReportExpired | Istniejący raport kondycji aplikacji wygasł | HM | Informacyjne | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Istniejący wdrożony Raport kondycji aplikacji wygasł | HM | Informacyjne | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Istniejący wdrożony Raport kondycji usługi wygasł | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji usługi**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Utworzono nowy raport kondycji usługi | HM | Informacyjne | 1 |
| 54433 | ServiceHealthReportExpired | Istniejący raport kondycji usługi wygasł | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji partycji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Utworzono nowy raport kondycji partycji | HM | Informacyjne | 1 |
| 54431 | PartitionHealthReportExpired | Istniejący raport kondycji partycji wygasł | HM | Informacyjne | 1 |

**Zdarzenia raportu kondycji repliki**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Raport kondycji replice stanowej został utworzony | HM | Informacyjne | 1 |
| 54430 | StatelessInstanceNewHealthReport | Utworzono nowy raport kondycji wystąpienia bezstanowego | HM | Informacyjne | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Istniejący raport kondycji repliki stanowej wygasł | HM | Informacyjne | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Istniejący raport kondycji wystąpienia bezstanowego wygasł | HM | Informacyjne | 1 |

## <a name="chaos-testing-events"></a>Zdarzenia testowania chaos 

**Zdarzenia sesji chaos**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Rozpoczęto sesję testową chaos | Testowalności | Informacyjne | 1 |
| 50023 | ChaosStopped | Sesja testowania chaos została zatrzymana | Testowalności | Informacyjne | 1 |

**Zdarzenia węzła chaos**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Zaplanowano ponowne uruchomienie węzła w ramach sesji testowania chaos | Testowalności | Informacyjne | 1 |
| 50087 | ChaosNodeRestartCompleted | Węzeł zakończył ponowne uruchamianie w ramach sesji testowania chaos | Testowalności | Informacyjne | 1 |

**Zdarzenia aplikacji chaos**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Zaplanowano ponowne uruchomienie pakietu kodu w trakcie sesji testowania chaos | Testowalności | Informacyjne | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Zakończono ponowne uruchamianie pakietu kodu podczas sesji testowania chaos | Testowalności | Informacyjne | 1 |

**Zdarzenia partycji chaos**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Zaplanowano przechodzenie na partycję podstawową w ramach sesji testowania chaos | Testowalności | Informacyjne | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Zaplanowano przechodzenie na partycję pomocniczą w ramach sesji testowania chaos | Testowalności | Informacyjne | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Dostępna jest dokładniejsza analiza przenoszenia partycji podstawowej. | Testowalności | Informacyjne | 1 |

**Zdarzenia repliki chaos**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Zaplanowano ponowne uruchomienie repliki w ramach sesji testowania chaos | Testowalności | Informacyjne | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Zaplanowano usunięcie repliki w ramach sesji testowania chaos | Testowalności | Informacyjne | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Usuwanie repliki zostało zakończone w ramach sesji testowania chaos | Testowalności | Informacyjne | 1 |

## <a name="other-events"></a>Inne zdarzenia

**Zdarzenia korelacji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Wykryto korelację | Testowalności | Informacyjne | 1 |

## <a name="events-prior-to-version-62"></a>Zdarzenia przed wersjami 6,2

Poniżej przedstawiono kompleksową listę zdarzeń dostarczonych przez Service Fabric przed wersjami 6,2.

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadanie) | Poziom |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informacyjne |
| 25621 | NodeOpenedSuccess | FabricNode | Informacyjne |
| 25622 | NodeOpenedFailed | FabricNode | Informacyjne |
| 25623 | NodeClosing | FabricNode | Informacyjne |
| 25624 | NodeClosed | FabricNode | Informacyjne |
| 25625 | NodeAborting | FabricNode | Informacyjne |
| 25626 | NodeAborted | FabricNode | Informacyjne |
| 29627 | ClusterUpgradeStart | CM | Informacyjne |
| 29628 | ClusterUpgradeComplete | CM | Informacyjne |
| 29629 | ClusterUpgradeRollback | CM | Informacyjne |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informacyjne |
| 29631 | ClusterUpgradeDomainComplete | CM | Informacyjne |
| 23074 | ContainerActivated | Hosting | Informacyjne |
| 23075 | ContainerDeactivated | Hosting | Informacyjne |
| 29620 | ApplicationCreated | CM | Informacyjne |
| 29621 | ApplicationUpgradeStart | CM | Informacyjne |
| 29622 | ApplicationUpgradeComplete | CM | Informacyjne |
| 29623 | ApplicationUpgradeRollback | CM | Informacyjne |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informacyjne |
| 29625 | ApplicationDeleted | CM | Informacyjne |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informacyjne |
| 18566 | Servicecreated | FM | Informacyjne |
| 18567 | Nie usunięto | FM | Informacyjne |

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z omówieniem [diagnostyki w Service Fabric](service-fabric-diagnostics-overview.md)
* Dowiedz się więcej na temat EventStore w [Service Fabric EventStore — Omówienie](service-fabric-diagnostics-eventstore.md)
* Modyfikowanie konfiguracji [Diagnostyka Azure](service-fabric-diagnostics-event-aggregation-wad.md) w celu zbierania dodatkowych dzienników
* [Konfigurowanie Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) do wyświetlania dzienników kanałów operacyjnych
