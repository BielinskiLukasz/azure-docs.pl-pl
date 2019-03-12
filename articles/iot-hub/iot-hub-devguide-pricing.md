---
title: Zrozumienie cennika usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — dowiedzieć się, jak metod rozliczania i cen współpracuje z usługą IoT Hub, w tym przykłady pracy.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 23b53e852672c129ff148b0b493a44172f9baf9a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769748"
---
# <a name="azure-iot-hub-pricing-information"></a>Informacje o cenach usługi Azure IoT Hub

[Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) zawiera ogólne informacje dotyczące różnych jednostek SKU i ceny dla usługi IoT Hub. Ten artykuł zawiera więcej informacji na temat sposobu różnych funkcji usługi IoT Hub są mierzone jako komunikaty przez usługę IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Opłaty za operację

| Operacja | Informacja o rozliczeniach | 
| --------- | ------------------- |
| Operacje rejestru tożsamości <br/> (Tworzenie, pobieranie, listy, aktualizowanie i usuwanie) | Nie są naliczane. |
| Komunikaty z urządzenia do chmury | Pomyślnie wysłane wiadomości są naliczane we fragmentach po 4 KB na ruch przychodzący do usługi IoT Hub. Na przykład komunikat 6-KB jest rozliczany według 2 komunikaty. |
| Komunikaty z chmury do urządzenia | Pomyślnie wysłane wiadomości są rozliczane we fragmentach po 4 KB, na przykład komunikat 6-KB jest rozliczany według 2 komunikaty. |
| Operacje przekazywania plików | Transfer plików do usługi Azure Storage nie jest mierzone przez usługę IoT Hub. Komunikaty rozpoczęcia i zakończenia transferu plików są naliczane zgodnie z messaged mierzone w przyrostach o rozmiarze 4 KB. Na przykład przesyłanie pliku 10 MB jest rozliczane jako dwa komunikaty oprócz kosztów usługi Azure Storage. |
| Metody bezpośrednie | Metoda pomyślne żądania są rozliczane we fragmentach po 4 KB, a odpowiedzi są rozliczane we fragmentach po 4 KB jako dodatkowe komunikaty. Żądania odłączone urządzenia są rozliczane jako komunikaty we fragmentach po 4 KB. Na przykład metody z treścią 4 KB, powstałego w odpowiedzi z bez treści z urządzenia jest rozliczane jako dwa komunikaty. Metody z treścią 6-KB, powstałego w odpowiedzi na 1 KB z urządzenia jest rozliczane jako dwa komunikaty żądania plus inny komunikat odpowiedzi. |
| Bliźniacza reprezentacja urządzenia i moduł odczytuje | Bliźniacze odczyty z urządzenia lub moduł i z powrotem rozwiązania end są rozliczane jako wiadomości we fragmentach 512-bajtowego. Na przykład odczytywania bliźniaczego elementu 6-KB jest rozliczane jako wiadomości o 12. |
| Bliźniacza reprezentacja urządzenia i moduł aktualizacji (tagów i właściwości) | Liczba aktualizacji bliźniaczej reprezentacji z urządzenia lub modułu i zapleczem rozwiązania są rozliczane jako komunikaty we fragmentach 512-bajtowego. Na przykład odczytywania bliźniaczego elementu 6-KB jest rozliczane jako wiadomości o 12. |
| Zapytania dotyczące bliźniaczych reprezentacji urządzeń i modułu | Zapytania są rozliczane jako komunikaty, w zależności od rozmiaru wynik we fragmentach 512-bajtowego. |
| Operacje zadań <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | Nie są naliczane. |
| Operacje dotyczące zadań poszczególnych urządzeń. | Operacje dotyczące zadań (na przykład liczba aktualizacji bliźniaczej reprezentacji i metody) są naliczane w zwykły sposób. Na przykład zadanie, co w wywołaniach metod 1000 żądań na 1 KB i pusta treść odpowiedzi jest rozliczany według 1000 komunikatów. |
| Komunikaty utrzymywania aktywności | Podczas korzystania z protokołów AMQP lub protokołu MQTT, komunikatów wymienianych do nawiązania połączenia i wiadomości wymieniane w negocjacji nie są naliczane. |

> [!NOTE]
> Wszystkie rozmiary są obliczane, biorąc pod uwagę rozmiar ładunku w bajtach (ramek protokołu jest ignorowany). Dla wiadomości, które mają właściwości i treści, rozmiar jest obliczany w sposób niezależny od protokołu. Aby uzyskać więcej informacji, zobacz [format komunikatów usługi IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Przykład #1

Urządzenie wysyła jeden komunikat urządzenia do chmury 1 KB na minutę do usługi IoT Hub, który jest odczytywany przez usługę Azure Stream Analytics. Zaplecze rozwiązania wywołuje metodę (z ładunkiem 512-bajtowych) na urządzeniu co 10 minut do wyzwalania określonych akcji. Urządzenie odpowiada metody z wynikiem bajty 200.

Urządzenie zużywa:

* Jeden komunikat * 60 minut * 24 godziny = 1440 wiadomości dziennie w przypadku komunikatów z urządzenia do chmury.
* Dwa żądania i odpowiedzi * 6 razy na godzinę * 24 godziny = 288 komunikaty dla metod.

To obliczenie daje w sumie 1728 komunikatów dziennie.

## <a name="example-2"></a>Przykład #2

Urządzenie wysyła jeden komunikat z urządzenia do chmury 100 KB co godzinę. Aktualizuje również jego bliźniaczej reprezentacji urządzenia z ładunkami 1 KB co cztery godziny. Rozwiązania zakończenia, raz dziennie, odczytuje bliźniaczej reprezentacji urządzenia 14 KB i aktualizuje go z ładunkami 512-bajtowych w celu zmiany konfiguracji.

Urządzenie zużywa:

* 25 wiadomości (100 KB/4 KB) * 24 godziny na komunikaty z urządzenia do chmury.
* Dwa komunikaty (1 KB/o rozmiarze 0,5 KB) * sześć razy dziennie w przypadku aktualizacji bliźniaczej reprezentacji urządzenia.

To obliczenie daje w sumie 612 komunikatów dziennie.

Zaplecze rozwiązania korzysta z 28 wiadomości (14 KB/o rozmiarze 0,5 KB) można odczytać bliźniaczej reprezentacji urządzenia, a także jeden komunikat, aby zaktualizować go, w sumie 29 wiadomości.

W sumie 641 komunikatów na dzień, korzystać z urządzenia i zapleczem rozwiązania.
