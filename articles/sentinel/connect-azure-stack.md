---
title: Dołączanie maszyn wirtualnych Azure Stack Hub do centrów danych platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób aprowizacji rozszerzenia maszyny wirtualnej Azure Monitor, aktualizacji i zarządzania konfiguracją na maszynach wirtualnych Azure Stack Hub i uruchamiania monitorowania ich przy użyciu centrów kontrolnych.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 9ac4aa9e93ac296913d7a62e83b182d673015bae
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655973"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Łączenie maszyn wirtualnych Azure Stack Hub z platformą Azure — wskaźnikiem




Za pomocą centrów danych platformy Azure możesz monitorować maszyny wirtualne działające na platformie Azure i w jednym miejscu Azure Stack centrum. Aby dołączyć maszyny Azure Stack do centrów danych platformy Azure, musisz najpierw dodać rozszerzenie maszyny wirtualnej do istniejących maszyn wirtualnych Azure Stack Hub. 

Po nawiązaniu połączenia z maszynami Azure Stack Hub wybierz z galerii pulpitów nawigacyjnych informacje na podstawie danych. Te pulpity nawigacyjne mogą być łatwo dostosowane do Twoich potrzeb.



## <a name="add-the-virtual-machine-extension"></a>Dodaj rozszerzenie maszyny wirtualnej 

Dodaj rozszerzenie maszyny wirtualnej **Azure monitor, aktualizacji i zarządzania konfiguracją** do maszyn wirtualnych działających w centrum Azure Stack. 

1. Na nowej karcie przeglądarki Zaloguj się do [portalu Azure Stack Hub](/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Przejdź do strony **maszyny wirtualne** , wybierz maszynę wirtualną, która ma być chroniona za pomocą platformy Azure. Aby uzyskać informacje na temat sposobu tworzenia maszyny wirtualnej w centrum Azure Stack, zobacz [Tworzenie maszyny wirtualnej z systemem Windows Server za pomocą centrum Azure Stack Hub](/azure-stack/user/azure-stack-quick-windows-portal) lub [Tworzenie maszyny wirtualnej serwera z systemem Linux przy użyciu portalu Centrum Azure Stack](/azure-stack/user/azure-stack-quick-linux-portal).
3. Wybierz pozycję **Rozszerzenia**. Zostanie wyświetlona lista rozszerzeń maszyn wirtualnych zainstalowanych na tej maszynie wirtualnej.
4. Kliknij kartę **Dodaj** . Zostanie otwarty blok menu **nowy zasób** i zostanie wyświetlona lista dostępnych rozszerzeń maszyn wirtualnych. 
5. Wybierz rozszerzenie **Azure monitor, aktualizacji i zarządzania konfiguracją** , a następnie kliknij przycisk **Utwórz**. Zostanie otwarte okno Konfiguracja **rozszerzenia instalacji** .

   ![Ustawienia Azure Monitor, aktualizacji i zarządzania konfiguracją](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Jeśli nie widzisz rozszerzenia **Azure monitor, aktualizacji i zarządzania konfiguracją** w portalu Marketplace, skontaktuj się z operatorem Azure Stack Hub, aby go udostępnić.

6. W menu wskaźnikowym platformy Azure wybierz opcję **Ustawienia obszaru roboczego** , a następnie **Zaawansowane** i skopiuj **Identyfikator obszaru** roboczego i **klucz obszaru roboczego (klucz podstawowy)**. 
1. W oknie Azure Stack **rozszerzenia instalacji** centrum wklej je w wskazanych polach i kliknij przycisk **OK**.
1. Po zakończeniu instalacji rozszerzenia jego stan wskazuje, że **Inicjowanie obsługi powiodło się**. Aby maszyna wirtualna mogła zostać wyświetlona w portalu wskaźnikowym platformy Azure, może upłynąć do godziny.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta dla systemu Windows, zobacz [łączenie komputerów z systemem Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Rozwiązywanie problemów z agentami w systemie Linux można znaleźć w temacie [Rozwiązywanie problemów z usługą Azure log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

W portalu wskaźnikowym platformy Azure na platformie Azure w obszarze **Virtual Machines** masz przegląd wszystkich maszyn wirtualnych i komputerów wraz z ich stanem. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy nie jest już potrzebne, można usunąć rozszerzenie z maszyny wirtualnej za pośrednictwem portalu Azure Stack Hub.

Aby usunąć rozszerzenie:

1. Otwórz **Portal Azure Stack Hub**.
2. Przejdź do strony **maszyny wirtualne** wybierz maszynę wirtualną, z której chcesz usunąć rozszerzenie.
3. Wybierz pozycję **rozszerzenia**, wybierz rozszerzenie **Microsoft. EnterpriseCloud. Monitoring**.
4. Kliknij przycisk **Odinstaluj** i potwierdź wybór.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- Przesyłaj strumieniowo dane ze [wspólnych formatów błędów](connect-common-event-format.md) do platformy Azure.