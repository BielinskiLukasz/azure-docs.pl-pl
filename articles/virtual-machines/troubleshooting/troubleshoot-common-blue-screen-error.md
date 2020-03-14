---
title: Błędy niebieskiego ekranu podczas uruchamiania maszyny wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z komunikatem o błędzie niebieskiego ekranu podczas rozruchu | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: beb1562738699bbcede58d8214e69342abbb7c93
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266938"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>System Windows wyświetla błąd niebieskiego ekranu podczas uruchamiania maszyny wirtualnej platformy Azure
W tym artykule opisano błędy niebieskiego ekranu, które mogą wystąpić podczas uruchamiania maszyny wirtualnej z systemem Windows w Microsoft Azure. Zawiera instrukcje ułatwiające zbieranie danych dla biletu pomocy technicznej. 


## <a name="symptom"></a>Objaw 

Nie uruchomiono maszyny wirtualnej z systemem Windows. Po sprawdzeniu zrzutów ekranu rozruchowego w ramach [diagnostyki rozruchu](./boot-diagnostics.md)zobaczysz jeden z następujących komunikatów o błędach na niebieskim ekranie:

- Wystąpił problem z naszym komputerem i wymaga ponownego uruchomienia. Właśnie zbieramy pewne informacje o błędzie, a następnie można uruchomić ponownie.
- KOMPUTER napotkał problem i wymaga ponownego uruchomienia.

W tej sekcji wymieniono typowe komunikaty o błędach, które można napotkać podczas zarządzania maszynami wirtualnymi:

## <a name="cause"></a>Przyczyna

Może istnieć wiele powodów, dla których wystąpił błąd zatrzymania. Najczęstszymi przyczynami są:

- Problem z sterownikiem
- Uszkodzony plik lub pamięć systemowa
- Aplikacja uzyskuje dostęp do niedozwolonego sektora pamięci

## <a name="collect-memory-dump-file"></a>Zbierz plik zrzutu pamięci

Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu dla awarii i skontaktować się z pomocą techniczną pliku zrzutu. Aby zebrać plik zrzutu, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania

1. Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).
2. [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md). 
3. Pulpit zdalny do maszyny wirtualnej odzyskiwania.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1. Na maszynie wirtualnej odzyskiwania przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera sterownika, która jest przypisana do dołączonego dysku systemu operacyjnego, to F, należy przejść do F:\Windows.
2. Zlokalizuj plik Memory. dmp, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu. 

Jeśli nie możesz znaleźć pliku zrzutu, przejdź do następnego kroku, aby włączyć dziennik zrzutów i konsolę seryjną.

### <a name="enable-dump-log-and-serial-console"></a>Włącz dziennik zrzutu i konsoli szeregowej

Aby włączyć dziennik zrzutu i konsoli szeregowej, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujący skrypt:

    W tym skrypcie Załóżmy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F.  Zastąp ją odpowiednią wartością na maszynie wirtualnej.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Upewnij się, że na dysku jest wystarczająca ilość miejsca, aby przydzielić pamięć jako pamięć RAM, która zależy od rozmiaru wybieranego dla tej maszyny wirtualnej.
    2. Jeśli nie ma wystarczającej ilości miejsca lub jest to maszyna wirtualna o dużej wielkości (G, GS lub E), możesz zmienić lokalizację, w której ten plik zostanie utworzony, i odnieść się do dowolnego innego dysku z danymi, który jest dołączony do maszyny wirtualnej. W tym celu należy zmienić następujący klucz:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Odłącz dysk systemu operacyjnego, a następnie ponownie Dołącz dysk systemu operacyjnego do maszyny wirtualnej, której to dotyczy](../windows/troubleshoot-recovery-disks-portal.md).
4. Uruchom maszynę wirtualną, aby odtworzyć problem, a następnie wygenerowany plik zrzutu.
5. Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania, Zbierz plik zrzutu, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) z plikiem zrzutu.



