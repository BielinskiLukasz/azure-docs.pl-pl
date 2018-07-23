---
title: Komunikaty z chmury do urządzenia z usługą Azure IoT Hub (Python) | Dokumentacja firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia na urządzeniu z usługi Azure IoT hub przy użyciu zestawów SDK usługi Azure IoT dla języka Python. Możesz zmodyfikować aplikacji symulowanego urządzenia do odbierania komunikatów z chmury do urządzeń i modyfikowania aplikacji zaplecza, do wysyłania komunikatów z chmury do urządzeń.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: kgremban
ms.openlocfilehash: 316a8cd9ebf58e06ba39ba18fa19ede4b6a62229
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187302"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Wprowadzenie
Usługa Azure IoT Hub to w pełni zarządzana usługa, która ułatwia włączanie bezpieczną i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Rozpoczynanie pracy z usługą IoT Hub] samouczek pokazuje sposób tworzenia Centrum IoT hub, aprowizować w nim tożsamości urządzenia i kodu aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się [Rozpoczynanie pracy z usługą IoT Hub]. Przedstawia on sposób do:

* Z zapleczem rozwiązania wysyłanie komunikatów z chmury do urządzeń do pojedynczego urządzenia za pomocą usługi IoT Hub.
* Odbieranie komunikatów z chmury do urządzeń na urządzeniu.
* Z zapleczem rozwiązania, żądania potwierdzenia dostarczania (*opinii*) dla wiadomości wysyłanych do urządzenia z usługi IoT Hub.

Można znaleźć więcej informacji na temat komunikatów z chmury do urządzeń w [usługi IoT Hub — przewodnik dewelopera][IoT Hub developer guide - C2D].

Na końcu tego samouczka możesz uruchomić dwie aplikacje konsolowe środowiska Python:

* **SimulatedDevice.py**, zmodyfikowanej wersji aplikacji utworzonej w [Rozpoczynanie pracy z usługą IoT Hub], który nawiązuje połączenie z Centrum IoT i odbiera komunikaty z chmury do urządzenia.
* **SendCloudToDeviceMessage.py**, która jest wysyłana wiadomość chmury do urządzenia w aplikacji symulowanego urządzenia za pomocą usługi IoT Hub i odbiera jego potwierdzenie dostawy.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK na wielu platformach i językach (w tym C, Java i Javascript) za pomocą zestawów SDK urządzeń Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące łączenia urządzenia do kodu w tym samouczku i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT].
> 

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

> [!NOTE]
> Pakiety *pip* dla składników `azure-iothub-service-client` i `azure-iothub-device-client` są obecnie dostępne tylko w przypadku systemu operacyjnego Windows. System Linux lub Mac OS można znaleźć w sekcji Linux i Mac specyficznych dla systemu operacyjnego na wpis [lnk-python-devbox] [przygotowywanie środowiska projektowego dla języka Python].
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie wiadomości w aplikacji symulowanego urządzenia
W tej sekcji utworzysz aplikację konsoli języka Python w celu symulowania urządzenia i odbierać komunikaty z chmury do urządzenia z usługi IoT hub.

1. Za pomocą edytora tekstów Utwórz **SimulatedDevice.py** pliku.

1. Dodaj następujący kod `import` instrukcji i zmienne na początku **SimulatedDevice.py** pliku:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. Dodaj następujący kod do **SimulatedDevice.py** pliku. Zastąp wartość symbolu zastępczego "{deviceConnectionString}" przy użyciu parametrów połączenia urządzenia dla urządzenia utworzone w [Rozpoczynanie pracy z usługą IoT Hub] samouczka:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Dodaj następującą funkcję umożliwiającą wydrukowanie Odebrane komunikaty wyjściowe do konsoli:
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. Dodaj następujący kod do zainicjowania klienta i czekać na otrzymanie komunikatu chmura urządzenie:
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. Dodaj następującą funkcję, głównym:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Zapisz i Zamknij **SimulatedDevice.py** pliku.


## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatów chmura urządzenie
W tej sekcji utworzysz aplikację konsoli języka Python, która wysyła komunikaty z chmury do urządzenia w aplikacji symulowanego urządzenia. Potrzebny jest identyfikator urządzenia, które urządzenia zostały dodane w [Rozpoczynanie pracy z usługą IoT Hub] samouczka. Należy również parametry połączenia Centrum IoT Hub dla Centrum można znaleźć w [Azure Portal].

1. Za pomocą edytora tekstów Utwórz **SendCloudToDeviceMessage.py** pliku.

1. Dodaj następujący kod `import` instrukcji i zmienne na początku **SendCloudToDeviceMessage.py** pliku:
   
    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Dodaj następujący kod do **SendCloudToDeviceMessage.py** pliku. Zastąp wartość symbolu zastępczego "{IoTHubConnectionString}" przy użyciu parametrów połączenia usługi IoT Hub dla Centrum utworzonego w [Rozpoczynanie pracy z usługą IoT Hub] samouczka. Zastąp symbol zastępczy "{deviceId}" z Identyfikatorem urządzenia, które urządzenia zostały dodane w [Rozpoczynanie pracy z usługą IoT Hub] samouczka:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Dodaj następującą funkcję umożliwiającą wydrukowanie opinii komunikaty wyjściowe do konsoli:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Dodaj następujący kod, aby wysłać wiadomość do Twojego urządzenia i obsłużyć komunikat opinii, gdy urządzenie potwierdza komunikatu chmura urządzenie:
   
    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

1. Dodaj następującą funkcję, głównym:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. Zapisz i Zamknij **SendCloudToDeviceMessage.py** pliku.


## <a name="run-the-applications"></a>Uruchamianie aplikacji
Teraz można uruchomić aplikacje.

1. Otwórz wiersz polecenia i zainstaluj **zestawu SDK urządzenia Centrum IoT platformy Azure dla języka Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. W wierszu polecenia Uruchom następujące polecenie, aby nasłuchiwać komunikatów z chmury do urządzeń:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![Uruchamianie aplikacji symulowanego urządzenia][img-simulated-device]

1. Otwórz nowy wiersz polecenia i zainstaluj **Azure IoT Hub Service SDK dla języka Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. W wierszu polecenia Uruchom następujące polecenie, aby wysłać komunikatu chmura urządzenie i poczekaj na opinie komunikat:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Uruchamianie aplikacji na wysyłanie poleceń chmura urządzenie][img-send-command]
   
1. Należy pamiętać, wiadomość odebrana przez urządzenie.

    ![Odebrano komunikat][img-message-recieved]


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób wysyłania i odbierania komunikatów z chmury do urządzeń. 

Aby wyświetlić przykłady kompletne rozwiązania end-to-end, które używają usługi IoT Hub, zobacz [Akcelerator rozwiązań IoT zdalnego monitorowania platformy Azure].

Aby dowiedzieć się więcej na temat opracowywania rozwiązań usługi IoT Hub, zobacz [Przewodnik dla deweloperów usługi IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[Rozpoczynanie pracy z usługą IoT Hub]: quickstart-send-telemetry-node.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Przewodnik dla deweloperów usługi IoT Hub]: iot-hub-devguide.md
[Centrum deweloperów Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Akcelerator rozwiązań IoT zdalnego monitorowania platformy Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/
