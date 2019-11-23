---
title: Rozwiązywanie problemów z netvsc. sys podczas zdalnego nawiązywania połączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 na platformie Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z protokołem RDP związanym z Netsvc. sys w przypadku nawiązywania połączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 na platformie Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057982"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nie można nawiązać zdalnego połączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 na platformie Azure z powodu netvsc. sys

W tym artykule wyjaśniono, jak rozwiązać problem polegający na tym, że podczas łączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 (VM) na hoście funkcji Hyper-V Server 2016 nie ma połączenia sieciowego.

## <a name="symptoms"></a>Objawy

Nie można nawiązać połączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 przy użyciu usługi Remote Desktop Protocol (RDP). W przypadku [diagnostyki rozruchu](boot-diagnostics.md)ekran przedstawia czerwoną skrzyżowanie za pośrednictwem karty interfejsu sieciowego (nic). Oznacza to, że maszyna wirtualna nie ma łączności po całkowitym załadowaniu systemu operacyjnego.

Zazwyczaj ten problem występuje w [kompilacjach](https://support.microsoft.com/help/4093120/) systemu Windows 14393 i [kompilacji 15063](https://support.microsoft.com/help/4015583/). Jeśli wersja systemu operacyjnego jest nowsza niż ta wersja, ten artykuł nie dotyczy Twojego scenariusza. Aby sprawdzić wersję systemu, Otwórz sesję CMD w [funkcji konsola dostępu szeregowego](serial-console-windows.md), a następnie uruchom polecenie **Ver**.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli wersja zainstalowanego pliku systemowego netvsc. sys to **10.0.14393.594** lub **10.0.15063.0**. Te wersje netvsc. sys mogą uniemożliwić współdziałanie systemu z platformą Azure.


## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy [wykonać migawkę dysku systemowego](../windows/snapshot-copy-managed-disk.md) , której dotyczy dana maszyna wirtualna, jako kopię zapasową. Aby rozwiązać ten problem, należy użyć konsoli szeregowej lub [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline) przez dołączenie dysku maszyny wirtualnej do maszyny Wirtualnej odzyskiwania.


### <a name="use-the-serial-console"></a>Korzystanie z konsoli szeregowej

Połącz się z [konsolą szeregową, Otwórz wystąpienie programu PowerShell](serial-console-windows.md), a następnie wykonaj poniższe kroki.

> [!NOTE]
> Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, przejdź do strony [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline) sekcji.

1. Uruchom następujące polecenie w wystąpieniu programu PowerShell, aby uzyskać wersję pliku (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Pobierz odpowiednią aktualizację do nowego lub istniejącego dysku z danymi, który jest dołączony do działającej maszyny wirtualnej z tego samego regionu:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) lub nowsza aktualizacja
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) lub nowsza

3. Odłącz dysk narzędzia z działającej maszyny wirtualnej, a następnie dołącz go do uszkodzonej maszyny wirtualnej.

4. Uruchom następujące polecenie, aby zainstalować aktualizację na maszynie wirtualnej:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Uruchom ponownie maszynę wirtualną.

### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb Offline

1. [Dołącz dysk systemu do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).

2. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.

3. Upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Należy pamiętać, literę dysku, która jest przypisana do dysku systemowego dołączone.

4. Utwórz kopię folderu **\Windows\System32\config** w przypadku konieczności wycofania zmian.

5. Na maszynie z systemem ratowniczym Uruchom Edytor rejestru (regedit. exe).

6. Wybierz klucz **HKEY_LOCAL_MACHINE** , a następnie wybierz pozycję **plik** > **Załaduj gałąź** z menu.

7. Zlokalizuj plik SYSTEMowy w folderze **\Windows\System32\config** .

8. Wybierz pozycję **Otwórz**, wpisz **BROKENSYSTEM** jako nazwę, rozwiń klucz **HKEY_LOCAL_MACHINE** , a następnie odszukaj dodatkowy klucz o nazwie **BROKENSYSTEM**.

9. Przejdź do następującej lokalizacji:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. W każdym podkluczu (np. 0000) Przejrzyj wartość **DriverDesc** , która jest wyświetlana jako **karta sieciowa Microsoft Hyper-V**.

11. W podkluczu należy przejrzeć wartość **DriverVersion** , która jest wersja sterownika karty sieciowej maszyny wirtualnej.

12. Pobierz odpowiednią aktualizację:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) lub nowsza aktualizacja
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) lub nowsza

13. Dołącz dysk systemowy jako dysk danych na maszynie wirtualnej, na której można pobrać aktualizację.

14. Uruchom następujące polecenie, aby zainstalować aktualizację na maszynie wirtualnej:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Uruchom następujące polecenie, aby odinstalować gałęzie:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Odłącz dysk systemowy i ponownie utwórz maszynę wirtualną](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać swój problem.
