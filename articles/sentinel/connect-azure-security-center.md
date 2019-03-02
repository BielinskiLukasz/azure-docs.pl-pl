---
title: Zbieranie danych usługi Azure Security Center w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane z usługi Azure Security Center w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f59c5f6a9f497a6420172996f9f327f16ffd26f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242278"
---
# <a name="collect-data-from-azure-security-center"></a>Zbieranie danych z usługi Azure Security Center

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Wartownik platformy Azure umożliwia zbieranie alertów z [usługi Azure Security Center](../security-center/security-center-intro.md) i przekazywać je strumieniowo do usługi Azure przez wartownika. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz wyeksportować alertów z usługi Azure Security Center, musi być współautorem subskrypcji, dla których dzienniki przesyłania strumieniowego.

- Konieczne jest posiadanie [Azure Security Center w warstwie standardowa](../security-center/security-center-pricing.md) uruchomiony w ramach subskrypcji. W przeciwnym razie [Uaktualnij swoją subskrypcję na warstwę standardowa](https://azure.microsoft.com/pricing/details/security-center/).

- Należy zalogować się jako użytkownik, który ma administratora globalnego lub uprawnienia administratora zabezpieczeń w każdej subskrypcji, którą chcesz się połączyć.


## <a name="connect-to-azure-security-center"></a>Łączenie z usługą Azure Security Center

1. Na platformie Azure przez wartownika, wybierz **zbierania danych** a następnie kliknij przycisk **usługi Azure Security Center** kafelka.
1. Po prawej stronie, kliknij **Connect** obok każdej subskrypcji alertów, którego chcesz strumieniowo przesyłać do platformy Azure przez wartownika. Upewnij się, że uaktualnienie każdej subskrypcji do warstwy standardowej usługi Azure Security Center do strumienia alertów do platformy Azure przez wartownika.

3. Kliknij przycisk **Połącz**.

4. Aby użyć odpowiednich schematu w usłudze Log Analytics dla alertów w usłudze Azure Security Center, możesz wyszukać **SecurityEvent**.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia usługi Azure Security Center na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
