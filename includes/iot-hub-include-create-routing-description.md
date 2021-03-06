---
title: plik dołączania
description: plik dołączania
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72808816"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Planujesz rozsyłać komunikaty do różnych zasobów na podstawie właściwości dołączonych do komunikatu za pośrednictwem urządzenia symulowanego. Komunikaty bez tras niestandardowych są wysyłane do domyślnego punktu końcowego (wiadomości/zdarzenia). W następnym samouczku wysyłane są komunikaty do IoT Hub i są one przekazywane do różnych miejsc docelowych.

|Wartość |Wynik|
|------|------|
|level=„storage” |Zapis w usłudze Azure Storage.|
|level=„critical” |Zapis w kolejce usługi Service Bus. Aplikacja logiki pobiera komunikat z kolejki i używa usługi Office 365 do wysłania go pocztą e-mail.|
|default |Wyświetlanie tych danych przy użyciu usługi Power BI.|

Pierwszym krokiem jest skonfigurowanie punktu końcowego, do którego będą kierowane dane. Drugim krokiem jest skonfigurowanie trasy wiadomości korzystającej z tego punktu końcowego. Po skonfigurowaniu routingu można wyświetlić punkty końcowe i trasy komunikatów w portalu.