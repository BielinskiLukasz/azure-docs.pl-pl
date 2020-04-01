---
title: Tworzenie zespołu przy użyciu node.js i Visual Studio Code
services: azure-dev-spaces
ms.date: 07/09/2018
ms.topic: tutorial
description: W tym samouczku pokazano, jak używać usługi Azure Dev Spaces i Visual Studio Code do tworzenia zespołu w aplikacji Node.js w usłudze Azure Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s '
ms.openlocfilehash: abcf4934af056d508ac136f80758597294d40b1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251933"
---
# <a name="team-development-using-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Tworzenie zespołu przy użyciu node.js i visual studio code z usługi Azure Dev Spaces

Z tego samouczka dowiesz się, jak zespół deweloperów może równocześnie współpracować w tym samym klastrze Kubernetes przy użyciu usługi Dev Spaces.

## <a name="learn-about-team-development"></a>Więcej informacji na temat programowania zespołowego
Do tej pory kod aplikacji był uruchamiany tak, jakby nad aplikacją pracował tylko jeden deweloper. W tej sekcji dowiesz się, jak usługa Azure Dev Spaces upraszcza programowanie zespołowe:
* Pozwól zespołowi deweloperów pracować w tym samym środowisku: w zależności od potrzeb w udostępnionej przestrzeni deweloperskiej lub w odrębnych przestrzeniach deweloperskich.
* Środowisko umożliwia każdemu deweloperowi iterację na jego kodzie w izolacji i bez obaw o przeszkadzanie pozostałym.
* Przed zatwierdzeniem kodu można go kompleksowo przetestować bez konieczności tworzenia makiet czy symulowania zależności.

### <a name="challenges-with-developing-microservices"></a>Problemy z tworzeniem mikrousług
W tej chwili przykładowa aplikacja nie jest zbyt skomplikowana. Jednak w rzeczywistym środowisku deweloperskim w miarę dodawania kolejnych usług i rozwoju zespołu programistycznego szybko pojawiają się problemy. Uruchomienie ich wszystkich lokalnie na potrzeby programowania może okazać się nierealne.

* Maszyna deweloperska może nie mieć wystarczających zasobów, aby uruchamiać jednocześnie wszystkie potrzebne usługi.
* W przypadku niektórych usług może być wymagana dostępność publiczna. Na przykład w usłudze może być wymagany punkt końcowy, który odpowiada na element webhook.
* Jeśli chcesz uruchomić podzestaw usług, musisz znać pełną hierarchię zależności między wszystkimi swoimi usługami. Określenie tej hierarchii może być trudne, szczególnie, jeśli liczba usług się zwiększa.
* Niektórzy deweloperzy uciekają się do symulowania lub budowania makiet wielu zależności swoich usług. Takie podejście może pomóc, jednak zarządzanie tymi makietami może szybko zwiększyć koszt programowania. Ponadto takie podejście prowadzi do tego, że środowisko deweloperskie jest inne, niż środowisko produkcyjne, co może prowadzić do występowania drobnych błędów.
* Z tego względu przeprowadzenie jakiegokolwiek testu integracji staje się trudne. Testowanie integracji realnie może nastąpić po zatwierdzeniu, co oznacza, że problemy pojawią się na dalszych etapach cyklu programowania.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Praca w udostępnionej przestrzeni deweloperskiej
W usłudze Azure Dev Spaces możesz skonfigurować *udostępnione* przestrzenie deweloperskie na platformie Azure. Każdy deweloper może skupić się wyłącznie na swojej części aplikacji oraz iteracyjnie tworzyć *wstępnie zatwierdzony kod* w przestrzeni deweloperskiej zawierającej już wszystkie pozostałe usługi i zasoby w chmurze, od których zależą jego scenariusze. Zależności są zawsze aktualne, a deweloperzy pracują w sposób odzwierciedlający środowisko produkcyjne.

