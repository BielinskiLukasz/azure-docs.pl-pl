---
title: Samouczek opracowywania C# modułu dla systemu Linux — Azure IoT Edge | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł IoT Edge przy C# użyciu kodu i wdrożyć go na urządzeniu z systemem Linux IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d1a1a610c5cb198bd4df47033f4b8359b3d93007
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452556"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Samouczek: opracowywanie C# modułu IoT Edge dla urządzeń z systemem Linux

Użyj Visual Studio Code, aby C# opracować kod i wdrożyć go na urządzeniu z systemem Linux z Azure IoT Edge. 

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z przewodnikami Szybki start dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows](quickstart.md) lub [Linux](quickstart-linux.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge opartego na zestawie .NET Core SDK 2.1 przy użyciu programu Visual Studio Code.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku przedstawiono sposób tworzenia modułu w **C#** programie przy użyciu **Visual Studio Code**i sposobu wdrażania go na **urządzeniu z systemem Linux**. Jeśli tworzysz moduły dla urządzeń z systemem Windows, przejdź do [obszaru C# opracowywanie modułu IoT Edge dla urządzeń z systemem Windows](tutorial-csharp-module-windows.md) .

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania C# modułów w systemie Linux: 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#moduły dla LinuxAMD64 w VS Code](./media/tutorial-c-module/green-check.png) | ![C#moduły dla LinuxAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |
| **ARM32 systemu Linux** | ![C#moduły dla LinuxARM32 w VS Code](./media/tutorial-c-module/green-check.png) | ![C#moduły dla LinuxARM64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Obsługa urządzeń z systemem Linux ARM64 jest dostępna w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać więcej informacji, zobacz [programowanie i debugowanie modułów IoT Edge arm64 w Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy zapoznać się z poprzednim samouczkiem dotyczącym konfigurowania środowiska programistycznego, [opracowywania modułu IoT Edge dla urządzenia z systemem Linux](tutorial-develop-for-linux.md). Po ukończeniu tego samouczka należy spełnić następujące wymagania wstępne: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Linux Azure IoT Edge](quickstart-linux.md).
* Rejestr kontenerów, taki jak [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany przy użyciu [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Platforma [Docker ce](https://docs.docker.com/install/) skonfigurowana do uruchamiania kontenerów systemu Linux.

Aby ukończyć te samouczki, przygotuj następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniżej przedstawiono procedurę tworzenia projektu modułu IoT Edge dla C# programu przy użyciu Visual Studio Code i rozszerzenia narzędzi Azure IoT Tools. Po utworzeniu szablonu projektu Dodaj nowy kod, aby moduł odfiltruje komunikaty na podstawie ich raportowanych właściwości. 

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

2. W palecie poleceń wprowadź i uruchom polecenie **Azure: zaloguj się**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **C# Module**. |
   | Podaj nazwę modułu | Nazwij moduł **CSharpModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/csharpmodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z sekcji **klucze dostępu** w rejestrze kontenerów platformy Azure. 

1. W eksploratorze programu VS Code otwórz plik **env**. 
2. Zaktualizuj pola przy użyciu wartości **Nazwa użytkownika** i **hasło** z usługi Azure Container Registry. 
3. Zapisz ten plik. 

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie Visual Studio Code mogą opracowywać C# moduły dla urządzeń z systemem Linux amd64 i Linux ARM32v7. Należy wybrać, która architektura ma być ukierunkowana na każde rozwiązanie, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Wartość domyślna to Linux AMD64. 

1. Otwórz paletę poleceń i Wyszukaj **Azure IoT Edge: Ustaw domyślną platformę docelową dla rozwiązania brzegowego**lub wybierz ikonę skrótu na pasku bocznym u dołu okna. 

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, co spowoduje zachowanie domyślnego **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze programu VS Code otwórz plik **modules** > **CSharpModule** > **Program.cs**.

2. Na początku przestrzeni nazw **CSharpModule** dodaj trzy instrukcje **using** dla typów, które będą używane później:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Dodaj zmienną **temperatureThreshold** do klasy **Program**. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do centrum IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Dodaj klasy **MessageBody**, **Machine**, i **Ambient** do klasy **Program**. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}         
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}         
    }
    ```

5. Znajdź funkcję **init** . Ta funkcja tworzy i konfiguruje obiekt **ModuleClient** , który umożliwia modułowi łączenie się z lokalnymi Azure IoT Edge środowiska uruchomieniowego w celu wysyłania i odbierania komunikatów. Po utworzeniu elementu **ModuleClient** kod odczytuje wartość **temperatureThreshold** z żądanych właściwości bliźniaczej reprezentacji modułu. Ten kod rejestruje wywołanie zwrotne w celu odbierania komunikatów z centrum usługi IoT Edge za pośrednictwem punktu końcowego **input1**. Zastąp metodę **SetInputMessageHandlerAsync** nową metodą i dodaj metodę **SetDesiredPropertyUpdateCallbackAsync** dla aktualizacji do żądanych właściwości. Aby dokonać tej zmiany, zastąp ostatni wiersz metody **Init** następującym kodem:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Dodaj metodę **onDesiredPropertiesUpdate** do klasy **Program**. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Zastąp metodę **PipeMessage** metodą **FilterMessages**. Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu. Dodaje ona również właściwość **MessageType** do komunikatu z wartością ustawioną na **Alert**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Zapisz plik Program.cs.

9. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. 

10. Dodaj bliźniaczą reprezentację modułu **CSharpModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **modulesContent** po bliźniaczej reprezentacji modułu **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-csharp-module/module-twin.png)

11. Zapisz plik deployment.template.json.


## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do CSharpModule, który będzie odfiltrować komunikaty, w przypadku których zgłoszona temperatura maszyny mieści się w dopuszczalnym limicie. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)**  > **Terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z usługi Azure Container Registry. Te wartości można pobrać z sekcji **klucze dostępu** rejestru w Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające korzystanie z `--password-stdin`. Chociaż najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje dotyczące [logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrożenia, z wbudowanymi informacjami w szablonie wdrożenia i innych plikach rozwiązania. Następnie działa `docker build`, aby skompilować obraz kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie uruchamia `docker push`, aby wypchnąć repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Aby wdrożyć projekt modułu na urządzeniu IoT Edge, użyj rozszerzenia Eksploratora Visual Studio Code i narzędzia Azure IoT Tools. Masz już manifest wdrożenia przygotowany dla danego scenariusza, plik **Deployment. JSON** w folderze config. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W Eksploratorze Visual Studio Code rozwiń sekcję **urządzenia IoT Hub platformy Azure** , aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**. 

5. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json. 

6. Kliknij przycisk Odśwież. Powinna zostać wyświetlona nowa **CSharpModule** z uruchomionym modułem **SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.  

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Wyświetl komunikaty docierające do IoT Hub. Dostarczenie komunikatów może chwilę potrwać, ponieważ urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie CModule zaczekają na 25 stopni przed wysłaniem komunikatów. Dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury. 

   ![Wyświetl komunikaty docierające do IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

W manifeście wdrożenia użyto sznurka modułu CSharpModule w celu ustawienia progu temperatury na 25 stopni. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W Visual Studio Code rozwiń Szczegóły na urządzeniu IoT Edge, aby zobaczyć uruchomione moduły. 

2. Kliknij prawym przyciskiem myszy pozycję **CSharpModule** i wybierz pozycję **Edytuj sznurek modułu**. 

3. Znajdź **TemperatureThreshold** w odpowiednich właściwościach. Zmień jej wartość na nową temperaturę o 5 stopni do 10 stopni wyższych niż Najnowsza zgłoszona temperatura. 

4. Zapisz plik splotu modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w okienku Edycja sznurka modułu i wybierz polecenie **Aktualizuj sznurek modułu**. 

5. Monitoruj przychodzące komunikaty z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat [opracowywania własnych modułów IoT Edge](module-development.md) lub tworzenia [modułów z Visual Studio Code](how-to-vs-code-develop-module.md). Możesz przejść do kolejnych samouczków, aby dowiedzieć się, jak Azure IoT Edge może pomóc w wdrażaniu usług Azure Cloud Services w celu przetwarzania i analizowania danych na krawędzi.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
