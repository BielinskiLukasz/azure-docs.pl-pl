---
title: Zarządzanie serwer przetwarzania na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano zarządzanie serwerem proces konfigurowania odzyskiwania po awarii maszyn wirtualnych VMware i serwera fizycznego na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: ba80c8ce57495eaa46e915cb0c472eb4aabcee57
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863631"
---
# <a name="manage-process-servers"></a>Zarządzanie serwerami przetwarzania

Domyślnie serwer przetwarzania używany w przypadku replikacji maszyn wirtualnych VMware lub serwery fizyczne do platformy Azure jest instalowany na komputerze z serwerem konfiguracji w środowisku lokalnym. Istnieje kilka wystąpień, w których należy skonfigurować oddzielnego serwera przetwarzania:

- W przypadku dużych wdrożeń możesz potrzebować dodatkowych lokalnych serwerów przetwarzania skalować wydajność.
- Powrót po awarii, należy to tymczasowy serwer przetwarzania na platformie Azure. Po zakończeniu powrotu po awarii, można usunąć tej maszyny Wirtualnej. 

Ten artykuł zawiera podsumowanie typowych zadań zarządzania dla tych dodatkowych serwerów przetwarzania.

## <a name="upgrade-a-process-server"></a>Uaktualnij serwer przetwarzania

Uaktualnij serwer przetwarzania, uruchomiony w środowisku lokalnym lub na platformie Azure (na potrzeby powrotu po awarii) w następujący sposób:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
>   Zazwyczaj gdy używasz obrazu z galerii platformy Azure do utworzenia serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii jest uruchomiona najnowsza dostępna wersja. Usługa Site Recovery zespołów wersji poprawki i ulepszenia w regularnych odstępach czasu i zalecamy serwerów przetwarzania zapewnianie aktualności.

## <a name="balance-the-load-on-process-server"></a>Równoważenie obciążenia na serwerze przetwarzania

Aby równoważyć obciążenie między dwoma serwerami procesu

1. Przejdź do **magazyn usług Recovery Services** > **zarządzanie** > **infrastruktura usługi Site Recovery** > **dla Maszyn VMware i fizycznych** > **serwery konfiguracji**.
2. Kliknij na serwerze konfiguracji, do którego zarejestrowano serwerów przetwarzania.
3. Lista serwerów procesu zarejestrowanych serwerów configuration są dostępne na stronie.
4. Kliknij na serwerze przetwarzania, na którym chcesz zmodyfikować obciążenia.

    ![DSM](media/vmware-azure-manage-process-server/LoadBalance.png)

5. Można użyć **Równoważenie obciążenia** lub **przełącznika** opcji, co zostało opisane poniżej, dla każdego wymagania.

### <a name="load-balance"></a>Równoważenie obciążenia

Za pomocą tej opcji można wybrać co najmniej jednej maszyny wirtualnej i można je przenieść na inny serwer przetwarzania.

1. Kliknij pozycję **Równoważenie obciążenia**, wybierz docelowy serwer przetwarzania z listy rozwijanej. Kliknij przycisk **OK**.

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Kliknij pozycję **wybierz maszyny**, wybierz maszyny wirtualne, które chcesz przenieść z bieżącego serwera przetwarzania do docelowego serwera przetwarzania. Szczegółowe informacje o zmianie uśrednianie danych są wyświetlane na każdej maszynie wirtualnej.
3. Kliknij przycisk **OK**. Monitorowanie postępu zadania w obszarze **magazyn usługi Recovery Services** > **monitorowanie** > **zadania usługi Site Recovery**.
4. W ciągu 15 minut zmieniony, aby odzwierciedlić po pomyślnym zakończeniu tej operacji lub [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) efekt natychmiastowy.

### <a name="switch"></a>Przełącznik

Za pomocą tej opcji całe obciążenie chroniony w obszarze serwer przetwarzania jest przenoszona do innego serwera przetwarzania.

1. Kliknij pozycję **przełącznika**wybierz docelowy serwer przetwarzania, kliknij przycisk **OK**.

    ![Przełącznik](media/vmware-azure-manage-process-server/Switch.PNG)

2. Monitorowanie postępu zadania w obszarze **magazyn usługi Recovery Services** > **monitorowanie** > **zadania usługi Site Recovery**.
3. W ciągu 15 minut zmieniony, aby odzwierciedlić po pomyślnym zakończeniu tej operacji lub [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server) efekt natychmiastowy.

## <a name="reregister-a-process-server"></a>Zarejestruj ponownie serwer przetwarzania

Jeśli chcesz ponownie zarejestrować serwer przetwarzania, działającego lokalnie lub na platformie Azure z serwerem konfiguracji, wykonaj następujące czynności:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po zapisaniu ustawień, wykonaj następujące czynności:

1. Na serwerze przetwarzania Otwórz wiersz polecenia administratora.
2. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**, a następnie uruchom polecenie:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modyfikowanie ustawień serwera proxy dla serwera przetwarzania w środowisku lokalnym

Jeśli serwer przetwarzania używa serwera proxy do łączenia z usługą Site Recovery na platformie Azure, użyj tej procedury, jeśli trzeba zmodyfikować istniejących ustawień serwera proxy.

1. Zaloguj się na komputerze serwera przetwarzania. 
2. Otwórz okno poleceń programu PowerShell i uruchom następujące polecenie:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**, i uruchom następujące polecenie:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```


## <a name="remove-a-process-server"></a>Usuń serwer przetwarzania

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>Zarządzanie oprogramowania antywirusowego na serwerze przetwarzania

Jeśli oprogramowanie antywirusowe jest aktywna na autonomicznym serwerze przetwarzania lub główny serwer docelowy, należy wyłączyć następujące foldery z oprogramowania antywirusowego operacje:


- Agent usług C:\Program Files\Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Katalog instalacyjny serwera przetwarzania, przykład: C:\Program Files (x86) \Microsoft Azure Site Recovery

