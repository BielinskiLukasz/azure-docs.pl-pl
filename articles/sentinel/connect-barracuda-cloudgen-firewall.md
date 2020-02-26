---
title: Podłączanie zapory Barracuda CloudGen do usługi Azure wskaźnikowej | Microsoft Docs
description: Dowiedz się, jak połączyć zaporę Barracuda CloudGen z platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aaedbfdd3b1bbbc653756d74ee86fc277b21caec
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588505"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Łączenie z rozwiązaniem Barracuda CloudGen Firewall

Łącznik Barracuda CloudGen Firewall (CGFW) umożliwia łatwe łączenie dzienników Barracuda CGFW z platformą Azure, aby wyświetlać pulpity nawigacyjne, tworzyć niestandardowe alerty i usprawnić badanie. Zapewnia to dokładniejszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń.




## <a name="prerequisites"></a>Wymagania wstępne

- Uprawnienia do odczytu i zapisu do obszaru roboczego wskaźnikowego platformy Azure.

- Zapora CloudGen Barracuda musi być skonfigurowana do eksportowania dzienników za pośrednictwem dziennika systemowego.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Łączenie z platformą Azure do Barracuda CloudGen firewall

1. W Azure Portal przejdź do **łącznika danych** **platformy Azure** , > , a następnie wybierz łącznik **zapory Barracuda CloudGen** .

2. Wybierz pozycję **Otwórz stronę łącznika**.

3. Postępuj zgodnie z instrukcjami na stronie **zapory Barracuda CloudGen** .


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia zapory Barracuda CloudGen z usługą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


