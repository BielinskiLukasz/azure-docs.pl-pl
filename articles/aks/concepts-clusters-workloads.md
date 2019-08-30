---
title: Koncepcje — podstawy Kubernetes dla usług Azure Kubernetes Services (AKS)
description: Informacje o podstawowym klastrze i składnikach obciążenia Kubernetes oraz o tym, jak odnoszą się do funkcji w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: e606b4fee2c46f66f13c45586bcc25577bd90a1f
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147199"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Podstawowe pojęcia Kubernetes dla usługi Azure Kubernetes Service (AKS)

Gdy Programowanie aplikacji przenosi się do podejścia opartego na kontenerach, trzeba zorganizować i zarządzać zasobami. Kubernetes to wiodąca platforma zapewniająca niezawodne planowanie obciążeń aplikacji odpornych na błędy. Usługa Azure Kubernetes Service (AKS) to zarządzana oferta Kubernetes, która ułatwia wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi.

W tym artykule wprowadzono podstawowe składniki infrastruktury Kubernetes, takie jak *wzorzec klastra*, *węzły*i *Pule węzłów*. Wprowadzono również zasoby obciążenia, takie jak zbiory, *wdrożenia*i *zestawy* , wraz z sposobem grupowania zasobów w *przestrzeni nazw*.

## <a name="what-is-kubernetes"></a>Co to jest Kubernetes?

Kubernetes to gwałtownie rozwijający się platforma, która zarządza aplikacjami opartymi na kontenerach i skojarzonymi z nimi składnikami sieci i magazynu. Fokus dotyczy obciążeń aplikacji, a nie podstawowych składników infrastruktury. Kubernetes zapewnia deklaracyjne podejście do wdrożeń, które są obsługiwane przez niezawodny zestaw interfejsów API dla operacji zarządzania.

Możesz tworzyć i uruchamiać nowoczesne, przenośne, oparte na mikrousługach aplikacje, które korzystają z Kubernetes organizowania i zarządzania dostępnością tych składników aplikacji. Usługa Kubernetes obsługuje zarówno bezstanowe, jak i stanowe aplikacje jako zespoły postępują zgodnie z wdrażaniem aplikacji opartych na mikrousługach.

Jako otwarta platforma Kubernetes umożliwia tworzenie aplikacji przy użyciu preferowanego języka programowania, systemu operacyjnego, bibliotek lub magistrali obsługi komunikatów. Istniejące narzędzia ciągłej integracji i ciągłego dostarczania (CI/CD) można zintegrować z usługą Kubernetes w celu planowania i wdrażania wersji.

Usługa Azure Kubernetes Service (AKS) oferuje zarządzaną usługę Kubernetes, która zmniejsza złożoność wdrażania i podstawowych zadań zarządzania, w tym koordynowania uaktualnień. Wzorce klastra AKS są zarządzane przez platformę Azure i płacisz tylko za węzły AKS, na których działają aplikacje. AKS jest tworzona na podstawie aparatu usługi Azure Kubernetes ([AKS-Engine][aks-engine]) typu open source.

## <a name="kubernetes-cluster-architecture"></a>Architektura klastra Kubernetes

Klaster Kubernetes jest podzielony na dwa składniki:

- Węzły *wzorca klastra* zapewniają podstawowe usługi Kubernetes i aranżację obciążeń aplikacji.
- *Węzły* uruchamiają obciążenia aplikacji.

