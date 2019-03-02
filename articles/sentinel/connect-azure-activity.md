---
title: Zbieraj dane o aktywności platformy Azure w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane o aktywności platformy Azure w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 42ce8d06489cfacf7ba9bb3de1425224b5df95e7
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240579"
---
# <a name="collect-data-from-azure-activity-log"></a>Zbieranie danych z dziennika aktywności platformy Azure

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można przesyłać strumieniowo dzienniki z [dziennik aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md) do platformy Azure przez wartownika za pomocą jednego kliknięcia. Dziennik aktywności jest Dziennik subskrypcji, która zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. W tym zakresie danych z usługi Azure Resource Manager danych operacyjnych do aktualizacji dla zdarzeń kondycji usługi. Przy użyciu dziennika aktywności, można określić "co, kto i kiedy" dla wszelkie zapisu operacji (PUT, POST, DELETE) wykonywanych na zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości. Dziennik aktywności nie obejmują operacji odczytu (GET) ani operacji dotyczących zasobów, które używają klasycznego / modelu "RDFE". 


## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z administratorem globalnym lub uprawnienia administratora zabezpieczeń


## <a name="connect-to-azure-activity-log"></a>Łączenie z dziennikiem aktywności platformy Azure

1. Na platformie Azure przez wartownika, wybierz **zbierania danych** a następnie kliknij przycisk **dziennik aktywności platformy Azure** kafelka.

2. W okienku dziennika aktywności platformy Azure Wybierz subskrypcje, które mają być przesyłane strumieniowo do usługi Azure przez wartownika. 

3. Kliknij przycisk **Połącz**.

4. Aby użyć odpowiednich schematu w usłudze Log Analytics dla alertów aktywności platformy Azure, możesz wyszukać **AzureActivity**.


 

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia dziennika aktywności platformy Azure do platformy Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
