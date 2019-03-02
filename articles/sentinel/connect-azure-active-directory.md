---
title: Zbieranie danych usługi Azure AD w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane z usługi Azure Active Directory w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: 315b18feb74862bbeca6ff8265ee003fbad48595
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242319"
---
# <a name="collect-data-from-azure-active-directory"></a>Zbieranie danych z usługi Azure Active Directory

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wartownik platformy Azure umożliwia zbieranie danych z [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesłać go strumieniowo do usługi Azure przez wartownika. Możesz wybrać do strumienia [dzienniki logowania](../active-directory/reports-monitoring/concept-sign-ins.md) i [dzienniki inspekcji](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz wyeksportować dane logowania z usługi Active Directory, musi mieć licencję usługi Azure AD P1 lub P2.

- Użytkownik z globalnego administratora lub zabezpieczeń uprawnienia administratora dla dzierżawy ma być przesyłana strumieniowo dzienniki z.


## <a name="connect-to-azure-ad"></a>Łączenie z usługą Azure AD

1. Na platformie Azure przez wartownika, wybierz **zbierania danych** a następnie kliknij przycisk **usługi Azure Active Directory** kafelka.

2. Obok dzienniki mają być przesyłane strumieniowo do usługi Azure przez wartownika, kliknij **Connect**.






## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia usługi Azure AD na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
