---
title: Grupowanie maszyn za pomocą zależności maszyn za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia oceny za pomocą zależności maszyn za pomocą usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/21/2018
ms.author: raynew
ms.openlocfilehash: 2755cc4e8e0e5a1b2a0e491b00fc73530dd9b958
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635683"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Grupowanie maszyn za pomocą mapowania zależności maszyn

W tym artykule opisano sposób tworzenia grupy maszyn na potrzeby [usługi Azure Migrate](migrate-overview.md) oceny przez wizualizację zależności maszyn. Ta metoda jest zazwyczaj używana, gdy chcesz ocenić grupy maszyn wirtualnych z wyższego poziomu zaufania, krzyżowe sprawdzanie zależności maszyny, przed uruchomieniem oceny. Wizualizacja zależności mogą pomóc w efektywnego planowania migracji do platformy Azure. Ułatwia on upewnij się, że nic nie pozostawione awarii dziwnego, że nie występują podczas migracji na platformę Azure. Może odnajdywać wszystkie współzależne systemy, które trzeba migrowane razem i ustalić, czy na komputerze z uruchomionym systemem nadal działa jako użytkownicy lub kwalifikuje się do likwidacji zamiast migracji.


## <a name="prepare-for-dependency-visualization"></a>Przygotowanie do wizualizacji zależności
Usługa Azure Migrate korzysta z rozwiązania mapy usługi w usłudze Log Analytics, aby umożliwić wizualizacja zależności maszyn.

### <a name="associate-a-log-analytics-workspace"></a>Skojarzyć obszar roboczy usługi Log Analytics
Aby korzystać z wizualizacji zależności, należy skojarzyć obszar roboczy usługi Log Analytics, nowej lub istniejącej z projektem migracji platformy Azure. Możesz tworzyć lub dołączyć obszaru roboczego w tej samej subskrypcji, w której jest tworzony projekt migracji.

