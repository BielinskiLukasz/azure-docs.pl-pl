---
title: Kopiowanie danych między Azure Data Lake Storage Gen1 i Azure SQL Database przy użyciu Sqoop | Microsoft Docs
description: Użyj Sqoop do kopiowania danych między Azure SQL Database i Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 22789deca0934a9d4e88d587cd24aacacc9b12c6
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620012"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopiuj dane między Data Lake Storage Gen1 i Azure SQL Database przy użyciu Sqoop

Dowiedz się, jak importować i eksportować dane między Azure SQL Database i Azure Data Lake Storage Gen1 przy użyciu oprogramowania Apache Sqoop.

## <a name="what-is-sqoop"></a>Co to jest Sqoop?

Aplikacje danych Big Data to naturalny wybór służący do przetwarzania danych bez struktury i z częściową strukturą, takich jak dzienniki i pliki. Jednak może być również konieczne przetworzenie danych strukturalnych przechowywanych w relacyjnych bazach danych.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) to narzędzie przeznaczone do przesyłania danych między relacyjnymi bazami danych i repozytorium danych Big Data, takimi jak Data Lake Storage Gen1. Można go użyć do zaimportowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS), takiego jak Azure SQL Database do Data Lake Storage Gen1. Następnie można przekształcać i analizować dane przy użyciu obciążeń danych Big Data, a następnie eksportować dane z powrotem do RDBMS. W tym artykule użyto bazy danych Azure SQL Database jako relacyjnej bazy danych do importowania/eksportowania z programu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać następujące czynności:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia konta, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra usługi HDInsight przy użyciu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). W tym artykule przyjęto założenie, że masz klaster usługi HDInsight w systemie Linux z dostępem Data Lake Storage Gen1.
* **Usługa Azure SQL Database**. Aby uzyskać instrukcje dotyczące sposobu tworzenia jednego z nich, zobacz [Tworzenie bazy danych Azure SQL Database](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Tworzenie przykładowych tabel w bazie danych SQL Azure

1. Aby rozpocząć, Utwórz dwie przykładowe tabele w bazie danych SQL Azure. Użyj [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) lub Visual Studio, aby nawiązać połączenie z bazą danych, a następnie uruchom następujące zapytania.

    **Utwórz tabelę Tabela1**

       CREATE TABLE [dbo].[Table1](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

    **Utwórz Tabela2**

       CREATE TABLE [dbo].[Table2](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

1. Uruchom następujące polecenie, aby dodać przykładowe dane do **Tabela1**. Pozostaw **to** pole puste. Później importujesz dane z **Tabela1** do Data Lake Storage Gen1. Następnie będziesz eksportować dane z Data Lake Storage Gen1 do **tabela2**.

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Korzystanie z Sqoop z klastra usługi HDInsight z dostępem do Data Lake Storage Gen1

Dla klastra HDInsight An dostępne są już pakiety Sqoop. Jeśli klaster usługi HDInsight został skonfigurowany tak, aby używał Data Lake Storage Gen1 jako dodatkowego magazynu, możesz użyć Sqoop (bez zmian konfiguracji), aby zaimportować/wyeksportować dane między relacyjną bazą danych, taką jak Azure SQL Database, a kontem Data Lake Storage Gen1 .

1. W tym artykule przyjęto założenie, że utworzono klaster systemu Linux, dlatego należy użyć protokołu SSH do nawiązania połączenia z klastrem. Zobacz [nawiązywanie połączenia](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)z klastrem usługi HDInsight opartej na systemie Linux.

1. Sprawdź, czy możesz uzyskać dostęp do konta Data Lake Storage Gen1 z klastra. Uruchom następujące polecenie w wierszu polecenia SSH:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   To polecenie udostępnia listę plików/folderów na koncie Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importuj dane z Azure SQL Database do Data Lake Storage Gen1

1. Przejdź do katalogu, w którym są dostępne pakiety Sqoop. Zwykle jest `/usr/hdp/<version>/sqoop/bin`to lokalizacja.

1. Zaimportuj dane z programu **Tabela1** do konta Data Lake Storage Gen1. Użyj następującej składni:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   Symbol zastępczy **SQL-Database-Server-Name** reprezentuje nazwę serwera, na którym działa baza danych SQL Azure. Symbol zastępczy **SQL-Database-Name** reprezentuje rzeczywistą nazwę bazy danych.

   Na przykład

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Sprawdź, czy dane zostały przeniesione na konto Data Lake Storage Gen1. Uruchom następujące polecenie:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   Powinny zostać wyświetlone następujące dane wyjściowe.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Każdy plik **części-m-** * odnosi się do wiersza w tabeli źródłowej ( **Tabela1**). Możesz wyświetlić zawartość części-m-* plików do zweryfikowania.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Eksportowanie danych z Data Lake Storage Gen1 do Azure SQL Database

1. Wyeksportuj dane z konta Data Lake Storage Gen1 do pustej tabeli, **tabela2**, w Azure SQL Database. Użyj następującej składni.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Na przykład

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Sprawdź, czy dane zostały przekazane do tabeli SQL Database. Użyj [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) lub Visual Studio, aby nawiązać połączenie z Azure SQL Database, a następnie uruchom następujące zapytanie.

       SELECT * FROM TABLE2

   To polecenie powinno mieć następujące dane wyjściowe.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Zagadnienia dotyczące wydajności podczas korzystania z Sqoop

Aby uzyskać informacje o wydajności dostrajania Sqoop zadania do kopiowania danych do Data Lake Storage Gen1, zobacz wpis w blogu dotyczącym [wydajności Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="next-steps"></a>Kolejne kroki

* [Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
