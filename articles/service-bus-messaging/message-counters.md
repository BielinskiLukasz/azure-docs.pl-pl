---
title: Liczba komunikatów w usłudze Azure Service Bus | Dokumentacja firmy Microsoft
description: Pobierz liczba komunikatów usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: 954c16cefe6d7ffe61a0b04b274b9bf92306a587
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857588"
---
# <a name="message-counters"></a>Liczniki komunikatów

Liczba komunikatów w subskrypcji i kolejek usługi za pomocą usługi Azure Resource Manager i Service Bus można pobrać [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) interfejsów API w programie .NET Framework SDK.

Za pomocą programu PowerShell możesz uzyskać liczby w następujący sposób:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Szczegóły liczba wiadomości

Liczba aktywnych komunikatów, wiedząc, jest przydatne w określaniu, czy kolejka są gromadzone zaległości, która wymaga więcej zasobów do przetwarzania niż co aktualnie wdrożonych. Poniższe szczegóły licznika są dostępne w [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) klasy:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): wiadomości w kolejce lub subskrypcji, które są aktywnie stanu i gotowe do dostarczenia.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): wiadomości w kolejce wiadomości utraconych.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): wiadomości w stanie Zaplanowane.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): wiadomości, które nie można przenieść do innej kolejki lub tematu i została przeniesiona do kolejki utraconych wiadomości transferu.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Oczekujące przenieść do innej kolejki lub tematu wiadomości.

Jeśli aplikacja chce, aby skalować swoje zasoby na podstawie długości kolejki, go to zrobić przy użyciu mierzonego tempie. Nabycie liczniki komunikatów jest kosztowną operacją wewnątrz brokera komunikatów, a jej wykonanie, często bezpośrednio i negatywnie wpływa na wydajność jednostki.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)