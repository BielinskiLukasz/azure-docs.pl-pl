---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987775"
---
Po uruchomieniu przykładowej aplikacji urządzenia w dalszej części tego samouczka potrzebne są następujące wartości konfiguracji:

* Zakres identyfikatorów: w aplikacji IoT Central przejdź do obszarze **administracja > połączenie z urządzeniem**. Zanotuj wartość **identyfikatora Scope** .
* Klucz podstawowy grupy: w aplikacji IoT Central przejdź do obszarze **administracja > połączenie z urządzeniem > SAS-IoT-Devices**. Zanotuj wartość **klucza podstawowego** sygnatury dostępu współdzielonego.

Użyj Cloud Shell do wygenerowania klucza urządzenia z właśnie pobranego klucza SAS grupy:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Zanotuj wygenerowany klucz urządzenia i użyj go w dalszej części tego samouczka.
