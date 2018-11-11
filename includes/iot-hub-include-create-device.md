---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4a4ec51430242cc3e3d6a0b801f2b4be7858ab50
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516113"
---
<!-- put the ## header in the file that includes this file -->

W tej sekcji utworzysz tożsamości urządzenia w rejestrze tożsamości w usłudze IoT hub. Urządzenie nie może połączyć się z centrum IoT, jeśli nie ma wpisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz sekcję "Identity registry" [usługi IoT Hub — przewodnik dewelopera](../articles/iot-hub/iot-hub-devguide-identity-registry.md) 

1. W menu nawigacyjnym Centrum IoT Otwórz **urządzeń IoT**, następnie kliknij przycisk **Dodaj** można zarejestrować nowego urządzenia w usłudze IoT hub.

    ![Tworzenie tożsamości urządzenia w portalu](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Podaj nazwę dla nowego urządzenia, takie jak **myDeviceId**i kliknij przycisk **Zapisz**. Ta akcja tworzy nową tożsamość urządzenia w Centrum IoT.

   ![Dodaj nowe urządzenie](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Po utworzeniu urządzenia otwórz je z listy w **urządzeń IoT** okienka. Kopiuj **parametry połączenia---klucza podstawowego** do późniejszego użycia.

    ![Parametry połączenia urządzenia](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. Przechowuje identyfikatory urządzeń i klucze, które będą używane jako poświadczenia zabezpieczeń, oraz flagę włączone/wyłączone, która umożliwia wyłączenie dostępu do poszczególnych urządzeń. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub — przewodnik dewelopera](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
