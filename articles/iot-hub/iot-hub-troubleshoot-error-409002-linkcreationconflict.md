---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 409002 LinkCreationConflict
description: Dowiedz się, jak naprawić błąd 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758743"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

W tym artykule opisano przyczyny i rozwiązania **409002 błędów LinkCreationConflict** .

## <a name="symptoms"></a>Objawy

Zobaczysz błąd **409002 LinkCreationConflict** zarejestrowanych w dziennikach diagnostycznych wraz z rozłączeniem urządzenia lub błędem komunikatu z chmury do urządzenia. 

<!-- When using AMQP? -->

## <a name="cause"></a>Przyczyna

Ogólnie rzecz biorąc, ten błąd występuje, gdy IoT Hub wykryje, że klient ma więcej niż jedno połączenie. W rzeczywistości gdy nowe żądanie połączenia zostanie odebrane dla urządzenia z istniejącym połączeniem, IoT Hub zamyka istniejące połączenie z tym błędem.

### <a name="cause-1"></a>Przyczyna 1

W najbardziej typowym przypadku, oddzielnym problemem (takim jak [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) powoduje rozłączenie urządzenia. Urządzenie próbuje ponownie nawiązać połączenie, ale IoT Hub nadal uznaje, że urządzenie jest połączone. IoT Hub zamyka poprzednie połączenie i rejestruje ten błąd.

### <a name="cause-2"></a>Przyczyna 2

Uszkodzona logika po stronie urządzenia powoduje, że urządzenie nawiązuje połączenie, gdy jeden z nich jest już otwarty.

## <a name="solution"></a>Rozwiązanie

Ten błąd zazwyczaj występuje jako efekt uboczny innego, przejściowego problemu, dlatego wyszukasz inne błędy w dziennikach, aby rozwiązać problem. W przeciwnym razie należy wydać nowe żądanie połączenia tylko wtedy, gdy połączenie zostanie porzucane.
