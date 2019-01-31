---
title: Rozwiązywanie problemów z | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: article
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 37ee9fec8940231a01b0014b020ca3f0dffb53bf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467105"
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Ten przewodnik zawiera informacje o typowych problemów, które mogą mieć w przypadku korzystania z usługi Azure Dev miejsca do magazynowania.

## <a name="enabling-detailed-logging"></a>Włączanie rejestrowania szczegółowe

Aby bardziej efektywnie rozwiązać problemy, może pomóc tworzyć bardziej szczegółowych dzienników do przeglądu.

Rozszerzenie programu Visual Studio można ustawić `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` zmiennej środowiskowej 1. Pamiętaj ponownie uruchomić program Visual Studio dla zmiennej środowiskowej zaczęły obowiązywać. Po włączeniu szczegółowe dzienniki będą zapisywane w swojej `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` katalogu.

W interfejsie wiersza polecenia, użytkownik może zapewniać dane wyjściowe informacji podczas wykonywania polecenia przy użyciu `--verbose` przełącznika. Można również przeglądać szczegółowe dzienniki w `%TEMP%\Azure Dev Spaces`. Na komputerze Mac, można znaleźć katalogu TEMP, uruchamiając `echo $TMPDIR` z okna terminalu. Na komputerze z systemem Linux katalogu TEMP jest zazwyczaj `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Debugowanie usług z wieloma wystąpieniami

W tej chwili usługi Azure Dev miejsca do magazynowania działa najlepiej, gdy debugowanie jednego wystąpienia (pod). Plik azds.yaml zawiera ustawienie, replicaCount, wskazującą liczbę zasobników, które będą uruchamiane dla Twojej usługi. Jeśli zmienisz replicaCount skonfigurować aplikację do uruchamiania wielu zasobnikach dla danej usługi, debuger będzie połączyć się pierwszy zasobnika (w przypadku alfabetycznym). Jeśli tego zasobnika jest odtwarzany jakiegokolwiek powodu, debuger będzie dołączyć do różnych zasobnik, przez co nieoczekiwane zachowanie.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Błąd "Nie można utworzyć kontroler Azure Dev miejsca do magazynowania"

Można napotkać ten błąd, gdy coś pójdzie nie tak z tworzeniem kontrolera. Jeśli jest to błąd przejściowy, usunięcie i ponowne utworzenie kontrolera naprawi go.

### <a name="try"></a>Wypróbuj:

Aby usunąć kontroler, należy użyć wiersza polecenia platformy Azure Dev miejsca do magazynowania. Nie jest możliwe tylko w programie Visual Studio lub usłudze Cloud Shell. Aby zainstalować interfejs wiersza polecenia AZDS, najpierw zainstaluj wiersza polecenia platformy Azure, a następnie uruchom następujące polecenie:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

A następnie uruchom to polecenie, aby usunąć kontrolera:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Ponowne tworzenie kontrolera może odbywać się z interfejsu wiersza polecenia lub programu Visual Studio. Postępuj zgodnie z instrukcjami w samouczkach tak, jakby uruchamiania po raz pierwszy.


## <a name="error-service-cannot-be-started"></a>Błąd "nie można uruchomić usługi."

Ten błąd może pojawić się po kodzie usługi nie powiedzie się. Przyczyną jest często w kodzie użytkownika. Aby uzyskać więcej informacji diagnostycznych, należy wprowadzić następujące zmiany w Twoich poleceń i ustawień:

### <a name="try"></a>Wypróbuj:

W wierszu polecenia:

Korzystając z _azds.exe_, użyj--verbose opcji wiersza polecenia i użyj opcji wiersza polecenia dane wyjściowe, aby określić format danych wyjściowych.
 
```cmd
azds up --verbose --output json
```

W programie Visual Studio:

1. Otwórz **Narzędzia > Opcje** i w obszarze **projekty i rozwiązania**, wybierz **kompilowanie i uruchamianie**.
2. Zmień ustawienia **poziom szczegółowości danych wyjściowych kompilacji projektu programu MSBuild** do **szczegółowe** lub **diagnostycznych**.

    ![Zrzut ekranu opcji narzędzi okna dialogowego](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Wieloetapowe pliki Dockerfile:
Ten błąd może zostać wyświetlony podczas próby użycia wieloetapowych pliku Dockerfile. Pełne dane wyjściowe będą wyglądać następująco:

```cmd
$ azds up
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Jest to spowodowane węzłów AKS uruchomić starszej wersji platformy docker, który nie obsługuje wieloetapowych kompilacji. Konieczne będzie ponowne zapisywanie adresów z pliku Dockerfile, aby uniknąć wieloetapowych kompilacji.

