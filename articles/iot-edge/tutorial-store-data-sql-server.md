---
title: Samouczek — przechowywanie danych za pomocą modułu SQL przy użyciu Azure IoT Edge
description: W tym samouczku pokazano, jak przechowywać dane lokalnie na urządzeniu IoT Edge przy użyciu modułu SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d1b5ea9a9f78bc8a83159a34026d58d7a8cc89b
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944268"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Samouczek: przechowywanie danych na brzegu sieci przy użyciu baz danych programu SQL Server

Wdróż moduł SQL Server w celu przechowywania danych na urządzeniu z systemem Linux, na których jest uruchomiony Azure IoT Edge.

Usługi Azure IoT Edge i programu SQL Server można używać do przechowywania danych i wykonywania zapytań dotyczących danych na brzegu sieci. Usługa Azure IoT Edge ma podstawowe funkcje magazynu w celu buforowania komunikatów, jeśli urządzenie przejdzie w tryb offline, a następnie przekazania ich dalej po ponownym ustanowieniu połączenia. Możesz jednak potrzebować bardziej zaawansowanych możliwości magazynu, takich jak możliwość lokalnego wykonywania zapytań o dane. Urządzenia IoT Edge mogą korzystać z lokalnych baz danych w celu przeprowadzenia bardziej złożonej obliczeń bez konieczności utrzymania połączenia z IoT Hub.