### <a name="work-in-your-own-space"></a>Praca we własnym obszarze
Kiedy tworzysz kod dla swojej usługi i nie jest on jeszcze gotowy do zaewidencjonowania, często znajduje się on w niezbyt dobrym stanie. Wciąż w sposób iteracyjny go kształtujesz, testujesz i eksperymentujesz z rozwiązaniami. Usługa Azure Dev Spaces wprowadza pojęcie **miejsca**, które umożliwia pracę w izolacji i bez obaw o przeszkadzanie członkom zespołu.

## <a name="use-dev-spaces-for-team-development"></a>Programowanie zespołowe za pomocą usługi Dev Spaces
Zademonstrujmy te pomysły konkretnym przykładem za pomocą *naszej* -> przykładowej aplikacji*mywebapi.* Wyobraźmy sobie scenariusz, w którym programista Scott musi wprowadzić zmianę w usłudze *mywebapi* — i *tylko* w tej usłudze. Usługa *webfrontend* nie musi być zmieniana w ramach aktualizacji Scotta.

_Bez_ używania usługi Dev Spaces Scott miałby kilka sposobów na opracowanie i przetestowanie swojej aktualizacji, jednak żaden z nich nie jest idealny:
* Uruchomienie WSZYSTKICH składników w środowisku lokalnym. Wymaga to bardziej zaawansowanej maszyny deweloperskiej z zainstalowaną platformą Docker i potencjalnie z rozwiązaniem MiniKube.
* Uruchomienie WSZYSTKICH składników w izolowanej przestrzeni nazw w klastrze Kubernetes. Ponieważ usługa *webfrontend* nie zmienia się, jest to marnowanie zasobów klastra.
* Uruchomienie TYLKO usługi *mywebapi* i wykonanie ręcznych wywołań REST w celu testowania. Nie powoduje to testowania całego przepływu, od początku do końca.
* Dodanie do usługi *webfrontend* skoncentrowanego kodu, który umożliwia deweloperowi wysyłanie żądań do innego wystąpienia usługi *mywebapi*. Komplikuje to działanie usługi *webfrontend*.

### <a name="set-up-your-baseline"></a>Konfigurowanie punktu odniesienia
Najpierw musimy wdrożyć punkt odniesienia naszych usług. To wdrożenie będzie reprezentować „ostatni znany dobry” punkt, aby można było łatwo porównać zachowanie kodu lokalnego z wersją zaewidencjonowaną. Następnie, na podstawie tego punktu odniesienia, utworzymy przestrzeń podrzędną, aby umożliwić testowanie zmian w usłudze *mywebapi* w kontekście większej aplikacji.

