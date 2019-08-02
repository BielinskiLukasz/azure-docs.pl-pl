---
title: Azure Analysis Services kopii zapasowej i przywracania bazy danych | Microsoft Docs
description: Opisuje sposób tworzenia kopii zapasowej i przywracania bazy danych Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 15d2d0a043271da18d7884b684ffe8cf5c1f4dc8
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619212"
---
# <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Tworzenie kopii zapasowych baz danych modeli tabelarycznych w Azure Analysis Services jest znacznie takie samo jak dla Analysis Services lokalnych. Podstawowa różnica polega na tym, że pliki kopii zapasowej są przechowywane. Pliki kopii zapasowej muszą być zapisane w kontenerze na [koncie usługi Azure Storage](../storage/common/storage-create-storage-account.md). Możesz użyć już istniejącego konta magazynu i kontenera lub można je utworzyć podczas konfigurowania ustawień magazynu dla serwera.

> [!NOTE]
> Utworzenie konta magazynu może spowodować powstanie nowej usługi do rozliczania. Aby dowiedzieć się więcej, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Kopie zapasowe są zapisywane z rozszerzeniem ABF. Dla modeli tabelarycznych w pamięci są przechowywane zarówno dane modelu, jak i metadane. W przypadku modeli tabelarycznych zapytania bezpośredniego są przechowywane tylko metadane modelu. Kopie zapasowe mogą być kompresowane i szyfrowane w zależności od wybranych opcji.


## <a name="configure-storage-settings"></a>Konfigurowanie ustawień magazynu
Przed utworzeniem kopii zapasowej należy skonfigurować ustawienia magazynu dla serwera.


### <a name="to-configure-storage-settings"></a>Aby skonfigurować ustawienia magazynu
1.  W obszarze **ustawienia**> Azure Portal kliknij pozycję **kopia zapasowa**.

    ![Kopie zapasowe w ustawieniach](./media/analysis-services-backup/aas-backup-backups.png)

2.  Kliknij pozycję **włączone**, a następnie kliknij pozycję **ustawienia magazynu**.

    ![Włączenie](./media/analysis-services-backup/aas-backup-enable.png)

3. Wybierz konto magazynu lub Utwórz nowe.

4. Wybierz kontener lub Utwórz nowy.

    ![Wybieranie kontenera](./media/analysis-services-backup/aas-backup-container.png)

5. Zapisz ustawienia kopii zapasowej.

    ![Zapisz ustawienia kopii zapasowej](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Tworzenie kopii zapasowej

### <a name="to-backup-by-using-ssms"></a>Aby utworzyć kopię zapasową przy użyciu programu SSMS

1. W programie SSMS kliknij prawym przyciskiem myszy bazę danych > **utworzyć kopię zapasową**.

2. W obszarze kopia zapasowa**pliku**kopii zapasowej **bazy danych** > kliknij przycisk **Przeglądaj**.

3. W oknie dialogowym **Zapisz plik jako** Sprawdź ścieżkę folderu, a następnie wpisz nazwę pliku kopii zapasowej. 

4. W oknie dialogowym **kopia zapasowa bazy danych** wybierz pozycję Opcje.

    **Zezwalaj na zastępowanie pliku** — wybierz tę opcję, aby zastąpić pliki kopii zapasowej o tej samej nazwie. Jeśli ta opcja nie jest zaznaczona, zapisywany plik nie może mieć takiej samej nazwy jak plik, który już istnieje w tej samej lokalizacji.

    **Zastosuj kompresję** — wybierz tę opcję, aby skompresować plik kopii zapasowej. Skompresowane pliki kopii zapasowej oszczędzają miejsce na dysku, ale wymagają nieco wyższego użycia procesora CPU. 

    **Szyfruj plik kopii zapasowej** — wybierz tę opcję, aby zaszyfrować plik kopii zapasowej. Ta opcja wymaga hasła dostarczonego przez użytkownika w celu zabezpieczenia pliku kopii zapasowej. Hasło uniemożliwia odczytywanie danych kopii zapasowych w inny sposób niż operacja przywracania. Jeśli zdecydujesz się na szyfrowanie kopii zapasowych, Zapisz je w bezpiecznym miejscu.

5. Kliknij przycisk **OK** , aby utworzyć i zapisać plik kopii zapasowej.


### <a name="powershell"></a>PowerShell
Użyj polecenia cmdlet [Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) .

## <a name="restore"></a>Przywróć
Podczas przywracania plik kopii zapasowej musi znajdować się na koncie magazynu skonfigurowanym dla serwera. Jeśli musisz przenieść plik kopii zapasowej z lokalizacji lokalnej na konto magazynu, użyj [Eksplorator usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) lub narzędzia wiersza polecenia [AzCopy](../storage/common/storage-use-azcopy.md) . 



> [!NOTE]
> W przypadku przywracania z serwera lokalnego należy usunąć wszystkich użytkowników domeny z ról modelu i dodać je z powrotem do ról jako Azure Active Directory użytkownicy.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Aby przywrócić za pomocą programu SSMS

1. W programie SSMS kliknij prawym przyciskiem myszy bazę danych > **przywracanie**.

2. W oknie dialogowym **kopia zapasowa bazy danych** w **pliku kopii zapasowej**kliknij przycisk **Przeglądaj**.

3. W oknie dialogowym **Lokalizowanie plików bazy danych** wybierz plik, który chcesz przywrócić.

4. W obszarze **Przywróć bazę danych**wybierz bazę danych.

5. Określ opcje. Opcje zabezpieczeń muszą być zgodne z opcjami tworzenia kopii zapasowych użytymi podczas tworzenia kopii zapasowej.


### <a name="powershell"></a>PowerShell

Użyj polecenia cmdlet [Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) .


## <a name="related-information"></a>Informacje pokrewne

[Konta usługi Azure Storage](../storage/common/storage-create-storage-account.md)  
[Wysoka dostępność](analysis-services-bcdr.md)     
[Zarządzaj Azure Analysis Services](analysis-services-manage.md)
