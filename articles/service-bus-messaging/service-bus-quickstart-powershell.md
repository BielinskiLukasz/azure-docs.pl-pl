---
title: Przewodnik Szybki start — używanie programu Azure PowerShell do tworzenia kolejki usługi Service Bus | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak użyć programu Azure PowerShell do utworzenia kolejki usługi Service Bus. Następnie użyjesz przykładowej aplikacji, aby wysyłać komunikaty do kolejki i odbierać komunikaty z kolejki.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 80cef3efd65a3f68dba1856a892a9a120f7d95cd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990294"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>Szybki start: używanie programu Azure PowerShell do tworzenia kolejki usługi Service Bus
W tym przewodniku Szybki start opisano, jak wysyłać i odbierać komunikaty z i do kolejki usługi Service Bus, używając programu PowerShell do utworzenia przestrzeni nazw na potrzeby przesyłania komunikatów i kolejki w ramach tej przestrzeni nazw, a także jak uzyskać poświadczenia autoryzacji do tej przestrzeni nazw. Następnie w procedurze przedstawiono, jak wysyłać i odbierać komunikaty z tej kolejki przy użyciu [biblioteki platformy .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że zainstalowano następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][]. 
- [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](https://www.visualstudio.com/vs) lub nowszy. Tworzenie przykładowej, który wysyła wiadomości i odbiera wiadomości z kolejki przy użyciu programu Visual Studio. Przykład służy do testowania kolejki utworzonej w portalu. 
- [Zestaw NET Core SDK](https://www.microsoft.com/net/download/windows), wersja 2.0 lub nowsza.

Ten przewodnik Szybki start wymaga używania najnowszej wersji programu Azure PowerShell. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell][]. Jeśli znasz usługę Azure Cloud Shell, można użyć go bez konieczności instalowania programu Azure PowerShell na komputerze. Aby uzyskać szczegółowe informacje o usłudze Azure Cloud Shell, zobacz [Omówienie usługi Azure Cloud Shell](../cloud-shell/overview.md)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Najpierw, o ile ta czynność nie została jeszcze wykonana, należy zainstalować moduł programu PowerShell usługi Service Bus:

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

   ```azurepowershell-interactive
   Login-AzAccount
   ```

3. Uruchom następujące polecenia, aby ustawić kontekst bieżącej subskrypcji lub wyświetlić aktualnie aktywną subskrypcję:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Inicjowanie zasobów

W wierszu polecenia programu PowerShell uruchom następujące polecenia w celu zainicjowania zasobów usługi Service Bus. Pamiętaj, aby zastąpić wszystkie elementy zastępcze odpowiednimi wartościami:

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Po uruchomieniu polecenia cmdlet `Get-AzServiceBusKey` skopiuj i wklej parametry połączenia oraz nazwę wybranej kolejki do lokalizacji tymczasowej, np. Notatnika. Będą one potrzebne w kolejnym kroku.

## <a name="send-and-receive-messages"></a>Wysyłanie i odbieranie komunikatów

Po utworzeniu przestrzeni nazw i kolejki (i jeśli masz niezbędne poświadczenia) możesz wysyłać i odbierać komunikaty. Kod można zbadać w [tym folderze przykładów usługi GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Aby uruchomić kod, wykonaj następujące czynności:

1. Sklonuj [repozytorium GitHub usługi Service Bus](https://github.com/Azure/azure-service-bus/), wydając następujące polecenie:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. Przejdź do folderu przykładów `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Jeśli ta czynność nie została jeszcze wykonana, uzyskaj parametry połączenia przy użyciu następującego polecenia cmdlet programu PowerShell. Pamiętaj, aby zastąpić zmienne `my-resourcegroup` i `namespace-name` konkretnymi wartościami: 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5. W wierszu polecenia programu PowerShell wpisz następujące polecenie:

   ```shell
   dotnet build
   ```

6. Przejdź do folderu `bin\Debug\netcoreapp2.0`.

7. Wpisz następujące polecenie, aby uruchomić program. Pamiętaj, aby zastąpić `myConnectionString` wcześniej uzyskaną wartością, a `myQueueName` nazwą utworzonej przez siebie kolejki:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. Obserwuj 10 komunikatów wysłanych do kolejki, a następnie odebranych z niej:

   ![dane wyjściowe programu](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Uruchom następujące polecenie, aby usunąć grupę zasobów, przestrzeń nazw i wszystkie powiązane zasoby:

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

Ta sekcja zawiera więcej szczegółów na temat operacji wykonywanych przez przykładowy kod. 

### <a name="get-connection-string-and-queue"></a>Pobieranie kolejki i parametrów połączenia

Nazwa ciągu i kolejki połączenia są przekazywane do `Main()` metodę jako argumenty wiersza polecenia. Element `Main()` deklaruje dwie zmienne ciągu do przechowywania tych wartości:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Metoda `Main()` następnie rozpoczyna asynchroniczną pętlę komunikatów, `MainAsync()`.

### <a name="message-loop"></a>Pętla komunikatów

Metoda MainAsync() tworzy klienta kolejki za pomocą argumentów wiersza polecenia, wywołuje odbieranie obsługi wiadomości o nazwie `RegisterOnMessageHandlerAndReceiveMessages()`, a następnie wysyła zestaw komunikatów:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

Metoda `RegisterOnMessageHandlerAndReceiveMessages()` po prostu ustawia kilka opcji procedury obsługi komunikatów, a następnie wywołuje metodę `RegisterMessageHandler()` klienta kolejki, która rozpoczyna odbieranie:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Wysyłanie komunikatów

Operacje tworzenia i wysyłania komunikatów są wykonywane w metodzie `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Przetwarzanie komunikatów

Metoda `ProcessMessagesAsync()` potwierdza, przetwarza i kończy odbieranie komunikatów:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

> [!NOTE]
> Możesz zarządzać zasobami usługi Service Bus przy użyciu [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus pozwala użytkownikom na łączenie do przestrzeni nazw usługi Service Bus i administrować jednostek obsługi komunikatów w łatwy sposób. To narzędzie zawiera zaawansowane funkcje, takie jak funkcja Importuj/Eksportuj lub możliwość testowania tematu, kolejek, subskrypcji, usługi przekazywania, usługi notification hubs i centrów zdarzeń. 

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono przestrzeń nazw usługi Service Bus oraz inne zasoby wymagane do wysyłania i odbierania komunikatów z kolejki. Aby dowiedzieć się więcej na temat pisania kodu w celu wysyłania i odbierania wiadomości, przejdź do samouczków w **wysyłania i odbierania komunikatów** sekcji. 

> [!div class="nextstepaction"]
> [Wysyłanie i odbieranie komunikatów](service-bus-dotnet-get-started-with-queues.md)

[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Instalowanie i konfigurowanie programu Azure PowerShell]: /powershell/azure/install-Az-ps