1. Klonowanie [przykładowej aplikacji Dev Spaces:](https://github.com/Azure/dev-spaces)`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Wyewidencjonuj zdalną gałąź *azds_updates:*`git checkout -b azds_updates origin/azds_updates`
1. Wybierz przestrzeń _dev_: `azds space select --name dev`. Po wyświetleniu monitu o wybranie nadrzędnego obszaru dewelopera wybierz _ \<brak\>_.
1. Przejdź do katalogu _mywebapi_, a następnie wykonaj: `azds up -d`
1. Przejdź do katalogu _webfrontend_, a następnie wykonaj: `azds up -d`
1. Wykonaj `azds list-uris`, aby wyświetlić publiczny punkt końcowy usługi _webfrontend_

> [!TIP]
> Powyższe kroki pozwalają ręcznie skonfigurować punkt odniesienia, jednak zalecamy, aby zespoły używały ciągłej integracji/ciągłego wdrażania w celu automatycznego aktualizowania punktu odniesienia zgodnie z zatwierdzonym kodem.
>
> Zapoznaj się z naszym [przewodnikiem konfigurowania ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps](how-to/setup-cicd.md), aby utworzyć przepływ pracy podobny do przedstawionego na poniższym diagramie.
>
> ![Przykładowy diagram ciągłej integracji/ciągłego wdrażania](media/common/ci-cd-complex.png)

W tym momencie punkt odniesienia powinien działać. Uruchom polecenie `azds list-up --all`. Zobaczysz dane wyjściowe podobne do poniższych:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

Kolumna DevSpace pokazuje, że obie usługi zostały uruchomione w przestrzeni o nazwie _dev_. Każda osoba otwierająca publiczny adres URL i przechodząca do aplikacji internetowej wywoła zaewidencjonowaną ścieżkę kodu, która zostanie uruchomiona w obydwu usługach. Teraz załóżmy, że chcesz kontynuować tworzenie aplikacji _mywebapi_. Jak możesz wprowadzać zmiany kodu i testować je, aby nie przerywać pracy innych deweloperów, którzy używają środowiska deweloperskiego? W tym celu skonfigurujesz własne miejsce.

### <a name="create-a-dev-space"></a>Tworzenie przestrzeni deweloperskiej
Aby uruchomić własną wersję aplikacji _mywebapi_ w przestrzeni innej niż _dev_, możesz utworzyć własną przestrzeń za pomocą następującego polecenia:

```cmd
azds space select --name scott
```

Po wyświetleniu monitu wybierz opcję _dev_ jako **nadrzędną przestrzeń deweloperską**. Oznacza to, że nasza nowa przestrzeń, _dev/scott_, będzie pochodzić z przestrzeni _dev_. Wkrótce zobaczymy, jak pomoże nam to podczas testowania.

W naszym hipotetycznym wprowadzeniu używaliśmy nazwy _scott_ dla nowej przestrzeni, aby współpracownicy mogli zidentyfikować pracującą w niej osobę. Można jej jednak nadać dowolną nazwę i zastosować elastyczne podejście do jej znaczenia, używając nazw, takich jak _sprint4_ lub _demo_. W każdej sytuacji opcja _dev_ służy jako punkt odniesienia dla wszystkich deweloperów nad elementem tej aplikacji:

![](media/common/ci-cd-space-setup.png)

Uruchom polecenie `azds space list`, aby wyświetlić listę wszystkich przestrzeni w środowisku deweloperskim. Kolumna _Wybrane_ wskazuje, która przestrzeń została aktualnie wybrana (true/false). W Twoim przypadku przestrzeń o nazwie _dev/scott_ została automatycznie wybrana po utworzeniu. W dowolnym momencie możesz wybrać inną przestrzeń, używając polecenia `azds space select`.

Zobaczmy, jak to działa.

### <a name="make-a-code-change"></a>Wprowadzanie zmiany w kodzie
Przejdź do okna kod `mywebapi` VS dla i dokonać `/` edycji `server.js`kodu do domyślnego programu obsługi GET w , na przykład:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

### <a name="run-the-service"></a>Uruchamianie usługi

Aby uruchomić usługę, naciśnij klawisz `azds up` F5 (lub wpisz okno terminala), aby uruchomić usługę. Usługa jest uruchamiana automatycznie w nowo wybranej przestrzeni _dev/scott_. Upewnij się, że usługa działa we `azds list-up`własnym miejscu, uruchamiając:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Zwróć uwagę, że *wystąpienie mywebapi* jest teraz uruchomione w przestrzeni _dev/scott._ Wersja uruchomiona w _programach deweloperskich_ jest nadal uruchomiona, ale nie jest wymieniona.

Wyświetl listę adresów URL bieżącego miejsca, uruchamiając plik `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Zwróć uwagę, że adres URL publicznego punktu dostępu dla *webfrontendu* jest poprzedzony *scott.s*. Ten adres URL jest unikatowy w przestrzeni _dev/scott_. Ten prefiks adresu URL informuje kontrolera transferu danych przychodzących do kierowania żądań do wersji _dev/scott_ usługi. Gdy żądanie z tym adresem URL jest obsługiwane przez dev spaces, kontroler transferu danych przychodzących najpierw próbuje przekierować żądanie do usługi *webfrontend* w przestrzeni _dev/scott._ Jeśli to się nie powiedzie, żądanie zostanie przekierowane do usługi *webfrontend* w przestrzeni _deweloperów_ jako rezerwowy. Należy również zauważyć, że istnieje localhost URL, aby uzyskać dostęp do usługi za pośrednictwem localhost przy użyciu funkcji kubernetes *port-forward.* Aby uzyskać więcej informacji na temat adresów URL i routingu w usłudze Azure Dev Spaces, zobacz [Jak działa usługa Azure Dev Spaces i jest skonfigurowana.](how-dev-spaces-works.md)

![Routing przestrzeni](media/common/Space-Routing.png)

Ta wbudowana funkcja usługi Azure Dev Spaces umożliwia testowanie kodu w udostępnionej przestrzeni bez konieczności ponownego tworzenia pełnych stosów usług przez wszystkich deweloperów w ich przestrzeniach. Taki routing wymaga, aby kod aplikacji przekazywał nagłówki propagowania, jak pokazano w poprzednim kroku tego przewodnika.

### <a name="test-code-in-a-space"></a>Testowanie kodu w przestrzeni
Aby przetestować nową wersję aplikacji *mywebapi* z aplikacją *webfrontend*, otwórz w przeglądarce adres URL publicznego punktu dostępu dla aplikacji *webfrontend* i przejdź do strony Informacje. Powinien zostać wyświetlony Twój nowy komunikat.

Teraz usuń część „scott.s.” z adresu URL, a następnie odśwież przeglądarkę. Powinno zostać zastosowane stare zachowanie (dla wersji usługi *mywebapi* działającej w przestrzeni _dev_).

Mamy nadzieję, że po utworzeniu przestrzeni _dev_, która zawsze zawiera najnowsze zmiany, i zakładając, że aplikacja jest zaprojektowana tak, aby wykorzystywać oparty na przestrzeniach routing usługi DevSpace, łatwo będzie zobaczyć, że usługa Dev Spaces może znacznie ułatwić testowanie nowych funkcji w kontekście większej aplikacji. Zamiast wdrażać _wszystkie_ usługi w przestrzeni prywatnej, możesz utworzyć przestrzeń prywatną, która wywodzi się z przestrzeni _dev_, i uruchamiać tylko te usługi, nad którymi tak naprawdę pracujesz. Infrastruktura routingu usługi Dev Space zajmie się resztą, wykorzystując tyle usług spoza przestrzeni prywatnej, ile uda jej się znaleźć, jednocześnie przyjmując domyślnie najnowszą wersję uruchomioną w przestrzeni _dev_. A co najlepsze, _wielu_ deweloperów może aktywnie programować różne usługi w tym samym czasie we własnych przestrzeniach, nie przeszkadzając sobie nawzajem.

### <a name="well-done"></a>Gotowe!
Przewodnik Wprowadzenie został ukończony! W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
> * Iteracyjne tworzenie kodu w kontenerach.
> * Niezależne tworzenie dwóch oddzielnych usług i wywoływanie innej usługi przy użyciu funkcji odnajdywania usług DNS w środowisku Kubernetes.
> * Efektywne tworzenie i testowanie kodu w środowisku zespołu.
> * Ustalanie punktu odniesienia funkcjonalności za pomocą usługi Dev Spaces w celu łatwego testowania izolowanych zmian w kontekście większej aplikacji mikrousług.

Teraz, gdy zostały zbadane usługi Azure Dev Spaces, [udostępnij swoje miejsce deweloperów członkowi zespołu](how-to/share-dev-spaces.md) i rozpocznij współpracę.

## <a name="clean-up"></a>Czyszczenie
Aby całkowicie usunąć z klastra wystąpienie usługi Azure Dev Spaces, w tym wszystkie przestrzenie deweloperów i usługi działające w ich obrębie, należy użyć polecenia `az aks remove-dev-spaces`. Pamiętaj, że ta akcja jest nieodwracalna. Możesz ponownie dodać obsługę usługi Azure Dev Spaces w klastrze, ale wszystko trzeba będzie zacząć od początku. Stare usługi i przestrzenie nie zostaną przywrócone.

Poniższy przykład wyświetla listę kontrolerów usługi Azure Dev Spaces w Twojej aktywnej subskrypcji, a następnie usuwa kontroler usługi Azure Dev Spaces, który jest skojarzony z klastrem usługi AKS „myaks” w grupie zasobów „myaks-rg”.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
