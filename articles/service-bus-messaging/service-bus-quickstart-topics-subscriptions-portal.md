---
title: 'Szybki Start: tworzenie Service Bus tematów i subskrypcji za pomocą Azure Portal'
description: 'Szybki Start: w tym przewodniku szybki start dowiesz się, jak utworzyć temat Service Bus i subskrypcje w tym temacie przy użyciu Azure Portal.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6788bf43ef97daf5ef2f823a502c223d718abaac
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718866"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Szybki Start: Użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie
W tym przewodniku szybki start użyjesz Azure Portal do utworzenia tematu Service Bus, a następnie tworzenia subskrypcji w tym temacie. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co to są tematy i subskrypcje usługi Service Bus?
Tematy i subskrypcje usługi Service Bus obsługują model komunikacji z użyciem *publikowania/subskrypcji* komunikatów. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, która działa jako pośrednik.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

W przeciwieństwie do kolejek Service Bus, w których każdy komunikat jest przetwarzany przez jednego konsumenta, tematy i subskrypcje zapewniają formę komunikacji typu "jeden do wielu" przy użyciu wzorca publikowania/subskrybowania. Istnieje możliwość zarejestrowania wielu subskrypcji danego tematu. Po wysłaniu komunikatu do tematu jest on następnie udostępniany poszczególnym subskrypcjom w celu niezależnej obsługi lub niezależnego przetworzenia. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Opcjonalnie można zarejestrować reguły filtru dla tematu w oparciu o subskrypcję, co umożliwia filtrowanie lub ograniczanie komunikatów do tematu otrzymywanych przez poszczególne subskrypcje tematów.

Tematy Service Bus i subskrypcje umożliwiają skalowanie do przetwarzania dużej liczby komunikatów w dużej liczbie użytkowników i aplikacji.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak wysyłać komunikaty do tematu i odbierać je za pośrednictwem subskrypcji, zobacz następujący artykuł: Wybierz język programowania w spisie treści. 

> [!div class="nextstepaction"]
> [Publikowanie i subskrybowanie komunikatów](service-bus-dotnet-how-to-use-topics-subscriptions.md)
