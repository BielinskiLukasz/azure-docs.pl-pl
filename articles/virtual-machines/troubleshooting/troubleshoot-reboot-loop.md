---
title: Pętla ponownego uruchomienia systemu Windows na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Informacje na temat rozwiązywania problemów z pętlą ponownego uruchamiania systemu Windows | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: d8a1d64ac8e65fd52730ee1750c0b0b1949b3512
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088466"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Pętla ponownego uruchamiania systemu Windows na maszynie wirtualnej platformy Azure
W tym artykule opisano pętlę ponownego uruchamiania, którą można napotkać na maszynie wirtualnej z systemem Windows w Microsoft Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu pobrania zrzutów ekranu maszyny wirtualnej można uruchomić maszynę wirtualną, ale proces rozruchu zostanie przerwany, a proces jest uruchamiany ponownie.

![Ekran startowy 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Przyczyna

Pętla ponownego uruchamiania występuje z powodu następujących przyczyn:

### <a name="cause-1"></a>Przyczyny 1

Istnieje inna usługa, która jest oflagowana jako krytyczna i nie można jej uruchomić. Powoduje to ponowne uruchomienie systemu operacyjnego.

### <a name="cause-2"></a>Przyczyny 2

Wprowadzono pewne zmiany w systemie operacyjnym. Zazwyczaj są one powiązane z instalacją aktualizacji, instalacją aplikacji lub nową zasadą. Aby uzyskać dodatkowe informacje, może być konieczne sprawdzenie następujących dzienników:

- Dzienniki zdarzeń
- CBS.logWindows
- Update. log

### <a name="cause-3"></a>Przyczyna 3

Przyczyną może być uszkodzenie systemu plików. Trudno jest jednak zdiagnozować i zidentyfikować zmianę powodującą uszkodzenie systemu operacyjnego.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Wykonaj kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md)i [Dołącz dysk systemu operacyjnego do ratowniczej maszyny wirtualnej](../windows/troubleshoot-recovery-disks-portal.md), a następnie postępuj zgodnie z opcjami rozwiązania lub wypróbuj te rozwiązania po jednej.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

1. Po dołączeniu dysku systemu operacyjnego do działającej maszyny wirtualnej upewnij się, że dysk jest oznaczony jako **online** w konsoli Zarządzanie dyskami i Zanotuj literę dysku partycji, która zawiera folder **\Windows** .

2. Jeśli dysk jest ustawiony na **tryb offline**, ustaw go w **tryb online**.

3. Utwórz kopię folderu **\Windows\System32\config** w przypadku konieczności wycofania zmian.

4. Na maszynie ratowniczej, Otwórz Edytor rejestru systemu Windows (regedit).

5. Wybierz klucz **HKEY_LOCAL_MACHINE** , a następnie wybierz pozycję **plik** > **Załaduj gałąź** z menu.

6. Przejdź do pliku SYSTEMowego w folderze **\Windows\System32\config**

7. Wybierz pozycję **Otwórz**, wpisz **BROKENSYSTEM** jako nazwę, rozwiń klucz **HKEY_LOCAL_MACHINE** , a następnie zobaczysz dodatkowy klucz o nazwie **BROKENSYSTEM**.

8. Sprawdź, z którego ControlSet komputer jest uruchamiany. Numer klucza zostanie wyświetlony w następującym kluczu rejestru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Sprawdź, czy usługa agenta maszyny wirtualnej jest krytyczna, za pomocą następującego klucza rejestru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Jeśli wartość klucza rejestru nie jest ustawiona na **2**, przejdź do następnego ograniczenia.

11. Jeśli wartość klucza rejestru jest ustawiona na **2**, Zmień wartość z **2** na **1**.

12. Jeśli którykolwiek z następujących kluczy istnieje i ma wartość **2** lub **3**, a następnie zmieni te wartości na **1** :

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Wybierz klucz **BROKENSYSTEM** , a następnie wybierz pozycję **plik** > **Załaduj gałąź** z menu.

14. Odłącz dysk systemu operacyjnego od maszyny wirtualnej rozwiązywania problemów.

15. Wyjmij dysk z maszyny wirtualnej rozwiązywania problemów i poczekaj około 2 minut na wydanie tego dysku przez platformę Azure.

16. [Utwórz nową maszynę wirtualną z dysku systemu operacyjnego](../windows/create-vm-specialized.md).

17. Jeśli problem został rozwiązany, może być konieczne ponowne zainstalowanie programu [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent. exe).

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przywróć ostatnią znaną dobrą konfigurację maszyny wirtualnej, wykonaj kroki opisane w temacie [jak uruchomić maszynę wirtualną z systemem Azure z ostatnią znaną dobrą konfiguracją](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Rozwiązanie dla przyczyny 3
>[!NOTE]
>Poniższa procedura powinna być używana tylko jako ostatni zasób. Chociaż przywracanie z Regback może przywrócić dostęp do maszyny, system operacyjny nie jest uważany za stabilny, ponieważ w rejestrze znajdują się dane utracone między sygnaturą czasową Hive a bieżącym dniem. Musisz utworzyć nową maszynę wirtualną i utworzyć plany migracji danych.

1. Po dołączeniu dysku do maszyny wirtualnej rozwiązywania problemów upewnij się, że dysk jest oznaczony jako **online** w konsoli Zarządzanie dyskami.

2. Utwórz kopię folderu **\Windows\System32\config** w przypadku konieczności wycofania zmian.

3. Skopiuj pliki w folderze **\Windows\System32\config\regback** i Zastąp pliki w folderze **\Windows\System32\config** .

4. Wyjmij dysk z maszyny wirtualnej rozwiązywania problemów i poczekaj około 2 minut na wydanie tego dysku przez platformę Azure.

5. [Utwórz nową maszynę wirtualną z dysku systemu operacyjnego](../windows/create-vm-specialized.md).


