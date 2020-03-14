---
title: Opisywanie klastra przy użyciu Menedżer zasobów klastra
description: Opisz klaster Service Fabric, określając domeny błędów, domeny uaktualnień, właściwości węzła i pojemności węzłów dla Menedżer zasobów klastra.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258774"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Opisywanie klastra Service Fabric przy użyciu Menedżer zasobów klastra
Funkcja Menedżer zasobów klastra platformy Azure Service Fabric oferuje kilka mechanizmów opisywania klastra:

* Domeny błędów
* Uaktualnij domeny
* Właściwości węzła
* Pojemności węzłów

W czasie wykonywania klaster Menedżer zasobów używa tych informacji w celu zapewnienia wysokiej dostępności usług uruchomionych w klastrze. Podczas wymuszania tych ważnych reguł próbuje również zoptymalizować użycie zasobów w klastrze.

## <a name="fault-domains"></a>Domeny błędów
Domena błędów jest dowolnym obszarem skoordynowanego błędu. Pojedynczy komputer jest domeną błędów. Może ona się nie powieść z różnych powodów, z błędów zasilacza awaryjnego do uszkodzenia oprogramowania układowego kart interfejsu sieciowego. 

Maszyny podłączone do tego samego przełącznika sieci Ethernet znajdują się w tej samej domenie błędów. Są to maszyny, które współużytkują pojedyncze źródło mocy lub w jednej lokalizacji. 

Ponieważ przyczyną błędów sprzętowych jest nakładanie się, domeny błędów są z natury hierarchiczne. Są one reprezentowane jako identyfikatory URI w Service Fabric.

Należy pamiętać, że domeny błędów są poprawnie skonfigurowane, ponieważ Service Fabric używają tych informacji do bezpiecznego umieszczania usług. Service Fabric nie chce umieszczać usług w taki sposób, że utrata domeny błędów (spowodowana uszkodzeniem składnika) powoduje, że usługa zostanie wyłączona. 

W środowisku platformy Azure Service Fabric używa informacji o domenie błędów zapewnianych przez środowisko w celu poprawnego skonfigurowania węzłów w klastrze w Twoim imieniu. W przypadku autonomicznych wystąpień Service Fabric domeny błędów są definiowane w momencie skonfigurowania klastra. 

