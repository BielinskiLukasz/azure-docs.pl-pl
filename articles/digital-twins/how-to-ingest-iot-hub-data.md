---
title: Pozyskiwanie danych telemetrycznych z usługi IoT Hub
titleSuffix: Azure Digital Twins
description: Zobacz, jak pozyskiwanie komunikatów telemetrycznych urządzenia z IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 47e4bb291d031c41c89c88435a795004490e20a1
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505329"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Pozyskiwanie danych telemetrycznych IoT Hub na platformie Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji jest oparta na danych z urządzeń IoT i innych źródeł. Typowym źródłem danych urządzenia do użycia w usłudze Azure Digital bliźniaczych reprezentacji jest [IoT Hub](../iot-hub/about-iot-hub.md).

Proces pozyskiwania danych w usłudze Azure Digital bliźniaczych reprezentacji polega na skonfigurowaniu zewnętrznego zasobu obliczeniowego, takiego jak [Funkcja platformy Azure](../azure-functions/functions-overview.md), która odbiera dane, i używa [interfejsów API DigitalTwins](how-to-use-apis-sdks.md) do ustawiania właściwości lub zdarzeń telemetrii na potrzeby [bliźniaczych reprezentacji w cyfrowych](concepts-twins-graph.md) . 

Ten dokument zawiera instrukcje dotyczące tworzenia funkcji platformy Azure, która może pozyskać dane telemetryczne z IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem tego przykładu należy skonfigurować następujące zasoby jako wymagania wstępne:
* **Centrum IoT**. Aby uzyskać instrukcje, zobacz sekcję *tworzenie IoT Hub* w [tym IoT Hub przewodniku szybki start](../iot-hub/quickstart-send-telemetry-cli.md).
* **Funkcja platformy Azure** z prawidłowymi uprawnieniami do wywoływania Twojego wystąpienia cyfrowej sieci dwuosiowej. Aby uzyskać instrukcje, zobacz [*How to: set up a Azure Function for processing Data*](how-to-create-azure-function.md). 
* **Wystąpienie usługi Azure Digital bliźniaczych reprezentacji** , które będzie odbierać dane telemetryczne urządzenia. Aby uzyskać instrukcje, zobacz [*How to: set up the Azure Digital bliźniaczych reprezentacji instance and Authentication*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Przykładowy scenariusz telemetrii

Ten sposób przedstawia sposób wysyłania komunikatów z IoT Hub do usługi Azure Digital bliźniaczych reprezentacji przy użyciu funkcji platformy Azure. Istnieje wiele możliwych konfiguracji i strategie dopasowania, których można użyć w tym przypadku, ale przykład tego artykułu zawiera następujące części:
* Urządzenie termometru w IoT Hub ze znanym IDENTYFIKATORem urządzenia.
* Cyfrowa dwuosiowa do reprezentowania urządzenia, o zgodnym IDENTYFIKATORze

> [!NOTE]
> W tym przykładzie zastosowano prostą zgodność identyfikatora między IDENTYFIKATORem urządzenia i odpowiadającym mu IDENTYFIKATORem cyfrowej sznurka, ale można zapewnić bardziej zaawansowane mapowania z urządzenia do jego sznurka (na przykład z tabelą mapowania).

Za każdym razem, gdy zdarzenie telemetrii temperatury jest wysyłane przez urządzenie termometru, właściwość *temperatura* cyfr cyfrowych powinna zostać zaktualizowana. Ten scenariusz przedstawiono na poniższym diagramie:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagram przedstawiający wykres przepływu. Na wykresie urządzenie IoT Hub wysyła dane telemetryczne dotyczące temperatury za pomocą IoT Hub do funkcji platformy Azure, która aktualizuje właściwość temperatury na sznurze w usłudze Azure Digital bliźniaczych reprezentacji." border="false":::

## <a name="add-a-model-and-twin"></a>Dodaj model i sznurki

Do aktualizowania informacji w usłudze IoT Hub potrzebna jest dwuosiowa.

Model wygląda następująco:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Aby **przekazać ten model do wystąpienia usługi bliźniaczych reprezentacji**, Otwórz interfejs wiersza polecenia platformy Azure i uruchom następujące polecenie:
```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Następnie należy **utworzyć jedną sznurek przy użyciu tego modelu**. Użyj następującego polecenia, aby utworzyć dwuosiową i ustawić 0,0 jako początkową wartość temperatury.
```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

Dane wyjściowe pomyślnego utworzenia dwuosiowego polecenia powinny wyglądać następująco:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Ta sekcja używa tych samych kroków uruchamiania programu Visual Studio i szkieletu funkcji platformy Azure z pozostałej części [*: Konfigurowanie funkcji platformy Azure do przetwarzania danych*](how-to-create-azure-function.md). Szkielet obsługuje uwierzytelnianie i tworzy klienta usługi, gotowy do przetwarzania danych i wywoływania interfejsów API Digital bliźniaczych reprezentacji w odpowiedzi na platformie Azure. 

W kolejnych krokach dodasz do niego konkretny kod służący do przetwarzania zdarzeń telemetrii IoT z IoT Hub.  

### <a name="add-telemetry-processing"></a>Dodaj przetwarzanie danych telemetrycznych
    
Zdarzenia telemetrii są dostarczane w formie komunikatów z urządzenia. Pierwszym krokiem w dodawaniu kodu przetwarzania danych telemetrycznych jest wyodrębnienie odpowiedniej części tego komunikatu urządzenia ze zdarzenia Event Grid. 

Różne urządzenia mogą być w inny sposób struktury swoich komunikatów, więc kod dla **tego kroku zależy od podłączonego urządzenia.** 

Poniższy kod przedstawia przykład prostego urządzenia, które wysyła dane telemetryczne jako kod JSON. Ten przykład jest w pełni omawiany w [*samouczku: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md). Poniższy kod umożliwia znalezienie identyfikatora urządzenia, które wysłało komunikat, a także wartości temperatury.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

Następny przykład kodu przyjmuje identyfikator i wartość temperatury i używa ich do "poprawki" (do) tego sznurka.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Aktualizowanie kodu funkcji platformy Azure

Teraz, gdy rozumiesz kod z wcześniejszych przykładów, Otwórz program Visual Studio i Zastąp kod funkcji platformy Azure tym przykładowym kodem.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>Połącz funkcję z IoT Hub

1. Skonfiguruj miejsce docelowe zdarzenia dla danych centrum. W [Azure Portal](https://portal.azure.com/)przejdź do wystąpienia IoT Hub. W obszarze **zdarzenia**Utwórz subskrypcję dla funkcji platformy Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, który pokazuje Dodawanie subskrypcji zdarzeń.":::

2. Na stronie **Tworzenie subskrypcji zdarzeń** Wypełnij pola w następujący sposób:
    1. W polu **Nazwa**Nazwij subskrypcję, którą chcesz.
    2. W obszarze **schemat zdarzenia**wybierz pozycję **Event Grid schemat**.
    3. W obszarze **nazwa tematu systemu**wybierz unikatową nazwę.
    4. W obszarze **typy zdarzeń**wybierz pozycję **Telemetria urządzenia** jako typ zdarzenia do filtrowania.
    5. W obszarze **Szczegóły punktu końcowego**wybierz funkcję platformy Azure jako punkt końcowy.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Zrzut ekranu przedstawiający Azure Portal pokazujący szczegóły subskrypcji zdarzeń":::

## <a name="send-simulated-iot-data"></a>Wysyłanie symulowanych danych IoT

Aby przetestować nową funkcję transferu danych przychodzących, użyj symulatora urządzeń z [*samouczka: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md). Ten samouczek jest oparty na przykładowym projekcie pisanym w języku C#. Przykładowy kod znajduje się tutaj: [bliźniaczych reprezentacji Digital Azure — przykłady](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Będziesz używać projektu **DeviceSimulator** w tym repozytorium.

W kompleksowym samouczku wykonaj następujące czynności:
1. [*Rejestrowanie symulowanego urządzenia za pomocą IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurowanie i uruchamianie symulacji*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Weryfikowanie wyników

W przypadku korzystania z symulatora urządzenia powyżej wartość temperatury dwuosiowej sieci cyfrowej zostanie zmieniona. W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie, aby wyświetlić wartość temperatury.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Dane wyjściowe powinny zawierać wartość temperatury podobną do:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Aby wyświetlić zmianę wartości, należy wielokrotnie uruchomić polecenie zapytania powyżej.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z informacjami dotyczącymi transferu danych przychodzących i wychodzących przy użyciu usługi Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: integracja z innymi usługami*](concepts-integration.md)
