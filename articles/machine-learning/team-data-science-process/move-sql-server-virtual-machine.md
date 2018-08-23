---
title: Przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Przenoszenie danych z plików prostych lub na lokalnym serwerze SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 2c26c26f201e9b500018b4851ac0bc462820a8e5
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42059627"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure
W tym temacie opisano opcje przenoszenia danych z plików prostych (w formatach plików CSV lub TSV) lub z lokalnego programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure. Te zadania dotyczące przenoszenia danych do chmury są częścią zespołu danych dla celów naukowych.

Temat, który przedstawia opcje przenoszenia danych do usługi Azure SQL Database dla usługi Machine Learning, można zobaczyć [przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning](move-sql-azure.md).

**Menu** poniżej łącza do tematów opisujących sposób pozyskiwania danych w innych środowiskach docelowych, gdzie dane mogą być przechowywane i przetwarzane podczas procesu do nauki o danych zespołu (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Poniższa tabela podsumowuje Opcje przenoszenia danych do programu SQL Server na maszynie wirtualnej platformy Azure.

| <b>ŹRÓDŁO</b> | <b>Miejsce docelowe: SQL Server na maszynie Wirtualnej platformy Azure</b> |
| --- | --- |
| <b>Plik prosty</b> |1. <a href="#insert-tables-bcp">Narzędzie wiersza polecenia zbiorczego kopiowania (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Zapytanie SQL wstawiania zbiorczego </a><br> 3. <a href="#sql-builtin-utilities">Graficznych narzędzi wbudowanych w programie SQL Server</a> |
| <b>Na lokalnym serwerze SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Wdrażanie bazy danych programu SQL Server do kreatora Microsoft Azure VM</a><br> 2. <a href="#export-flat-file">Eksportuj do pliku prostego </a><br> 3. <a href="#sql-migration">Kreator migracji bazy danych SQL </a> <br> 4. <a href="#sql-backup">Baza danych kopii zapasowej i przywracanie </a><br> |

Należy pamiętać, że w tym dokumencie przyjęto założenie, że polecenia SQL są wykonywane z programu SQL Server Management Studio lub Visual Studio Explorer bazy danych.

> [!TIP]
> Alternatywnie, można użyć [usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/) do tworzenia i zaplanować potok, który spowoduje przeniesienie danych do maszyny Wirtualnej programu SQL Server na platformie Azure. Aby uzyskać więcej informacji, zobacz [kopiowanie danych przy użyciu usługi Azure Data Factory (działanie kopiowania)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Wymagania wstępne
W tym samouczku przyjęto założenie, że masz:

* **Subskrypcji platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konta usługi Azure storage**. Użyjesz konta usługi Azure storage do przechowywania danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure storage, zobacz [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md) artykułu. Po utworzeniu konta magazynu, należy uzyskać klucz konta, które umożliwiają dostęp do magazynu. Zobacz [zarządzanie kluczami dostępu do magazynu](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Zainicjowano obsługę administracyjną **programu SQL Server na maszynie Wirtualnej platformy Azure**. Aby uzyskać instrukcje, zobacz [Konfigurowanie maszyny wirtualnej serwera SQL Azure jako serwera IPython Notebook na potrzeby zaawansowanej analizy](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Zainstalowany i skonfigurowany **programu Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Przenoszenie danych ze źródła pliku prostego do programu SQL Server na Maszynie wirtualnej platformy Azure
Jeśli dane pochodzą z pliku prostego (ułożone w formacie wiersza/kolumny), można ją przenosić do maszyny Wirtualnej programu SQL Server na platformie Azure za pomocą następujących metod:

1. [Narzędzie wiersza polecenia zbiorczego kopiowania (BCP)](#insert-tables-bcp)
2. [Zapytanie SQL wstawiania zbiorczego ](#insert-tables-bulkquery)
3. [Graficznych narzędzi wbudowanych w programie SQL Server (Import/Export, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Narzędzie wiersza polecenia zbiorczego kopiowania (BCP)
Narzędzie BCP jest narzędziem wiersza polecenia, zainstalowane z programem SQL Server i najszybszy sposób przenoszenia danych. Działa we wszystkich trzech wariantów programu SQL Server (On-premises SQL Server, SQL Azure i maszynę Wirtualną SQL Server na platformie Azure).

> [!NOTE]
> **Gdzie należy Moje dane dla narzędzia BCP**  
> Mimo że nie jest wymagany, o pliki zawierające źródło danych znajduje się na tym samym komputerze co docelowego programu SQL Server umożliwia szybszy transfer (sieci szybkość vs lokalny dysk szybkości operacji We/Wy). Można przenieść pliki proste zawierający dane do maszyny w przypadku, gdy jest zainstalowany program SQL Server przy użyciu różnych kopiowanie plików narzędzia takie jak [AZCopy](../../storage/common/storage-use-azcopy.md), [Eksploratora usługi Azure Storage](http://storageexplorer.com/) lub skopiuj/Wklej systemu windows za pomocą pulpitu zdalnego Protocol (RDP).
>
>

1. Upewnij się, że bazy danych i tabele są tworzone w docelowej bazie danych programu SQL Server. Oto przykład sposobu wykonania tego za pomocą `Create Database` i `Create Table` poleceń:

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Wygeneruj plik formatu, opisujący schematu dla tabeli, wydając polecenie z wiersza polecenia maszyny zainstalowanym bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Wstawianie danych do bazy danych za pomocą polecenia bcp w następujący sposób. Powinno to działać z wiersza polecenia, przy założeniu, że serwer SQL jest zainstalowany na tym samym komputerze:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optymalizacja wstawia BCP** zapoznaj się z następującym artykułem [dotyczących optymalizacji importu zbiorczego](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) zoptymalizować takie operacje wstawiania.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Przekształcają wstawia w przypadku przenoszenia danych szybciej
Jeśli dane, które są przenoszone jest duża, można przyspieszyć rzeczy przez jednoczesne wykonywanie wielu poleceń narzędzia BCP równolegle w skrypcie programu PowerShell.

> [!NOTE]
> **Pozyskiwanie danych big data** w celu zoptymalizowania danych ładowania dla dużych i bardzo dużych zestawów danych, Partycjonowanie tabel bazy danych logicznych i fizycznych przy użyciu wielu tabel grup plików i partycji. Aby uzyskać więcej informacji na temat tworzenia i ładowanie danych do tabel partycji SQL, zobacz [równoległe tabel partycji SQL obciążenia](parallel-load-sql-partitioned-tables.md).
>
>

Poniższy przykładowy skrypt programu PowerShell pokazują wstawia równoległych, przy użyciu narzędzia bcp:

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
      }


    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }


    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }

    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Zapytanie SQL wstawiania zbiorczego
[Zbiorcze Wstawianie zapytania SQL](https://msdn.microsoft.com/library/ms188365) może służyć do importowania danych do bazy danych z plików na podstawie wiersza/kolumny (obsługiwane typy są objęte[przygotowania danych dla zbiorczego eksportu lub importu (SQL Server)](https://msdn.microsoft.com/library/ms188609)) tematu.

Poniżej przedstawiono niektóre przykładowe polecenia dla Bulk Insert są tak jak pokazano poniżej:  

1. Analizowanie danych i ustaw opcje niestandardowych przed zaimportowaniem, aby upewnić się, że bazy danych programu SQL Server przyjęto założenie, tego samego formatu dla wszystkich pól specjalnych, takich jak daty. Poniżej przedstawiono przykładowy sposób ustawić format daty jako rok, miesiąc, dzień, (Jeśli dane zawierają datę w formacie rok, miesiąc, dzień):

        SET DATEFORMAT ymd;    
2. Importowanie danych przy użyciu instrukcji importowania zbiorczego:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Wbudowane narzędzia w programie SQL Server
SQL Server integracji Services (SSIS) służy do importowania danych do maszyny Wirtualnej z programu SQL Server na platformie Azure z pliku prostego.
SSIS jest dostępna w dwóch środowiskach studio. Aby uzyskać więcej informacji, zobacz [Integration Services (SSIS) i Studio środowisk](https://technet.microsoft.com/library/ms140028.aspx):

* Aby uzyskać szczegółowe informacje dotyczące programu SQL Server Data Tools, zobacz [programu Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Szczegółowe informacje na temat Kreatora importu/eksportu, [Kreatora programu SQL Server importowania i eksportowania](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Przenoszenie danych z lokalnego programu SQL Server do programu SQL Server na Maszynie wirtualnej platformy Azure
Można również użyć następujących strategii migracji:

1. [Wdrażanie bazy danych programu SQL Server do kreatora Microsoft Azure VM](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Eksportuj do pliku prostego](#export-flat-file)
3. [Kreator migracji bazy danych SQL](#sql-migration)
4. [Baza danych kopii zapasowej i przywracanie](#sql-backup)

Opisano każdy z nich poniżej:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Wdrażanie bazy danych programu SQL Server do kreatora Microsoft Azure VM
**Wdrażanie bazy danych programu SQL Server do kreatora Microsoft Azure VM** jest proste i zalecanym sposobem przenieść dane z lokalnego wystąpienia programu SQL Server do programu SQL Server na Maszynie wirtualnej platformy Azure. Aby uzyskać szczegółowy opis kroków, jak również dyskusję na temat innych rozwiązań alternatywnych, zobacz [migracji bazy danych programu SQL Server na Maszynie wirtualnej platformy Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Eksportuj do pliku prostego
Różne metody może służyć do zbiorczego Eksportuj dane z programu SQL Server On-Premises, zgodnie z opisem w [zbiorcze importowanie i eksportowanie danych (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) tematu. W tym dokumencie opisano Program kopiowania zbiorczego (BCP) jako przykład. Gdy dane zostaną wyeksportowane do pliku prostego, można go było zaimportować do innego serwera SQL za pomocą importowania zbiorczego.

1. Wyeksportować dane z lokalnego programu SQL Server w pliku w następujący sposób za pomocą narzędzia bcp

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Tworzenie bazy danych i tabeli na maszynie Wirtualnej programu SQL Server na platformie Azure przy użyciu `create database` i `create table` dla schematu tabeli wyeksportowany w kroku 1.
3. Tworzenie pliku formatu do opisywania schematu tabeli ilość, eksportu/importu danych. Szczegółowe informacje o pliku formatu są opisane w [Utwórz plik w formacie (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Generowanie pliku formatu podczas uruchamiania narzędzia BCP z komputera programu SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Generowanie pliku formatu podczas uruchamiania narzędzia BCP zdalnie względem programu SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Użyć dowolnej z metod opisanych w sekcji [przenoszenie danych ze źródła pliku](#filesource_to_sqlonazurevm) Aby przenieść dane z plików prostych do programu SQL Server.

### <a name="sql-migration"></a>Kreator migracji bazy danych SQL
[Kreator migracji bazy danych programu SQL Server](http://sqlazuremw.codeplex.com/) oferuje przyjazny dla użytkownika sposób przenoszenia danych między dwa wystąpienia programu SQL server. Umożliwia użytkownikowi do mapy schematu danych między źródłami a tabel docelowych, wybierz typy kolumn i różnych innych funkcji. Używa ona kopiowania masowego (BCP) w sposób niewidoczny. Poniżej przedstawiono zrzut ekranu przedstawiający ekran powitalny Kreatora migracji bazy danych SQL.  

![Kreator migracji programu SQL Server][2]

### <a name="sql-backup"></a>Baza danych kopii zapasowej i przywracanie
Obsługa programu SQL Server:

1. [Baza danych kopii zapasowej i przywracania oferowane](https://msdn.microsoft.com/library/ms187048.aspx) (zarówno w lokalnym pliku lub pliku bacpac Eksport do obiektu blob) i [aplikacji warstwy danych](https://msdn.microsoft.com/library/ee210546.aspx) (przy użyciu pliku bacpac).
2. Możliwość bezpośrednio utworzyć maszyny wirtualne SQL Server na platformie Azure za pomocą skopiowanego bazy danych lub Kopiuj do istniejącej bazy danych SQL Azure. Aby uzyskać więcej informacji, zobacz [za pomocą Kreatora kopiowania bazy danych](https://msdn.microsoft.com/library/ms188664.aspx).

Zrzut ekranu przedstawiający kopii bazy danych w górę/przywracania opcji dostępnych w programie SQL Server Management Studio znajdują się poniżej.

![Narzędzie importu programu SQL Server][1]

## <a name="resources"></a>Zasoby
[Migrowanie bazy danych do programu SQL Server na Maszynie wirtualnej platformy Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Omówienie programu SQL Server w usłudze Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
