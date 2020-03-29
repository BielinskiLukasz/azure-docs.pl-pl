---
title: Konfigurowanie narzędzia importu/eksportowania platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować narzędzie do przygotowywania i naprawy dysków dla usługi Azure Import/Export.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 01432ab68fc399f3e97eac2de5a7c356bef7078a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74979040"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Konfigurowanie narzędzia Importu/Eksportu platformy Azure

Narzędzie Import/Eksport platformy Microsoft Azure to narzędzie do przygotowywania i naprawy dysków, którego można używać z usługą Import/Eksportowanie platformy Microsoft Azure. Narzędzia można używać do następujących funkcji:

* Przed utworzeniem zadania importu można użyć tego narzędzia do kopiowania danych na dyski twarde, które mają być dostarczane do centrum danych platformy Azure.
* Po zakończeniu zadania importu można użyć tego narzędzia do naprawy obiektów blob, które zostały uszkodzone, brakowało lub były w konflikcie z innymi obiektami blob.
* Po otrzymaniu dysków z ukończonego zadania eksportu można użyć tego narzędzia do naprawy plików, które zostały uszkodzone lub których brakuje na dyskach.

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku **przygotowywania dysków** do zadania importowania muszą być spełnione następujące wymagania wstępne:

* Musisz mieć aktywną subskrypcję platformy Azure.
* Subskrypcja musi zawierać konto magazynu z wystarczającą ilością dostępnego miejsca do przechowywania plików, które zamierzasz zaimportować.
* Potrzebujesz co najmniej jednego klucza dostępu do konta magazynu.
* Potrzebny jest komputer ("kopiuj komputer") z zainstalowanymi systemami Windows 7, Windows Server 2008 R2 lub nowszym systemem operacyjnym Windows.
* Program .NET Framework 4 musi być zainstalowany na komputerze kopii.
* Funkcja BitLocker musi być włączona na komputerze kopii.
* Potrzebujesz jednego lub więcej pustych dysków twardych SATAII lub III lub SSD o przełęczech 2,5-calowych lub 3,5-calowych podłączonych do urządzenia do kopiowania.
* Pliki, które zamierzasz zaimportować, muszą być dostępne z komputera kopii, niezależnie od tego, czy znajdują się na udziale sieciowym, czy lokalnym dysku twardym.

Jeśli próbujesz **naprawić import,** który częściowo nie powiódł się, musisz:

* Pliki dziennika kopiowania
* Klucz konta magazynu

Jeśli próbujesz **naprawić eksport,** który częściowo nie powiódł się, musisz:

* Pliki dziennika kopiowania
* Pliki manifestu (opcjonalnie)
* Klucz konta magazynu

## <a name="installing-the-azure-importexport-tool"></a>Instalowanie narzędzia Importuj/Eksportuj platformy Azure

Najpierw [pobierz narzędzie importu/eksportu platformy Azure](https://www.microsoft.com/download/details.aspx?id=55280) i wyodrębnij je `c:\WAImportExport`do katalogu na komputerze, na przykład .

Narzędzie importu/eksportu platformy Azure składa się z następujących plików:

* plik dataset.csv
* plik driveset.csv
* plik hddid.dll
* Microsoft.Data.Services.Client.dll
* Plik Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

Następnie otwórz okno wiersza polecenia w **trybie administratora**i zmień katalog zawierający wyodrębnione pliki.

Aby wyprowadzić pomoc dla polecenia,`WAImportExport.exe`uruchom narzędzie ( ) bez parametrów:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Następne kroki

* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Wyświetlanie podglądu użycia dysków przez zadanie eksportu](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
