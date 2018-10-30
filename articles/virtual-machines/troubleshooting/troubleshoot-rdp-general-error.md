---
title: Rozwiązywanie problemów z błędem Ogólne RDP maszyny Wirtualnej z systemem Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z błędem Ogólne RDP maszyny Wirtualnej z systemem Windows na platformie Azure | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 701373efa3c3c22eb5969705927e1c0cc6e3f36b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215803"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Rozwiązywanie problemów z błędu ogólnego protokołu RDP w maszynie Wirtualnej platformy Azure

W tym artykule opisano ogólny błąd, które mogą wystąpić po nawiązaniu połączenia protokołu RDP (Remote Desktop) do Windows maszyn wirtualnych (VM) na platformie Azure.

## <a name="symptoms"></a>Objawy

Po wprowadzeniu z połączeniem RDP z maszyną wirtualną okna, na platformie Azure może być wyświetlany następujący komunikat ogólny błąd:

**Pulpit zdalny nie może połączyć się z komputerem zdalnym dla jednego z następujących powodów:**

1. **Zdalny dostęp do serwera nie jest włączony.**

2. **Komputer zdalny jest wyłączona.**

3. **Komputer zdalny nie jest dostępny w sieci**

**Upewnij się, komputer zdalny jest włączone i połączone z siecią i włączenie dostępu zdalnego.**

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić z powodu następujących przyczyn:

### <a name="cause-1"></a>Przyczyny 1

Składnik protokołu RDP jest wyłączona w następujący sposób:

- Na poziomie składnika
- Na poziomie odbiornika
- Na serwerze terminali
- W roli hosta sesji usług pulpitu zdalnego

### <a name="cause-2"></a>Przyczyny 2

Usługi pulpitu zdalnego (TermService) nie jest uruchomiony.

### <a name="cause-3"></a>Przyczyny 3

Odbiornik protokołu RDP jest nieprawidłowo skonfigurowana.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Utwórz kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md), i [Dołącz dysk systemu operacyjnego do ratownictwa maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md), a następnie postępuj zgodnie z opcjami rozwiązania lub wypróbuj rozwiązania jedno po drugim.

### <a name="serial-console"></a>Konsola szeregowa

#### <a name="step-1-turn-on-remote-desk"></a>Krok 1: Włączanie zdalnego technicznej

1. Dostęp do [konsoli szeregowej](serial-console-windows.md) , wybierając **pomoc techniczna i rozwiązywanie problemów** > **Konsola szeregowa (wersja zapoznawcza)**. Jeśli ta funkcja jest włączona na maszynie Wirtualnej, możesz połączyć maszynę Wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Typ **CMD** uruchomić kanału można pobrać nazwy kanału.

3. Przełącz się do kanału, na którym uruchomiono wystąpienie CMD, w tym przypadku powinna być kanał 1.

   ```
   ch -si 1
   ```

4. Włączanie pulpitu zdalnego za pomocą zasad obiektu zasad grupy, zmieniając następujące zasady:

   ```
   Computer Configuration\Policies\Administrative Templates: Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
   ```

5. Niektóre inne klucze rejestru, które może spowodować, że ten problem, sprawdź wartości kluczy rejestru w następujący sposób:

   1. Upewnij się, że włączono składników protokołu RDP.

      ```
      REM Get the local policy
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      Jeśli zasady domeny istnieje, zostanie zastąpiony ustawień zasad lokalnych.

         - Jeśli zasady domeny stanów, że protokołu RDP jest wyłączona (1), a następnie aktualizacja zasad AD.
         - Jeśli zasady domeny stwierdza, że protokół RDP jest włączony (0), aktualizacja nie jest potrzebna.

      Jeśli zasady domeny nie istnieje, a zasady lokalne stany wyłączenia protokołu RDP (1), Włącz protokół RDP przy użyciu następującego polecenia:

         ```
         reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
         ```

   2. Sprawdź bieżącą konfigurację serwera terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

   3. Jeśli polecenie zwróci wartość 0, serwer terminali jest wyłączone. Następnie należy włączyć serwera terminali w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

   4. Moduł serwera terminali ustawiono trybu opróżniania, jeśli serwer znajduje się w farmie serwerów terminali (usług pulpitu zdalnego lub Citrix). Sprawdź, czy bieżący tryb modułu serwera terminali

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

   5. Jeśli polecenie zwróci 1, moduł serwera terminali ustawiono trybu opróżniania. Następnie ustaw moduł do trybu pracy w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

   6. Sprawdź, czy użytkownik może łączyć się z serwera terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

   7. Polecenie zwraca wartość 1, nie można nawiązać serwera terminali. Następnie Włącz połączenie w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```

   8. Sprawdź bieżącą konfigurację odbiornika protokołu RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

   9. Jeśli polecenie zwróci wartość 0, odbiornik protokołu RDP jest wyłączone. Następnie Włącz odbiornik w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

   10. Sprawdź, czy możesz nawiązać połączenie odbiornik protokołu RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   11. Polecenie zwraca wartość 1, nie można nawiązać odbiornik protokołu RDP. Następnie Włącz połączenie w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

