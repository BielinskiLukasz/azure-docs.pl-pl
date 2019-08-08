---
title: Tworzenie niestandardowego modułu języka Python — Azure IoT Edge | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka Python i wdrożyć go na urządzeniu brzegowym.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 55cdb70a02aae62a69f4fc930f4c6ee385a24702
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839547"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Samouczek: Opracowywanie i wdrażanie modułu IoT Edge Python dla urządzeń z systemem Linux

Użyj Visual Studio Code, aby opracować kod C i wdrożyć go na urządzeniu z systemem Linux z Azure IoT Edge. 

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. Ten samouczek przeprowadzi Cię przez proces tworzenia i wdrażania modułu IoT Edge, który filtruje dane czujników na urządzeniu IoT Edge skonfigurowanym w ramach przewodnika Szybki Start. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge w języku Python przy użyciu programu Visual Studio Code.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku przedstawiono sposób tworzenia modułu w języku **Python** przy użyciu **Visual Studio Code**i sposobu wdrażania go na **urządzeniu z systemem Linux**. IoT Edge nie obsługuje modułów języka Python dla urządzeń z systemem Windows. 

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów języka Python w systemie Linux: 

| Python | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Używanie VS Code dla modułów języka Python w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **ARM32 systemu Linux** | ![Używanie VS Code dla modułów języka Python w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy zapoznać się z poprzednim samouczkiem dotyczącym konfigurowania środowiska deweloperskiego do tworzenia kontenerów systemu Linux: [Opracowywanie modułów IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wykonując jeden z tych samouczków, należy spełnić następujące wymagania wstępne: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Linux Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenerów, taki jak [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany przy użyciu [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Platforma [Docker ce](https://docs.docker.com/install/) skonfigurowana do uruchamiania kontenerów systemu Linux.

Aby utworzyć moduł IoT Edge w języku Python, Zainstaluj następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Rozszerzenie języka Python dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Python](https://www.python.org/downloads/).
* Narzędzie [pip](https://pip.pypa.io/en/stable/installing/#installation) do instalacji pakietów języka Python (zazwyczaj instalowane wraz ze środowiskiem języka Python).

>[!Note]
>Upewnij się, że folder `bin` znajduje się w ramach ścieżki dla Twojej platformy. Zazwyczaj jest to `~/.local/` w przypadku systemów UNIX i macOS lub `%APPDATA%\Python` w przypadku systemu Windows.

## <a name="create-a-module-project"></a>Tworzenie projektu modułu
W następujących krokach przedstawiono sposób tworzenia modułu usługi IoT Edge w języku Python przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Użyj VS Code, aby utworzyć szablon rozwiązania w języku Python, który można skompilować w oparciu o. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Terminal**, aby otworzyć zintegrowany terminal programu VS Code.

1. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

1. W palecie poleceń wprowadź i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)** , a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami i podaj następujące informacje, aby utworzyć rozwiązanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz pozycję **Moduł Python**. |
   | Podaj nazwę modułu | Nazwij moduł **PythonModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/pythonmodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia repozytorium kontenera, udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. 

1. W eksploratorze programu VS Code otwórz plik **env**. 
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry. 
3. Zapisz plik env. 

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie Visual Studio Code mogą opracowywać moduły C dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać, która architektura ma być ukierunkowana na każde rozwiązanie, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Wartość domyślna to Linux AMD64. 

1. Otwórz paletę poleceń i Wyszukaj **Azure IoT Edge: Ustaw domyślną platformę docelową**dla rozwiązania brzegowego lub wybierz ikonę skrótu na pasku bocznym u dołu okna. 

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, co spowoduje zachowanie domyślnego **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Każdy szablon zawiera przykładowy kod, który wykonuje symulowane dane czujników z modułu **SimulatedTemperatureSensor** i kieruje go do centrum IoT Hub. W tej sekcji dodasz kod, który rozszerza moduł **pythonModule** w celu analizowania komunikatów przed ich wysłaniem. 

1. W eksploratorze programu VS Code otwórz kolejno pozycje **modules** > **PythonModule** > **main.py**.

2. Na początku pliku **main.py** zaimportuj bibliotekę **json**:

    ```python
    import json
    ```

3. Dodaj zmienne **TEMPERATURE_THRESHOLD** i **TWIN_CALLBACKS** w obszarze liczników globalnych. Zmienna „temperature_threshold” określa wartość zmierzonej temperatury maszyny, której przekroczenie spowoduje wysłanie danych do centrum IoT Hub.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Zastąp funkcję **receive_message_callback** następującym kodem:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Dodaj nową funkcję o nazwie **module_twin_callback**. Ta funkcja jest wywoływana w przypadku zaktualizowania odpowiednich właściwości.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. W klasie **HubManager** dodaj nowy wiersz do metody **__init__** , aby zainicjować właśnie dodaną funkcję **module_twin_callback**:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Zapisz plik main.py.

8. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. 

9. Dodaj bliźniaczą reprezentację modułu **PythonModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **moduleContent**, po bliźniaczej reprezentacji modułu **$edgeHub**: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-python-module/module-twin.png)

10. Zapisz plik deployment.template.json.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do PythonModule, który będzie odfiltrować komunikaty, w przypadku których zgłoszona temperatura maszyny mieści się w dopuszczalnym limicie. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)**  > **Terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z usługi Azure Container Registry. Te wartości można pobrać z sekcji **klucze dostępu** rejestru w Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające użycie programu `--password-stdin`. Chociaż najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje dotyczące [logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrożenia, z wbudowanymi informacjami w szablonie wdrożenia i innych plikach rozwiązania. Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.


## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Aby wdrożyć projekt modułu na urządzeniu IoT Edge, użyj rozszerzenia Eksploratora Visual Studio Code i narzędzia Azure IoT Tools. Masz już manifest wdrożenia przygotowany dla danego scenariusza, plik **Deployment. JSON** w folderze config. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W Eksploratorze Visual Studio Code rozwiń sekcję **urządzenia IoT Hub platformy Azure** , aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json.

4. Kliknij przycisk Odśwież. Powinna zostać wyświetlona nowa **PythonModule** z uruchomionym modułem **SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Wyświetl komunikaty docierające do IoT Hub. Dostarczenie komunikatów może chwilę potrwać, ponieważ urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie PythonModule zaczekają na 25 stopni przed wysłaniem komunikatów. Dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury. 

## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

W manifeście wdrożenia użyto sznurka modułu PythonModule w celu ustawienia progu temperatury na 25 stopni. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W Visual Studio Code rozwiń Szczegóły na urządzeniu IoT Edge, aby zobaczyć uruchomione moduły. 

2. Kliknij prawym przyciskiem myszy pozycję **PythonModule** i wybierz pozycję **Edytuj sznurek modułu**. 

3. Znajdź **TemperatureThreshold** w odpowiednich właściwościach. Zmień jej wartość na nową temperaturę o 5 stopni do 10 stopni wyższych niż Najnowsza zgłoszona temperatura. 

4. Zapisz plik splotu modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w okienku Edycja sznurka modułu i wybierz polecenie **Aktualizuj sznurek modułu**. 

6. Monitoruj przychodzące komunikaty z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat [opracowywania własnych modułów IoT Edge](module-development.md) lub tworzenia [modułów z Visual Studio Code](how-to-vs-code-develop-module.md). Możesz przejść do kolejnych samouczków, aby dowiedzieć się, jak Azure IoT Edge może pomóc w wdrażaniu usług Azure Cloud Services w celu przetwarzania i analizowania danych na krawędzi.

> [!div class="nextstepaction"]
> [](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)[](tutorial-deploy-custom-vision.md) [](tutorial-deploy-machine-learning.md)funkcji Machine LearningCustomVisionService
> 
> 