---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 403004 DeviceMaximumQueueDepthExceeded
description: Dowiedz się, jak naprawić błąd 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d48fd9aa9ba52c850a514d392f25b980d0219470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960908"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

W tym artykule opisano przyczyny i rozwiązania **403004 błędów DeviceMaximumQueueDepthExceeded** .

## <a name="symptoms"></a>Objawy

Podczas próby wysłania komunikatu z chmury do urządzenia żądanie kończy się niepowodzeniem z błędem **403004** lub **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Przyczyna

Podstawową przyczyną jest to, że liczba komunikatów umieszczonych w kolejce dla urządzenia przekracza [limit kolejki (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Najbardziej prawdopodobną przyczyną jest to, że używasz protokołu HTTPS, aby odebrać komunikat, który prowadzi do ciągłego sondowania przy użyciu `ReceiveAsync`, co spowoduje IoT Hub ograniczenie żądania.

## <a name="solution"></a>Rozwiązanie

Obsługiwany wzorzec dla komunikatów z chmury do urządzenia z protokołem HTTPS jest sporadycznie połączonymi urządzeniami, które często sprawdzają obecność komunikatów (mniej niż co 25 minut). Aby zmniejszyć prawdopodobieństwo uruchomienia do limitu kolejki, przełącz się do AMQP lub MQTT w celu uzyskania komunikatów z chmury do urządzenia.

Alternatywnie możesz ulepszyć logikę po stronie urządzenia, aby szybko zakończyć, odrzucić lub zrezygnować z kolejkowanej wiadomości, skrócić czas wygaśnięcia lub rozważyć wysłanie mniejszej liczby komunikatów. Zobacz [Czas wygaśnięcia komunikatu C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Na koniec Rozważ użycie [interfejsu API przeczyszczania kolejki](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) , aby okresowo czyścić oczekujące komunikaty przed osiągnięciem limitu.