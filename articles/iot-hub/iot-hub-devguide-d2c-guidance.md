---
title: Azure IoT Hub opcje z urządzenia do chmury | Microsoft Docs
description: Przewodnik dla deweloperów — wskazówki dotyczące sytuacji, w których należy używać komunikatów z urządzenia do chmury, raportowanych właściwości lub przekazywania plików na potrzeby komunikacji między chmurą i urządzeniem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b984ee7ed662bf089e7bcb6fc7e948fb61ed1209
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733214"
---
# <a name="device-to-cloud-communications-guidance"></a>Wskazówki dotyczące komunikacji między urządzeniami i chmurą

W przypadku wysyłania informacji z aplikacji urządzenia do zaplecza rozwiązania IoT Hub uwidacznia trzy opcje:

* [Komunikaty z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) na potrzeby telemetrii i alertów szeregów czasowych.

* Informacje o [raportowanych właściwościach urządzenia](iot-hub-devguide-device-twins.md) , takie jak dostępne możliwości, warunki lub stan długotrwałych przepływów pracy. Na przykład konfiguracja i aktualizacje oprogramowania.

* [Operacje przekazywania](iot-hub-devguide-file-upload.md) plików multimedialnych i dużych partii danych telemetrycznych przekazanych przez sporadycznie połączone urządzenia lub skompresowane, aby zaoszczędzić przepustowość.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Poniżej przedstawiono szczegółowe porównanie różnych opcji komunikacji między urządzeniami i chmurą.

|  | Komunikaty z urządzenia do chmury | Raportowane właściwości przędzy urządzenia | Operacje przekazywania plików |
| ---- | ------- | ---------- | ---- |
| Scenariusz | Szeregi i alerty telemetrii czasowej. Na przykład wsadowe dane czujników 256-KB są wysyłane co 5 minut. | Dostępne możliwości i warunki. Na przykład bieżący tryb łączności urządzenia, taki jak sieć komórkowa lub Wi-Fi. Synchronizacja długotrwałych przepływów pracy, takich jak konfiguracja i aktualizacje oprogramowania. | Pliki multimedialne. Duże (zazwyczaj skompresowane) partie danych telemetrycznych. |
| Magazynowanie i pobieranie | Tymczasowo przechowywane przez IoT Hub, do 7 dni. Tylko Odczyt sekwencyjny. | Przechowywane przez IoT Hub w bliźniaczych urządzeniach. Pobieranie przy użyciu [języka zapytań IoT Hub](iot-hub-devguide-query-language.md). | Przechowywane na koncie usługi Azure Storage dostarczonego przez użytkownika. |
| Rozmiar | Do 256-KB komunikatów. | Maksymalny rozmiar raportowanych właściwości to 32 KB. | Maksymalny rozmiar pliku obsługiwany przez Blob Storage platformy Azure. |
| Częstotliwość | Wysoka. Aby uzyskać więcej informacji, zobacz [limity IoT Hub](iot-hub-devguide-quotas-throttling.md). | Średnia. Aby uzyskać więcej informacji, zobacz [limity IoT Hub](iot-hub-devguide-quotas-throttling.md). | Niski. Aby uzyskać więcej informacji, zobacz [limity IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protocol (Protokół) | Dostępne we wszystkich protokołach. | Dostępne za pomocą MQTT lub AMQP. | Dostępne w przypadku korzystania z dowolnego protokołu, ale wymaga protokołu HTTPS na urządzeniu. |

Aplikacja może wymagać wysłania informacji zarówno jako szeregów czasowych lub alertów telemetrycznych. W tym scenariuszu można wybrać jedną z następujących opcji:

* Aplikacja urządzenia wysyła komunikat z urządzenia do chmury i raportuje zmianę właściwości.
* Zaplecze rozwiązania może przechowywać informacje w znacznikach sznurka urządzenia po odebraniu wiadomości.

Ze względu na to, że komunikaty między urządzeniami a chmurą zapewniają znacznie wyższą przepływność niż aktualizacje urządzeń z systemem, czasami warto unikać aktualizowania sznurka urządzenia dla każdej wiadomości z urządzenia do chmury.
