---
title: Informacje o tym, jak działa proces automatycznej migracji dla Azure Monitor klasycznych alertów
description: Dowiedz się, jak działa proces automatycznego migrowania.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: fbc024b6ae6e3ebfed6f64067158352e8201aef5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642567"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Omówienie procesu migracji automatycznej dla klasycznych reguł alertów

Zgodnie z [dotychczas ogłoszone](monitoring-classic-retirement.md), klasyczne alerty w Azure monitor są wycofywane we wrześniu 2019 (wersja była pierwotnie 2019 lipca). W ramach procesu wycofywania, [Narzędzie do migracji](alerts-using-migration-tool.md) jest dostępne w Azure Portal, aby klienci mogli wyzwolić migrację. Jeśli nie korzystasz z narzędzia migracji do 31 sierpnia 2019, Azure Monitor rozpocznie proces automatycznej migracji alertów klasycznych od 1 września 2019.
W tym artykule przedstawiono proces migracji automatycznej i pomoc w rozwiązywaniu wszelkich problemów, które mogą zostać uruchomione.

  > [!NOTE]
  > Ten artykuł dotyczy tylko chmury publicznej platformy Azure. Proces wycofywania dla Azure Monitor klasycznych alertów w chmurze Azure Government i Azure Chiny 21Vianet będą ogłaszane w przyszłości.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Co się stanie w trakcie procesu migracji automatycznej?

- Od **1 września 2019**klienci nie będą mogli tworzyć żadnych nowych klasycznych reguł alertów z wyjątkiem [określonych metryk](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).
  - W przypadku wyjątków klient może nadal tworzyć nowe reguły klasycznego alertu i korzystać z ich klasycznych alertów do 2020 czerwca.
- Od **1 września 2019**migracja klasycznych alertów zostanie wyzwolona w partiach dla wszystkich klientów, którzy mają klasyczne alerty.
- Podobnie jak w przypadku narzędzia do migracji dobrowolnej, niektóre reguły klasycznego alertu, które nie są migrowalna, zostaną pozostawione. Te klasyczne reguły alertów będą nadal obsługiwane do 2020 czerwca. Jednak wszystkie nieprawidłowe reguły klasycznego alertu zostaną usunięte, ponieważ są one niefunkcjonalne.
Wszystkie klasyczne reguły alertów monitorujące usunięte zasoby docelowe lub [metryki, które nie są już obsługiwane,](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) są uznawane za nieprawidłowe.
- Po rozpoczęciu migracji do subskrypcji, o ile nie występują żadne problemy, migracja powinna zostać zakończona w ciągu godziny. Klienci mogą monitorować stan migracji w [bloku migracja w Azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Właściciele subskrypcji otrzymają wiadomość e-mail po pomyślnym zakończeniu migracji.
- Jeśli podczas migracji wystąpią jakieś problemy, właściciele subskrypcji otrzymają także wiadomość e-mail z informacją o tym samym. Klienci mogą korzystać z bloku migracji, aby wyświetlić szczegółowe informacje o problemie.
- W przypadku gdy klient jest niezbędny do tymczasowego wyłączenia blokady zasobu lub zmiany przypisania zasad, klienci będą musieli rozwiązać wszelkie problemy do 31 października 2019. Jeśli problemy nie są rozwiązywane przez program, nie można zagwarantować pomyślnej migracji alertów klasycznych.

    > [!NOTE]
    > Jeśli nie chcesz czekać na uruchomienie procesu migracji automatycznej, można nadal wyzwolić migrację dobrowolnie przy użyciu narzędzia migracji.

## <a name="important-things-to-note"></a>Ważne rzeczy do zanotowania

Proces migracji umożliwia konwertowanie klasycznych reguł alertów na nowe, równoważne reguły alertów i tworzenie grup akcji. W przygotowaniu należy pamiętać o następujących kwestiach:

- Formaty ładunku powiadomień dla nowych reguł alertów różnią się od ustawień klasycznych reguł alertów, ponieważ obsługują one więcej funkcji. Jeśli masz aplikacje logiki, elementy Runbook lub webhook, które są wyzwalane przez klasyczną regułę alertów, mogą przestać działać zgodnie z oczekiwaniami, gdy migracja zostanie zakończona z powodu różnic w ładunku powiadomień. [Dowiedz się, jak przygotować się do migracji](alerts-prepare-migration.md).

- Niektórych klasycznych reguł alertów nie można migrować za pomocą narzędzia. [Dowiedz się, których reguł nie można migrować i co należy zrobić z nimi](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Proces migracji nie ma wpływu na ocenę klasycznych reguł alertów. Będą oni nadal uruchamiać i wysyłać alerty, dopóki nie zostaną zmigrowane i zaczną obowiązywać nowe reguły alertów.

## <a name="what-if-the-automatic-migration-fails"></a>Co zrobić, jeśli automatyczna migracja nie powiedzie się?

Gdy proces migracji automatycznej nie powiedzie się, właściciele subskrypcji otrzymają wiadomość e-mail z powiadomieniem o problemie. Możesz użyć bloku migracja w Azure Monitor, aby wyświetlić szczegółowe informacje o problemie.

Zobacz [Przewodnik rozwiązywania problemów](alerts-understand-migration.md#common-problems-and-remedies) , aby uzyskać pomoc dotyczącą problemów, które można napotkać podczas migracji.

  > [!NOTE]
  > W przypadku gdy klient jest niezbędny do tymczasowego wyłączenia blokady zasobu lub zmiany przypisania zasad, klienci będą musieli rozwiązać wszelkie problemy do 31 października 2019. Jeśli problemy nie są rozwiązywane przez program, nie można zagwarantować pomyślnej migracji alertów klasycznych.

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do migracji](alerts-prepare-migration.md)
- [Informacje o sposobie działania narzędzia do migracji](alerts-understand-migration.md)
