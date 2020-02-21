---
title: Łączenie Azure Security Center IoT z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć Azure Security Center danych IoT z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: rkarlin
ms.openlocfilehash: b68745f04a49565090b0f9b14331036f76e5f132
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501292"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Łączenie danych z Azure Security Center dla IoT z platformą Azure — wskaźnikiem 


> [!IMPORTANT]
> Azure Security Center dla łącznika danych IoT jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Skorzystaj z łącznika Azure Security Center for IoT, aby przesłać strumieniowo wszystkie Azure Security Center zdarzeń IoT do platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Uprawnienia do **odczytu** i **zapisu** w obszarze roboczym, na którym jest wdrażany wskaźnik na platformie Azure
- **Azure Security Center usługi IoT** muszą być **włączone** na odpowiednich IoT Hub
- Uprawnienia do **odczytu** i **zapisu** na **IoT Hub platformy Azure** , które chcesz połączyć
- Uprawnienia do **odczytu** i **zapisu** w **grupie zasobów usługi Azure IoT Hub**

> [!NOTE]
> Należy włączyć Azure Security Center licencji warstwy **standardowej** w ramach subskrypcji, aby przesyłać strumieniowo alerty zasobów IoT do platformy Azure, musisz włączyć tylko licencję **na Azure Security Centerą** w ramach tej subskrypcji, aby wyświetlić Azure Security Center alertów IoT na platformie Azure. 

## <a name="connect-to-azure-security-center-for-iot"></a>Nawiązywanie połączenia z usługą Azure Security Center dla IoT

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure Security Center for IoT** .
1. W prawym dolnym okienku kliknij pozycję **Otwórz stronę łącznika**. 
1. Kliknij przycisk **Połącz**obok każdej IoT Hub subskrypcji, której alerty i alerty urządzeń chcesz przesłać do usługi Azure wskaźnikowej. 
    - Jeśli Azure Security Center dla usługi IoT nie jest włączona w tym centrum, zobaczysz komunikat z ostrzeżeniem o **włączeniu** . Kliknij link **Włącz** , aby uruchomić usługę. 
1. Możesz zdecydować, czy alerty od Azure Security Center dla IoT mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń**wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną w celu automatycznego tworzenia zdarzeń z alertów generowanych w połączonej usłudze zabezpieczeń. Tę regułę można zmienić lub edytować w obszarze **analiza** > **aktywnych** reguł.

> [!NOTE]
> Odświeżanie listy centrów może potrwać trochę czasu po wprowadzeniu zmian w połączeniu. 

## <a name="log-analytics-alert-display"></a>Wyświetlanie alertów Log Analytics

Aby użyć odpowiedniego schematu w Log Analytics, aby wyświetlić Azure Security Center alertów IoT:

1. Otwórz **dzienniki** > **SecurityInsights** > **SecurityAlert**lub wyszukaj ciąg **SecurityAlert**. 
2. Filtruj, aby wyświetlić tylko Azure Security Center dla alertów generowanych przez IoT przy użyciu następującego filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Uwagi dotyczące usługi

Po nawiązaniu połączenia IoT Hub dane centrum są dostępne na platformie Azure — około 15 minut później.


## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia Azure Security Center danych IoT z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
