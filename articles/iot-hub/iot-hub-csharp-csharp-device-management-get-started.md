---
title: Wprowadzenie do zarządzania urządzeniami Azure IoT Hub (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak zainicjować ponownego uruchomienia urządzenia zdalnego za pomocą zarządzania urządzeniami Azure IoT Hub. Urządzenia usługi Azure IoT SDK dla platformy .NET umożliwia implementowanie aplikacji symulowanego urządzenia, która obejmuje metody bezpośredniej i usługi Azure IoT SDK dla platformy .NET w celu zaimplementowania app service, która wywołuje metody bezpośredniej.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: fd26fb9e45f8790342472fa0bae92f74bea2fc70
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242470"
---
# <a name="get-started-with-device-management-netnet"></a>Wprowadzenie do zarządzania urządzeniami (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj witryny Azure portal do utworzenia Centrum IoT, a następnie tworzenie tożsamości urządzenia w usłudze IoT hub.

* Tworzenie aplikacji symulowanego urządzenia, która zawiera bezpośrednie metodę, która wywołuje ponowne uruchomienie tego urządzenia. Metody bezpośrednie są wywoływane z poziomu chmury.

* Tworzenie aplikacji konsolowej .NET, która wywołuje metody bezpośredniej ponowny rozruch w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET:

* **SimulateManagedDevice**, łączy się z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia otrzymuje metody bezpośredniej o ponowne uruchomienie komputera, symuluje ponowne uruchomienie komputera fizycznego, a następnie raportuje czas ostatniego ponownego uruchomienia.

* **TriggerReboot**, która wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia, wyświetla odpowiedzi i wyświetla zaktualizowany zgłoszonych właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2017.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Zdalne ponowne uruchamianie systemu na urządzeniu, korzystając z metody bezpośredniej wyzwalacza

W tej sekcji służy do tworzenia aplikacji konsolowej .NET (przy użyciu języka C#) inicjujący zdalnego ponowny rozruch na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań bliźniaczych reprezentacji urządzeń, aby odnaleźć ostatniego ponownego uruchomienia dla tego urządzenia.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do nowego rozwiązania, używając szablonu projektu **Aplikacja konsoli (.NET Framework)**. Upewnij się, że program .NET Framework jest w wersji 4.5.1 lub nowszej. Nadaj projektowi nazwę **TriggerReboot**.

    ![Nowy projekt Visual C# Windows Classic Desktop](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **TriggerReboot** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.

3. W **Menedżera pakietów NuGet** wybierz **Przeglądaj**, wyszukaj **Microsoft.Azure.Devices**, wybierz opcję **zainstalować** do zainstalowania **Microsoft.Azure.Devices** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, instaluje i dodanie odwołania do [zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet pakietu oraz jego zależności.

    ![Okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia Centrum IoT Hub dla Centrum utworzonego w sekcji "Tworzenie Centrum IoT". 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Dodaj następującą metodę do klasy **Program**.  Ten kod pobiera bliźniaczą reprezentację urządzenia dla trwa jego ponowne uruchamianie urządzenia i wysyła zgłaszane właściwości.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Dodaj następującą metodę do klasy **Program**.  Ten kod powoduje zainicjowanie ponowny rozruch na urządzeniu, korzystając z metody bezpośredniej.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

7. Na koniec dodaj następujące wiersze do metody **Główne**:
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Skompiluj rozwiązanie.

> [!NOTE]
> W tym samouczku wykonuje jedno zapytanie zgłaszanych właściwości na urządzeniu. W kodzie produkcyjnym zalecamy sondowania, aby wykryć zmiany w zgłaszanych właściwości.

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji wykonano następujące czynności:

* Tworzenie aplikacji konsoli .NET, która reaguje na metodę bezpośrednią wywołaną przez chmurę.

* Wyzwalanie ponownego uruchomienia urządzenia symulowanego.

* Włączanie zapytań bliźniaczych reprezentacji urządzeń w celu zidentyfikowania urządzeń i po ich zostały ostatnio uruchomiona ponownie przy użyciu zgłoszonych właściwości.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **SimulateManagedDevice**.
   
    ![Nowa aplikacja urządzenia Visual C# Windows Classic](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateManagedDevice** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

3. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **Microsoft.Azure.Devices.Client**. Wybierz **zainstalować** zainstalował **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, instaluje i dodanie odwołania do [zestaw SDK urządzeń Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet pakietu oraz jego zależności.
   
    ![Aplikacja kliencka okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia urządzenia zanotowanym w poprzedniej sekcji.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Dodaj następujący kod do implementacji metody bezpośredniej na urządzeniu:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. Na koniec Dodaj następujący kod do **Main** metodę, aby otworzyć połączenie do usługi IoT hub i zainicjować odbiornika metody:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```
        
8. W Eksploratorze rozwiązań programu Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij polecenie **Ustaw projekty startowe...**. Wybierz **pojedynczy projekt startowy**, a następnie wybierz pozycję **SimulateManagedDevice** project w menu rozwijanym. Skompiluj rozwiązanie.       

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. Aby uruchomić aplikację urządzenia platformy .NET **SimulateManagedDevice**. Kliknij prawym przyciskiem myszy **SimulateManagedDevice** projektu, wybierz opcję **debugowania**, a następnie wybierz pozycję **Uruchom nowe wystąpienie**. Należy zacząć, nasłuchiwanie wywołania metody z usługi IoT hub. 

2. Teraz, gdy urządzenie jest podłączone i Oczekiwanie na wywołania metody uruchamiania .NET **TriggerReboot** aplikacji, które można wywołać metody ponownego uruchomienia w symulowanej aplikacji urządzenia. Aby to zrobić, kliknij prawym przyciskiem myszy **TriggerReboot** projektu, wybierz opcję **debugowania**, a następnie wybierz pozycję **Uruchom nowe wystąpienie**. Powinien zostać wyświetlony "Ponowne uruchomienie!" napisana **SimulatedManagedDevice** konsoli i zgłaszanych właściwości urządzenia, które obejmują ostatnich ponowny rozruch czasu, napisany w **TriggerReboot** konsoli.
   
    ![Uruchamiania aplikacji usługi i urządzenia](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