> [!WARNING]
> Należy pamiętać, że informacje o domenie błędów udostępniane Service Fabric są dokładne. Załóżmy na przykład, że węzły klastra Service Fabric działają w 10 maszynach wirtualnych uruchomionych na 5 hostach fizycznych. W takim przypadku, nawet jeśli są 10 maszyn wirtualnych, istnieją tylko 5 różnych domen błędów (najwyższego poziomu). Udostępnienie tego samego hosta fizycznego powoduje, że maszyny wirtualne mogą współużytkować tę samą główną domenę błędów, ponieważ środowisko maszyny wirtualnej działa niepowodzeniem w przypadku awarii hosta fizycznego.  
>
> Service Fabric oczekuje, że domena błędów węzła nie zostanie zmieniona. Inne mechanizmy zapewniające wysoką dostępność maszyn wirtualnych, takich jak [ha-](https://technet.microsoft.com/library/cc967323.aspx)VM, mogą powodować konflikty z Service Fabric. Te mechanizmy używają przezroczystej migracji maszyn wirtualnych z jednego hosta na inny. Nie konfigurują one ponownie ani nie powiadamiają uruchomionego kodu w ramach maszyny wirtualnej. W związku z tym nie są one *obsługiwane* jako środowiska do uruchamiania klastrów Service Fabric. 
>
> Service Fabric powinna być jedyną zastosowanymi technologiami wysokiej dostępności. Takie mechanizmy jak migracja maszyn wirtualnych na żywo i sieci SAN nie są konieczne. Jeśli te mechanizmy są używane w połączeniu z Service Fabric, _zmniejszają_ dostępność i niezawodność aplikacji. Przyczyną jest wprowadzenie dodatkowej złożoności, dodanie scentralizowanych źródeł błędów i użycie strategii niezawodności i dostępności, które powodują konflikt z tymi w Service Fabric. 
>
>

Na poniższej ilustracji przedstawiono kolor wszystkich jednostek, które współtworzyją w domenach błędów i wyświetlają wszystkie różne domeny błędów. W tym przykładzie mamy centra danych ("DC"), stojaki ("R") i bloki ("B"). Jeśli każdy blok zawiera więcej niż jedną maszynę wirtualną, może istnieć inna warstwa w hierarchii domeny błędów.

<center>

![węzły zorganizowane za pośrednictwem domen błędów][Image1]
</center>

W czasie wykonywania Service Fabric klastra Menedżer zasobów traktuje domeny błędów w klastrze i plany planów. Repliki stanowe lub bezstanowe wystąpienia usługi są dystrybuowane w taki sposób, aby znajdowały się w osobnych domenach błędów. Dystrybucja usługi między domenami błędów gwarantuje, że dostępność usługi nie zostanie naruszona, gdy domena błędów ulegnie awarii na dowolnym poziomie hierarchii.

Menedżer zasobów klastra nie ma wpływu na liczbę warstw znajdujących się w hierarchii domeny błędów. Podejmuje próbę upewnienia się, że utrata jednej części hierarchii nie ma wpływu na uruchomione w niej usługi. 

Najlepiej, jeśli ta sama liczba węzłów jest na każdym poziomie głębokości w hierarchii domeny błędów. W przypadku niezrównoważenia "drzewa" domen błędów w klastrze trudniejsze jest Menedżer zasobów klastrów, aby ustalić najlepszą alokację usług. Niezrównoważone układy domeny błędów oznaczają, że utrata niektórych domen wpływa na dostępność usług więcej niż w innych domenach. W efekcie Menedżer zasobów klastra zostanie rozdarte między dwoma celami: 

* Chcą używać maszyn w tej domenie "ciężki", umieszczając w nich usługi. 
* Chce umieścić usługi w innych domenach, aby utrata domeny nie powodowała problemów. 

Jak wyglądają niezrównoważone domeny? Na poniższym diagramie przedstawiono dwa różne układy klastra. W pierwszym przykładzie węzły są dystrybuowane równomiernie w domenach błędów. W drugim przykładzie jedna domena błędów ma wiele więcej węzłów niż pozostałe domeny błędów. 

<center>

![dwa różne układy klastra][Image2]
</center>

Na platformie Azure wybór domeny błędów zawiera węzeł, który jest zarządzany przez Ciebie. Ale w zależności od liczby wdrożonych węzłów można nadal zakończyć z domenami błędów, które mają więcej węzłów niż w innych. 

Załóżmy na przykład, że w klastrze znajdują się pięć domen błędów, ale Zainicjowano obsługę siedmiu węzłów dla typu węzła (**NodeType**). W takim przypadku pierwsze dwie domeny błędów kończą się wieloma węzłami. W przypadku kontynuowania wdrażania większej liczby wystąpień **NodeType** z tylko kilkoma wystąpieniami ten problem będzie gorszy. Z tego powodu zalecamy, aby liczba węzłów w każdym typie węzła była wielokrotnością liczby domen błędów.

## <a name="upgrade-domains"></a>Uaktualnij domeny
Domeny uaktualnień to kolejna funkcja ułatwiająca Service Fabric klastra Menedżer zasobów zrozumienia układu klastra. Uaktualnij domeny definiują zestawy węzłów, które są uaktualnione w tym samym czasie. Uaktualnij domeny ułatwiają klastrowi Menedżer zasobów zrozumienie i organizowanie operacji zarządzania, takich jak uaktualnienia.

Domeny uaktualnień są bardzo podobne do domen błędów, ale z niewielkimi różnicami między kluczami. Najpierw obszary skoordynowanej awarii sprzętu definiują domeny błędów. Domeny uaktualnień, z drugiej strony, są definiowane przez zasady. Możesz zdecydować, ile potrzebujesz, zamiast zezwalać na dyktowanie w środowisku. Jako węzły można mieć dowolną liczbę domen uaktualnienia. Inna różnica między domenami błędów i domenami uaktualnienia polega na tym, że domeny uaktualnień nie są hierarchiczne. Zamiast tego są one bardziej podobne do tagów prostych. 

Na poniższym diagramie przedstawiono trzy domeny uaktualnienia rozłożone na trzy domeny błędów. Przedstawiono w nim również jedno możliwe rozmieszczenie dla trzech różnych replik usługi stanowej, w przypadku których każdy z nich jest zakończony w różnych domenach błędów i uaktualnień. To umieszczanie umożliwia utratę domeny błędów w trakcie uaktualniania usługi i nadal ma jedną kopię kodu i danych.  

<center>

![umieszczanie w domenach błędów i uaktualnień][Image3]
</center>

Istnieją pewne wady i zalety posiadania dużej liczby domen uaktualnienia. Więcej domen uaktualnienia oznacza, że każdy krok uaktualnienia jest bardziej szczegółowy i ma wpływ na mniejszą liczbę węzłów lub usług. Mniejsza liczba usług musi być jednocześnie przenoszona do systemu. Pozwala to zwiększyć niezawodność, ponieważ w ramach uaktualnienia występuje problem z mniejszą częścią usługi. Więcej domen uaktualnienia oznacza również, że potrzebujesz mniej dostępnego buforu w innych węzłach, aby obsługiwać wpływ uaktualnienia. 

Na przykład jeśli masz pięć domen uaktualnienia, węzły w każdej z nich obsługują około 20 procent ruchu. Jeśli konieczne jest podjęcie tej domeny uaktualnienia do uaktualnienia, to obciążenie zwykle musi znajdować się w dowolnym miejscu. Ponieważ istnieją cztery pozostałe domeny uaktualnienia, każdy z nich musi mieć miejsce na około 5% całkowitego ruchu sieciowego. Więcej domen uaktualnienia oznacza, że potrzebujesz mniej buforów w węzłach klastra. 

Należy rozważyć, czy w zamian była 10 domen uaktualnienia. W takim przypadku Każda domena uaktualnienia będzie obsługiwała tylko 10% całkowitego ruchu sieciowego. Po wykonaniu kroków uaktualniania w klastrze Każda domena musiałaby mieć miejsce tylko w przypadku 1,1% całkowitego ruchu sieciowego. Więcej domen uaktualnienia zwykle umożliwia uruchamianie węzłów przy wyższym wykorzystaniu, ponieważ wymaga mniej zarezerwowanej pojemności. Ta sama wartość dotyczy domen błędów.  

Minusem z wieloma domenami uaktualnienia polega na tym, że uaktualnienia mogą trwać dłużej. Service Fabric czeka na krótki czas po zakończeniu uaktualniania domeny i przeprowadza sprawdzenia przed rozpoczęciem uaktualniania kolejnego. Te opóźnienia umożliwiają wykrywanie problemów wprowadzonych podczas uaktualniania przed kontynuowaniem uaktualniania. Kompromis jest akceptowalny, ponieważ zapobiega nieprawidłowym zmianom wpływającym na zbyt znaczną część usługi w danym momencie.

Obecność zbyt małej liczby domen uaktualnienia ma wiele negatywnych efektów ubocznych. Chociaż każda domena uaktualnienia nie działa i uaktualniana, duża część ogólnej pojemności jest niedostępna. Na przykład jeśli masz tylko trzy domeny uaktualnienia, możesz w danym momencie zacząć korzystać z jednej trzeciej ogólnej pojemności usługi lub klastra. Duża część usługi nie jest jeszcze pożądana, ponieważ w pozostałej części klastra potrzebujesz wystarczającej pojemności do obsługi obciążenia. Utrzymywanie tego buforu oznacza, że podczas normalnego działania te węzły są mniej załadowane niż w przeciwnym razie. Zwiększa to koszt działania usługi.

Nie istnieje rzeczywisty limit łącznej liczby domen błędów lub uaktualnień w środowisku lub ograniczeń dotyczących sposobu nakładania się. Istnieją jednak typowe wzorce:

- Domeny błędów i domeny uaktualnienia mapowane 1:1
- Jedna domena uaktualnienia na węzeł (wystąpienie fizyczne lub wirtualne systemu operacyjnego)
- Model "rozłożony" lub "macierz", w którym domeny błędów i domeny uaktualnień tworzą macierz z maszynami, na których zazwyczaj działa przekątne

<center>

![układy domen błędów i uaktualnień][Image4]
</center>

Nie ma najlepszej odpowiedzi dla wybranego układu. Każdy z nich ma wady i zalety. Na przykład model 1FD: 1UD jest prosty do skonfigurowania. Model jednej domeny uaktualnienia na model węzła jest najbardziej podobny do tego, do czego służą. Podczas uaktualniania każdy węzeł jest aktualizowany niezależnie. Jest to podobne do tego, jak małe zestawy maszyn zostały uaktualnione ręcznie w przeszłości.

Najbardziej typowym modelem jest macierz FD/UD, w której domeny błędów i domeny uaktualnień tworzą tabelę i węzły są umieszczane wzdłuż przekątnej. Jest to model używany domyślnie w klastrach Service Fabric na platformie Azure. W przypadku klastrów z wieloma węzłami wszystko jest zakończyło się jak gęsty wzorzec macierzy.

> [!NOTE]
> Klastry Service Fabric hostowane na platformie Azure nie obsługują zmiany domyślnej strategii. Tylko klastry autonomiczne oferują takie dostosowanie.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Ograniczenia dotyczące błędów i uaktualniania domen oraz zachowanie wyników
### <a name="default-approach"></a>Domyślne podejście
Domyślnie klaster Menedżer zasobów utrzymuje usługi zrównoważone w domenach błędów i uaktualnień. Jest to powiązane z modelem jako [ograniczeniem](service-fabric-cluster-resource-manager-management-integration.md). Ograniczenie dla domeny błędów i uaktualniania stanów: "dla danej partycji usługi nie powinna być nigdy większa niż jedna w liczbie obiektów usługi (bezstanowe wystąpienia usługi lub repliki usługi stanowej) między dowolnymi dwiema domenami w tej samej poziom hierarchii ".

Załóżmy, że to ograniczenie zapewnia gwarancję "Maksymalna różnica". Ograniczenie dotyczące domen błędów i uaktualniania uniemożliwia pewne przeniesienia lub uzgodnienia, które naruszają daną regułę.

Załóżmy na przykład, że mamy klaster z sześcioma węzłami, który został skonfigurowany z pięcioma domenami błędów i pięcioma domenami uaktualnienia.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Teraz Załóżmy, że tworzymy usługę z **wartość targetreplicasetsize** (lub w przypadku usługi bezstanowej **InstanceCount**) równą pięciu. Repliki są naładowywane na N1-N5. W rzeczywistości N6 nigdy nie jest używana bez względu na to, ile usług takie jak to zostało utworzone. Ale dlaczego? Przyjrzyjmy się różnicy między bieżącym układem i co się stanie, jeśli wybrano N6.

Oto układ i łączna liczba replik na awarię i domenę uaktualnienia:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ten układ jest zrównoważony w odniesieniu do węzłów na domenę błędów i domenę uaktualnienia. Jest ona również równoważna liczbie replik na awarię i domenę uaktualnienia. Każda domena ma taką samą liczbę węzłów i taką samą liczbę replik.

Teraz przyjrzyjmy się temu, co się stanie, jeśli będziemy używać N6 zamiast N2. Jak będą dystrybuowane repliki?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Ten układ narusza definicję gwarancji "maksymalnej różnicy" dla ograniczenia domeny błędów. FD0 ma dwie repliki, a FD1 ma wartość zero. Różnica między FD0 i FD1 jest sumą dwóch, która jest większa niż maksymalna różnica jednego z nich. Ponieważ ograniczenie jest naruszone, Menedżer zasobów klastra nie zezwala na to rozwiązanie. Podobnie w przypadku wybrania wartości N2 i N6 (zamiast N1 i N2) będziemy:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ten układ jest zrównoważony w odniesieniu do domen błędów. Jednak teraz narusza ograniczeń domeny uaktualnienia, ponieważ UD0 ma zero replik, a UD1 ma dwa. Ten układ jest również nieprawidłowy i nie jest wybierany przez Menedżer zasobów klastra.

Takie podejście do dystrybucji replik stanowych lub wystąpień bezstanowych zapewnia najlepszą możliwą odporność na uszkodzenia. Jeśli jedna domena ulegnie awarii, minimalna liczba replik/wystąpień zostanie utracona. 

Z drugiej strony takie podejście może być zbyt rygorystyczne i nie zezwalać klastrowi na korzystanie ze wszystkich zasobów. W przypadku niektórych konfiguracji klastra nie można użyć niektórych węzłów. Może to spowodować, że Service Fabric nie należy umieszczać usług, co powoduje wyświetlenie komunikatów ostrzegawczych. W poprzednim przykładzie niektóre węzły klastra nie mogą być używane (N6 w tym przykładzie). Nawet jeśli dodano węzły do tego klastra (N7-N10), repliki/wystąpienia byłyby umieszczane tylko na N1 – N5 z powodu ograniczeń dotyczących domen błędów i uaktualnień. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Podejście alternatywne

Menedżer zasobów klastra obsługuje inną wersję ograniczenia dla domen błędów i uaktualniania. Pozwala to na umieszczanie przy zachowaniu minimalnego poziomu bezpieczeństwa. Alternatywne ograniczenie można określić w następujący sposób: "w przypadku danej partycji usług dystrybucja repliki między domenami powinna mieć pewność, że partycja nie ponosi utraty kworum". Załóżmy, że to ograniczenie zapewnia gwarancję "bezpieczny" kworum. 

> [!NOTE]
> W przypadku usługi stanowej definiujemy *utratę kworum* w sytuacji, gdy większość replik partycji nie działa w tym samym czasie. Na przykład jeśli **wartość targetreplicasetsize** wynosi pięć, zestaw wszystkich trzech replik reprezentuje kworum. Podobnie, jeśli **wartość targetreplicasetsize** jest sześć, cztery repliki są niezbędne do kworum. W obu przypadkach nie ma więcej niż dwóch replik w tym samym czasie, jeśli partycja chce kontynuować normalne działanie. 
>
> W przypadku usługi bezstanowej nie jest to równoznaczne z *utratą kworum*. Usługi bezstanowe kontynuują normalne działanie nawet wtedy, gdy większość wystąpień jest w tym samym czasie. Dlatego będziemy skupić się na usługach stanowych w pozostałej części tego artykułu.
>

Wróćmy do poprzedniego przykładu. W przypadku wersji ograniczenia "bezpieczny dla kworum" wszystkie trzy układy będą prawidłowe. Nawet jeśli FD0 nie powiodło się w drugim układzie lub UD1 nie powiodło się w trzecim układzie, partycja nadal będzie mieć kworum. (Większość replik nadal będzie działać). W przypadku tej wersji ograniczenia N6 można prawie zawsze używać.

Podejście "bezpieczne kworum" zapewnia większą elastyczność niż podejście "z maksymalną różnicą". Przyczyną jest łatwiejsze znalezienie dystrybucji replik, które są prawidłowe w prawie każdej topologii klastra. Jednakże takie podejście nie gwarantuje najlepszych cech odporności na uszkodzenia, ponieważ niektóre błędy są gorsze niż inne. 

W scenariuszu najgorszego przypadku większość replik może zostać utracona w przypadku awarii jednej domeny i jednej dodatkowej repliki. Na przykład zamiast trzech niepowodzeń jest wymagane, aby utracić kworum z pięcioma replikami lub wystąpieniami, można teraz utracić większość z zaledwie dwoma awariami. 

### <a name="adaptive-approach"></a>Podejście adaptacyjne
Ponieważ oba podejścia mają mocne i słabe zalety, wprowadziliśmy podejście adaptacyjne, które łączy te dwie strategie.

> [!NOTE]
> Jest to domyślne zachowanie zaczynające się od Service Fabric w wersji 6,2. 
> 
> Podejście adaptacyjne używa logiki "Maksymalna różnica" domyślnie i przełącza do logiki "bezpieczny kworum" tylko wtedy, gdy jest to konieczne. Menedżer zasobów klastra automatycznie określa strategię, która jest niezbędna, sprawdzając, w jaki sposób klaster i usługi są skonfigurowane.
> 
> Menedżer zasobów klastra powinna używać logiki "kworum opartej na protokole" dla usługi obu następujących warunków jest spełnionych:
>
> * **Wartość targetreplicasetsize** dla usługi są równo widoczne przez liczbę domen błędów i liczbę domen uaktualnienia.
> * Liczba węzłów jest mniejsza lub równa liczbie domen błędów pomnożonych przez liczbę domen uaktualnienia.
>
> Należy pamiętać, że Menedżer zasobów klastra będzie używać tego podejścia zarówno dla usług bezstanowych, jak i stanowych, nawet jeśli utrata kworum nie jest odpowiednia dla usług bezstanowych.

Wróćmy do poprzedniego przykładu i założono, że klaster ma teraz osiem węzłów. Klaster jest nadal skonfigurowany z pięcioma domenami błędów i pięcioma domenami uaktualnienia, a wartość **wartość targetreplicasetsize** usługi hostowanej w tym klastrze pozostaje pięć. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Ponieważ wszystkie wymagane warunki są spełnione, Menedżer zasobów klastra będzie używać logiki "kworum opartej" w dystrybucji usługi. Umożliwia to użycie N6-N8. Jedną z możliwych dystrybucji usług w tym przypadku może wyglądać następująco:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Jeśli wartość **wartość targetreplicasetsize** usługi zostanie zredukowana do czterech (na przykład), klaster Menedżer zasobów zobaczy zmianę. Zostanie ona wznowiona przy użyciu logiki "maksymalnej różnicy", ponieważ **wartość targetreplicasetsize** nie jest podzielna przez liczbę domen błędów i domeny uaktualnienia. W związku z tym niektóre przesunięcia replik będą rozpowszechniać pozostałe cztery repliki w węzłach N1-N5. W ten sposób nie naruszono wersji "maksymalnej różnicy" w domenie błędów i logiki domeny uaktualnienia. 

W poprzednim układzie, jeśli wartość **wartość targetreplicasetsize** jest równa pięć, a N1 jest usuwana z klastra, liczba domen uaktualnienia będzie taka sama jak cztery. Ponownie klaster Menedżer zasobów zaczyna używać logiki "maksymalnej różnicy", ponieważ liczba domen uaktualnienia nie dzieli jeszcze wartości **wartość targetreplicasetsize** usługi. W związku z tym, replika R1, w przypadku ponownego skompilowania, musi być wykorzystana z N4, aby ograniczenia dotyczące domeny błędów i uaktualniania nie zostały naruszone.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurowanie błędów i domen uaktualnienia
W przypadku wdrożeń Service Fabric hostowanych na platformie Azure domeny błędów i domeny uaktualnień są definiowane automatycznie. Service Fabric pobiera i używa informacji o środowisku z platformy Azure.

Jeśli tworzysz własny klaster (lub chcesz uruchomić konkretną topologię w programowaniu), możesz samodzielnie udostępnić domenę błędów i informacje o domenie uaktualnienia. W tym przykładzie definiujemy lokalny klaster programistyczny z dziewięcioma węzłami, który obejmuje trzy centra danych (z trzema stojakami). Ten klaster ma również trzy domeny uaktualnienia rozłożone na te trzy centra danych. Oto przykład konfiguracji w ClusterManifest. XML:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

Ten przykład używa ClusterConfig. JSON dla wdrożeń autonomicznych:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Podczas definiowania klastrów za pośrednictwem Azure Resource Manager platforma Azure przypisuje domeny błędów i domeny uaktualnień. Dlatego definicja typów węzłów i zestawów skalowania maszyn wirtualnych w szablonie Azure Resource Manager nie zawiera informacji o domenie błędów lub domenie uaktualnienia.
>

## <a name="node-properties-and-placement-constraints"></a>Właściwości węzła i ograniczenia umieszczania
Czasami (w przypadku większości czasu) należy upewnić się, że pewne obciążenia są uruchamiane tylko na niektórych typach węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesora GPU lub dysków SSD, a inne mogą nie być. 

Doskonałym przykładem określania sprzętu dla konkretnych obciążeń jest niemal każda architektura n-warstwowa. Niektóre maszyny służą jako fronton lub strona obsługująca interfejs API aplikacji i są udostępniane klientom lub w Internecie. Różne komputery, często z różnymi zasobami sprzętowymi, obsługują zadania obliczeniowe i warstwy magazynu. Zwykle _nie_ są one ujawniane bezpośrednio klientom ani internetowi. 

Service Fabric oczekuje, że w niektórych przypadkach może być konieczne uruchomienie konkretnych obciążeń w określonych konfiguracjach sprzętu. Na przykład:

* Istniejąca aplikacja n-warstwowa została podniesiona i przeniesiona do środowiska Service Fabric.
* Obciążenie musi być uruchamiane na określonym sprzęcie dla powodów związanych z wydajnością, skalą lub izolacją zabezpieczeń.
* Obciążenie powinno być odizolowane od innych obciążeń związanych z zasadami lub użyciem zasobów.

Aby zapewnić obsługę tych rodzajów konfiguracji, Service Fabric zawiera Tagi, które można zastosować do węzłów. Te Tagi są nazywane *właściwościami węzła*. *Ograniczenia umieszczania* to instrukcje dołączone do poszczególnych usług wybranych dla jednej lub wielu właściwości węzła. Ograniczenia umieszczania definiują, gdzie mają być uruchamiane usługi. Zestaw ograniczeń jest rozszerzalny. Wszystkie pary klucz/wartość mogą funkcjonować. 

<center>

![różnych obciążeń dla układu klastra][Image5]
</center>

### <a name="built-in-node-properties"></a>Właściwości wbudowanego węzła
Service Fabric definiuje niektóre domyślne właściwości węzła, które mogą być używane automatycznie, aby nie trzeba było ich definiować. Domyślne właściwości zdefiniowane w każdym węźle to **NodeType** i **nodename**. 

Można na przykład napisać ograniczenie umieszczania jako `"(NodeType == NodeType03)"`. **NodeType** jest często używaną właściwością. Jest to przydatne, ponieważ odpowiada 1:1 z typem maszyny. Każdy typ maszyny odpowiada typowi obciążenia w tradycyjnej aplikacji n-warstwowej.

<center>

![ograniczenia umieszczania i właściwości węzła][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Ograniczenia umieszczania i składnia właściwości węzła 
Wartość określona we właściwości węzła może być ciągiem, wartością logiczną lub cyfrą. Instrukcja w usłudze jest nazywana *ograniczeniem* umieszczania, ponieważ ogranicza miejsce, w którym usługa może być uruchamiana w klastrze. Ograniczenie może być dowolną logiczną instrukcją, która operuje na właściwościach węzła w klastrze. Prawidłowe selektory w tych instrukcjach logicznych są następujące:

* Sprawdzanie warunkowe do tworzenia określonych instrukcji:

  | Wyciąg | Składnia |
  | --- |:---:|
  | "równa się" | "==" |
  | "nie równa się" | "!=" |
  | "większe niż" | ">" |
  | "większe niż lub równe" | ">=" |
  | "mniejsze niż" | "<" |
  | "mniejsze niż lub równe" | "<=" |

* Instrukcje logiczne dla operacji grupowania i logicznego:

  | Wyciąg | Składnia |
  | --- |:---:|
  | lub | "&&" |
  | oraz | "&#124;&#124;" |
  | niemożliwe | "!" |
  | "Grupuj jako pojedynczą instrukcję" | "()" |

Poniżej przedstawiono kilka przykładów podstawowych instrukcji ograniczeń:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Tylko węzły, w których ogólna instrukcja ograniczenia położenia ma wartość "true", mogą mieć w niej umieszczony usługi. Węzły, które nie mają zdefiniowanej właściwości, nie pasują do żadnego ograniczenia umieszczania, które zawiera właściwość.

Załóżmy, że następujące właściwości węzła zostały zdefiniowane dla typu węzła w ClusterManifest. XML:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

W poniższym przykładzie przedstawiono właściwości węzła zdefiniowane za pośrednictwem ClusterConfig. JSON dla wdrożeń autonomicznych lub Template. JSON dla klastrów hostowanych na platformie Azure. 

> [!NOTE]
> W szablonie Azure Resource Manager typ węzła jest zwykle sparametryzowane. Będzie wyglądać tak, jak `"[parameters('vmNodeType1Name')]"`, a nie NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

*Ograniczenia* dotyczące umieszczania w usłudze można utworzyć w następujący sposób:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Jeśli wszystkie węzły NodeType01 są prawidłowe, można również wybrać ten typ węzła z ograniczeniem `"(NodeType == NodeType01)"`.

Ograniczenia dotyczące umieszczania usługi mogą być aktualizowane dynamicznie w czasie wykonywania. Jeśli zachodzi taka potrzeba, można przenieść usługę w klastrze, dodać i usunąć wymagania i tak dalej. Service Fabric zapewnia, że usługa zostanie udostępniona i jest dostępna nawet po wprowadzeniu tych zmian.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Ograniczenia umieszczania są określone dla każdego nazwanego wystąpienia usługi. Aktualizacje zawsze mają miejsce (overwrite), co zostało wcześniej określone.

Definicja klastra definiuje właściwości węzła. Zmiana właściwości węzła wymaga uaktualnienia do konfiguracji klastra. Uaktualnienie właściwości węzła wymaga ponownego uruchomienia każdego węzła, którego to dotyczy, aby zgłosić nowe właściwości. Program Service Fabric zarządza tymi uaktualnieniami stopniowymi.

## <a name="describing-and-managing-cluster-resources"></a>Opisywanie zasobów klastra i zarządzanie nimi
Jednym z najważniejszych zadań każdego programu Orchestrator jest ułatwienie zarządzania użyciem zasobów w klastrze. Zarządzanie zasobami klastra może oznaczać kilka różnych rzeczy. 

Najpierw należy upewnić się, że maszyny nie są przeciążone. Oznacza to, że na komputerach nie jest uruchomiona więcej usług niż można obsłużyć. 

Po drugie, istnieje możliwość równoważenia i optymalizacji, co jest niezwykle ważne do wydajnego uruchamiania usług. Oferty usługi ekonomicznej lub dotyczącej wydajności nie mogą zezwalać na gorącą część węzłów, podczas gdy inne są zimne. Hot nodes prowadzi do rywalizacji o zasoby i niskiej wydajności. Zimne węzły przedstawiają zasoby niezajęte i zwiększają koszty. 

Service Fabric reprezentuje zasoby jako *metryki*. Metryki to zasoby logiczne lub fizyczne, które mają zostać opisane Service Fabric. Przykłady metryk to "WorkQueueDepth" lub "MemoryInMb". Aby uzyskać informacje o zasobach fizycznych, które Service Fabric mogą zarządzać na węzłach, zobacz temat [Zarządzanie zasobami](service-fabric-resource-governance.md). Aby uzyskać informacje dotyczące metryk domyślnych używanych przez klaster Menedżer zasobów i sposobu konfigurowania metryk niestandardowych, zobacz [ten artykuł](service-fabric-cluster-resource-manager-metrics.md).

Metryki różnią się od ograniczeń umieszczania i właściwości węzła. Właściwości węzła to statyczne deskryptory węzłów. Metryki opisują zasoby, które znajdują się w węzłach i których usługi używają, gdy są uruchamiane w węźle. Właściwość węzła może być **HasSSD** i może być ustawiona na wartość true lub false. Ilość miejsca dostępnego na tym dysku SSD i ilość zużywanej przez usługi byłyby metryką taką jak "DriveSpaceInMb". 

Podobnie jak w przypadku ograniczeń umieszczania i właściwości węzła, Service Fabric Menedżer zasobów klastra nie rozumie, jakie są nazwy metryk. Nazwy metryk są tylko ciągami. Dobrym sposobem jest zadeklarowanie jednostek jako części nazw metryk tworzonych, gdy mogą one być niejednoznaczne.

## <a name="capacity"></a>Pojemność
Jeśli wyłączysz opcję wszystkie *równoważenie*zasobów, Service Fabric klaster Menedżer zasobów nadal upewnij się, że żaden węzeł nie przejdzie na jego pojemność. Zarządzanie przedziałami pojemności jest możliwe, chyba że klaster jest zbyt pełny lub obciążenie jest większe niż w żadnym węźle. Pojemność jest innym *ograniczeniem* , którego Menedżer zasobów klaster używa do zrozumienia, jak część zasobu ma węzeł. Pozostała pojemność jest również śledzona dla klastra jako całości. 

Zarówno pojemność, jak i zużycie na poziomie usługi są wyrażane jako metryki. Na przykład Metryka może być "ClientConnections", a węzeł może mieć pojemność "ClientConnections" 32 768. Inne węzły mogą mieć inne limity. Usługa uruchomiona w tym węźle może wypowiedzieć, że aktualnie zużywa 32 256 metryki "ClientConnections".

W czasie wykonywania klaster Menedżer zasobów śledzi pozostałą pojemność w klastrze i w węzłach. W celu śledzenia pojemności klaster Menedżer zasobów odejmuje użycie poszczególnych usług od pojemności węzła, w której działa usługa. Korzystając z tych informacji, Menedżer zasobów klastra może ustalić, gdzie należy umieścić lub przenieść repliki, aby węzły nie przechodzą na pojemność.

<center>

![węzły klastra i][Image7]
pojemności </center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Możesz zobaczyć pojemności zdefiniowane w manifeście klastra. Oto przykład dla ClusterManifest. XML:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Oto przykład pojemności zdefiniowanych za pośrednictwem ClusterConfig. JSON dla wdrożeń autonomicznych lub Template. JSON dla klastrów hostowanych na platformie Azure: 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Obciążenie usługi często zmienia się dynamicznie. Załóżmy, że ładowanie repliki "ClientConnections" zostało zmienione z 1 024 na 2 048. Węzeł, na którym był uruchomiony, miał pojemność tylko 512 pozostałej dla tej metryki. Teraz, gdy replika lub lokalizacja wystąpienia jest nieprawidłowa, ponieważ nie ma wystarczającej ilości miejsca na tym węźle. Menedżer zasobów klastra musi odwrócić węzeł poniżej pojemności. Zmniejsza obciążenie węzła, który przekracza pojemność przez przeniesienie jednej lub więcej replik lub wystąpień z tego węzła do innych węzłów. 

Menedżer zasobów klastra próbuje zminimalizować koszt przeniesienia replik. Możesz dowiedzieć się więcej o [kosztach przenoszenia](service-fabric-cluster-resource-manager-movement-cost.md) oraz o [rerównoważeniu strategii i regułach](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Pojemność klastra
W jaki sposób klaster Service Fabric Menedżer zasobów utrzymywać, że ogólny klaster nie jest zbyt pełny? Dzięki obciążeniu dynamicznym nie ma możliwości wykonywania wielu operacji. Usługi mogą być wykonywane niezależnie od akcji, które Menedżer zasobów w klastrze. W związku z tym klaster o dużej wysokości może być nieobecny, jeśli istnieje gwałtowny skok. 

Kontrolki w Menedżer zasobów klastra pomagają zapobiegać problemom. W pierwszej kolejności można zapobiec tworzeniu nowych obciążeń, które spowodują, że klaster stanie się pełny.

Załóżmy, że tworzysz usługę bezstanową i jest z nią skojarzona pewna obciążenie. Usługa dba o metryki "DiskSpaceInMb". Usługa będzie korzystać z pięciu jednostek "DiskSpaceInMb" dla każdego wystąpienia usługi. Chcesz utworzyć trzy wystąpienia usługi. Oznacza to, że wymagane jest 15 jednostek "DiskSpaceInMb" w klastrze, aby można było nawet utworzyć te wystąpienia usługi.

Klaster Menedżer zasobów ciągle oblicza pojemności i zużycia każdej metryki, aby można było określić pozostałą pojemność w klastrze. Jeśli nie ma wystarczającej ilości miejsca, klaster Menedżer zasobów odrzuca wywołanie tworzenia usługi.

Ponieważ wymaganie dotyczy tylko 15 jednostek, można przydzielić ten obszar na wiele różnych sposobów. Na przykład może istnieć jedna pozostała jednostka pojemności dla 15 różnych węzłów lub trzy pozostałe jednostki pojemności w pięciu różnych węzłach. Jeśli Menedżer zasobów klastra może zmienić rozmieszczenie elementów, tak aby na trzech węzłach była dostępnych pięć jednostek, usługa zostanie umieszczona. Ponowne rozmieszczanie klastra jest zazwyczaj możliwe, chyba że klaster jest prawie pełny lub nie można skonsolidować istniejących usług z jakiegoś powodu.

## <a name="buffered-capacity"></a>Buforowana pojemność
Buforowana pojemność jest kolejną funkcją Menedżer zasobów klastrów. Umożliwia rezerwację części ogólnej pojemności węzła. Ten bufor pojemności służy tylko do umieszczania usług podczas uaktualnień i awarii węzłów. 

Buforowana pojemność jest określana globalnie na metrykę dla wszystkich węzłów. Wartość, którą wybierasz dla zarezerwowanej pojemności, to funkcja liczby domen błędów i uaktualniania znajdujących się w klastrze. Więcej domen błędów i uaktualnień oznacza, że można wybrać mniejszą liczbę dla buforowanej pojemności. Jeśli masz więcej domen, możesz oczekiwać, że mniejsza ilość klastra będzie niedostępna podczas uaktualniania i niepowodzeń. Określanie pojemności buforowanej ma sens tylko wtedy, gdy określono również pojemność węzła dla metryki.

Oto przykład sposobu określania pojemności buforowanej w ClusterManifest. XML:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Oto przykład sposobu określania pojemności pamięci podręcznej za pośrednictwem ClusterConfig. JSON dla wdrożeń autonomicznych lub pliku Template. JSON dla klastrów hostowanych na platformie Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

Tworzenie nowych usług kończy się niepowodzeniem, gdy w klastrze jest niebuforowana pojemność dla metryki. Uniemożliwianie tworzenia nowych usług w celu zachowania bufora gwarantuje, że uaktualnienia i błędy nie powodują, że węzły nie przechodzą z pojemności. Buforowana pojemność jest opcjonalna, ale zalecamy ją w dowolnym klastrze, który definiuje pojemność dla metryki.

Menedżer zasobów klastra uwidacznia te informacje o ładowaniu. Dla każdej metryki te informacje obejmują: 
- Ustawienia pojemności w buforze.
- Całkowita pojemność.
- Bieżące użycie.
- Czy każda Metryka jest uznawana za zrównoważoną, czy nie.
- Statystyka odchylenia standardowego.
- Węzły o największej i najmniejszej obciążeniu.  
  
Poniższy kod przedstawia przykład tego wyjścia:

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać informacje o architekturze i przepływie informacji w Menedżer zasobów klastra, zobacz [Omówienie architektury Menedżer zasobów klastra](service-fabric-cluster-resource-manager-architecture.md).
* Definiowanie metryk defragmentacji jest jednym ze sposobów konsolidacji obciążenia węzłów zamiast rozproszenia go. Aby dowiedzieć się, jak skonfigurować defragmentację, zobacz [defragmentacja metryk i ładowanie w Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Zacznij od początku i [zapoznaj się z wprowadzeniem do Service Fabric klastra Menedżer zasobów](service-fabric-cluster-resource-manager-introduction.md).
* Aby dowiedzieć się, jak Menedżer zasobów klaster zarządza i równoważenia obciążenia w klastrze, zobacz [równoważenie Service Fabric klastra](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
