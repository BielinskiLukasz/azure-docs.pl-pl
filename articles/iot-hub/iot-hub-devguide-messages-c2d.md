---
title: Informacje na temat platformy Azure IoT Hub komunikatów z chmury do urządzeń | Microsoft Docs
description: W tym przewodniku dla deweloperów omówiono sposób korzystania z komunikatów z chmury do urządzeń w centrum IoT. Zawiera informacje o cyklu życia komunikatów i opcjach konfiguracji.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 4b8df538110f6c0b17a1ed37a2a6063a5b89a6e4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880978"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Wysyłanie komunikatów z chmury do urządzeń z Centrum IoT Hub

Aby wysyłać jednokierunkowe powiadomienia do aplikacji urządzenia z zaplecza rozwiązania, Wyślij komunikaty z chmury do urządzenia z Centrum IoT. Aby zapoznać się z innymi opcjami w chmurze i urządzeniami obsługiwanymi przez usługę Azure IoT Hub, zobacz [wskazówki dotyczące komunikacji z chmury do urządzeń](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Wysyłasz komunikaty z chmury do urządzenia za pomocą punktu końcowego połączonego z usługą, */messages/devicebound*. Następnie urządzenie odbiera wiadomości za pomocą punktu końcowego określonego dla urządzenia, */Devices/{deviceId}/messages/devicebound*.

Aby można było kierować każdy komunikat z chmury do urządzenia na jednym urządzeniu, usługa IoT Hub ustawia właściwość **na** na */Devices/{deviceId}/messages/devicebound*.

Każda kolejka urządzeń przechowuje maksymalnie 50 komunikatów z chmury do urządzenia. Próba wysłania większej liczby komunikatów do tego samego urządzenia spowoduje wystąpienie błędu.

## <a name="the-cloud-to-device-message-life-cycle"></a>Cykl życia komunikatów z chmury do urządzenia

Aby zagwarantować dostarczanie komunikatów co najmniej raz, usługa IoT Hub utrzymuje komunikaty z chmury do urządzenia w kolejkach poszczególnych urządzeń. Aby program IoT Hub usunął komunikaty z kolejki, urządzenia muszą jawnie potwierdzić *ukończenie*. Takie podejście gwarantuje odporność na awarie łączności i urządzeń.

Wykres stanu cyklu życia jest wyświetlany na poniższym diagramie:

![Cykl życia komunikatów z chmury do urządzenia](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Gdy usługa IoT Hub wysyła komunikat do urządzenia, Usługa ustawia stan komunikatu na wartość z *kolejki*. Gdy urządzenie chce *odebrać* komunikat, centrum IoT *blokuje* komunikat, ustawiając stan na niewidoczny. Ten stan umożliwia innym wątkom na urządzeniu rozpoczęcie otrzymywania innych komunikatów. Gdy wątek urządzenia kończy przetwarzanie komunikatu, wysyła komunikat do centrum IoT Hub. Usługa IoT Hub ustawia stan na *ukończone*.

Urządzenie może również:

* *Odrzuć* komunikat, co spowoduje, że Centrum IoT zostanie ustawione na stan *utraconych* wiadomości. Urządzenia łączące się za pośrednictwem protokołu transportowego usługi kolejkowania komunikatów (MQTT) nie mogą odrzucać komunikatów z chmury do urządzenia.

* *Porzuć* komunikat, co spowoduje, że Centrum IoT przeniesie komunikat z powrotem do kolejki, a stan ustawi jako umieszczony na *kolejce*. Urządzenia, które nawiązują połączenie za pośrednictwem protokołu MQTT, nie mogą zrezygnować z komunikatów z chmury do urządzenia.

Wątek nie może przetworzyć komunikatu bez powiadomienia centrum IoT Hub. W takim przypadku komunikaty są automatycznie przenoszone z *niewidocznego* stanu po przekroczeniu limitu czasu *widoczności* (lub przekroczenia limitu czasu *blokady* ). Wartość tego limitu czasu wynosi jedna minuta i nie można jej zmienić.

Właściwość **Maksymalna liczba dostaw** w centrum IoT określa maksymalną liczbę przypadków przejścia komunikatu między podstanem w *kolejce* i niewidocznym stanie. Po tej liczbie zmian Centrum IoT ustawia stan wiadomości na *utracony*. Podobnie Centrum IoT określa stan wiadomości, które mają zostać *utracone* po upływie czasu wygaśnięcia. Aby uzyskać więcej informacji, zobacz [Time to Live](#message-expiration-time-to-live).

[Informacje o sposobie wysyłania komunikatów z chmury do urządzenia z IoT Hub](iot-hub-csharp-csharp-c2d.md) artykułu pokazują, jak wysyłać komunikaty z chmury do urządzenia z chmury i odbierać je na urządzeniu.

Urządzenie zwykle kończy komunikat z chmury do urządzenia, gdy utrata komunikatu nie wpłynie na logikę aplikacji. Przykładem może być to, że urządzenie utrwalał zawartość komunikatu lokalnie lub pomyślnie wykonał operację. Komunikat może również zawierać informacje przejściowe, których utrata nie wpłynie na funkcjonalność aplikacji. Czasami w przypadku długotrwałych zadań można:

* Ukończ komunikat z chmury do urządzenia po utrwaleniu przez urządzenie opisu zadania w magazynie lokalnym.

* Powiadom zaplecza rozwiązania z co najmniej jedną wiadomością z urządzenia do chmury na różnych etapach postępu zadania.

## <a name="message-expiration-time-to-live"></a>Wygaśnięcie komunikatu (czas wygaśnięcia)

Każdy komunikat z chmury do urządzenia ma czas wygaśnięcia. Ten czas jest ustawiany przez jedną z następujących wartości:

* Właściwość **ExpiryTimeUtc** w usłudze
* Centrum IoT Hub przy użyciu domyślnego *czasu do wygaśnięcia* określonego jako właściwość Centrum IoT Hub

Zobacz [Opcje konfiguracji chmury do urządzenia](#cloud-to-device-configuration-options).

Typowym sposobem wykorzystania wygaśnięcia komunikatów i uniknięcia wysyłania komunikatów do odłączonych urządzeń jest ustawienie krótkiego *czasu na wartości na żywo* . Takie podejście osiąga ten sam wynik, jak utrzymywanie stanu połączenia urządzenia, ale jest bardziej wydajne. W przypadku żądania potwierdzeń wiadomości Centrum IoT powiadamia o urządzeniach, które są:

* Można odbierać wiadomości.
* Nie są w trybie online lub zakończyły się niepowodzeniem.

## <a name="message-feedback"></a>Wiadomość zwrotna

Po wysłaniu komunikatu z chmury do urządzenia usługa może zażądać dostarczenia informacji zwrotnych na temat wiadomości o stanie końcowym tego komunikatu. W tym celu należy ustawić właściwość aplikacji **iothub-ACK** w komunikacie z chmury do urządzenia, który jest wysyłany do jednej z czterech następujących wartości:

| ACK — wartość właściwości | Zachowanie |
| ------------ | -------- |
| brak     | Centrum IoT nie generuje wiadomości z opiniami (domyślne zachowanie). |
| pozytywna | Jeśli komunikat z chmury do urządzenia osiągnie stan *ukończono* , usługa IoT Hub generuje wiadomość z opinią. |
| poziomem | Jeśli komunikat z chmury do urządzenia osiągnie stan utraconych wiadomości, usługa IoT Hub generuje wiadomość z opinią. |
| pełne     | W obu przypadkach w usłudze IoT Hub jest generowany komunikat z opinią. |

Jeśli wartość **ACK** jest *pełna*i nie otrzymasz wiadomości z opinią, oznacza to, że wiadomość dotycząca opinii wygasła. Usługa nie może znać, co się stało z oryginalnym komunikatem. W tym przypadku usługa powinna upewnić się, że może przetworzyć opinię przed jej wygaśnięciem. Maksymalny czas wygaśnięcia wynosi dwa dni, co pozostawia czas na ponowne uruchomienie usługi w przypadku wystąpienia błędu.

> [!NOTE]
> Gdy urządzenie zostanie usunięte, wszystkie oczekujące Opinie również zostaną usunięte.
>

Jak wyjaśniono [](iot-hub-devguide-endpoints.md)w punktach końcowych, usługa IoT Hub dostarcza informacje zwrotne za pomocą punktu końcowego opartego na usłudze, */messages/servicebound/feedback*jako komunikatów. Semantyka do otrzymywania opinii jest taka sama jak w przypadku komunikatów z chmury do urządzenia. Zawsze, gdy jest to możliwe, informacja zwrotna wiadomości jest przetwarzana w jednej wiadomości, w następującym formacie:

| Właściwość     | Opis |
| ------------ | ----------- |
| EnqueuedTime | Sygnatura czasowa wskazująca, kiedy wiadomość zwrotna została odebrana przez centrum |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Treść jest serializowaną w formacie JSON tablicą rekordów, z których każdy ma następujące właściwości:

| Właściwość           | Opis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Sygnatura czasowa wskazująca, kiedy wystąpił wynik komunikatu (na przykład centrum odebrało wiadomość z opinia lub wygasła oryginalna wiadomość) |
| OriginalMessageId  | Identyfikator *MessageID* komunikatu z chmury do urządzenia, do którego odnoszą się informacje o opinii |
| StatusCode         | Wymagany ciąg używany w wiadomościach zwrotnych, które są generowane przez Centrum IoT: <br/> *Prawnego* <br/> *Przeterminowanych* <br/> *DeliveryCountExceeded* <br/> *Odrzucono* <br/> *Przeczyszczane* |
| Opis        | Wartości ciągu dla elementu *StatusCode* |
| DeviceId           | Identyfikator urządzenia docelowego komunikatu z chmury do urządzenia, do którego odnosi się ten element opinii |
| DeviceGenerationId | *DeviceGenerationId* urządzenia docelowego komunikatu z chmury do urządzenia, do którego odnosi się ten element opinii |

Aby komunikat z chmury do urządzenia mógł skorelować swoją opinię z oryginalnym komunikatem, usługa musi określić wartość *MessageID*.

Treść wiadomości z opiniami jest pokazana w poniższym kodzie:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Opcje konfiguracji chmury do urządzenia

Każde Centrum IoT Hub udostępnia następujące opcje konfiguracji obsługi komunikatów między chmurą i urządzeniem:

| Właściwość                  | Opis | Zakres i domyślny |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Domyślny czas wygaśnięcia dla komunikatów z chmury do urządzenia | Interwał ISO_8601 do 2 dni (minimum 1 min); wartooć 1 godzina |
| maxDeliveryCount          | Maksymalna liczba dostaw dla kolejek dla poszczególnych urządzeń w chmurze | od 1 do 100; wartooć 10 |
| feedback.ttlAsIso8601     | Przechowywanie komunikatów z odpowiedziami związanymi z usługą | Interwał ISO_8601 do 2 dni (minimum 1 min); wartooć 1 godzina |
| feedback.maxDeliveryCount | Maksymalna liczba dostaw dla kolejki opinii | od 1 do 100; wartooć 100 |

Aby uzyskać więcej informacji na temat sposobu ustawiania tych opcji konfiguracji, zobacz [Tworzenie centrów IoT](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o zestawach SDK, których można użyć do odbierania komunikatów z chmury do urządzeń, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).

Aby wypróbować otrzymywanie komunikatów z chmury do urządzeń, zobacz Samouczek dotyczący [wysyłania chmury do urządzenia](iot-hub-csharp-csharp-c2d.md) .
