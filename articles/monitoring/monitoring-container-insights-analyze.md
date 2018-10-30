---
title: Monitorowanie wydajności klastra AKS przy użyciu usługi Azure Monitor dla kontenerów (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wyświetlać i analizować dane wydajności i dzienników z usługą Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: magoedte
ms.openlocfilehash: daec3d6e6cd8e4df3fdfe45fbb8ee98966c8a38e
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214158"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers-preview"></a>Zrozumienie wydajności klastra AKS przy użyciu usługi Azure Monitor dla kontenerów (wersja zapoznawcza)
Wyświetlanie wydajności z usługi Azure Kubernetes Service (AKS) klastrów można zaobserwować, z dwóch perspektyw z usługą Azure Monitor dla kontenerów bezpośrednio w klastrze AKS, lub wyświetlić wszystkie klastry usługi AKS w ramach subskrypcji z usługi Azure Monitor. 

Ten artykuł pomoże zrozumieć środowisko między dwóch perspektyw i jak szybko ocenić, zbadać i rozwiązać wykryte problemy.

Aby uzyskać informacje na temat włączania usługi Azure Monitor dla kontenerów, zobacz [dołączanie usługi Azure Monitor dla kontenerów](monitoring-container-insights-onboard.md).

Usługa Azure Monitor udostępnia widok wielu klastrów pokazujący stan kondycji wszystkich monitorowanych klastry usługi AKS, wdrożono między grupami zasobów w subskrypcji i klastry usługi AKS, nie są monitorowane przez rozwiązanie.  Natychmiast możesz zrozumieć kondycji klastra w tym miejscu możesz przechodzić do strony wydajność węzła i kontroler i możesz przejść do Zobacz wykresy wydajności dla klastra.  W przypadku klastrów usługi AKS wykryte i identyfikowane jako niemonitorowane możesz wybierz opcję, aby włączyć monitorowanie dla tego klastra w dowolnym momencie.  

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Wyświetlanie wielu klastrów z usługi Azure Monitor 
Zaznacz, aby wyświetlić stan kondycji wszystkich klastrach usługi AKS wdrożonych **Monitor** w okienku po lewej stronie w witrynie Azure portal.  W obszarze **Insights** sekcji wybierz **kontenery (wersja zapoznawcza)**.  

![Przykład pulpitu nawigacyjnego wielu klastrów w usłudze Azure Monitor](./media/monitoring-container-insights-analyze/azmon-containers-multiview.png)

Na **monitorowania klastrów** kartę, jesteś w stanie się następujące czynności:

1. Jak wiele klastrów znajdują się w stanie krytycznych lub w złej kondycji, a ile to dobrej kondycji i nie raportowania (nazywane stan nieznany)?
2. Jak wiele węzłów, użytkownika i zasobników systemu są wdrażane w klastrze.  

Stany kondycji zdefiniowane są następujące: 

1. **Dobra** — wykryte nie problemy dla maszyny Wirtualnej i czy działa on zgodnie z potrzebami. 
2. **Krytyczne** — co najmniej jeden problem krytyczny, są wykrywane, które należy uwzględnić w celu przywrócenia normalnej stanu operacyjnego, zgodnie z oczekiwaniami.
3. **Ostrzeżenie** — co najmniej jeden problem, są wykrywane, które muszą być kierowane lub stan kondycji może stać się krytyczna.
4. **Nieznany** — Jeśli usługa nie był w stanie nawiązać połączenie z węzłem lub zasobników, jego stan zmieni się stan nieznany.

Stan kondycji oblicza ogólny stan klastra jako *najgorszy*"trzy stany z jednym wyjątkiem — Jeśli jeden z trzech stanów *nieznany*, będą wyświetlane ogólny stan klastra **nieznany**.  

Poniższa tabela zawiera podział obliczeń kontrolowanie stanów kondycji monitorowanego klastra w widoku wielu klastrów.

| |Stan |Dostępność |  
|-------|-------|-----------------|  
|**Zasobnik użytkownika**| | |  
| |W dobrej kondycji |100% |  
| |Ostrzeżenie |90 - 99% |  
| |Krytyczny |< 90% |  
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |  
|**System Pod**| | |  
| |W dobrej kondycji |100% |
| |Ostrzeżenie |ND |
| |Krytyczny |< 100% |
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |
|**Node** | | |
| |W dobrej kondycji |> 85% |
| |Ostrzeżenie |60 - 84% |
| |Krytyczny |< 60% |
| |Nieznane |Jeśli nie zostały zgłoszone w ciągu ostatnich 30 minut |

Z listy klastrów, użytkownik może przejść do **klastra** strony, klikając nazwę klastra, do **węzłów** stronie wydajności, klikając zbiór węzłów w **węzłów** kolumny dla tego określonego klastra lub przejść do **kontrolerów** stronie wydajności, klikając zestawianie **zasobników użytkownika** lub **zasobników System**kolumny.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Wyświetl wydajność bezpośrednio z klastra usługi AKS
Dostęp do usługi Azure Monitor na kontenerów jest dostępne bezpośrednio z klastra usługi AKS, wybierając **Insights (wersja zapoznawcza)** z okienka po lewej stronie. Wyświetlanie informacji na temat klastra usługi AKS jest podzielony na cztery perspektywy:

- Klaster
- Węzły 
- Kontrolery  
- Containers

Domyślna strona otwarty po kliknięciu **Insights (wersja zapoznawcza)** jest **klastra**, i zawiera cztery wykresy wydajności wiersza, wyświetlanie kluczowe metryki wydajności klastra. 

![Przykładowe wykresy wydajności na karcie klastra](./media/monitoring-container-insights-analyze/containers-cluster-perfview.png)

Wykres wydajności przedstawia cztery metryki wydajności:

- **Wykorzystanie procesora CPU w węźle&nbsp;%**: perspektywy zagregowane wykorzystanie procesora CPU dla całego klastra. Wyniki zakresu czasu można filtrować, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- **Wykorzystanie pamięci węzła&nbsp;%**: perspektywy zagregowane wykorzystanie pamięci dla całego klastra. Wyniki zakresu czasu można filtrować, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- **Liczba węzłów**: liczba węzłów i stan z usługi Kubernetes. Są Stany węzłów klastra, reprezentowany *wszystkich*, *gotowe*, i *niegotowe* i może być filtrowana indywidualnie lub w połączeniu w selektorze powyżej wykresu. 
- **Liczba zasobników działań**: Liczba zasobników i stan z usługi Kubernetes. Są Stany zasobników reprezentowane *wszystkich*, *oczekujące*, *systemem*, i *nieznany* i może być filtrowana indywidualnie lub w Selektor powyżej wykresu. 

Po przełączeniu do **węzłów**, **kontrolerów**, i **kontenery** karta automatycznie wyświetlane w prawej części strony to okienko właściwości.  Pokazuje właściwości elementu zaznaczone, w tym etykiety, należy zdefiniować do organizowania obiekty usługi Kubernetes. Kliknij pozycję **>>** połączyć w okienku view\hide okienka.  

![W okienku właściwości perspektyw Kubernetes przykład](./media/monitoring-container-insights-analyze/perspectives-preview-pane-01.png)

Podczas rozwijania obiektów w hierarchii, na podstawie aktualizacji okienka właściwości w obiekcie, który został wybrany. W okienku można wyświetlić zdarzeń Kubernetes za pomocą wstępnie zdefiniowanych wyszukiwań w dziennikach, klikając **dzienniki zdarzeń Kubernetes widoku** widocznego u góry okienka. Aby uzyskać dodatkowe informacje o wyświetlaniu danych dzienników platformy Kubernetes, zobacz [przeszukiwanie dzienników w celu analizowania danych](#search-logs-to-analyze-data).

Użyj **+ Dodaj filtr** opcję w górnej części strony, aby filtrować wyniki dla widoku przez **usługi**, **węzła**, lub **Namespace** oraz po Wybieranie zakresu filtru, które następnie wybrać jedną z wartości podanych w **wybierz wartości** pola.  Po skonfigurowaniu filtru jest stosowana globalnie podczas wyświetlania dowolnego punktu widzenia klastra AKS.  Formuła obsługuje tylko znaku równości.  Możesz dodać dodatkowe filtry na podstawie pierwszy z nich w celu dalszego zawężenia wyników.  Na przykład, jeśli określono filtr według **węzła**, drugi filtr będzie tylko pozwalają na wybór **usługi** lub **Namespace**.  

![Przykład przy użyciu filtru, aby zawęzić wyniki](./media/monitoring-container-insights-analyze/add-filter-option-01.png)

Określenie filtru w jednej karty w dalszym ciągu stosowane, gdy można wybrać inny, i zostanie usunięta po kliknięciu **x** symbol obok określonego filtru.   

Przełącz się do **węzłów** kartę i hierarchii wiersz poniżej model obiektów usługi Kubernetes, począwszy od węzła w klastrze. Rozwiń węzeł i można wyświetlić co najmniej jeden zasobników, uruchomione w węźle. Jeśli więcej niż jednego kontenera jest zgrupowany do zasobnik, są wyświetlane jako ostatni wiersz w hierarchii. Można również wyświetlić, jak wiele powiązanych obciążeniach-pod są uruchomione na hoście, jeśli host ma procesor lub dużego wykorzystania pamięci.

![Przykładowa hierarchia Kubernetes węzeł w widoku wydajności](./media/monitoring-container-insights-analyze/containers-nodes-view.png)

Z rozwinięty węzeł należy można przejść pod lub kontenerów uruchomione w węźle z kontrolerem, aby wyświetlić dane dotyczące wydajności przefiltrowana pod kątem tego kontrolera. Kliknij wartość, w obszarze **kontrolera** kolumny dla określonego węzła.   

![Przykład przechodzenie do szczegółów z węzła do kontrolera w widoku wydajności](./media/monitoring-container-insights-analyze/drill-down-node-controller.png)

Można wybrać kontrolery lub kontenerów w górnej części strony i sprawdź stan i użycia zasobów dla tych obiektów.  Jeśli zamiast tego chcesz przejrzeć wykorzystanie pamięci w **metryki** listy rozwijanej wybierz **RSS pamięci** lub **zestaw roboczy pamięci**. **Pamięć RSS** jest obsługiwany tylko w przypadku rozwiązania Kubernetes w wersji 1.8 i nowszych. W przeciwnym razie do wyświetlania wartości **Min&nbsp; %**  jako *NaN&nbsp;%*, który jest wartość typu danych liczbowych, który reprezentuje niezdefiniowany lub wartość wyniku. 

![Widok wydajności węzłów kontenerów](./media/monitoring-container-insights-analyze/containers-node-metric-dropdown.png)

Domyślnie dane dotyczące wydajności opiera się na ostatnie 6 godzin, ale okno można zmienić za pomocą **TimeRange** opcji w lewym górnym rogu. Można również filtrować wyniki do zakresu, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyl. 

![Wybór percentyl filtrowanie danych](./media/monitoring-container-insights-analyze/containers-metric-percentile-filter.png)

W poniższym przykładzie należy zauważyć, dla węzła *aks-nodepool1 -*, wartość **kontenery** jest 14, które dokonują zestawienia z łączną liczbą kontenerów wdrożonych.

![Zbiorczy kontenerów na przykład węzeł](./media/monitoring-container-insights-analyze/containers-nodes-containerstotal.png)

Ułatwia ona szybko ustalić, czy masz kompromisu kontenerów między węzłami w klastrze. 

W poniższej tabeli opisano informacje, które są prezentowane podczas wyświetlania węzłów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa hosta. |
| Stan | Kubernetes widok stanu węzła. |
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Średni procent węzła, w oparciu o procentowy podczas wybranego okresu. |
| Avg, Min, Max, 50., 90 | Węzły średnia wartość rzeczywista oparciu o procentowy podczas tego okresu czasu wybrany. Średnia wartość jest mierzony od limitu Procesora/pamięci dla węzła; dla zasobników i kontenerów to wartość średnia zgłaszaną przez hosta. |
| Containers | Liczba kontenerów. |
| Czas pracy | Reprezentuje czas, ponieważ węzeł uruchomiony lub został ponownie uruchomiony. |
| Kontrolery | Tylko w przypadku kontenerów i zasobników. Przedstawia on kontrolera, który jest we wszystkich. Nie wszystkie zasobników są w kontrolerze, więc niektóre może być wyświetlany **n/d**. | 
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Trend wykres słupkowy prezentowanie percentyla metryk wartość procentowa kontrolera. |

W selektorze, wybierz **kontrolerów**.

![Wybierz kontrolerów widoku](./media/monitoring-container-insights-analyze/containers-controllers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontrolerów.

![Widok wydajności kontrolerów < nazwa >](./media/monitoring-container-insights-analyze/containers-controllers-view.png)

Hierarchia wiersz rozpoczyna się z kontrolerem i po rozwinięciu kontrolera, możesz wyświetlić co najmniej jeden zasobników.  Rozwiń zasobnik, a ostatni wiersz wyświetla kontenera grupowania zasobnik. Z rozwiniętej kontrolera należy można przejść do węzła, w którym jest uruchomiona na, aby wyświetlić dane dotyczące wydajności filtrowane dla tego węzła. Kliknij wartość, w obszarze **węzła** kolumny dla określonego kontrolera.   

![Przykład przechodzenie do szczegółów z węzła do kontrolera w widoku wydajności](./media/monitoring-container-insights-analyze/drill-down-controller-node.png)

W poniższej tabeli opisano informacje, które jest wyświetlane, gdy wyświetlanie kontrolerów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| Stan | Stan rozwinięcia kontenery po ukończeniu uruchomione o stanie, takie jak *OK*, *zwolniony*, *niepowodzenie* *zatrzymane*, lub *Wstrzymana*. Jeśli kontener jest uruchomiony, ale stan był albo nie zostało prawidłowo wyświetlane lub nie została pobrana przez agenta i nie odpowiedział ponad 30 minut, stan jest *nieznany*. W poniższej tabeli znajdują się dodatkowe szczegóły ikonę stanu.|
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Średnia zbiorczy średnią wartość procentową poszczególnych jednostek dla wybranej metryki i percentyl. |
| Avg, Min, Max, 50., 90  | Zbiorcze średni wydajność użycia CPU millicore lub pamięci przez kontener dla wybranych percentyl. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Containers | Łączna liczba kontenerów dla kontrolera lub zasobników. |
| Ponowne uruchomienie | Zbiorcza liczba ponowne uruchomienie z kontenerów. |
| Czas pracy | Reprezentuje czas od momentu uruchomienia kontenera. |
| Węzeł | Tylko w przypadku kontenerów i zasobników. Przedstawia on kontrolera, który jest znajdującej się. | 
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;%| Trend wykresu słupkowego, reprezentujący metryki percentyl kontrolera. |

Ikony w polu Stan wskazują stan online kontenerów:
 
| Ikona | Stan | 
|--------|-------------|
| ![Gotowe uruchomionej ikona stanu](./media/monitoring-container-insights-analyze/containers-ready-icon.png) | Uruchamianie (gotowe)|
| ![Ikona stanu oczekuje lub wstrzymana](./media/monitoring-container-insights-analyze/containers-waiting-icon.png) | Oczekuje lub wstrzymana|
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/monitoring-container-insights-analyze/containers-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|
| ![Ikona stanu powodzenia](./media/monitoring-container-insights-analyze/containers-green-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|

Ikona stanu przedstawia liczbę oparte na zasobnik udostępnia. Pokazuje dwa najgorsze Stany i po najechaniu kursorem na stan, wyświetla stan zbiorcze z wszystkich zasobników w kontenerze. Jeśli nie ma stanu gotowości, wyświetlana jest wartość stanu **(0)**. 

W selektorze, wybierz **kontenery**.

![Wybierz kontenery widoku](./media/monitoring-container-insights-analyze/containers-containers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontenerów usługi Kubernetes na platformie Azure.  

![Widok wydajności kontrolerów < nazwa >](./media/monitoring-container-insights-analyze/containers-containers-view.png)

Z kontenera należy można przejść do zasobnika lub węzeł, aby wyświetlić dane dotyczące wydajności przefiltrowana pod kątem tego obiektu. Kliknij wartość, w obszarze **Pod** lub **węzła** kolumny dla określonego kontenera.   

![Przykład przechodzenie do szczegółów z węzła do kontrolera w widoku wydajności](./media/monitoring-container-insights-analyze/drill-down-controller-node.png)

W poniższej tabeli opisano informacje, który jest wyświetlany podczas przeglądania kontenerów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| Stan | Stan kontenerów, jeśli istnieje. Dodatkowe szczegóły ikona stanu znajdują się w następnej tabeli.|
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Roll-up średnią wartość procentową poszczególnych jednostek dla wybranej metryki i percentyl. |
| Avg, Min, Max, 50., 90  | Zbiorczy średni Procesora pamięci lub millicore wydajności kontenera dla wybranych percentyl. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Zasobnik | Kontener, w którym znajduje się pod.| 
| Węzeł |  Węzeł, w którym znajduje się kontener. | 
| Ponowne uruchomienie | Reprezentuje czas od momentu uruchomienia kontenera. |
| Czas pracy | Reprezentuje czas, ponieważ kontener został uruchomiony lub ponownie uruchomiony. |
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Trend wykresu słupkowego, reprezentująca średnią wartość procentową metryki kontenera. |

Ikony w polu Stan wskazują online stany zasobników, zgodnie z opisem w poniższej tabeli:
 
| Ikona | Stan |  
|--------|-------------|  
| ![Gotowe uruchomionej ikona stanu](./media/monitoring-container-insights-analyze/containers-ready-icon.png) | Uruchamianie (gotowe)|  
| ![Ikona stanu oczekuje lub wstrzymana](./media/monitoring-container-insights-analyze/containers-waiting-icon.png) | Oczekuje lub wstrzymana|  
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/monitoring-container-insights-analyze/containers-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|  
| ![Ikona stanu zakończone](./media/monitoring-container-insights-analyze/containers-terminated-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|  
| ![Ikona stanu nie powiodło się](./media/monitoring-container-insights-analyze/containers-failed-icon.png) | Stan niepowodzenia |  

## <a name="container-data-collection-details"></a>Szczegóły dotyczące zbierania danych kontenera
Szczegółowe informacje o kontenerze zbiera różne dane metryk i dzienników wydajności hostach kontenerów i kontenery. Dane są gromadzone co trzy minuty.

### <a name="container-records"></a>Rekordów kontenera

Przykłady rekordy, które są zbierane przez usługi Azure Monitor dla kontenerów i typy danych, które są wyświetlane w wynikach wyszukiwania w dzienniku są wyświetlane w poniższej tabeli:

| Typ danych | Typ danych podczas wyszukiwania dziennika | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | CounterName komputera, nazwa obiektu, &#40;czas procesora (%), dysk odczytuje MB, dysku zapisuje MB, MB użycia pamięci, sieci odbierania bajtów, sieci wysyłania w bajtach, procesor s użycia, sieć&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy |
| Kontener magazynu | `ContainerInventory` | TimeGenerated, komputera, nazwa kontenera, ContainerHostname, obraz, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenia, wartością CreatedTime, StartedTime, FinishedTime, system źródłowy, ContainerID, ImageID |
| Spis obrazów kontenera | `ContainerImageInventory` | TimeGenerated, komputer, obraz, ImageTag, ImageSize, VirtualSize, uruchamianie, wstrzymana, zatrzymana, nie powiodło się, system źródłowy, ImageID, TotalContainer |
| Dziennik kontenera | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, pola Nazwa kontenera LogEntrySource, LogEntry, system źródłowy, ContainerID |
| Dziennik usługi kontenera | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Spis węzłów kontenerów | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Proces kontenera | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Spis zasobników w klastrze Kubernetes | `KubePodInventory` | TimeGenerated, komputer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, element ControllerName, ContainerStatus, ContainerID, ContainerName, PodLabel, adresem IP zasobnika, Namespace, ClusterName, nazwa, PodStatus, system źródłowy |
| Spis węzłów będących częścią klastra Kubernetes | `KubeNodeInventory` | TimeGenerated, komputera, ClusterName, ClusterId, LastTransitionTimeReady, etykiety, stan, KubeletVersion, KubeProxyVersion, CreationTimeStamp, system źródłowy | 
| Zdarzenia Kubernetes | `KubeEvents_CL` | TimeGenerated, komputera, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, komunikat o błędzie, system źródłowy | 
| Usługi w klastrze Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, SelectorLabels_s, ServiceType_s, ClusterId_s, ClusterIP_s, Namespace_s, ClusterName_s, system źródłowy | 
| Metryki wydajności dla części węzłów klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SNode" | CounterName komputera, nazwa obiektu, &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy | 
| Metryki wydajności dla kontenerów częścią klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SContainer" | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, System źródłowy | 

## <a name="search-logs-to-analyze-data"></a>Dzienniki wyszukiwania do analizy danych
Usługa log Analytics może pomóc wyszukiwania trendach, diagnozowanie wąskich gardeł, prognozy lub korelowanie danych, które mogą pomóc ustalić, czy bieżąca konfiguracja klastra działa optymalnie. Wstępnie zdefiniowane wyszukiwań w dziennikach znajdują się za Ciebie, aby od razu rozpocząć korzystanie z lub dostosować do zwracania informacji w żądany sposób. 

W obszarze roboczym można wykonywać interakcyjne analizy danych, zaznaczając **dzienniki zdarzeń Kubernetes widoku** lub **wyświetlanie dzienników kontenera** opcji w okienku podglądu. **Wyszukiwanie w dzienniku** po prawej stronie portalu platformy Azure były na zostanie wyświetlona strona.

![Analiza danych w usłudze Log Analytics](./media/monitoring-container-insights-analyze/container-health-log-search-example.png)   

Dane wyjściowe dzienników kontenera, który jest przekazywany do usługi Log Analytics to STDOUT i STDERR. Ponieważ usługa Azure Monitor jest monitorowanie usługi Azure managed Kubernetes (AKS), system klastra kubernetes w usłudze nie są zbierane już dziś ze względu na duże obciążenie wygenerowane dane. 

### <a name="example-log-search-queries"></a>Przykład zapytania wyszukiwania w Dzienniku
Często jest to przydatne do tworzenia zapytań, które zaczynać się przykładem lub dwa, a następnie zmodyfikuj je zgodnie z wymaganiami. Aby ułatwić tworzenie bardziej zaawansowanych zapytań, możesz eksperymentować z następujące przykładowe zapytania:

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt komputera, nazwa, obraz, ImageTag, ContainerState, wartością CreatedTime, StartedTime, FinishedTime<br> &#124;Renderowanie tabeli | Wyświetlić listę wszystkich informacji o cyklu życia kontenera| 
| KubeEvents_CL<br> &#124;gdzie not(isempty(Namespace_s))<br> &#124;Sortuj według malejącej TimeGenerated<br> &#124;Renderowanie tabeli | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by obrazu, ImageTag, działa | Spis obrazów | 
| **Advanced Analytics wybierz wykresów liniowych**:<br> Perf<br> &#124;Gdzie ObjectName == "Container" i CounterName == "% czasu procesora"<br> &#124;Podsumowanie AvgCPUPercent avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Procesora CPU kontenera | 
| **Advanced Analytics wybierz wykresów liniowych**:<br> Perf &#124; gdzie ObjectName == "Container" i CounterName == "MB użycia pamięci"<br> &#124;Podsumowanie AvgUsedMemory avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Pamięci kontenera |
