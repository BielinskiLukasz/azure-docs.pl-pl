---
title: Uaktualnienie do usługi Azure Search .NET Management SDK wersja 2 — Usługa Azure Search
description: Uaktualnienie do usługi Azure Search .NET Management SDK w wersji 2 z poprzednich wersji. Poznaj nowe funkcje i zmiany kodu są wymagane.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.custom: seodec2018
ms.openlocfilehash: 369e5283d7350729e0d8cc44f94f1f2e71b133c3
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316877"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Uaktualnienie do usługi Azure Search .NET Management SDK w wersji 2
Jeśli używasz wersji 1.0.2 lub starszy z [zestawu SDK usługi Azure Search .NET zarządzania](https://aka.ms/search-mgmt-sdk), ten artykuł pomoże Ci uaktualnić aplikację do używania w wersji 2.

W wersji 2 zestawu SDK usługi Azure Search .NET zarządzania zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim pomocnicza, dzięki czemu Zmienianie kodu należy wymagać tylko minimalnym nakładzie pracy. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) zawiera instrukcje dotyczące sposobu zmiany kodu do nowej wersji zestawu SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Co nowego w wersji 2
W wersji 2 zestawu SDK usługi Azure Search .NET zarządzania jest przeznaczony dla tego samego ogólnie dostępnej wersji interfejsu API REST usługi Azure Search zarządzania niż poprzednie wersje zestawu SDK, w szczególności 2015-08-19. Zmiany do zestawu SDK są zmiany wyłącznie po stronie klienta w celu zwiększenia użyteczności samego zestawu SDK. Te zmiany są następujące:

* `Services.CreateOrUpdate` i jego wersji asynchronicznej teraz sondują aprowizacji `SearchService` i nie zwracają przed zakończeniem inicjowania obsługi usługi. To pozwala uniknąć konieczności pisania kodu takie sondowania, samodzielnie.
* Jeśli chcesz nadal sondować usługę aprowizacji ręcznie, można użyć nowej `Services.BeginCreateOrUpdate` metody lub jednej z jego wersji asynchronicznej.
* Nowe metody `Services.Update` i jego wersji asynchroniczne zostały dodane do zestawu SDK. Te metody umożliwiają HTTP PATCH obsługuje aktualizacji przyrostowych usługi. Na przykład, teraz możesz skalować usługę, przekazując `SearchService` wystąpienia tych metod, które zawiera tylko żądany `partitionCount` i `replicaCount` właściwości. Stary sposób wywoływania `Services.Get`, modyfikując zwracanego `SearchService`i przekazanie jej do `Services.CreateOrUpdate` nadal jest obsługiwany, ale nie jest już konieczne. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Management.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety i ich zależności, należy ponownie skompilować projekt. W zależności od struktury kodu jego może odbudować pomyślnie. Jeśli tak, jesteś gotowy!

Jeśli kompilacja zakończy się niepowodzeniem, być może został zaimplementowany, niektóre interfejsy zestawu SDK (na przykład dla celów testowania jednostek), które zostały zmienione. Aby rozwiązać ten problem, należy zaimplementować nowych metod, takich jak `BeginCreateOrUpdateWithHttpMessagesAsync`.

Gdy problem został rozwiązany błędów kompilacji, można wprowadzić zmiany do aplikacji w taki sposób, aby móc korzystać z nowych funkcji, jeśli chcesz, aby. Nowe funkcje w zestawie SDK są szczegółowo opisane w [co nowego w wersji 2](#WhatsNew).

## <a name="conclusion"></a>Podsumowanie
Chętnie poznamy Twoją opinię na zestawie SDK. Jeśli napotkasz problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Jeśli znajdziesz błąd, możesz Prześlij zgłoszenie do [repozytorium Azure .NET SDK w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że prefiks tytuł problemu z usługą "[Azure Search]".

Dziękujemy za pomocą usługi Azure Search.