![Składniki wzorca i węzła klastra Kubernetes](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Wzorzec klastra

Podczas tworzenia klastra AKS zostanie automatycznie utworzony i skonfigurowany główny klaster. Ten wzorzec klastra jest dostarczany jako zarządzany zasób platformy Azure, który został wyodrębniony przez użytkownika. Nie ma kosztu dla wzorca klastra, tylko węzłów, które są częścią klastra AKS.

Wzorzec klastra zawiera następujące podstawowe składniki Kubernetes:

- *polecenia-apiserver* — serwer interfejsu API to sposób, w jaki są ujawniane bazowe interfejsy API Kubernetes. Ten składnik zapewnia interakcję z narzędziami do zarządzania, `kubectl` takimi jak lub pulpitem nawigacyjnym Kubernetes.
- *etcd* — aby zachować stan klastra Kubernetes i konfiguracji, *etcd* wysoka dostępność to kluczowy magazyn wartości w ramach Kubernetes.
- *polecenia-Scheduler* — podczas tworzenia lub skalowania aplikacji harmonogram określa węzły, które mogą uruchamiać obciążenie i je uruchamia.
- *polecenia-Controller-Manager* — Menedżer kontrolerów widzi kilka mniejszych kontrolerów, które wykonują takie działania, jak replikowanie i obsługa operacji węzła.

AKS udostępnia wzorzec klastra z jedną dzierżawą, przy użyciu dedykowanego serwera interfejsu API, harmonogramu itp. Zdefiniuj liczbę i rozmiar węzłów, a platforma Azure skonfiguruje bezpieczną komunikację między wzorcem i węzłami klastra. Interakcja z wzorcem klastra odbywa się za pomocą interfejsów API `kubectl` Kubernetes, takich jak lub pulpitu nawigacyjnego Kubernetes.

Ten zarządzany serwer główny klastra oznacza, że nie trzeba konfigurować składników takich jak magazyn *etcd* o wysokiej dostępności, ale oznacza to również, że nie można uzyskać dostępu bezpośrednio do wzorca klastra. Uaktualnienia do Kubernetes są zorganizowane za pomocą interfejsu wiersza polecenia platformy Azure lub Azure Portal, który uaktualnia wzorzec klastra, a następnie węzły. Aby rozwiązać ewentualne problemy, można przejrzeć dzienniki główne klastra za pomocą dzienników Azure Monitor.

Jeśli musisz skonfigurować wzorzec klastra w określony sposób lub potrzebujesz bezpośredniego dostępu do nich, możesz wdrożyć własny klaster Kubernetes przy użyciu [aparatu AKS-Engine][aks-engine].

W przypadku skojarzonych najlepszych rozwiązań należy zapoznać się [z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń i uaktualnień klastra w programie AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Węzły i pule węzłów

Aby uruchamiać aplikacje i usługi pomocnicze, potrzebny jest *węzeł*Kubernetes. Klaster AKS ma co najmniej jeden węzeł, który jest maszyną wirtualną platformy Azure, na której działa składniki węzła Kubernetes i środowisko uruchomieniowe kontenera:

- `kubelet` Jest to Agent Kubernetes, który przetwarza żądania aranżacji z głównego serwera klastra i planowania uruchamiania żądanych kontenerów.
- Sieć wirtualna jest obsługiwana przez *polecenia-proxy* w każdym węźle. Serwer proxy kieruje ruchem sieciowym i zarządza adresami IP dla usług i zasobników.
- *Środowisko uruchomieniowe kontenera* to składnik, który umożliwia aplikacjom kontenerowym uruchamianie i współdziałanie z dodatkowymi zasobami, takimi jak sieć wirtualna i magazyn. W AKS, Moby jest używany jako środowisko uruchomieniowe kontenera.

![Maszyna wirtualna platformy Azure i obsługa zasobów dla węzła Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Rozmiar maszyny wirtualnej platformy Azure dla węzłów definiuje liczbę procesorów CPU, ilość pamięci oraz ilość dostępnego miejsca do magazynowania (na przykład dysk SSD o wysokiej wydajności lub zwykły dysk twardy). Jeśli przewidywane jest zapotrzebowanie na aplikacje wymagające dużej ilości zasobów procesora CPU i pamięci lub magazynu o wysokiej wydajności, należy odpowiednio zaplanować rozmiar węzła. Możesz również skalować liczbę węzłów w klastrze AKS, aby spełnić zapotrzebowanie.

W AKS obraz maszyny wirtualnej dla węzłów w klastrze jest obecnie oparty na Ubuntu Linux lub Windows Server 2019. Podczas tworzenia klastra AKS lub skalowania w górę liczby węzłów platforma Azure tworzy żądaną liczbę maszyn wirtualnych i konfiguruje je. Nie istnieje ręczna konfiguracja do wykonania. Węzły agentów są rozliczane jako standardowe maszyny wirtualne, w związku z czym wszelkie zniżki na używanym rozmiarze maszyny wirtualnej (w tym [rezerwacje platformy Azure][reservation-discounts]) są automatycznie stosowane.

Jeśli konieczne jest użycie innego systemu operacyjnego hosta, środowiska uruchomieniowego kontenera lub dołączenie pakietów niestandardowych, można wdrożyć własny klaster Kubernetes przy użyciu [aparatu AKS][aks-engine]. Funkcje wersji nadrzędnej `aks-engine` i udostępniają opcje konfiguracji, zanim są oficjalnie obsługiwane w klastrach AKS. Na przykład jeśli chcesz użyć środowiska uruchomieniowego kontenera innego niż Moby, możesz użyć `aks-engine` programu, aby skonfigurować i wdrożyć klaster Kubernetes, który spełnia Twoje bieżące potrzeby.

### <a name="resource-reservations"></a>Rezerwacje zasobów

Nie trzeba zarządzać podstawowymi składnikami Kubernetes w każdym węźle, takim jak *kubelet*, *polecenia-proxy*i *polecenia-DNS*, ale używają niektórych dostępnych zasobów obliczeniowych. Aby zapewnić wydajność i funkcjonalność węzła, następujące zasoby obliczeniowe są zarezerwowane dla każdego węzła:

- **Procesor** — 60 MS
- **Pamięć** — 20% do 4 GIB

Te rezerwacje oznaczają, że ilość dostępnego procesora i pamięci dla aplikacji może być mniejsza niż w przypadku węzła. Jeśli istnieją ograniczenia zasobów ze względu na liczbę uruchomionych aplikacji, te rezerwacje zapewniają, że procesor i pamięć są dostępne dla podstawowych składników Kubernetes. Nie można zmienić rezerwacji zasobów.

Na przykład:

- **Standardowy rozmiar węzła DS2 v2** zawiera 2 vCPU i 7 pamięci GIB
    - 20% z 7 GiB pamięci = 1,4 GiB
    - Łącznie *(7-1,4) = 5,6 pamięci GIB* jest dostępna dla węzła
    
- Rozmiar węzła **standardowego E4s v3** zawiera 4 vCPU i 32 pamięci GIB
    - 20% z 32 GiB pamięci = 6,4 GiB, ale AKS rezerwuje tylko 4 GiB
    - Łącznie *(32-4) = 28 GIB* jest dostępna dla węzła
    
Podstawowy system operacyjny nie wymaga również pewnej ilości zasobów procesora i pamięci do ukończenia własnych funkcji podstawowych.

Aby zapoznać się z najlepszymi rozwiązaniami, zobacz [najlepsze rozwiązania dotyczące podstawowych funkcji usługi Scheduler w AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pule węzłów

Węzły tej samej konfiguracji są pogrupowane w *Pule węzłów*. Klaster Kubernetes zawiera co najmniej jedną pulę węzłów. Początkowa liczba węzłów i rozmiar są definiowane podczas tworzenia klastra AKS, który tworzy *domyślną pulę węzłów*. Ta domyślna pula węzłów w AKS zawiera podstawowe maszyny wirtualne, na których są uruchomione węzły agentów. Obsługa wielu węzłów jest obecnie dostępna w wersji zapoznawczej w AKS.

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów.

Po skalowaniu lub uaktualnieniu klastra AKS akcja jest wykonywana względem domyślnej puli węzłów. Możesz również wybrać skalowanie lub uaktualnianie określonej puli węzłów. W przypadku operacji uaktualniania uruchomione kontenery są planowane w innych węzłach w puli węzłów do momentu, aż wszystkie węzły zostaną pomyślnie uaktualnione.

Aby uzyskać więcej informacji na temat używania wielu pul węzłów w AKS, zobacz [Tworzenie wielu pul węzłów i zarządzanie nimi w klastrze w AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Selektory węzłów

W klastrze AKS zawierającym wiele pul węzłów może być konieczne poinformowanie usługi Kubernetes Scheduler, której puli węzłów użyć dla danego zasobu. Na przykład kontrolery transferu danych przychodzących nie powinny działać w węzłach systemu Windows Server (obecnie w wersji zapoznawczej w AKS). Selektory węzłów umożliwiają definiowanie różnych parametrów, takich jak węzeł systemu operacyjnego, w celu kontrolowania, gdzie należy zaplanować.

Poniższy przykład podstawowy zaplanuje wystąpienie NGINX w węźle systemu Linux przy użyciu selektora węzła *"beta.Kubernetes.IO/OS": Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Aby uzyskać więcej informacji na temat sposobu kontrolowania, gdzie są planowane planowanie, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Zasobników

Kubernetes używa *zasobników* do uruchomienia wystąpienia aplikacji. Element pod reprezentuje pojedyncze wystąpienie aplikacji. Zasobniki zwykle mają 1:1 mapowania z kontenerem, chociaż istnieją zaawansowane scenariusze, w których część może zawierać wiele kontenerów. Te wielokontenerowe działy są planowane razem w tym samym węźle i umożliwiają kontenerom udostępnianie powiązanych zasobów.

Podczas tworzenia elementu pod można zdefiniować *limity zasobów* , aby zażądać określonej ilości zasobów procesora CPU lub pamięci. Harmonogram Kubernetes próbuje zaplanować uruchomienie programu w węźle z dostępnymi zasobami w celu spełnienia żądania. Można również określić maksymalne limity zasobów uniemożliwiające korzystanie z zbyt dużej ilości zasobów obliczeniowych z bazowego węzła. Najlepszym rozwiązaniem jest uwzględnienie limitów zasobów dla wszystkich zasobników, aby pomóc harmonogramowi Kubernetes zrozumieć, które zasoby są zbędne i dozwolone.

Aby uzyskać więcej informacji, zobacz [Kubernetes][kubernetes-pods] i [Kubernetes pod cykl życia][kubernetes-pod-lifecycle].

A pod jest zasobem logicznym, ale kontenery są, w których są uruchamiane obciążenia aplikacji. Zasobniki to zazwyczaj tymczasowe, jednorazowe zasoby, a indywidualnie zaplanowane zasobniki nie mogą mieć niektórych funkcji wysokiej dostępności i nadmiarowości Kubernetes. Zamiast tego są zwykle wdrażane i zarządzane przez *Kontrolery*Kubernetes, takie jak kontroler wdrażania.

## <a name="deployments-and-yaml-manifests"></a>Wdrożenia i manifesty YAML

*Wdrożenie* reprezentuje jeden lub więcej identycznych zasobników zarządzanych przez kontroler wdrażania Kubernetes. Wdrożenie definiuje liczbę *replik* (na podst.) do utworzenia, a usługa Kubernetes Scheduler gwarantuje, że w przypadku wystąpienia problemów z planami lub w węzłach w dobrej kondycji są planowane dodatkowe numery.

Możesz zaktualizować wdrożenia, aby zmienić konfigurację zasobników, używanego obrazu kontenera lub dołączonego magazynu. Kontroler wdrożenia opróżnia i kończy daną liczbę replik, tworzy repliki z nowej definicji wdrożenia i kontynuuje proces do momentu zaktualizowania wszystkich replik we wdrożeniu.

Większość aplikacji bezstanowych w AKS powinna używać modelu wdrażania zamiast planowania pojedynczych zasobników. Kubernetes może monitorować kondycję i stan wdrożeń, aby upewnić się, że wymagana liczba replik działa w ramach klastra. W przypadku zaplanowania tylko pojedynczych zasobników te nie są ponownie uruchamiane, Jeśli napotkają problem, i nie są ponownie planowane w węzłach o dobrej kondycji, jeśli ich bieżący węzeł napotka problem.

Jeśli aplikacja wymaga, aby kworum wystąpień było zawsze dostępne do podejmowania decyzji dotyczących zarządzania, nie chcesz, aby proces aktualizacji mógł przerwać tę możliwość. *Budżety* na zakłócenia mogą służyć do definiowania liczby replik w ramach wdrożenia, które mogą zostać wyłączone podczas uaktualniania aktualizacji lub węzła. Na przykład jeśli w danym wdrożeniu znajdują się *5* replik, można zdefiniować zakłócenia pod kątem wartości *4* , aby zezwolić na usunięcie lub ponowne zaplanowanie jednej repliki. Podobnie jak w przypadku limitów zasobów, najlepszym rozwiązaniem jest zdefiniowanie budżetów w aplikacjach, które wymagają minimalnej liczby replik, aby zawsze były obecne.

Wdrożenia są zwykle tworzone i zarządzane przy `kubectl create` użyciu `kubectl apply`programu lub. Aby utworzyć wdrożenie, należy zdefiniować plik manifestu w formacie YAML (YAML Ain't Markup Language). Poniższy przykład tworzy podstawowe wdrożenie serwera sieci Web NGINX. Wdrożenie określa *3* repliki do utworzenia, a port *80* będzie otwarty w kontenerze. Żądania zasobów i limity są również zdefiniowane dla procesora CPU i pamięci.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Bardziej złożone aplikacje można utworzyć, uwzględniając także usługi, takie jak moduły równoważenia obciążenia w manifeście YAML.

Aby uzyskać więcej informacji, zobacz [Kubernetes Deployments][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Zarządzanie pakietami za pomocą Helm

Typowym podejściem do zarządzania aplikacjami w programie Kubernetes jest [Helm][helm]. Możesz tworzyć i używać istniejących publicznych *wykresów* Helm, które zawierają spakowaną wersję kodu aplikacji i manifestów YAML Kubernetes do wdrażania zasobów. Te wykresy Helm mogą być przechowywane lokalnie lub często w repozytorium zdalnym, takim jak repozytorium wykresu programu [Azure Container Registry Helm][acr-helm].

Aby korzystać z Helm, składnik serwera o nazwieer jest instalowany w klastrze Kubernetes. Zarządzający do instalacji wykresów w klastrze. Klient Helm jest instalowany lokalnie na komputerze lub może być używany w [Azure Cloud Shell][azure-cloud-shell]. Możesz wyszukać i utworzyć wykresy Helm z klientem, a następnie zainstalować je w klastrze Kubernetes.

![Helm obejmuje składnik klienta i składnik do przydziałania po stronie serwera, który tworzy zasoby wewnątrz klastra Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Aby uzyskać więcej informacji, zobacz [Instalowanie aplikacji przy użyciu usługi Helm w usłudze Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets i DaemonSets

Kontroler wdrażania używa usługi Kubernetes Scheduler, aby uruchomić daną liczbę replik w dowolnym z dostępnych zasobów. Takie podejście do używania wdrożeń może być wystarczające dla aplikacji bezstanowych, ale nie dla aplikacji, które wymagają trwałej konwencji nazewnictwa lub magazynu. W przypadku aplikacji, które wymagają repliki w każdym węźle lub wybranych węzłów w klastrze, kontroler wdrożenia nie sprawdza, jak repliki są dystrybuowane między węzłami.

Istnieją dwa zasoby Kubernetes, które umożliwiają zarządzanie tymi typami aplikacji:

- *StatefulSets* — utrzymuje stan aplikacji poza indywidualnym cyklem życia, takim jak magazyn.
- *DaemonSets* — upewnij się, że uruchomione wystąpienie w każdym węźle jest wczesne w procesie ładowania początkowego Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Nowoczesne opracowywanie aplikacji jest często stosowane w przypadku aplikacji bezstanowych, ale *StatefulSets* można używać w przypadku aplikacji stanowych, takich jak aplikacje, które zawierają składniki bazy danych. StatefulSet jest podobny do wdrożenia w przypadku tworzenia co najmniej jednego identycznego zasobnika i zarządzania nim. Repliki w StatefulSet wykonują bezpieczne, sekwencyjne podejście do wdrożenia, skalowania, uaktualnienia i zakończenia. W przypadku StatefulSet, konwencji nazewnictwa, nazw sieci i magazynu utrwalają się, gdy replika jest zaplanowana.

Aplikację można zdefiniować w formacie YAML za pomocą `kind: StatefulSet`programu, a kontroler StatefulSet obsługuje wdrażanie wymaganych replik i zarządzanie nimi. Dane są zapisywane w magazynie trwałym, udostępnianym przez usługę Azure Managed Disks lub Azure Files. W przypadku programu StatefulSets podstawowy magazyn trwały pozostaje nawet wtedy, gdy StatefulSet zostanie usunięta.

Aby uzyskać więcej informacji, zobacz [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliki w StatefulSet są zaplanowane i uruchamiane w dowolnym z dostępnych węzłów w klastrze AKS. Jeśli chcesz upewnić się, że co najmniej jeden z tych zestawów jest uruchomiony w węźle, możesz zamiast tego użyć elementu daemonset.

### <a name="daemonsets"></a>DaemonSets

W przypadku konkretnych kolekcji dzienników lub potrzeb związanych z monitorowaniem może być konieczne uruchomienie danego elementu pod względem wszystkich lub wybranych węzłów. *Elementu daemonset* ponownie służy do wdrożenia co najmniej jednego identycznego zasobnika, ale kontroler elementu daemonset zapewnia, że każdy określony węzeł uruchamia wystąpienie elementu.

Przed uruchomieniem domyślnego harmonogramu Kubernetes w kontrolerze elementu daemonset można zaplanować w węzłach na wczesny proces rozruchu klastra. Pozwala to zagwarantować, że zasobniki w elementu daemonset są uruchamiane przed zaplanowaniem tradycyjnych zasobników we wdrożeniu lub StatefulSet.

Podobnie jak w przypadku StatefulSets, elementu daemonset jest definiowana jako część definicji YAML za `kind: DaemonSet`pomocą.

Aby uzyskać więcej informacji, zobacz [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> W przypadku używania [dodatku węzłów wirtualnych](virtual-nodes-cli.md#enable-virtual-nodes-addon)DaemonSets nie utworzy zasobnika w węźle wirtualnym.

## <a name="namespaces"></a>Przestrzenie nazw

Zasoby Kubernetes, takie jak grupy miar i wdrożenia, są logicznie pogrupowane w *przestrzeni nazw*. Dzięki tym grupom można logicznie podzielić klaster AKS i ograniczyć dostęp do tworzenia, wyświetlania i zarządzania zasobami. Można na przykład utworzyć przestrzenie nazw w celu oddzielenia grup firmy. Użytkownicy mogą korzystać tylko z zasobami w ramach przypisanych przestrzeni nazw.

![Kubernetes przestrzenie nazw, aby logicznie podzielić zasoby i aplikacje](media/concepts-clusters-workloads/namespaces.png)

Podczas tworzenia klastra AKS dostępne są następujące przestrzenie nazw:

- *default* — Ta przestrzeń nazw to miejsce, w którym domyślnie tworzone są i wdrożenia, gdy żaden z nich nie jest dostarczany. W mniejszych środowiskach można wdrażać aplikacje bezpośrednio w domyślnej przestrzeni nazw bez tworzenia dodatkowych rozbarwień logicznych. W przypadku korzystania z interfejsu API Kubernetes, takiego jak with `kubectl get pods`, domyślna przestrzeń nazw jest używana, gdy nie jest określony.
- *polecenia-system* — Ta przestrzeń nazw to miejsce, w którym istnieją podstawowe zasoby, takie jak usługa DNS i serwer proxy lub pulpit nawigacyjny Kubernetes. Zwykle nie są wdrażane własne aplikacje w tej przestrzeni nazw.
- *polecenia-Public* — Ta przestrzeń nazw zazwyczaj nie jest używana, ale może być używana do wyświetlania zasobów w całym klastrze i może być wyświetlana przez dowolnego użytkownika.

Aby uzyskać więcej informacji, zobacz [Kubernetes przestrzenie nazw][kubernetes-namespaces].

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono niektóre podstawowe składniki Kubernetes oraz sposób ich stosowania do klastrów AKS. Aby uzyskać dodatkowe informacje na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

- [Kubernetes/AKS, dostęp i tożsamość][aks-concepts-identity]
- [Zabezpieczenia Kubernetes/AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes/AKS][aks-concepts-network]
- [Magazyn Kubernetes/AKS][aks-concepts-storage]
- [Skala Kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md