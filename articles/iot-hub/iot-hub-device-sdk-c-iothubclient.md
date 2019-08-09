---
title: Zestaw SDK urządzeń Azure IoT dla języka C-usługi iothubclient | Microsoft Docs
description: Jak korzystać z biblioteki usługi iothubclient w zestawie SDK urządzeń Azure IoT dla języka C, aby tworzyć aplikacje dla urządzeń, które komunikują się z Centrum IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.openlocfilehash: fd3e02101f206ebdb183da87089eadcbc9619b33
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883174"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Zestaw SDK urządzeń Azure IoT dla języka C — więcej informacji na temat usługi iothubclient

[Zestaw SDK urządzeń Azure IoT dla języka c](iot-hub-device-sdk-c-intro.md) to pierwszy artykuł w tej serii przedstawiający **zestaw SDK urządzeń Azure IoT dla języka c**. W tym artykule wyjaśniono, że w zestawie SDK istnieją dwie warstwy architektury. Na podstawie bazy jest biblioteką **usługi iothubclient** , która bezpośrednio zarządza komunikacją z IoT Hub. Istnieje również biblioteka **serializatorów** , która kompiluje się w stosunku do programu w celu zapewnienia usług serializacji. Ten artykuł zawiera dodatkowe szczegóły dotyczące biblioteki **usługi iothubclient** .

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

W poprzednim artykule opisano, jak używać biblioteki **usługi iothubclient** do wysyłania zdarzeń do IoT Hub i odbierania komunikatów. Ten artykuł rozszerza tę dyskusję, wyjaśniając, jak dokładniej zarządzać *podczas* wysyłania i odbierania danych, wprowadzając do **interfejsów API niższego poziomu**. Wyjaśnimy również, jak dołączyć właściwości do zdarzeń (i pobrać je z komunikatów) przy użyciu funkcji obsługi właściwości w bibliotece **usługi iothubclient** . Na koniec udostępnimy dodatkowe wyjaśnienie różnych sposobów obsługi komunikatów otrzymywanych z IoT Hub.

Artykuł zawiera kilka różnych tematów, w tym więcej informacji o poświadczeniach urządzeń oraz o sposobie zmiany zachowania **usługi iothubclient** za pomocą opcji konfiguracji.

Użyjemy przykładów zestawu **usługi iothubclient** SDK, aby wyjaśnić te tematy. Jeśli chcesz się dowiedzieć, zobacz **przykład\_\_iothub klienta\_http** i iothub **\_\_Client\_Sample AMQP** Applications, które są zawarte w usłudze Azure IoT zestaw SDK urządzeń dla języka C. wszystkie opisane w poniższych sekcjach przedstawiono w tych przykładach.