Ten artykuł zawiera instrukcje dotyczące wdrażania bazy danych programu SQL Server na urządzeniu usługi IoT Edge. Usługa Azure Functions, uruchomiona na urządzeniu usługi IoT Edge, strukturyzuje dane przychodzące, a następnie wysyła je do bazy danych. Kroki opisane w tym artykule można zastosować również do innych baz danych, które działają w kontenerach, na przykład MySQL lub PostgreSQL.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Używanie programu Visual Studio Code do tworzenia funkcji platformy Azure
> * Wdrażanie bazy danych SQL na urządzeniu usługi IoT Edge
> * Używanie programu Visual Studio Code do kompilowania modułów i wdrażania ich na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy zapoznać się z poprzednim samouczkiem dotyczącym konfigurowania środowiska deweloperskiego do tworzenia kontenerów systemu Linux: [Tworzenie modułów IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wykonując ten samouczek, należy spełnić następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* Urządzenie z [systemem amd64 Linux Azure IoT Edge](quickstart-linux.md).
  * Nie można uruchomić SQL Server urządzeń ARM, takich jak Raspberry PiS. Jeśli chcesz używać języka SQL na urządzeniu ARM, możesz zarejestrować się, aby wypróbować [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) w wersji zapoznawczej.
* Rejestr kontenerów, taki jak [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany przy użyciu [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Platforma [Docker ce](https://docs.docker.com/install/) skonfigurowana do uruchamiania kontenerów systemu Linux.

W tym samouczku do przesyłania danych do SQL Server służy moduł Azure Functions. Aby opracować moduł IoT Edge z Azure Functions, należy zainstalować następujące dodatkowe wymagania wstępne na komputerze deweloperskim:

* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp) dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji

Aby wysyłać dane do bazy danych, potrzebujesz modułu, który może poprawnie tworzyć struktury danych, a następnie przechowywać je w tabeli.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

W następujących krokach przedstawiono sposób tworzenia funkcji usługi IoT Edge przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

1. Otwórz program Visual Studio Code.

2. Otwórz paletę poleceń programu VS Code, wybierając pozycję **Widok** > **Paleta poleceń**.

3. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania, na przykład **SqlSolution**, lub zaakceptuj nazwę domyślną. |
   | Wybierz szablon modułu | Wybierz pozycję **Azure Functions — C#** . |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **sqlFunction**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Końcowy ciąg wygląda jak \<Nazwa rejestru\>. azurecr.io/sqlfunction. |

   W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie Visual Studio Code mogą opracowywać moduły C dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać, która architektura ma być ukierunkowana na każde rozwiązanie, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Wartość domyślna to Linux AMD64.

1. Otwórz paletę poleceń i Wyszukaj **Azure IoT Edge: Ustaw domyślną platformę docelową dla rozwiązania brzegowego**lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, co spowoduje zachowanie domyślnego **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze programu VS Code otwórz plik **modules** > **sqlFunction** > **sqlFunction.cs**.

2. Zastąp całą zawartość pliku następującym kodem:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

3. W wierszu 35 zastąp ciąg **\<sql connection string\>** następującym ciągiem. Właściwość **źródła danych** odwołuje się do kontenera SQL Server, który jeszcze nie istnieje. Utworzysz go przy użyciu nazwy **SQL** w następnej sekcji.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Zapisz plik **sqlFunction.cs**.

5. Otwórz plik **sqlFunction.csproj**.

6. Znajdź grupę odwołań do pakietów i Dodaj nową, aby uwzględnić SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Zapisz plik **sqlFunction.csproj**.

## <a name="add-the-sql-server-container"></a>Dodawanie kontenera SQL Server

[Manifest wdrożenia](module-composition.md) deklaruje moduły, które środowisko uruchomieniowe usługi IoT Edge zainstaluje na urządzeniu usługi IoT Edge. Podano kod, aby utworzyć dostosowany moduł funkcji w poprzedniej sekcji, ale moduł SQL Server jest już zbudowany i dostępny w witrynie Azure Marketplace. Wystarczy poinstruować środowisko uruchomieniowe usługi IoT Edge, że ma dołączyć ten moduł, a następnie skonfigurować go na urządzeniu.

1. W Visual Studio Code Otwórz paletę poleceń, wybierając pozycję **wyświetl** > **paletę poleceń**.

2. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: Dodaj moduł IoT Edge**. W palecie poleceń podaj następujące informacje, aby dodać nowy moduł:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz plik szablonu wdrożenia | Paleta poleceń podświetla plik Deployment. Template. JSON w bieżącym folderze rozwiązania. Wybierz ten plik.  |
   | Wybierz szablon modułu | Wybierz pozycję **moduł w witrynie Azure Marketplace**. |

3. W witrynie Azure IoT Edge Portal Marketplace Wyszukaj i wybierz **moduł SQL Server**.

4. Zmień nazwę modułu na **SQL**, wszystkie małe litery. Ta nazwa jest zgodna z nazwą kontenera zadeklarowaną w parametrach połączenia w pliku sqlFunction.cs.

5. Wybierz pozycję **Importuj** , aby dodać moduł do rozwiązania.

6. W folderze rozwiązania otwórz plik **Deployment. Template. JSON** .

7. Znajdź sekcję **modules**. Powinny być widoczne trzy moduły. Moduł *SimulatedTemperatureSensor* jest dołączany domyślnie do nowych rozwiązań i udostępnia dane testowe do użycia z innymi modułami. Module *SqlFunction* to moduł, który początkowo został utworzony i zaktualizowany przy użyciu nowego kodu. Na koniec moduł *SQL* został zaimportowany z portalu Azure Marketplace.

   >[!Tip]
   >Moduł SQL Server jest dostarczany z domyślnym hasłem ustawionym w zmiennych środowiskowych manifestu wdrożenia. Za każdym razem, gdy tworzysz kontener programu SQL Server w środowisku produkcyjnym, zaleca się [zmienić domyślne hasło administratora systemu](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Zamknij plik **Deployment. Template. JSON** .

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzednich sekcjach utworzono rozwiązanie z jednym modułem, a następnie dodano drugi do szablonu manifestu wdrożenia. Moduł SQL Server jest hostowany publicznie przez firmę Microsoft, ale należy konteneryzowanie kod w module Functions. W tej sekcji można skompilować rozwiązanie, utworzyć obrazy kontenerów dla modułu SqlFunctions i wypchnąć obraz do rejestru kontenerów.

1. W programie Visual Studio Code otwórz zintegrowany terminal, wybierając pozycję **Widok** > **Terminal**.  

1. Zaloguj się do rejestru kontenerów w programie Visual Studio Code, aby mógł on wypchnąć obrazy do rejestru. Użyj tych samych poświadczeń Azure Container Registry (ACR), które zostały dodane do pliku. env. W zintegrowanym terminalu wprowadź następujące polecenie:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające użycie parametru--Password-stdin. Użycie tego parametru wykracza poza zakres tego artykułu, jednak zalecamy zastosowanie tego najlepszego rozwiązania. Aby uzyskać więcej informacji, zobacz informacje dotyczące polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

1. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

Gdy poinformujesz Visual Studio Code, aby skompilować rozwiązanie, najpierw przyjmuje informacje w szablonie wdrożenia i wygeneruje plik Deployment. JSON w nowym folderze o nazwie **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Polecenie Build kompiluje kod i utworzy moduł. Następnie polecenie push wypycha kod do rejestru kontenerów, który został określony podczas inicjowania rozwiązania.

Można sprawdzić, czy moduł SqlFunction został pomyślnie wypchnięte do rejestru kontenerów. W Azure Portal przejdź do rejestru kontenerów. Wybierz pozycję **repozytoria** i wyszukaj ciąg **SqlFunction**. Pozostałe dwa moduły, SimulatedTemperatureSensor i SQL, nie będą przekazywane do rejestru kontenerów, ponieważ ich repozytoria znajdują się już w rejestrach firmy Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Wdrażanie rozwiązania na urządzeniu

Moduły można ustawić na urządzeniu za pomocą usługi IoT Hub, ale dostęp do usługi IoT Hub i urządzeń można również uzyskać za pomocą programu Visual Studio Code. W tej sekcji skonfigurujesz dostęp do usługi IoT Hub, a następnie użyjesz programu VS Code do wdrożenia rozwiązania na urządzeniu usługi IoT Edge.

1. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**.

2. Kliknij prawym przyciskiem myszy urządzenie, które ma być urządzeniem docelowym wdrożenia, i wybierz polecenie **Create deployment for single device** (Utwórz wdrożenie dla pojedynczego urządzenia).

3. W Eksploratorze plików przejdź do folderu **config** wewnątrz rozwiązania i wybierz plik **deployment.amd64**. Kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**.

   Nie należy używać pliku Deployment. Template. JSON jako manifestu wdrożenia.

Jeśli wdrożenie zakończy się pomyślnie, komunikat potwierdzenia zostanie wydrukowany w danych wyjściowych programu VS Code.

Odśwież stan urządzenia w sekcji Urządzenia usługi Azure IoT Hub w programie VS Code. Nowe moduły będą widoczne na liście i w ciągu kilku kolejnych minut zaczną być zgłaszane jako działające, w miarę instalowania i uruchamiania kontenerów. Możesz również sprawdzić, czy wszystkie moduły są wdrożone i uruchomione na urządzeniu. Na urządzeniu usługi IoT Edge uruchom następujące polecenie, aby wyświetlić stan modułów.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Tworzenie bazy danych SQL

Po zastosowaniu manifestu wdrożenia do urządzenia uzyskujesz trzy uruchomione moduły. Moduł SimulatedTemperatureSensor generuje symulowane dane środowiska. Moduł sqlFunction pobiera dane i formatuje je na potrzeby bazy danych. Ta sekcja zawiera opis konfigurowania bazy danych SQL do przechowywania danych dotyczących temperatury.

Uruchom następujące polecenia na urządzeniu usługi IoT Edge. Te polecenia łączą się z modułem **sql** działającym na urządzeniu oraz tworzą bazę danych i tabelę na potrzeby przechowywania przesyłanych do niego danych o temperaturze.

1. W narzędziu wiersza polecenia na urządzeniu usługi IoT Edge nawiąż połączenie z bazą danych.

      ```bash
      sudo docker exec -it sql bash
      ```

2. Otwórz narzędzie polecenia SQL.

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Utwórz bazę danych:

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Zdefiniuj tabelę.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Plik docker programu SQL Server możesz dostosować, aby automatycznie skonfigurować program SQL Server do wdrożenia na wielu urządzeniach usługi IoT Edge. Aby uzyskać więcej informacji, zobacz [projekt demonstracyjny kontenera programu Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="view-the-local-data"></a>Wyświetlanie danych lokalnych

Po utworzeniu tabeli moduł sqlFunction uruchamia przechowywanie danych w lokalnej bazie danych programu SQL Server 2017 na urządzeniu usługi IoT Edge.

W narzędziu polecenia SQL uruchom następujące polecenie, aby wyświetlić sformatowane dane tabeli:

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Wyświetlanie zawartości lokalnej bazy danych](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi Azure Functions zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat tego, jak [programować usługę Azure Functions za pomocą usługi Azure IoT Edge dla programu Visual Studio Code](how-to-develop-csharp-function.md).

Jeśli chcesz wypróbować inną metodę magazynu na krawędzi, przeczytaj temat jak korzystać z usługi Azure Blob Storage w IoT Edge.

> [!div class="nextstepaction"]
> [Store data at the edge with Azure Blob Storage on IoT Edge (Przechowywanie danych na urządzeniu brzegowym dzięki usłudze Azure Blob Storage w usłudze IoT Edge)](how-to-store-data-blob.md)