- Aby dołączyć obszar roboczy usługi Log Analytics do projektu, w **Przegląd**, przejdź do **Essentials** części projektu, kliknij przycisk **wymaga konfiguracji**

    ![Skojarzyć obszar roboczy usługi Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Gdy tworzysz nowy obszar roboczy, musisz określić nazwę obszaru roboczego. Obszar roboczy zostanie utworzony w tej samej subskrypcji co projekt migracji i region, w tym samym [lokalizacja geograficzna platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migracji.
- **Użyj istniejącej** opcji wyświetla tylko te obszary robocze, które są tworzone w regionach, w których jest dostępna usługa Service Map. Jeśli masz obszar roboczy w regionie, w których nie jest dostępna usługa Service Map, nie będzie ono wyświetlane w polu listy rozwijanej.

> [!NOTE]
> Nie można zmienić obszar roboczy skojarzony z projektem migracji.

### <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej
Po skonfigurowaniu obszar roboczy, musisz pobrać i zainstalować agentów na każdym komputerze w środowisku lokalnym, który ma zostać oceniona. Ponadto w przypadku maszyn bez łączności internetowej musisz pobrać i zainstalować [bramy usługi Log Analytics](../azure-monitor/platform/gateway.md) na nich.

1. W **Przegląd**, kliknij przycisk **Zarządzaj** > **maszyn**i wybierz maszynę wymagane.
2. W **zależności** kolumny, kliknij przycisk **agenci**.
3. Na **zależności** strony, Pobierz i zainstaluj program Microsoft Monitoring Agent (MMA) i agenta zależności na każdej maszynie Wirtualnej, które ma zostać oceniona.
4. Skopiuj identyfikator i klucz obszaru roboczego. Będą one potrzebne po zainstalowaniu programu MMA na maszynie lokalnej.

> [!NOTE]
> Można zautomatyzować instalację agentów można użyć dowolnego narzędzia wdrażania, takich jak System Center Configuration Manager lub użyć naszego narzędzia do partnera [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), która ma agenta wdrażania rozwiązania dla usługi Azure Migrate.

### <a name="install-the-mma"></a>Instalowanie programu MMA

Aby zainstalować agenta na komputerze Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
3. W **Folder docelowy**, zachować lub zmienić domyślny folder instalacji > **dalej**.
4. W **opcje instalacji agenta**, wybierz opcję **usługi Azure Log Analytics** > **dalej**.
5. Kliknij przycisk **Dodaj** Aby dodać nowy obszar roboczy usługi Log Analytics. Wklej identyfikator obszaru roboczego i klucz, który został skopiowany z portalu. Kliknij przycisk **Dalej**.

[Dowiedz się więcej](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) dotyczące listy obsługiwane systemy operacyjne Windows przez MMA.

Aby zainstalować agenta na maszynie z systemem Linux:

1. Przenieś odpowiedni zbiór (x86 lub x64), aby komputer z systemem Linux przy użyciu protokołu scp/sftp.
2. Zainstaluj zbiór przy użyciu argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) dotyczące listy obsługiwane systemy operacyjne Linux przez MMA.

### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności
1. Aby zainstalować agenta zależności na komputerze Windows, kliknij dwukrotnie plik instalacyjny i wykonaj instrukcje kreatora.
2. Aby zainstalować agenta zależności na maszynę z systemem Linux, zainstaluj jako użytkownik główny, używając następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

Dowiedz się więcej o obsłudze agenta zależności [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) i [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) systemów operacyjnych.

[Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) dotyczące wykorzystania skryptów do zainstalowania agenta zależności.

## <a name="create-a-group"></a>Tworzenie grupy

1. Po zainstalowaniu agentów, przejdź do portalu i kliknij przycisk **Zarządzaj** > **maszyn**.
2. Wyszukaj maszyny, na którym zainstalowano agentów.
3. **Zależności** kolumny maszyny powinny być teraz wyświetlane jako **wyświetlanie zależności**. Kliknij kolumnę, aby wyświetlić zależności maszyny.
4. Mapa zależności maszyny zawiera następujące informacje:
    - Dla ruchu przychodzącego (klientów) oraz połączenia wychodzące TCP (serwery) do/z maszyny
        - Zależnych maszyn, które nie mają zainstalowany agent MMA i zależności są pogrupowane według numerów portów
        - Zależnych maszyn, które mają programu MMA i zainstalować agenta zależności są wyświetlane jako oddzielne pola
    - Procesy uruchomione na maszynie, możesz rozwinąć każde pole maszyny do wyświetlania procesów
    - Właściwości, takie jak w pełni kwalifikowanej nazwy domeny, System operacyjny i każdej maszyny, itp. adres MAC, możesz kliknąć pole każdej maszyny w taki sposób, aby wyświetlić te szczegóły

 ![Wyświetlanie zależności maszyn](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Zapoznanie się z zależnościami dla różnych czasów trwania, klikając na czas trwania w Etykieta zakresu czasu. Domyślnie zakres jest godzinę. Możesz modyfikować zakres czasu lub określ rozpoczęcia i zakończenia daty i czasu trwania.
5. Po zidentyfikowaniu zależnych maszyn, które chcesz zgrupować, za pomocą kombinacji Ctrl + kliknięcie wybrać wiele maszyn na mapie, a następnie kliknij przycisk **grupowanie maszyn**.
6. Określ nazwę grupy. Upewnij się, że zależnych maszyn są wykrywane przez usługę Azure Migrate.

    > [!NOTE]
    > Jeśli maszynę zależny nie został odnaleziony przez usługę Azure Migrate, nie można dodać go do grupy. Aby dodać takich maszyn do grupy, należy ponownie uruchomić proces wykrywania z zakresem prawej w programie vCenter Server i upewnij się, że komputer został odnaleziony przez usługę Azure Migrate.  

7. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij przycisk **OK** można zapisać grupy.

Po utworzeniu grupy zaleca się instalowanie agentów na wszystkich komputerach w grupie i ulepszyć grupę przez wizualizację zależności w całej grupy.

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) temat — często zadawane pytania na wizualizacji zależności.
- [Dowiedz się, jak](how-to-create-group-dependencies.md) uściślić grupy przez wizualizację zależności między grupami.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
