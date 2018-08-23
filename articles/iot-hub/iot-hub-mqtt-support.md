---
title: Omówienie pomocy technicznej usługi Azure IoT Hub MQTT | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Obsługa urządzeń nawiązujących połączenie z punktem końcowym skierowaną do urządzenia usługi IoT Hub przy użyciu protokołu MQTT. Zawiera informacje o wbudowanych MQTT obsługi w zestawy SDK urządzeń Azure IoT.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: elioda
ms.openlocfilehash: 2e45422ca6a861894193600eff17f192bc20b357
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2018
ms.locfileid: "42057307"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikować się z Centrum IoT hub przy użyciu protokołu MQTT

Usługa IoT Hub udostępnia urządzenia do komunikowania się z punktami końcowymi urządzenia usługi IoT Hub przy użyciu:

* [MQTT 3.1.1] [ lnk-mqtt-org] na porcie 8883
* MQTT 3.1.1 za pośrednictwem protokołu WebSocket na porcie 443.

Usługa IoT Hub nie jest brokerem MQTT w pełni funkcjonalne i nie obsługuje wszystkich zachowań, które są określone w standardzie MQTT 3.1.1. W tym artykule opisano, jak urządzenia za pomocą usługi obsługiwanej zachowania MQTT komunikować się z usługą IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Cała komunikacja urządzenia z usługą IoT Hub musi zostać zabezpieczony za pomocą protokołów TLS/SSL. W związku z tym usługi IoT Hub nie obsługuje niezabezpieczone połączenia za pośrednictwem portu 1883.

## <a name="connecting-to-iot-hub"></a>Łączenie z usługą IoT Hub

Urządzenia można użyć protokołu MQTT, połączyć się z Centrum IoT przy użyciu:

* Bibliotek w [Azure IoT SDKs][lnk-device-sdks].
* Lub bezpośrednio za pomocą protokołu MQTT.

## <a name="using-the-device-sdks"></a>Za pomocą zestawów SDK urządzeń

[Zestawy SDK urządzeń] [ lnk-device-sdks] obsługujące protokołu MQTT są dostępne dla języka Java, Node.js, C, C# i Python. Zestawy SDK urządzeń umożliwia standardowe parametry połączenia Centrum IoT Hub nawiązać połączenie z Centrum IoT hub. Aby użyć protokołu MQTT, musi być równa parametr Protokół klienta **MQTT**. Domyślnie, połączenie urządzenia z zestawów SDK usługi IoT Hub przy użyciu **CleanSession** flaga jest ustawiona na **0** i użyj **QoS 1** w wymianie wiadomości z usługą IoT hub.

Gdy urządzenie jest podłączone do usługi IoT hub, zestawy SDK urządzeń zapewnia metody, które umożliwiają urządzeniu wymiana komunikatów z Centrum IoT hub.

Poniższa tabela zawiera linki do przykładów kodu dla każdego obsługiwanego języka i określa parametr, aby nawiązać połączenie z Centrum IoT przy użyciu protokołu MQTT.

| Język | Parametr protokołu |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrowanie aplikacji urządzenia z protokołu AMQP do protokołu MQTT

Jeśli używasz [zestawy SDK urządzeń][lnk-device-sdks], przełączenie z za pomocą protokołu AMQP do MQTT konieczna jest zmiana parametru protokołu podczas inicjowania klienta, jak wspomniano wcześniej.

Po wykonaniu tej czynności upewnij się sprawdzić następujące elementy:

* Protokół AMQP zwraca informacje o błędach dla wielu warunków, gdy MQTT przerywa połączenie. W rezultacie logika obsługi wyjątku może wymagać pewnych zmian.
* Nie obsługuje protokołu MQTT *Odrzuć* operacje podczas odbierania [komunikatów z chmury do urządzeń][lnk-messaging]. Jeśli serwer zaplecza w aplikacji musi czekać na odpowiedź z aplikacji urządzenia, należy wziąć pod uwagę przy użyciu [metody bezpośrednie][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Bezpośrednio przy użyciu protokołu MQTT

Jeśli urządzenia nie można użyć zestawów SDK urządzeń, nadal można połączyć do urządzeń publicznych punktów końcowych przy użyciu protokołu MQTT na porcie 8883. W **CONNECT** pakietu urządzenia należy użyć następujących wartości:

* Aby uzyskać **ClientId** pola, użyj **deviceId**.

* Aby uzyskać **Username** pola, użyj `{iothubhostname}/{device_id}/api-version=2016-11-14`, gdzie `{iothubhostname}` jest pełny rekord CName usługi IoT hub.

    Na przykład jeśli nazwą Centrum IoT hub jest **contoso.azure-devices.net** i jeśli nazwa urządzenia jest **MyDevice01**, pełną **Username** pole powinno zawierać:

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* Aby uzyskać **hasło** pola, użyj tokenu sygnatury dostępu Współdzielonego. Format tokenu sygnatury dostępu Współdzielonego jest taka sama, jak w przypadku protokołów HTTPS i AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Jeśli używasz uwierzytelniania certyfikatów X.509, hasła tokenu sygnatury dostępu Współdzielonego nie są wymagane. Aby uzyskać więcej informacji, zobacz [konfigurowania zabezpieczeń X.509 w usłudze Azure IoT Hub][lnk-x509]

  Aby uzyskać więcej informacji na temat generowania tokenów sygnatur dostępu Współdzielonego, zobacz sekcję urządzenia [tokenów zabezpieczających za pomocą usługi IoT Hub][lnk-sas-tokens].

  Podczas testowania, możesz również użyć dla wielu platform [rozszerzenie Azure IoT Toolkit dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) lub [Device Explorer] [ lnk-device-explorer] narzędzie, aby szybko wygenerować sygnaturę dostępu Współdzielonego token, który Możesz skopiować i wkleić do własnego kodu:

Aby uzyskać zestaw narzędzi platformy Azure IoT:

  1. Rozwiń **AZURE IOT HUB DEVICES** kartę w lewym dolnym rogu programu Visual Studio Code.
  2. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **generowania tokenu sygnatury dostępu Współdzielonego dla urządzenia**.
  3. Ustaw **czas wygaśnięcia** i naciśnij klawisz "Enter".
  4. Token sygnatury dostępu Współdzielonego jest utworzony i skopiowany do Schowka.

Aby uzyskać Device Explorer:

  1. Przejdź do **zarządzania** karcie **Device Explorer**.
  2. Kliknij przycisk **tokenu sygnatury dostępu Współdzielonego** (w prawym górnym po prawej stronie).
  3. Na **SASTokenForm**, wybierz urządzenie **DeviceID** listy rozwijanej. Ustaw użytkownika **TTL**.
  4. Kliknij przycisk **Generuj** do utworzenia tokenu.

     Token sygnatury dostępu Współdzielonego, który jest generowany ma następującą strukturę:

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     Część tego tokenu do użycia jako **hasło** pole, aby nawiązać połączenie przy użyciu protokołu MQTT jest:

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Dla protokołu MQTT połączenia i odłączyć pakietów, usługi IoT Hub wysyła zdarzenia w **monitorowanie operacji** kanału. To zdarzenie zawiera dodatkowe informacje, które mogą ułatwić rozwiązywanie problemów z łącznością.

Można określić aplikacji urządzenia **będzie** wiadomość **CONNECT** pakietów. Skorzystaj z aplikacji urządzenia `devices/{device_id}/messages/events/{property_bag}` lub `devices/{device_id}/messages/events/{property_bag}` jako **będzie** nazwy tematu, aby zdefiniować **będzie** mają być przekazywane jako komunikaty telemetryczne wiadomości. W tym przypadku Jeśli zamknięto połączenie sieciowe, ale **ROZŁĄCZENIA** pakietów nie otrzymano wcześniej z urządzenia, a następnie wysyła usługi IoT Hub **będzie** wiadomości podano w **CONNECT** pakietów do kanału danych telemetrycznych. Kanał danych telemetrycznych można albo domyślnie **zdarzenia** punkt końcowy lub niestandardowy punkt końcowy zdefiniowany przez usługę IoT Hub routingu. Komunikat ma **iothub MessageType** właściwość z wartością **będzie** do niej przypisany.

### <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby użyć protokołu MQTT protokołu bezpośrednio klienta *musi* łączenie za pośrednictwem protokołu TLS/SSL. Aby pominąć ten krok zakończy się niepowodzeniem z błędami połączenia.

Aby możliwe było nawiązanie połączenia TLS, może być konieczne do pobrania i odwoływać się do certyfikatu głównego Baltimore firmy DigiCert. Ten certyfikat jest ten, który korzysta z platformy Azure do zabezpieczenia połączenia. Możesz znaleźć tego certyfikatu w [Azure-iot-sdk-c] [ lnk-sdk-c-certs] repozytorium. Więcej informacji o tych certyfikatach znajduje się na [witryny sieci Web firmy Digicert][lnk-digicert-root-certs].

Przykładowy sposób implementacji przy użyciu wersji języka Python [biblioteki Paho MQTT] [ lnk-paho] przez Eclipse Foundation może wyglądać podobnie do poniższego.

Najpierw zainstaluj bibliotekę Paho ze środowiska wiersza polecenia:

```cmd/sh
pip install paho-mqtt
```

Następnie należy zaimplementować klienta skrypt w języku Python. Zastąp symbole zastępcze w następujący sposób:

* `<local path to digicert.cer>` jest to ścieżka do pliku lokalnego, który zawiera certyfikat główny Baltimore firmy DigiCert. Ten plik można utworzyć, kopiując informacje o certyfikacie z [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) w zestawie Azure IoT SDK dla języka C. zawierają wiersze `-----BEGIN CERTIFICATE-----` i `-----END CERTIFICATE-----`, Usuń `"` znaki na początku i końcu każdego wiersza i Usuń `\r\n` znaków na końcu każdego wiersza.
* `<device id from device registry>` jest to identyfikator urządzenia dodanego do usługi IoT hub.
* `<generated SAS token>` jest tokenem sygnatury dostępu Współdzielonego dla urządzenia, utworzony zgodnie z opisem w poprzedniej sekcji w niniejszym artykule.
* `<iot hub name>` Nazwa centrum IoT hub.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Wysyłanie komunikatów z urządzenia do chmury

Po wprowadzeniu pomyślnie nawiązać połączenie, urządzenie może wysyłać wiadomości do usługi IoT Hub przy użyciu `devices/{device_id}/messages/events/` lub `devices/{device_id}/messages/events/{property_bag}` jako **nazwy tematu**. `{property_bag}` Element umożliwia urządzeniu do wysyłania komunikatów za pomocą dodatkowych właściwości w formacie zakodowane w adresie url. Na przykład:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> To `{property_bag}` element używa tego samego kodu dla ciągów zapytań za pomocą protokołu HTTPS.

Oto lista zachowania specyficzne dla implementacji usługi IoT Hub:

* Usługi IoT Hub nie obsługuje wiadomości QoS 2. Jeśli aplikacja urządzenia publikuje wiadomość z **QoS 2**, usługi IoT Hub zamyka połączenie sieciowe.
* Usługa IoT Hub nie jest trwały Zachowaj wiadomości. Jeśli urządzenie wysyła komunikat z **Zachowaj** Flaga ustawiona na 1, dodaje usługę IoT Hub **x — zoptymalizowany pod kątem zachować** właściwość aplikacji do wiadomości. W tym przypadku zamiast przechowywanie komunikatów Zachowaj, usługi IoT Hub przekazuje go do aplikacji zaplecza.
* Usługa IoT Hub obsługuje tylko jedno aktywne połączenie MQTT na każdym urządzeniu. Wszelkie nowe połączenie MQTT imieniu takim samym Identyfikatorem urządzenia powoduje, że usługi IoT Hub w celu usunięcia istniejącego połączenia.

Aby uzyskać więcej informacji, zobacz [komunikatów przewodnik dewelopera][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Odbieranie komunikatów z chmury do urządzeń

Aby odbierać komunikaty z Centrum IoT Hub, urządzenia powinien uzyskać subskrypcję za pomocą `devices/{device_id}/messages/devicebound/#` jako **filtr tematu**. Wielopoziomowe symbolu wieloznacznego `#` w filtrze tematu jest używana tylko po to, aby zezwolić na urządzenia otrzymać dodatkowe właściwości w nazwę tematu. Usługa IoT Hub nie zezwala na użycie `#` lub `?` symboli wieloznacznych do filtrowania podtematy. Ponieważ usługa IoT Hub nie jest ogólnego przeznaczenia pub-sub brokera obsługi komunikatów, go obsługuje tylko nazwy tematu udokumentowane i filtry tematów.

Urządzenie nie odbiera komunikaty z usługi IoT Hub, aż pomyślnie przyłącza się do jej punkt końcowy specyficzny dla urządzenia, reprezentowane przez `devices/{device_id}/messages/devicebound/#` filtru tematu. Po ustaleniu subskrypcji otrzymaniem przez nie tych komunikatów z chmury do urządzeń, które zostały wysłane do niej po upływie czasu subskrypcji. Jeśli urządzenie łączy się z **CleanSession** flaga jest ustawiona na **0**, subskrypcji są utrwalane w różnych sesjach. W takim przypadku podczas następnego urządzenie łączy się z **CleanSession 0** otrzymuje żadnych oczekujących wiadomości wysyłane do niej przy braku połączenia. Jeśli urządzenie korzysta **CleanSession** flaga jest ustawiona na **1** , nie otrzyma jakiekolwiek komunikaty z usługi IoT Hub, dopóki nie subskrybuje punktu końcowego urządzenia.

IoT Hub dostarczy komunikatów za pomocą **nazwy tematu** `devices/{device_id}/messages/devicebound/`, lub `devices/{device_id}/messages/devicebound/{property_bag}` przypadku właściwości komunikatu. `{property_bag}` zawiera pary klucz/wartość zakodowane w adresie url właściwości komunikatu. Tylko właściwości aplikacji i użytkownika można ustawić właściwości (takie jak **messageId** lub **correlationId**) są uwzględnione w zbiorze właściwości. System właściwości nazwy zawierają prefiks **$**, właściwości aplikacji za pomocą oryginalna nazwa właściwości żadnego prefiksu.

Gdy aplikacja urządzenia subskrybowanie tematu z **QoS 2**, usługi IoT Hub daje maksymalny poziom QoS 1 w **SUBACK** pakietów. Po utworzeniu tego Centrum IoT Hub dostarcza komunikaty do urządzenia przy użyciu QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Pobieranie właściwości bliźniaczej reprezentacji urządzenia

Po pierwsze urządzenie subskrybuje `$iothub/twin/res/#`, aby otrzymywać odpowiedzi operacji. Następnie wysyła pustego komunikatu do tematu `$iothub/twin/GET/?$rid={request id}`, z wypełnione wartością **identyfikator żądania**. Usługa wysyła następnie komunikat odpowiedzi zawierające dane bliźniaczej reprezentacji urządzenia, na temat `$iothub/twin/res/{status}/?$rid={request id}`, korzystając z tych samych **identyfikator żądania** jako żądania.

Identyfikator żądania może być dowolnego prawidłową wartość dla wartości właściwości komunikatu zgodnie [usługi IoT Hub komunikatów przewodnik dewelopera][lnk-messaging], i stan sprawdzania poprawności jako liczba całkowita.

Treść odpowiedzi zawiera sekcja właściwości bliźniaka urządzenia. Poniższy fragment kodu przedstawia przykład treść wpisu rejestru tożsamości ograniczone do elementu "properties":

```json
{
    "properties": {
        "desired": {
            "telemetrySendFrequency": "5m",
            "$version": 12
        },
        "reported": {
            "telemetrySendFrequency": "5m",
            "batteryLevel": 55,
            "$version": 123
        }
    }
}
```

Możliwe kody są:

|Stan | Opis |
| ----- | ----------- |
| 200 | Powodzenie |
| 429 | Za dużo żądań (ograniczone), jak na [usługi IoT Hub, ograniczanie przepustowości][lnk-quotas] |
| 5** | Błędy serwera |

Aby uzyskać więcej informacji, zobacz [— przewodnik dewelopera bliźniaczych reprezentacji urządzeń][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Zgłoszonych właściwości bliźniaka urządzenia aktualizacji

Poniższa sekwencja opisuje, jak urządzenie aktualizuje zgłoszonych właściwości w bliźniaczej reprezentacji urządzenia w usłudze IoT Hub:

1. Urządzenia muszą najpierw zasubskrybować `$iothub/twin/res/#` tematu, aby otrzymywać odpowiedzi operacji w usłudze IoT Hub.

1. Urządzenie wysyła komunikat zawierający aktualizacji bliźniaczej reprezentacji urządzenia, aby `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tematu. Ten komunikat zawiera **identyfikator żądania** wartość.

1. Usługa wysyła następnie komunikat odpowiedzi, który zawiera nową wartość elementu ETag dla kolekcji zgłaszanych właściwości na temat `$iothub/twin/res/{status}/?$rid={request id}`. Ten komunikat odpowiedzi używa tych samych **identyfikator żądania** jako żądania.

Treść żądania zawiera dokument JSON, który zawiera nowe wartości zgłaszanych właściwości. Każdy element członkowski w dokumencie JSON aktualizacji lub Dodaj odpowiedniego elementu członkowskiego w dokumencie bliźniaczej reprezentacji urządzenia. Zestaw elementów członkowskich `null`, usuwa element członkowski z zawierającego go obiektu. Na przykład:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Możliwe kody są:

|Stan | Opis |
| ----- | ----------- |
| 200 | Powodzenie |
| 400 | Nieprawidłowe żądanie. Nieprawidłowo sformatowany kod JSON |
| 429 | Za dużo żądań (ograniczone), jak na [usługi IoT Hub, ograniczanie przepustowości][lnk-quotas] |
| 5** | Błędy serwera |

Aby uzyskać więcej informacji, zobacz [— przewodnik dewelopera bliźniaczych reprezentacji urządzeń][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Odbieranie powiadomień o aktualizacji żądanych właściwości

Gdy urządzenie jest połączone, usługi IoT Hub wysyła powiadomienia do tematu `$iothub/twin/PATCH/properties/desired/?$version={new version}`, które zawierają zawartość aktualizacji wykonywana przez zaplecze rozwiązania. Na przykład:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

Jak w przypadku aktualizacji właściwości `null` wartości oznacza, że elemencie członkowskim obiektu JSON jest usuwana.

> [!IMPORTANT]
> Usługa IoT Hub generuje powiadomienia o zmianach, tylko wtedy, gdy urządzenia są podłączone. Upewnij się zaimplementować [przepływ ponownego łączenia urządzeń] [ lnk-devguide-twin-reconnection] do zachowania żądanych właściwości synchronizowane między centrum IoT i aplikacji urządzenia.

Aby uzyskać więcej informacji, zobacz [— przewodnik dewelopera bliźniaczych reprezentacji urządzeń][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Odpowiadanie na metody bezpośredniej

Po pierwsze, urządzenie może mieć do subskrybowania `$iothub/methods/POST/#`. Usługa IoT Hub wysyła żądania metody do tematu `$iothub/methods/POST/{method name}/?$rid={request id}`z prawidłowym kodem JSON lub pustą treść.

Aby odpowiedzieć, urządzenie wysyła komunikat z prawidłowym plikiem JSON lub pustą treść do tematu `$iothub/methods/res/{status}/?$rid={request id}`. W tym komunikacie **identyfikator żądania** musi pasować do w komunikacie żądania i **stan** musi być liczbą całkowitą.

Aby uzyskać więcej informacji, zobacz [bezpośrednie metody przewodnik dewelopera usługi][lnk-methods].

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

Jak jest ostatnim brany pod uwagę, jeśli trzeba dostosować zachowanie protokołu MQTT po stronie chmury, należy zapoznać się [brama protokołu Azure IoT][lnk-azure-protocol-gateway]. To oprogramowanie umożliwia wdrażanie bramy o wysokiej wydajności niestandardowego protokołu tego interfejsów bezpośrednio z usługą IoT Hub. Brama protokołu Azure IoT umożliwia dostosowanie protokołu urządzenia, aby pomieścić brownfield MQTT wdrożeń lub innych niestandardowych protokołów. Takie podejście wymaga jednak uruchamiasz i działają bramy protokołu niestandardowego.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat protokołu MQTT, zobacz [dokumentacji MQTT][lnk-mqtt-docs].

Aby dowiedzieć się więcej na temat planowania wdrożenia usługi IoT Hub, zobacz:

* [Certyfikat platformy Microsoft Azure dla IoT — wykaz urządzeń][lnk-devices]
* [Obsługa dodatkowych protokołów][lnk-protocols]
* [Porównanie z usługą Event Hubs][lnk-compare]
* [Skalowanie i wysoka dostępność i odzyskiwanie po awarii][lnk-scaling]

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