6. Uruchom ponownie maszynę wirtualną.

7. Wyjdź z wystąpienia CMD, wpisując `exit`, a następnie naciśnij klawisz **Enter** dwa razy.

8. Uruchom ponownie maszynę Wirtualną, wpisując `restart`.

Jeśli problem nadal występuje, przejdź do kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2. Włączanie usług pulpitu zdalnego

Aby uzyskać więcej informacji, zobacz [usług pulpitu zdalnego nie jest uruchamiania na Maszynie wirtualnej platformy Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3. zresetowanie odbiornik protokołu RDP

Aby uzyskać więcej informacji, zobacz [pulpitu zdalnego rozłączy się często w maszynie Wirtualnej platformy Azure](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Naprawy w trybie offline

#### <a name="step-1-turn-on-remote-desk"></a>Krok 1: Włączanie zdalnego technicznej

> [!NOTE]  
> Przyjęto założenie, że litery dysku, która jest przypisana do dołączonym dysku systemu operacyjnego jest Zamień F. ją z odpowiednią wartość w maszynie Wirtualnej. Gałęzie systemu i oprogramowania muszą być odinstalować, a następnie zainstalowany.

1. Otwórz wystąpienie CMD z podwyższonym poziomem uprawnień i uruchom następujące skrypty na ratownictwa maszyny Wirtualnej:

   ```
   reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM.hiv
   reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE.hiv

   REM Ensure that Terminal Server is enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f

   REM Ensure Terminal Service is not set to Drain mode
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f

   REM Ensure Terminal Service has logon enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f

   REM Ensure the RDP Listener is not disabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f

   REM Ensure the RDP Listener accepts logons
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f

   REM RDP component is enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f

   reg unload HKLM\BROKENSYSTEM
   reg unload HKLM\BROKENSOFTWARE
   ```

2. Zainstaluj gałęzi systemu i oprogramowania.

   ```
   reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
   reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE
   ```

3. Jeśli maszyna wirtualna jest przyłączona do domeny, protokołu RDP można wyłączyć na poziomie zasad. Aby sprawdzić, czy tak jest, sprawdź następujący klucz rejestru:

   ```
   HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
   ```

4. Jeśli ta wartość klucza jest równa 1, protokołu RDP jest wyłączone przez zasady.

5. Włączanie pulpitu zdalnego za pomocą zasad obiektu zasad grupy, zmieniając następujące zasady:

   ```
   Computer Configuration\Policies\Administrative Templates: Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
   ```

6. Jeśli ten klucz rejestru nie istnieje, sprawdź następujący klucz rejestru:

   ```
   HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections
   ```

7. Jeśli ten klucz jest ustawiona na 1, Włącz jest protokołu RDP jest wyłączona. Zmień wartość klucza na 0.
8. Odłącz dysk od ratownictwa maszyny Wirtualnej.
9. [Tworzenie nowej maszyny Wirtualnej na podstawie dysku](../windows/create-vm-specialized.md).

Jeśli problem nadal występuje, przejdź do kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2. Włączanie usług pulpitu zdalnego

Aby uzyskać więcej informacji, zobacz [usług pulpitu zdalnego nie jest uruchamiania na Maszynie wirtualnej platformy Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3. zresetowanie odbiornik protokołu RDP

Aby uzyskać więcej informacji, zobacz [pulpitu zdalnego rozłączy się często w maszynie Wirtualnej platformy Azure](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
