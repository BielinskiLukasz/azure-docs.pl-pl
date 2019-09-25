---
title: Wysyłanie powiadomień zaplanowanych | Microsoft Docs
description: W tym temacie opisano używanie zaplanowanych powiadomień z usługą Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: powiadomienia wypychane, powiadomienia wypychane, planowanie powiadomień wypychanych
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213005"
---
# <a name="how-to-send-scheduled-notifications"></a>Instrukcje: Wysyłanie powiadomień zaplanowanych

Jeśli masz scenariusz, w którym chcesz wysłać powiadomienie w pewnym momencie w przyszłości, ale nie masz łatwego sposobu na wznowienie kodu zaplecza w celu wysłania powiadomienia. Centra powiadomień warstwy standardowej obsługują funkcję, która umożliwia planowanie powiadomień do siedmiu dni w przyszłości.


## <a name="schedule-your-notifications"></a>Planowanie powiadomień
Podczas wysyłania powiadomienia po prostu Użyj [ `ScheduledNotification` klasy](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) w zestawie SDK Notification Hubs, jak pokazano w następującym przykładzie:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Anuluj zaplanowane powiadomienia
Ponadto możesz anulować wcześniej zaplanowane powiadomienie przy użyciu jego notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Nie ma ograniczeń dotyczących liczby zaplanowanych powiadomień, które można wysłać.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące samouczki:

 - [Powiadomienia wypychane do wszystkich zarejestrowanych urządzeń](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Wypychanie powiadomień zlokalizowanych](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Wysyłanie powiadomień push do konkretnych użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Wypychanie powiadomień na podstawie lokalizacji](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
