---
title: Programowanie i debugowanie modułów dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Visual Studio Code umożliwia tworzenie, kompilowanie i debugowanie modułu dla usługi Azure IoT Edge przy użyciu C#, Python, Node.js, Java lub C
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 463ab617051bf97bb3b1c38ed431c4b6936a9c90
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118697"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Używanie programu Visual Studio Code do tworzenia i debugowania modułów dla usługi Azure IoT Edge

Logikę biznesową można przekształcić w moduły, dla usługi Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio Code jako głównego narzędzia do tworzenia i debugowania modułów.

## <a name="prerequisites"></a>Wymagania wstępne

Można użyć komputera lub maszyny wirtualnej z systemem Windows, macOS lub Linux jako komputerze deweloperskim. Urządzenia usługi IoT Edge może być inny urządzenia fizycznego.

Dla modułów napisanych w C#, Node.js lub Java, istnieją dwa sposoby do debugowania modułu w programie Visual Studio Code: Możesz dołączyć do procesu w kontenerze modułu lub uruchamianie kodu modułu w trybie debugowania. Dla modułów napisanych w języku Python lub C ich może być debugowany tylko przez dołączanie do procesu w systemie Linux amd64 kontenerach.

> [!TIP]
> Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj temat [debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

Zainstaluj [programu Visual Studio Code](https://code.visualstudio.com/) pierwszy, a następnie dodaj następujące rozszerzenia:

- [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Rozszerzenia platformy docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio rozszerzenia specyficznych dla języka opracowywano w:
  - C#, w tym usługi Azure Functions: [Rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  - Python: [Rozszerzenie języka Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Pakietu rozszerzenia języka Java dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [Rozszerzenie języka C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Należy także zainstalować niektórych dodatkowych narzędzi specyficznych dla języka, aby tworzyć modułu:

- C#, w tym usługi Azure Functions: [zestawu SDK programu .NET Core 2.1](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) i [Pip](https://pip.pypa.io/en/stable/installing/#installation) instalacji pakietów języka Python (najczęściej przygotowywanych do uwzględnienia z instalacją języka Python). Po zainstalowaniu narzędzia Pip, zainstalować **Cookiecutter** pakietu za pomocą następującego polecenia:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

- Node.js: Środowisko [Node.js](https://nodejs.org). Należy również zainstalować [Yeoman](https://www.npmjs.com/package/yo) i [Azure IoT Edge Node.js modułu Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) i [Maven](https://maven.apache.org/). Konieczne będzie [ustaw `JAVA_HOME` zmiennej środowiskowej](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) wskaż instalacji zestawu JDK.

Aby skompilować i wdrożyć obraz modułu, należy platformy Docker, aby skompilować obraz modułu i rejestru kontenerów do przechowywania obrazu modułu:

- [Platformę docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim.

- [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [usługi Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Można użyć lokalnego rejestru platformy Docker prototypów i testowania zamiast rejestru chmury.

Jeśli nie opracowujesz modułu w języku C, należy również oparta na środowisku Python [narzędzia deweloperskiego EdgeHub IoT Azure](https://pypi.org/project/iotedgehubdev/) aby można było skonfigurować lokalne Środowisko deweloperskie, debugowanie, uruchamianie i testowanie rozwiązania usługi IoT Edge. Jeśli jeszcze tego nie zrobiono, zainstaluj [języka Python (w wersji 2.7/3.6) i narzędzie Pip](https://www.python.org/) , a następnie zainstaluj **iotedgehubdev** , uruchamiając następujące polecenie w terminalu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Aby przetestować modułu na urządzeniu, należy aktywnym Centrum IoT przy użyciu co najmniej jedno urządzenie usługi IoT Edge. Aby użyć komputera jako urządzenia usługi IoT Edge, postępuj zgodnie z instrukcjami w przewodniku Szybki Start dla [Linux](quickstart-linux.md) lub [Windows](quickstart.md). Jeśli demon usługi IoT Edge są uruchomione na komputerze deweloperskim, może być konieczne zatrzymanie EdgeHub i EdgeAgent przed przejściem do następnego kroku.

## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Poniższe kroki pokazują jak utworzyć moduł usługi IoT Edge w języku preferowanym rozwoju (w tym usługi Azure Functions, napisany w C#) przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT. Możesz rozpocząć tworzenie rozwiązania, a następnie generowania pierwszego modułu w ramach tego rozwiązania. Każdy roztwór może zawierać wiele modułów.

1. Wybierz **widoku** > **polecenia palety**.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: Nowe rozwiązanie IoT Edge**.

   ![Uruchamianie nowego rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie wybierz pozycję **Wybieranie folderu**.

1. Wprowadź nazwę dla swojego rozwiązania.

1. Wybierz szablon modułu dla języka programowania preferowanych jako pierwszego modułu w rozwiązaniu.

1. Wprowadź nazwę dla modułu. Wybierz nazwę, która jest unikatowa w obrębie usługi container registry.

1. Podaj nazwę modułu repozytorium obrazów. Visual Studio Code autopopulates modułu nazwa z **localhost:5000 / < Twoja nazwa modułu\>**. Zastąp go własną informacje rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania, następnie **localhost** jest w dobrym stanie. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda jak ***\<nazwa rejestru\>*. azurecr.io**. Jedynie zastąpić **localhost:5000** część ciągu, więc, że wynik końcowy wygląda jak **\<* nazwa rejestru*\>.azurecr.io/* \<swoją nazwę modułu\>***.

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code przyjmuje informacje podane lub tworzy rozwiązanie IoT Edge, a następnie ładuje go w nowym oknie.

Istnieją cztery elementy w ramach rozwiązania:

- A **.vscode** folder zawiera konfiguracji debugowania.

- A **modułów** folder zawiera podfoldery dla każdego modułu. W tym momencie masz tylko jedną licencję. Jednak możesz dodać więcej licencji w palecie poleceń za pomocą polecenia **usługi Azure IoT Edge: Add IoT Edge Module** (Azure IoT Edge: dodawanie modułu usługi IoT Edge).

- **ENV** plik zawiera listę zmiennych środowiskowych. Jeśli rejestru Azure Container Registry, będziesz mieć usługi Azure Container Registry użytkownika i hasło w nim.

  > [!NOTE]
  > Plik środowiska jest tworzony tylko, jeśli podasz repozytorium obrazów w module. Jeżeli użytkownik zaakceptował domyślnie localhost, Testuj i Debuguj lokalnie, nie należy do deklarowania zmiennych środowiskowych.

- A **deployment.template.json** nowego modułu wraz z przykładu zawiera listę plików **tempSensor** modułu, która symuluje sieć danych można używać do testowania. Aby uzyskać więcej informacji na temat sposobu wdrażania manifestów pracy, zobacz [Dowiedz się, jak wdrażać moduły oraz ustalenia tras za pomocą manifesty wdrożenia](module-composition.md).

## <a name="add-additional-modules"></a>Dodawanie dodatkowych modułów

Aby dodać dodatkowe moduły do rozwiązania, uruchom polecenie **usługi Azure IoT Edge: Dodaj moduł usługi IoT Edge** z palety poleceń. Również prawym przyciskiem myszy **modułów** folderu lub `deployment.template.json` plików w widoku Eksplorator kodu w usłudze Visual Studio, a następnie wybierz pozycję **Dodaj moduł usługi IoT Edge**.

## <a name="develop-your-module"></a>Tworzenie modułu

Domyślny kod modułu w rozwiązaniu znajduje się w następującej lokalizacji:

- Funkcja platformy Azure (C#): **modułów >  *&lt;swoją nazwę modułu&gt;* > *&lt;swoją nazwę modułu&gt;*.cs**
- C#: **modułów > *&lt;swoją nazwę modułu&gt;* > pliku Program.cs**
- Python: **modułów > *&lt;swoją nazwę modułu&gt;* > main.py**
- Node.js: **modułów > *&lt;swoją nazwę modułu&gt;* > app.js**
- Java: **modułów > *&lt;swoją nazwę modułu&gt;* > src > główny > java > com > edgemodulemodules > App.java**
- C: **modułów > *&lt;swoją nazwę modułu&gt;* > main.c**

W module, plik deployment.template.json są konfigurowane tak, aby skompilować rozwiązanie, Wypchnij go do rejestru kontenerów i wdrożyć ją na urządzeniu do rozpoczęcia testowania bez dotykania żadnego kodu. Moduł został opracowany pod kątem wystarczy pobrać dane wejściowe ze źródła (w tym przypadku moduł tempSensor, która symuluje sieć danych) i przekazać go do usługi IoT Hub.

Gdy wszystko będzie gotowe dostosować szablon przy użyciu własnego kodu, należy użyć [zestawami SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów ten adres klucz dla rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodności.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Debugowanie modułu bez kontenera (C#, Node.js, Java)

Jeśli tworzysz w C#, Node.js lub Java, wymagane jest użycie modułu **ModuleClient** obiektu w kodzie modułu domyślne tak, aby go uruchomić, uruchamianie i kierowanie komunikatów w postaci. Skorzystasz także domyślnego kanału wejściowego **wejście1** podjęcie działań, gdy moduł odbiera komunikaty.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie usługi IoT Edge symulatora dla rozwiązania IoT Edge

W komputerze deweloperskim można uruchomić symulatora usługi IoT Edge, zamiast instalować ją z demona zabezpieczeń usługi IoT Edge, aby uruchomić rozwiązanie IoT Edge.

1. W Eksploratorze urządzenia po lewej stronie, kliknij prawym przyciskiem myszy na swój identyfikator urządzenia usługi IoT Edge, a następnie wybierz **Instalatora IoT Edge symulator** można uruchomić symulatora przy użyciu parametrów połączenia urządzenia.
1. Widać, że symulator IoT Edge pomyślnie skonfigurowano, zapoznając się szczegóły postępu w zintegrowanym terminalu.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Konfigurowanie usługi IoT Edge symulator modułu pojedynczej aplikacji

Aby skonfigurować i uruchomić symulator, uruchom polecenie **usługi Azure IoT Edge: Rozpocznij IoT Edge Hub symulatora dla jednego modułu** z palety poleceń programu Visual Studio Code. Po wyświetleniu monitu użyj wartości **wejście1** z domyślnego modułu kodu (lub równowartości w kodzie) jako danych wejściowych nazwę swojej aplikacji. Wyzwalacze polecenia **iotedgehubdev** interfejsu wiersza polecenia, a następnie uruchamia symulator usługi IoT Edge i kontener modułu narzędzia testowania. Symulator został uruchomiony w trybie jednego modułu pomyślnie można wyświetlić dane wyjściowe poniżej w zintegrowanym terminalu. Można również wyświetlić `curl` polecenie, aby ułatwić wysłać. Użyjesz jej później.

   ![Konfigurowanie usługi IoT Edge symulator modułu pojedynczej aplikacji](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Aby wyświetlić stan uruchomionego modułu, można użyć widoku Eksploratora platformy Docker w programie Visual Studio Code.

   ![Stan modułu symulatora](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev** kontener jest podstawowy symulatora lokalnego usługi IoT Edge. Jego można uruchomić na komputerze deweloperskim bez demona zabezpieczeń usługi IoT Edge i udostępnia ustawienia środowiska dla aplikacji modułu macierzystego lub kontenery modułu. **Wejściowych** kontener uwidacznia interfejsy API REST do mostka wiadomości na kanał wejściowy docelowego w module.

### <a name="debug-module-in-launch-mode"></a>Debugowanie modułu w trybie uruchamiania

1. Przygotuj środowisko do debugowania zgodnie z wymogami języka programowania, ustaw punkt przerwania w module, a następnie wybierz konfigurację debugowania w celu użycia:
   - **C#**
     - W zintegrowanym terminalu programu Visual Studio Code, zmień katalog na ***&lt;swoją nazwę modułu&gt;*** folder, a następnie uruchom następujące polecenie w celu tworzenia.Net Core w aplikacji.

       ```cmd
       dotnet build
       ```

     - Otwórz plik `program.cs` i Dodaj punkt przerwania.

     - Przejdź do widoku debugowania programu Visual Studio Code, wybierając **Widok > debugowanie**. Wybierz konfigurację debugowania  ***&lt;swoją nazwę modułu&gt;* lokalnego debugowania (.NET Core)** z listy rozwijanej.

        > [!NOTE]
        > Jeśli Twoje.Net Core `TargetFramework` nie jest spójna ze swojej ścieżka programu w `launch.json`, musisz ręcznie zaktualizować ścieżkę programu w `launch.json` do dopasowania `TargetFramework` w pliku csproj tak że Visual Studio Code to pomyślnie uruchomić Program.

   - **Node.js**
     - W zintegrowanym terminalu programu Visual Studio Code, zmień katalog na ***&lt;swoją nazwę modułu&gt;*** folder, a następnie uruchom następujące polecenie, aby zainstalować pakiety węzła

       ```cmd
       npm install
       ```

     - Otwórz plik `app.js` i Dodaj punkt przerwania.

     - Przejdź do widoku debugowania programu Visual Studio Code, wybierając **Widok > debugowanie**. Wybierz konfigurację debugowania  ***&lt;swoją nazwę modułu&gt;* lokalnego debugowania (Node.js)** z listy rozwijanej.
   - **Java**
     - Otwórz plik `App.java` i Dodaj punkt przerwania.

     - Przejdź do widoku debugowania programu Visual Studio Code, wybierając **Widok > debugowanie**. Wybierz konfigurację debugowania  ***&lt;swoją nazwę modułu&gt;* lokalnego debugowania (Java)** z listy rozwijanej.

1. Kliknij przycisk **Rozpocznij debugowanie** lub naciśnij **F5** można uruchomić sesji debugowania.

1. W zintegrowanym terminalu programu Visual Studio Code, uruchom następujące polecenie, aby wysłać **Witaj, świecie** komunikatu do modułu. To polecenie pokazano w poprzednich krokach, podczas konfigurowania usługi IoT Edge symulatora.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Jeśli używasz Windows, upewnij się, że powłoka zintegrowany terminal programu Visual Studio Code jest **powłoki Git Bash** lub **WSL Bash**. Nie można uruchomić `curl` polecenia programu PowerShell lub wierszu polecenia.
   > [!TIP]
   > Można również użyć [PostMan](https://www.getpostman.com/) lub innych narzędzi interfejsu API, aby wysyłać komunikaty za pośrednictwem zamiast `curl`.

1. W widoku debugowania programu Visual Studio Code zobaczysz zmiennych w panelu po lewej stronie.

1. Aby zatrzymać sesję debugowania, wybierz przycisk Zatrzymaj lub naciśnij klawisz **Shift + F5**, a następnie uruchom **usługi Azure IoT Edge: Zatrzymaj IoT Edge symulator** w palecie poleceń, aby zatrzymać symulator i wyczyścić.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Debugowanie w dołączyć trybu z IoT Edge Symulatorem (C#, Node.js, Java, usługi Azure Functions)

Rozwiązanie domyślny zawiera dwa moduły, jeden z nich jest modułu czujnika temperatury symulowane, a drugi to moduł potoku. Czujnik temperatury symulowane wysyła komunikaty do modułu potoku, a następnie komunikaty są potokiem do usługi IoT Hub. W folderze modułu, który został utworzony istnieje kilka plików Docker dla kontenera różnych typów. Użyj tych plików, które kończą się rozszerzeniem **.debug** do tworzenia modułu do testowania.

Obecnie dołączyć debugowanie w trybie jest obsługiwana tylko następujący:

- C#moduły, w tym dla usługi Azure Functions obsługuje debugowanie w kontenerach amd64 systemu Linux
- Moduły node.js obsługi debugowania w amd64 systemu Linux i kontenerów arm32v7 i kontenery amd64 Windows
- Moduły języka Java obsługuje debugowanie w amd64 oraz arm32v7 kontenerów systemu Linux

> [!TIP]
> Można przełączać się między opcje dla platformy domyślnego rozwiązania usługi IoT Edge, klikając pozycję elementu w pasku stanu programu Visual Studio Code.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie usługi IoT Edge symulatora dla rozwiązania IoT Edge

W komputerze deweloperskim można uruchomić symulatora usługi IoT Edge, zamiast instalować ją z demona zabezpieczeń usługi IoT Edge, aby uruchomić rozwiązanie IoT Edge.

1. W Eksploratorze urządzenia po lewej stronie, kliknij prawym przyciskiem myszy na swój identyfikator urządzenia usługi IoT Edge, a następnie wybierz **Instalatora IoT Edge symulator** można uruchomić symulatora przy użyciu parametrów połączenia urządzenia.

1. Widać, że symulator IoT Edge pomyślnie skonfigurowano, zapoznając się szczegóły postępu w zintegrowanym terminalu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Kompilowanie i uruchamianie kontenerów do debugowania i debugowania w dołączyć tryb

1. Otwórz plik modułu (`program.cs`, `app.js`, `App.java`, lub `<your module name>.cs`) i Dodaj punkt przerwania.

1. W widoku Eksplorator kodu w usłudze Visual Studio kliknij prawym przyciskiem myszy `deployment.debug.template.json` pliku rozwiązania, a następnie wybierz pozycję **rozwiązanie kompilacji i uruchomienia usługi IoT Edge w symulatorze**. Możesz obejrzeć dzienniki kontenera modułu, w tym samym oknie. Możesz także przejść do widoku platformy Docker, aby obserwować stan kontenera.

   ![Obserwuj zmienne](media/how-to-develop-csharp-module/view-log.png)

1. Przejdź do widoku debugowania programu Visual Studio Code, a następnie wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinny być podobne do  ***&lt;swoją nazwę modułu&gt;* zdalnego debugowania**

1. Wybierz **Rozpocznij debugowanie** lub naciśnij **F5**. Wybierz proces do dołączenia.

1. W widoku debugowania programu Visual Studio Code zobaczysz zmiennych w panelu po lewej stronie.

1. Aby zatrzymać sesję debugowania, najpierw wybierz przycisk Zatrzymaj lub naciśnij **Shift + F5**, a następnie wybierz pozycję **usługi Azure IoT Edge: Zatrzymaj IoT Edge symulator** z palety poleceń.

> [!NOTE]
> Poprzedni przykład pokazuje, jak można debugować moduły usługi IoT Edge w kontenerach. Ujawnionych portów on dodany do kontenera usługi modułu `createOptions` ustawienia. Po zakończeniu debugowania moduły, zalecane jest usunięcie tych ujawnionych portów dla modułów usługi IoT Edge gotowe do produkcji.
>
> Dla modułów napisanych w C#, łącznie z usługi Azure Functions, w tym przykładzie opiera się na wersję debugowania `Dockerfile.amd64.debug`, która zawiera debuger wiersza polecenia platformy .NET Core (VSDBG) w obrazie kontenera podczas jego tworzenia. Po debugowaniu swoje C# moduły, firma Microsoft zaleca bezpośrednio używać pliku Dockerfile bez VSDBG dla modułów usługi IoT Edge gotowe do produkcji.

## <a name="debug-a-module-with-iot-edge-runtime"></a>Debugowanie modułu ze środowiskiem uruchomieniowym usługi IoT Edge

W każdym folderze modułu istnieje kilka plików Docker dla kontenera różnych typów. Użyj tych plików, które kończą się rozszerzeniem **.debug** do tworzenia modułu do testowania.

Podczas debugowania modułów ze środowiskiem uruchomieniowym usługi IoT Edge, moduły są uruchomione na podstawie środowisko uruchomieniowe usługi IoT Edge. Urządzenia usługi IoT Edge i usługi programu VS Code można w tym samym komputerze lub zazwyczaj są na różnych maszynach (VS Code znajduje się w komputerze deweloperskim i środowisko uruchomieniowe usługi IoT Edge i moduły, które są uruchomione w innej maszynie fizycznej). Poniższe kroki konieczne do wykonania dla sesji debugowania w programie VS Code.

- Konfigurowanie urządzenia usługi IoT Edge, twórz moduły usługi IoT Edge przy użyciu **.debug** pliku Dockerfile i wdrażanie na urządzeniu usługi IoT Edge. 
- Udostępnianie adresów IP i port w module dla debugera do dołączenia.
- Aktualizacja `launch.json` plików programu VS Code można dołączyć do procesu w kontenerze w komputerze zdalnym.

### <a name="build-and-deploy-your-module-and-deploy-to-iot-edge-device"></a>Tworzenie i wdrażanie modułu i wdrażanie na urządzeniu usługi IoT Edge

1. W programie Visual Studio Code Otwórz `deployment.debug.template.json` pliku, który zawiera wersję debugowania obrazów modułu poprawne `createOptions` zestaw wartości.

1. Jeżeli projektujesz modułu w języku Python, wykonaj następujące kroki, aby kontynuować:
   - Otwórz plik `main.py` i Dodaj następujący kod po sekcji importu:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```
   - Dodaj następujące jednego wiersza kodu do wywołania zwrotnego, który chcesz debugować:

      ```python
      ptvsd.break_into_debugger()
      ```

     Na przykład, jeśli chcesz debugować `receive_message_callback` metody, jak w przypadku wstawiania wiersza kodu, jak pokazano poniżej:

      ```python
      def receive_message_callback(message, hubManager):
          ptvsd.break_into_debugger()
          global RECEIVE_CALLBACKS
          message_buffer = message.get_bytearray()
          size = len(message_buffer)
          print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode ('utf-8'), size) )
          map_properties = message.properties()
          key_value_pair = map_properties.get_internals()
          print ( "    Properties: %s" % key_value_pair )
          RECEIVE_CALLBACKS += 1
          print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
          hubManager.forward_event_to_output("output1", message, 0)
          return IoTHubMessageDispositionResult.ACCEPTED
      ```

1. W palecie poleceń programu Visual Studio Code:
   1. Uruchom polecenie **usługi Azure IoT Edge: Rozwiązanie kompilacji i wypychania usługi IoT Edge**.

   1. Wybierz `deployment.debug.template.json` pliku rozwiązania.

1. W **Azure IoT Hub Devices** części widoku Eksploratora kodu w usłudze Visual Studio:
   1. Kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz pozycję **tworzenie wdrożenia dla jednego urządzenia**.

   1. Przejdź do swojego rozwiązania **config** folderu, wybierz `deployment.debug.amd64.json` pliku, a następnie wybierz **wybierz manifestu wdrażania krawędzi**.

Zobaczysz wdrożenie z Identyfikatorem wdrożenia w zintegrowanym terminalu pomyślnie utworzona.

Sprawdź stan usługi kontenera, uruchamiając `docker ps` polecenia w terminalu. Jeśli środowisko uruchomieniowe programu VS Code i usługi IoT Edge są uruchomione na tym samym komputerze, możesz również sprawdzić stan w widoku Docker kodu w usłudze Visual Studio.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger-to-attach"></a>Udostępnianie adresów IP i port w module dla debugera do dołączenia

Jeśli moduły są uruchomione w tym samym komputerze co kodu programu VS. Używasz localhost można dołączyć kontener i ustawienie prawidłowego portu w już **.debug** pliku Dockerfile i kontener modułu CreateOptions, można żądań, oraz `launch.json`. Można pominąć tę sekcję. Jeśli moduły i program VS Code są uruchomione na oddzielnych maszynach, wykonaj poniższe kroki dla każdego języka.

  - **C#, C# Funkcja**: [Konfigurowanie kanału SSH na maszynie deweloperskiej i urządzenie usługi IoT Edge](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes), Edytuj `launch.json` plik do załączenia.
  - **Node.js**: Upewnij się, moduł jest gotowy do debugery dołączyć i portu 9229 maszyny obiekt debugowany jest dostępny z zewnątrz. Można to sprawdzić, otwierając [http://%3cdebuggee-machine-IP%3e:9229/json] http:// < obiekt debugowany machine-IP >: 9229/json na maszynie debugera. Ten adres URL powinien być wyświetlony informacji na temat środowiska Node.js do debugowania. A następnie na komputerze debugera, Otwórz program VS Code, należy edytować `launch.json` plików, które dotyczą wartości profilu "< nazwa modułu > Remote Debug (Node.js)" (lub profilu "< nazwa modułu > zdalne debugowanie (środowiska Node.js na platformie Windows Container)", jeśli moduł działa jako Kontener Windows) jest adresem IP maszyny debugowany program.
  - **Java**: Tworzenie ssh tunel do urządzenia usługi edge, uruchamiając `ssh -f <username>@<edgedevicehost> -L 5005:127.0.0.1:5005 -N`, należy edytować `launch.json` plik do załączenia. Dowiedz się więcej o ustawieniach [tutaj](https://code.visualstudio.com/docs/java/java-debugging). 
  - **Python**: W kodzie `ptvsd.enable_attach(('0.0.0.0', 5678))`, zmień 0.0.0.0 na adres IP urządzenia usługi IoT Edge. Tworzenie, wypychania i ponownie wdrożyć moduły usługi IoT Edge. W `launch.json` w komputerze deweloperskim, zaktualizuj `"host"` `"localhost"` zmienić `"localhost"` z publicznym adresem IP ze zdalnym urządzeniem usługi IoT Edge.


### <a name="debug-your-module"></a>Debugowanie modułu

Visual Studio Code utrzymuje debugowania informacje o konfiguracji w `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania usługi IoT Edge. Aktualizuje po dodaniu każdego nowego modułu, który obsługuje debugowanie.

1. W widoku debugowania programu Visual Studio Code wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinny być podobne do  ***&lt;swoją nazwę modułu&gt;* zdalnego debugowania**

1. Otwórz plik modułu dla języka programowania, a następnie Dodaj punkt przerwania:
   - **C#, C# Funkcja**: Otwórz plik `Program.cs` i Dodaj punkt przerwania.
   - **Node.js**: Otwórz plik `app.js` i Dodaj breakpont.
   - **Java**: Otwórz plik `App.java` i Dodaj punkt przerwania.
   - **Python**: Otwórz `main.py` i Dodaj punkt przerwania w metodzie wywołania zwrotnego, w którym został dodany `ptvsd.break_into_debugger()` wiersza.
   - **C**: Otwórz plik `main.c` i Dodaj punkt przerwania.

1. Wybierz **Rozpocznij debugowanie** lub wybierz **F5**. Wybierz proces do dołączenia.

1. W widoku debugowania programu Visual Studio Code zobaczysz zmiennych w panelu po lewej stronie.

> [!NOTE]
> Poprzedni przykład pokazuje, jak można debugować moduły usługi IoT Edge w kontenerach. Ujawnionych portów on dodany do kontenera usługi modułu `createOptions` ustawienia. Po zakończeniu debugowania moduły, zalecane jest usunięcie tych ujawnionych portów dla modułów usługi IoT Edge gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu modułu, Dowiedz się jak [wdrożyć moduły usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md).

Tworzenie modułów na urządzeniach usługi IoT Edge [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
