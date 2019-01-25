---
title: Nie znaleziono żadnych subskrypcji błąd - portalu logowania do platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera rozwiązanie problemu, w którym subskrypcji nie można odnaleźć wystąpienia błędu podczas Zaloguj się do witryny Azure portal lub Centrum konta platformy Azure.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: c6a2f14900d3a0d6f9e16e9b0c6d0bdfff97d6b5
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903840"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Nie znaleziono żadnych subskrypcji logowanie w wyniku błędu dla witryny Azure portal lub Centrum konta platformy Azure

Można otrzymać komunikat o błędzie "Nie znaleziono żadnych subskrypcji", gdy próbuje zalogować się do [witryny Azure portal](https://portal.azure.com/) lub [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions). Ten artykuł zawiera rozwiązanie tego problemu.

## <a name="symptom"></a>Objaw

Podczas logowania się w [witryny Azure portal](https://portal.azure.com/) lub [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions), pojawi się następujący komunikat o błędzie: "Nie można odnaleźć subskrypcji".

## <a name="cause"></a>Przyczyna

Ten problem występuje w przypadku wybrania niewłaściwego katalogu, czy Twoje konto nie ma wystarczających uprawnień. 

## <a name="solution"></a>Rozwiązanie

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scenariusz 1: Odbiera komunikat o błędzie [witryny Azure portal](https://portal.azure.com)

Aby rozwiązać ten problem:

* Upewnij się, że wybrano prawidłowy katalog usługi Azure, klikając swoje konto w prawym górnym rogu.

  ![Wybierz katalog, w prawym górnym rogu witryny Azure portal](./media/billing-no-subscriptions-found/directory-switch.png)
* Jeśli wybrano katalogu bezpośrednio do platformy Azure, ale nadal otrzymywać komunikat o błędzie [przypisanie roli właściciela do swojego konta](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scenariusz 2: Odbiera komunikat o błędzie [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions)

Sprawdź, czy konta, którego użyto jest administratora konta. Aby sprawdzić, kim jest Administrator konta, wykonaj następujące kroki:

1. Zaloguj się do [subskrypcje wyświetlić w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, aby sprawdzić, a następnie sprawdź w obszarze **ustawienia**.
1. Wybierz **właściwości**. Administrator konta subskrypcji są wyświetlane w **administrator konta** pole.  

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