### <a name="re-running-a-service-after-controller-re-creation"></a>Ponowne uruchomienie usługi po ponownego utworzenia kontrolera
Ten błąd może zostać wyświetlony podczas próby ponownego uruchomienia usługi po usunięte i tworzony ponownie kontroler Azure Dev miejsca do magazynowania, skojarzone z tym klastrem. Pełne dane wyjściowe będą wyglądać następująco:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Jest to spowodowane usunięcie kontrolera Dev miejsca do magazynowania nie powoduje usunięcia usług zainstalowanych wcześniej przez tego kontrolera. Ponowne tworzenie kontrolera, a następnie próbując do uruchamiania usług przy użyciu nowego kontrolera nie działa, ponieważ stare usługi wciąż znajdują się w miejscu.

Aby rozwiązać ten problem, należy użyć `kubectl delete` polecenie, aby ręcznie usunąć stary usługi z klastrem, a następnie ponownie uruchom Dev miejsca do magazynowania, aby zainstalować nowych usług.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Rozpoznawanie nazw DNS nie powiedzie się publiczny adres URL skojarzony z usługą Dev miejsca do magazynowania

Podczas rozpoznawania nazw DNS zakończy się niepowodzeniem, może zostać wyświetlony "Nie można wyświetlić strony" lub "tej witryny nie można nawiązać połączenia" Błąd w przeglądarce sieci web podczas próby połączenia z publiczny adres URL skojarzony z usługą Dev miejsca do magazynowania.

### <a name="try"></a>Wypróbuj:

Służy następujące polecenie, aby wyświetlić listę wszystkich adresów URL skojarzonych z usługami Dev miejsca do magazynowania:

```cmd
azds list-uris
```

Jeśli adres URL znajduje się w *oczekujące* stanu, oznacza to, czy Dev miejsca do magazynowania nadal oczekuje na rejestrację DNS do wykonania. Czasami zajmuje kilka minut, zanim rejestracji w celu ukończenia. Miejsca do magazynowania dev otwiera również tunel localhost dla każdej usługi, którego można użyć podczas oczekiwania na rejestrację serwera DNS.

Jeśli adres URL pozostaje w *oczekujące* stanu na więcej niż 5 minut, może to oznaczać problem z zewnętrznego zasobnika DNS, który tworzy publiczny punkt końcowy i/lub zasobnika kontrolera ruch przychodzący serwera nginx, który uzyskuje publiczny punkt końcowy. Można użyć następujących poleceń, można usunąć tych zasobników. Zostaną one zostać odtworzone automatycznie.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Błąd "Wymagane narzędzia i konfiguracje są niedostępne"

Ten błąd może wystąpić przy uruchamianiu programu VS Code: "[Azure Dev miejsca do magazynowania] narzędzi i konfiguracji, aby kompilować i debugować,"[Nazwa projektu]"Brak wymaganych."
Ten błąd oznacza, że tego azds.exe nie znajduje się w zmiennej środowiskowej PATH, jak pokazano w programie VS Code.

### <a name="try"></a>Wypróbuj:

Uruchom program VS Code z poziomu wiersza polecenia, w którym zmiennej środowiskowej PATH jest prawidłowo.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Błąd "wymagane narzędzia do tworzenia i debugowania"projectname"są nieaktualne".

Zostanie wyświetlony ten błąd w programie Visual Studio Code, jeśli ma nowszą wersję rozszerzeń programu VS Code dla usługi Azure Dev miejsca do magazynowania, ale starszą wersję interfejsu wiersza polecenia Azure Dev miejsca do magazynowania.

### <a name="try"></a>Spróbuj

Pobierz i zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure Dev miejsca do magazynowania:

