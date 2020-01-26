---
title: Jak używać kolejek Azure Service Bus przy użyciu języka Ruby
description: W ramach tego samouczka nauczysz się tworzyć aplikacje Ruby do wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a699543bb442e7c57d57e72acb2cdf6ac40159c1
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760593"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Szybki Start: jak używać kolejek Service Bus przy użyciu języka Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W ramach tego samouczka nauczysz się tworzyć aplikacje Ruby do wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus. Przykłady są zapisywane w języku Ruby i korzystają z usługi Azure rozwiązania gem.

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Wykonaj czynności opisane w [Azure Portal Użyj, aby utworzyć artykuł kolejki Service Bus](service-bus-quickstart-portal.md) .
    1. Zapoznaj się z krótkim omówieniem **kolejek**Service Bus. 
    2. Utwórz **przestrzeń nazw**Service Bus. 
    3. Pobierz **Parametry połączenia**. 

        > [!NOTE]
        > W tym samouczku utworzysz **kolejkę** w przestrzeni nazw Service Bus przy użyciu języka Ruby. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Jak utworzyć kolejkę
Obiekt **Azure:: ServiceBusService** umożliwia korzystanie z kolejek. Aby utworzyć kolejkę, użyj metody `create_queue()`. Poniższy przykład tworzy kolejkę lub drukuje błędy.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Można również przekazać obiekt **Azure:: ServiceBus:: Queue** z dodatkowymi opcjami, co pozwala na zastąpienie domyślnych ustawień kolejki, takich jak czas wiadomości na żywo lub maksymalny rozmiar kolejki. Poniższy przykład pokazuje, jak ustawić maksymalny rozmiar kolejki na 5 GB i czas wygaśnięcia na 1 minutę:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Jak wysyłać komunikaty do kolejki
Aby wysłać komunikat do kolejki Service Bus, aplikacja wywołuje metodę `send_queue_message()` w obiekcie **Azure:: ServiceBusService** . Komunikaty wysyłane do (i odbieranych z) Service Bus kolejki są typu **Azure:: ServiceBus:: BrokeredMessage** i mają zestaw właściwości standardowych (takich jak `label` i `time_to_live`), słownik używany do przechowywania niestandardowych właściwości specyficznych dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja może ustawić treść komunikatu przez przekazanie wartości ciągu jako komunikat i wszystkie wymagane właściwości standardowe są wypełniane wartościami domyślnymi.

Poniższy przykład pokazuje, jak wysłać wiadomość testową do kolejki o nazwie `test-queue` przy użyciu `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Jak odbierać komunikaty z kolejki
Komunikaty są odbierane z kolejki przy użyciu metody `receive_queue_message()` w obiekcie **Azure:: ServiceBusService** . Domyślnie komunikaty są odczytywane i blokowane bez usuwania z kolejki. Można jednak usuwać wiadomości z kolejki w miarę ich odczytywania, ustawiając opcję `:peek_lock` na **false**.

Zachowanie domyślne polega na odczycie i usunięciu operacji dwuetapowej, co również umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niezawodny w przyszłości), kończy drugi etap procesu odbierania przez wywołanie metody `delete_queue_message()` i podanie komunikatu, który ma zostać usunięty jako parametr. Metoda `delete_queue_message()` oznaczy komunikat jako używany i usuń go z kolejki.

Jeśli parametr `:peek_lock` ma wartość **false**, odczytywanie i usuwanie wiadomości stanie się najprostszym modelem i sprawdza się najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetwarzanie komunikatu w przypadku awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznaczył komunikat jako używany, gdy aplikacja jest ponownie uruchamiana i zaczyna zużywać komunikaty, zostanie pominięty komunikat, który został zużyty przed awarią.

W poniższym przykładzie pokazano, jak odbierać i przetwarzać komunikaty przy użyciu `receive_queue_message()`. Przykład najpierw odbiera i usuwa komunikat przy użyciu `:peek_lock` ustawiona na **wartość false**, następnie otrzymuje kolejny komunikat, a następnie usuwa komunikat przy użyciu `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać metodę `unlock_queue_message()` w obiekcie **Azure:: ServiceBusService** . To wywołanie powoduje Service Bus odblokowywanie komunikatu w kolejce i udostępnienie go do odebrania przez tę samą aplikację wykorzystującą lub przez inną aplikację, która korzysta z aplikacji.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli awaria aplikacji), Service Bus odblokować komunikat automatycznie i spowoduje, że dostępne do ponownego odebrania.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed wywołaniem metody `delete_queue_message()`, komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Ten proces jest często wywoływany *co najmniej raz podczas przetwarzania*; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach może zostać ponownie dostarczony ten sam komunikat. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu właściwości `message_id` komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Omówienie [kolejek, tematów i subskrypcji](service-bus-queues-topics-subscriptions.md).
* Odwiedź witrynę [Azure SDK dla repozytorium Ruby](https://github.com/Azure/azure-sdk-for-ruby) w serwisie GitHub.

Aby uzyskać informacje na temat porównania między kolejkami Azure Service Bus omówione w tym artykule i kolejkami platformy Azure omówionymi w artykule [jak korzystać z usługi queue storage z poziomu języka Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) , zobacz kolejki [platformy azure i kolejki Azure Service Bus — porównane i różnicowe](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