[**Zestaw SDK urządzeń Azure IoT dla**](https://github.com/Azure/azure-iot-sdk-c) repozytorium w serwisie GitHub i widok szczegółów interfejsu API można znaleźć w [dokumentacji interfejsu API języka c](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>Interfejsy API niższego poziomu

W poprzednim artykule opisano podstawowe operacje **usługi iothubclient** w kontekście przykładowej **\_\_aplikacji\_AMQP klienta iothub** . Na przykład wyjaśniono, jak zainicjować bibliotekę przy użyciu tego kodu.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Opisano w nim również sposób wysyłania zdarzeń przy użyciu tego wywołania funkcji.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

W tym artykule opisano również sposób odbierania komunikatów przez zarejestrowanie funkcji wywołania zwrotnego.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

W tym artykule pokazano również, jak zwolnić zasoby przy użyciu kodu, takiego jak poniższe.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Istnieją funkcje towarzyszące dla każdego z tych interfejsów API:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Wszystkie te funkcje obejmują wszystko w nazwie interfejsu API. Druga część nazwy, parametry każdej z tych funkcji są identyczne z ich odpowiednikami nienależącymi do siebie. Zachowanie tych funkcji jest jednak inne w jednym z ważnych sposobów.

Gdy wywołasz **usługi iothubclient\_CreateFromConnectionString**, źródłowe biblioteki tworzą nowy wątek, który działa w tle. Ten wątek wysyła zdarzenia do i odbiera komunikaty z, IoT Hub. Podczas pracy z interfejsami API **szystkie** nie jest tworzony żaden wątek. Tworzenie wątku w tle jest wygodą dla deweloperów. Nie musisz martwić się o jawne wysyłanie zdarzeń i otrzymywanie komunikatów z IoT Hub — odbywa się to automatycznie w tle. Natomiast interfejsy API **szystkie** zapewniają jawną kontrolę nad komunikacją z IoT Hub, jeśli jest to konieczne.

Aby lepiej zrozumieć te koncepcje, przyjrzyjmy się przykładowi:

Gdy wywołasz **usługi iothubclient\_SendEventAsync**, to właśnie robi to zdarzenie w buforze. Wątek w tle utworzony po wywołaniu **usługi iothubclient\_CreateFromConnectionString** stale monitoruje ten bufor i wysyła wszystkie dane, które zawiera, do IoT Hub. Dzieje się tak w tle w tym samym czasie, kiedy wątek główny wykonuje inne czynności.

Podobnie, gdy rejestrujesz funkcję wywołania zwrotnego dla wiadomości za pomocą **usługi iothubclient\_SetMessageCallback**, nastąpi wymuszenie, aby wątek w tle wywołał funkcję wywołania zwrotnego po odebraniu komunikatu niezależnie od Główny wątek.

Interfejsy API **szystkie** nie tworzą wątku tła. Zamiast tego należy wywołać nowy interfejs API, aby jawnie wysyłać i odbierać dane z IoT Hub. Przedstawiono to w poniższym przykładzie.

**\_\_PrzykładowaaplikacjahttpklientaiothubzawartawzestawieSDKpokazujeinterfejsyAPIniższegopoziomu.\_** W tym przykładzie wysyłamy zdarzenia do IoT Hub przy użyciu kodu, takiego jak:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Pierwsze trzy wiersze tworzą komunikat, a ostatni wiersz wysyła zdarzenie. Jednak jak wspomniano wcześniej, wysłanie zdarzenia oznacza, że dane są po prostu umieszczane w buforze. Nic nie jest przesyłane w sieci, gdy wywołamy **\_usługi iothubclient\_ll SendEventAsync**. Aby faktycznie przetransferować dane do IoT Hub, należy wywołać **\_usługi iothubclient ll\_DoWork**, jak w poniższym przykładzie:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ten kod (z **przykładowej\_\_aplikacji\_http klienta iothub** ) wielokrotnie wywołuje **usługi iothubclient\_ll\_DoWork**. Za każdym razem, gdy jest wywoływana **\_usługi iothubclient szystkie\_DoWork** , wysyła ona zdarzenia z buforu do IoT Hub i pobiera komunikat w kolejce wysyłany do urządzenia. Ten ostatni przypadek oznacza, że jeśli zarejestrowano funkcję wywołania zwrotnego dla komunikatów, wywołanie zwrotne zostanie wywołane (przy założeniu, że wszystkie komunikaty są umieszczane w kolejce). Ta funkcja wywołania zwrotnego powinna zostać zarejestrowana przy użyciu kodu, takiego jak:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Powodem, **że\_usługi iothubclient\_ll DoWork** jest często wywoływany w pętli, jest to, że zawsze jest wywoływana, wysyła *pewne* zdarzenia buforowane do IoT Hub i pobiera *Następny* komunikat umieszczony w kolejce dla urządzenia. Każde wywołanie nie jest gwarantowane wysłanie wszystkich zdarzeń buforowanych lub pobranie wszystkich komunikatów umieszczonych w kolejce. Jeśli chcesz wysłać wszystkie zdarzenia w buforze, a następnie kontynuować inne przetwarzanie, możesz zamienić tę pętlę na kod, taki jak następujące:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ten kod wywołuje **usługi iothubclient\_ll\_DoWork** do momentu wysłania do IoT Hub wszystkich zdarzeń w buforze. Należy pamiętać, że nie oznacza to również, że wszystkie komunikaty w kolejce zostały odebrane. Przyczyną tego jest to, że sprawdzanie komunikatów "All" nie jest jednoznaczne z akcją. Co się stanie w przypadku pobrania "wszystkich" komunikatów, a następnie natychmiastowego wysłania ich do urządzenia? Lepszym sposobem postępowania z programem jest zaprogramowany limit czasu. Na przykład funkcja wywołania zwrotnego komunikatu może zresetować czasomierz przy każdym wywołaniu. Następnie można napisać logikę, aby kontynuować przetwarzanie, jeśli na przykład w ciągu ostatnich *X* sekund nie odebrano żadnych komunikatów.

Po zakończeniu wysyłania zdarzeń i otrzymywania komunikatów należy wywołać odpowiednią funkcję w celu oczyszczenia zasobów.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Zasadniczo istnieje tylko jeden zestaw interfejsów API do wysyłania i odbierania danych z wątkiem w tle i innym zestawem interfejsów API, które robią to samo bez wątku w tle. Wiele deweloperów może preferować interfejsy API inne niż wszystkie, ale interfejsy API niższego poziomu są przydatne, gdy deweloper chce jawnie kontrolować Transmisja sieci. Na przykład niektóre urządzenia zbierają dane w czasie i tylko zdarzenia związane z ruchem przychodzącym w określonych odstępach czasu (na przykład raz na godzinę lub raz dziennie). Interfejsy API niższego poziomu zapewniają możliwość jawnej kontroli podczas wysyłania i odbierania danych z IoT Hub. Inne będą po prostu wolą prostoty, że zapewniają interfejsy API niższego poziomu. Wszystkie operacje odbywają się w głównym wątku, a nie w tle.

Niezależnie od tego, który model wybierasz, upewnij się, że używane interfejsy API są spójne. Jeśli zaczniesz od wywołania **usługi iothubclient\_szystkie\_CreateFromConnectionString**, upewnij się, że używasz tylko odpowiednich interfejsów API niższego poziomu do wykonywania następujących czynności:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

Przeciwieństwem jest również wartość true. Jeśli zaczniesz od **usługi iothubclient\_CreateFromConnectionString**, Użyj interfejsów API innych niż w przypadku dodatkowego przetwarzania.

W zestawie SDK urządzeń Azure IoT dla języka C zapoznaj się z przykładową aplikacją **\_iothub klienta\_\_http** , aby zapoznać się z kompletnym przykładym interfejsów API niższego poziomu. Przykładowa aplikacja **\_iothub Client\_\_AMQP** może odwoływać się do pełnego przykładu interfejsów API innych niż wszystkie.

## <a name="property-handling"></a>Obsługa właściwości

Do tej pory, gdy opisano wysyłanie danych, odwołujemy się do treści wiadomości. Rozważmy na przykład następujący kod:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Ten przykład wysyła komunikat do IoT Hub z tekstem "Hello world". Jednak IoT Hub pozwala również dołączać właściwości do poszczególnych komunikatów. Właściwości to pary nazwa/wartość, które mogą być dołączone do wiadomości. Na przykład można zmodyfikować poprzedni kod w celu dołączenia właściwości do komunikatu:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Zaczynamy od wywołania **właściwości\_IoTHubMessage** i przekazania jej do uchwytu naszej wiadomości. Zawracamy do odwołania do **uchwytu mapy\_** , dzięki któremu możemy zacząć dodawać właściwości. Ten ostatni jest realizowany przez wywołanie **AddOrUpdate\_mapy**, która przyjmuje odwołanie do uchwytu mapy\_, nazwy właściwości i wartości właściwości. Za pomocą tego interfejsu API można dodać dowolną liczbę właściwości.

Gdy zdarzenie jest odczytywane z **Event Hubs**, odbiorca może wyliczyć właściwości i pobrać odpowiadające im wartości. Na przykład w programie .NET można to osiągnąć, uzyskując dostęp do [kolekcji właściwości w obiekcie EVENTDATA](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

W poprzednim przykładzie dołączamy właściwości do zdarzenia, które wysyłamy do IoT Hub. Właściwości można również dołączać do komunikatów odebranych z IoT Hub. Jeśli chcemy pobrać właściwości z komunikatu, możemy użyć kodu, takiego jak następujące w funkcji wywołania zwrotnego komunikatu:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Wywołanie **właściwości\_IoTHubMessage** zwraca odwołanie do **uchwytu\_mapy** . Następnie przekazujemy to odwołanie do **mapowania\_** getinternals, aby uzyskać odwołanie do tablicy par nazwa/wartość (a także liczba właściwości). W tym momencie jest to prosta kwestia wyliczenia właściwości, aby uzyskać dostęp do żądanych wartości.

Nie musisz używać właściwości w aplikacji. Jednak jeśli trzeba będzie je ustawić dla zdarzeń lub pobrać je z komunikatów, biblioteka **usługi iothubclient** ułatwia.

## <a name="message-handling"></a>Obsługa komunikatów

Jak wspomniano wcześniej, w przypadku nadejścia komunikatów z IoT Hub Biblioteka **usługi iothubclient** reaguje na wywołanie zarejestrowanej funkcji wywołania zwrotnego. Istnieje parametr zwracający tę funkcję, która zachowuje dodatkowe wyjaśnienie. Oto fragment funkcji wywołania zwrotnego w przykładowej aplikacji przykładowej **\_\_\_http klienta iothub** :

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Zwróć uwagę, że zwracany typ **to\_IOTHUBMESSAGE\_wynik** , a w tym konkretnym przypadku zwracamy **IOTHUBMESSAGE\_zaakceptowane**. Istnieją inne wartości, które można zwrócić z tej funkcji, aby zmienić sposób, w jaki Biblioteka **usługi iothubclient** reaguje na wywołanie zwrotne wiadomości. Poniżej przedstawiono opcje.

* **Zaakceptowano IOTHUBMESSAGE\_** — komunikat został przetworzony pomyślnie. Biblioteka **usługi iothubclient** nie wywoła ponownie funkcji wywołania zwrotnego z tym samym komunikatem.

* **IOTHUBMESSAGE\_odrzucone** — komunikat nie został przetworzony i nie ma potrzeby wykonywania tego zadania w przyszłości. Biblioteka **usługi iothubclient** nie powinna ponownie wywoływać funkcji wywołania zwrotnego z tym samym komunikatem.

* **IOTHUBMESSAGE\_porzucone** — komunikat nie został przetworzony pomyślnie, ale biblioteka **usługi iothubclient** powinna ponownie wywołać funkcję wywołania zwrotnego z tym samym komunikatem.

W przypadku dwóch pierwszych kodów powrotnych Biblioteka **usługi iothubclient** wysyła komunikat do IoT Hub wskazujący, że komunikat powinien zostać usunięty z kolejki urządzeń i nie został ponownie dostarczony. Efekt sieci jest taki sam (komunikat jest usuwany z kolejki urządzeń), ale bez względu na to, czy wiadomość została zaakceptowana, czy odrzucona jest nadal rejestrowana.  Rejestrowanie tego rozróżnienia jest przydatne dla nadawców wiadomości, którzy mogą nasłuchiwać informacji zwrotnych i dowiedzieć się, czy urządzenie zaakceptowało lub odrzuciło określoną wiadomość.

W ostatnim przypadku komunikat jest również wysyłany do IoT Hub, ale wskazuje, że komunikat powinien zostać ponownie dostarczony. Zazwyczaj porzucasz komunikat, jeśli wystąpi błąd, ale chcesz spróbować ponownie przetworzyć komunikat. Natomiast odrzucanie komunikatu jest odpowiednie w przypadku wystąpienia nieodwracalnego błędu (lub jeśli po prostu zdecydujesz, że nie chcesz przetworzyć komunikatu).

W każdym przypadku należy pamiętać o różnych kodach powrotu, aby można było wywołać żądane zachowanie z biblioteki **usługi iothubclient** .

## <a name="alternate-device-credentials"></a>Alternatywne poświadczenia urządzenia

Jak wyjaśniono wcześniej, pierwszą rzeczą, którą należy wykonać podczas pracy z biblioteką **usługi iothubclient** , jest **uzyskanie\_uchwytu klienta\_IOTHUB** z wywołaniem, takim jak następujące:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumentami **usługi iothubclient\_CreateFromConnectionString** są parametry połączenia urządzenia i parametr wskazujący protokół używany do komunikowania się z IoT Hub. Parametry połączenia urządzenia mają następujący format:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

W tym ciągu znajdują się cztery informacje: Nazwa IoT Hub, sufiks IoT Hub, identyfikator urządzenia i klucz dostępu współdzielonego. Podczas tworzenia wystąpienia Centrum IoT w Azure Portal jest uzyskiwana w pełni kwalifikowana nazwa domeny (FQDN) w usłudze IoT Hub, która zawiera nazwę Centrum IoT Hub (pierwszą część nazwy FQDN) i sufiks Centrum IoT (pozostałą część nazwy FQDN). Podczas rejestrowania urządzenia w IoT Hub (zgodnie z opisem w [poprzednim artykule](iot-hub-device-sdk-c-intro.md)) uzyskasz identyfikator urządzenia i klucz dostępu współdzielonego.

**Usługiiothubclient\_CreateFromConnectionString** to jeden ze sposobów na zainicjowanie biblioteki. Jeśli wolisz, możesz utworzyć nowe dojście **klienta\_\_IOTHUB** za pomocą tych indywidualnych parametrów zamiast parametrów połączenia urządzenia. Jest to osiągane przy użyciu następującego kodu:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Ma to taki sam efekt jak **usługi iothubclient\_CreateFromConnectionString**.

Może wydawać się oczywiste, że chcesz użyć **usługi iothubclient\_CreateFromConnectionString** , a nie do tej bardziej szczegółowej metody inicjalizacji. Należy jednak pamiętać, że po zarejestrowaniu urządzenia w IoT Hub co otrzymujesz identyfikator urządzenia i klucz urządzenia (a nie parametry połączenia). Narzędzie zestawu SDK *Eksploratora urządzeń* wprowadzone w [poprzednim artykule](iot-hub-device-sdk-c-intro.md) używa bibliotek w **zestawie SDK usługi Azure IoT** do tworzenia parametrów połączenia urządzenia z identyfikatorem urządzenia, kluczem urządzenia i IoT Hub nazwą hosta. Dlatego wywoływanie metody **\_usługi iothubclient ll\_Create** może być zalecane, ponieważ powoduje to zaoszczędzenie kroku generowania parametrów połączenia. Użycie niezależnie od metody jest wygodne.

## <a name="configuration-options"></a>Opcje konfiguracji

Dotychczas wszystkie informacje o sposobie działania biblioteki **usługi iothubclient** odzwierciedlają zachowanie domyślne. Istnieje jednak kilka opcji, które można ustawić, aby zmienić sposób działania biblioteki. Jest to realizowane za pomocą interfejsu API **usługi iothubclient\_szystkie\_SetOption** . Rozważmy następujący przykład:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Istnieje kilka opcji, które są powszechnie używane:

* Tworzenie wsadowe (bool) — w przypadku **wartości true**dane wysyłane do IoT Hub są wysyłane w partiach. Jeśli **wartość jest równa false**, wiadomości są wysyłane pojedynczo. Wartość domyślna to **false**. Obsługiwane jest przetwarzanie wsadowe za pośrednictwem usługi AMQP/AMQP-WS, a także dodawanie właściwości systemu w komunikatach D2C.

* **Limit czasu** (bez znaku int) — ta wartość jest reprezentowana w milisekundach. Jeśli wysyłanie żądania HTTPS lub odebranie odpowiedzi trwa dłużej niż ten czas, zostanie przekroczony limit czasu połączenia.

Opcja wsadowa jest ważna. Domyślnie Biblioteka ingresses zdarzenia pojedynczo (pojedyncze zdarzenie jest dowolnie przekazywane do **\_usługi iothubclient szystkie\_SendEventAsync**). Jeśli opcja wsadowa ma **wartość true**, biblioteka zbiera tyle zdarzeń, ile może z bufora (do maksymalnego rozmiaru komunikatu, który IoT Hub zostanie zaakceptowany).  Partia zdarzeń jest wysyłana do IoT Hub w pojedynczym wywołaniu HTTPS (poszczególne zdarzenia są powiązane z tablicą JSON). Włączenie przetwarzania wsadowego zwykle skutkuje dużym wzrostem wydajności, ponieważ skracane są sieci rundy. Znacznie zmniejsza to przepustowość, ponieważ wysyła jeden zestaw nagłówków HTTPS z użyciem partii zdarzeń zamiast zestawu nagłówków dla każdego pojedynczego zdarzenia. Chyba że masz konkretną przyczynę do wykonania w przeciwnym razie, zazwyczaj chcesz włączyć przetwarzanie wsadowe.

## <a name="next-steps"></a>Następne kroki

W tym artykule szczegółowo opisano zachowanie biblioteki **usługi iothubclient** znajdującej się w **zestawie SDK urządzeń Azure IoT dla języka C**. Korzystając z tych informacji, należy dobrze zrozumieć możliwości biblioteki **usługi iothubclient** . Drugi artykuł w tej serii jest [zestawem SDK urządzeń Azure IoT dla serializatorów języka C](iot-hub-device-sdk-c-serializer.md), który oferuje podobne szczegóły dotyczące biblioteki **serializatorów** .

Aby dowiedzieć się więcej na temat opracowywania IoT Hub, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz [wdrażanie AI na urządzeniach brzegowych z Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