* [Windows](http://aka.ms/get-azds-windows)
* [Mac](http://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Błąd "azds" nie jest rozpoznawana jako polecenie wewnętrzne lub zewnętrzne, program wykonywalny lub plik wsadowy
 
Można napotkać ten błąd, jeśli azds.exe nie jest zainstalowane lub prawidłowo skonfigurowane.

### <a name="try"></a>Wypróbuj:

1. Sprawdź %ProgramFiles%/Microsoft lokalizacji SDKs\Azure\Azure Dev miejsca do magazynowania interfejs wiersza polecenia (wersja zapoznawcza) dla azds.exe. Jeśli jest określony, dodanie jej do zmiennej środowiskowej PATH.
2. Jeśli nie zainstalowano azds.exe, uruchom następujące polecenie:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Ostrzeżenie "pliku Dockerfile nie można wygenerować z powodu nieobsługiwany język"
Usługa Azure Dev do magazynowania zapewnia macierzystą obsługę języka C# i Node.js. Po uruchomieniu *azds prep* w katalogu, zawierającego kod napisany w jednym z tych języków, Azure Dev miejsca do magazynowania automatycznie utworzy odpowiedni plik Dockerfile za Ciebie.

Można nadal używać usługi Azure Dev miejsca do magazynowania przy użyciu kodu napisanego w innych językach, ale musisz utworzyć plik Dockerfile, samodzielnie przed uruchomieniem polecenia *azds się* po raz pierwszy.

### <a name="try"></a>Wypróbuj:
Jeśli aplikacji został napisany w języku, że usługi Azure Dev miejsca do magazynowania nie obsługuje natywnie, należy podać odpowiednie Dockerfile, aby utworzyć obraz kontenera uruchomienia kodu. Środowisko docker zawiera [listę najlepsze rozwiązania dotyczące zapisywania plików Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) , a także [odwołanie do pliku Dockerfile](https://docs.docker.com/engine/reference/builder/) napisać plik Dockerfile, który odpowiada Twoim potrzebom, może pomóc.

Po utworzeniu odpowiedni plik Dockerfile w miejscu, możesz kontynuować uruchamianie *azds się* do uruchamiania aplikacji w usłudze Azure Dev spacjach.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Błąd "nadrzędne błąd połączenia lub odłącz/reset przed nagłówki"
Podczas próby uzyskania dostępu do usługi, zostanie wyświetlony ten błąd. Na przykład, gdy nastąpi przejście do adresu URL usługi, w przeglądarce. 

### <a name="reason"></a>Przyczyna 
Port kontenera nie jest dostępna. Ten problem może wystąpić, ponieważ: 
* Kontener trwa nadal trwa utworzeniu i wdrożeniu. Ten problem może wystąpić, jeśli uruchomisz `azds up` lub uruchom debuger, a następnie spróbuj dostępu do kontenera zanim została pomyślnie wdrożona.
* Konfiguracja portów nie jest spójność w ramach usługi _pliku Dockerfile_, narzędzia Helm i wszelki kod serwera, który otwiera port.

### <a name="try"></a>Wypróbuj:
1. Jeśli kontener jest właśnie utworzone/wdrażane, możesz odczekaj 2 – 3 sekund, a następnie spróbuj ponownie uzyskać dostęp do usługi. 
1. Sprawdź konfigurację portu. Numery określony port powinien być **identyczne** w poniższych zasobów:
    * **Dockerfile:** Określony przez `EXPOSE` instrukcji.
    * **[Narzędzia Helm](https://docs.helm.sh):** Określony przez `externalPort` i `internalPort` wartości dla usługi (często znajduje się w `values.yml` pliku),
    * Wszystkie porty są otwarte w kodzie aplikacji, na przykład w środowisku Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Nie można odnaleźć pliku konfiguracji
Możesz uruchomić `azds up` i występuje następujący błąd: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Przyczyna
Należy uruchomić `azds up` z katalogu głównego kodu chcesz uruchomić, a następnie należy zainicjować folder kodu do uruchomienia przy użyciu usługi Azure Dev miejsca do magazynowania.

### <a name="try"></a>Wypróbuj:
1. Zmień bieżący katalog do folderu głównego zawierającego kod w języku usługi. 
1. Jeśli nie masz _azds.yaml_ pliku w folderze kodu uruchamiania `azds prep` wygenerować zasoby platformy Docker, Kubernetes i usługi Azure Dev miejsca do magazynowania.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Błąd: "Program potoków"azds"nieoczekiwanie zakończył działanie z kodem 126."
Uruchamianie debugera programu VS Code czasami może spowodować błąd.

### <a name="try"></a>Wypróbuj:
1. Zamknij i otwórz ponownie program VS Code.
2. Ponownie naciśnij klawisz F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Błąd "Nie można odnaleźć rozszerzenia debugera dla typu: coreclr" debugowania
Uruchamianie debugera programu VS Code zgłasza błąd: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Przyczyna
Nie masz rozszerzenia kodu programu VS dla języka C# zainstalowane na komputerze deweloperskim. Rozszerzenie języka C# zawiera obsługę debugowania dla.Net Core (CoreCLR).

### <a name="try"></a>Wypróbuj:
Zainstaluj [rozszerzenia kodu programu VS dla języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Błąd debugowania "skonfigurowane debugowania typu"coreclr"nie jest obsługiwany"
Uruchamianie debugera programu VS Code zgłasza błąd: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Przyczyna
Nie masz rozszerzenie programu VS Code Azure Dev spacje zainstalowany na komputerze deweloperskim.

### <a name="try"></a>Wypróbuj:
Zainstaluj [rozszerzenie programu VS Code dla usługi Azure Dev miejsca do magazynowania](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Debugowanie błąd "nieprawidłowy"cwd"wartość" / src ". System nie może odnaleźć określonego pliku." lub "Uruchom: program"/ src / [ścieżka do pliku binarnego projektu]"nie istnieje."
Uruchamianie debugera programu VS Code zgłasza błąd `Invalid 'cwd' value '/src'. The system cannot find the file specified.` i/lub `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Przyczyna
Domyślnie używa rozszerzenia programu VS Code `src` jako katalog roboczy dla projektu w kontenerze. Jeśli zaktualizowano swoje `Dockerfile` Aby określić inny katalog roboczy, może zostać wyświetlony ten błąd.

### <a name="try"></a>Wypróbuj:
Aktualizacja `launch.json` plik `.vscode` podkatalogu w folderze projektu. Zmiana `configurations->cwd` dyrektywy, aby wskazywała na tym samym katalogu co `WORKDIR` zdefiniowane w swoim projekcie `Dockerfile`. Również może być konieczne zaktualizowanie `configurations->program` również dyrektywę.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Nie można odnaleźć nazwy typu lub przestrzeni nazw "MojaBiblioteka"

### <a name="reason"></a>Przyczyna 
Kontekst kompilacji jest domyślnie na poziomie projektu/usługi, w związku z tym projektu biblioteki, którego używasz nie będzie można znaleźć.

### <a name="try"></a>Wypróbuj:
Co trzeba zrobić:
1. Modyfikowanie _azds.yaml_ pliku, aby ustawić kontekstu kompilacji na poziomie rozwiązania.
2. Modyfikowanie _pliku Dockerfile_ i _Dockerfile.develop_ pliki do odwoływania się do projektu (_.csproj_) pliki poprawnie, względem nowej kompilacji kontekstu.
3. Miejsce _.dockerignore_ pliku obok pliku .sln i modyfikować zgodnie z potrzebami.

Na przykład można znaleźć https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Błąd autoryzacji "Microsoft.DevSpaces/register/action"
Może być wyświetlony następujący błąd, gdy są zarządzanie przestrzenią deweloperów platformy Azure i pracy z subskrypcją platformy Azure, dla której nie masz dostęp współautora lub właściciela.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Przyczyna
Wybranej subskrypcji platformy Azure nie został zarejestrowany `Microsoft.DevSpaces` przestrzeni nazw.

### <a name="try"></a>Wypróbuj:
Dostęp właściciela lub współautora do subskrypcji platformy Azure można uruchomić następujące polecenie z wiersza polecenia platformy Azure, aby ręcznie zarejestrować `Microsoft.DevSpaces` przestrzeni nazw:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Błąd: nie można znaleźć pod tiller gotowe" podczas uruchamiania Dev miejsca do magazynowania

### <a name="reason"></a>Przyczyna
Ten błąd występuje, jeśli klient narzędzia Helm już nie może komunikować się pod Tiller uruchomionych w klastrze.

### <a name="try"></a>Wypróbuj:
Ponowne uruchamianie węzłów agenta w klastrze zwykle rozwiązuje ten problem.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Serwer proxy usługi Azure Dev miejsca do magazynowania może zakłócać innych zasobników w miejsce dev

### <a name="reason"></a>Przyczyna
Po włączeniu Dev miejsca do magazynowania w przestrzeni nazw w klastrze AKS dodatkowy kontener o nazwie _mindaro proxy_ jest instalowany na każdym zasobników działającym wewnątrz tego obszaru nazw. Ten kontener przechwytuje wywołania usług pod, który jest dołączony do funkcji tworzenia zespołu miejsca do magazynowania deweloperów.

Niestety może to zakłócać pewne usługi działające w tych zasobników. W szczególności koliduje z zasobników systemem pamięć podręczna systemu Azure dla usługi Redis, powodując błędy połączeń i błędów w komunikacie główny/podrzędny.

### <a name="try"></a>Wypróbuj:
Dotyczy pod(s) można przenieść do przestrzeni nazw w ramach klastra, który wykonuje _nie_ zawierać spacji deweloperów podczas uruchomienia pozostałej części aplikacji wewnątrz przestrzeni nazw z obsługą tworzenia miejsca do magazynowania w dalszym ciągu włączona. Dev miejsca do magazynowania nie spowoduje zainstalowania _mindaro proxy_ kontenerów wewnątrz spacje innych deweloperów włączone obszary nazw.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Usługa Azure Dev spacje wydają się użyć Mój istniejący plik Dockerfile do utworzenia kontenera 

### <a name="reason"></a>Przyczyna
Azure Dev do magazynowania można skonfigurować, aby wskazać konkretną _pliku Dockerfile_ w projekcie. Jeśli wydaje się nie używa usługi Azure Dev miejsca do magazynowania _pliku Dockerfile_ oczekujesz, że tworzenie kontenerów, konieczne może poinformować usługi Azure Dev miejsca do magazynowania, gdzie jest jawnie. 

### <a name="try"></a>Wypróbuj:
Otwórz _azds.yaml_ pliku, który został wygenerowany przez usługi Azure Dev miejsca do magazynowania w projekcie. Użyj `configurations->develop->build->dockerfile` dyrektywy wskaż plik Dockerfile, którego chcesz użyć:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
