---
title: Przywracanie kopii zapasowej do wystąpienia zarządzanego
description: Przywracanie kopii zapasowej bazy danych do wystąpienia zarządzanego usługi Azure SQL Database za pomocą programu SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 3b12aa860bee189f706bd98df63fc194a3a0cc71
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874708"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Szybki start: przywracanie bazy danych do wystąpienia zarządzanego

W tym przewodniku Szybki start użyjesz programu SQL Server Management Studio (SSMS), aby przywrócić bazę danych (plik kopii zapasowej Wide World Importers — Standard) z usługi Azure Blob Storage do [wystąpienia zarządzanego](sql-database-managed-instance.md) usługi Azure SQL Database.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Aby uzyskać więcej informacji na temat migracji przy użyciu usługi Azure Database Migration Service (DMS), zobacz artykuł [Managed Instance migration using DMS](../dms/tutorial-sql-server-to-managed-instance.md) (Migracja wystąpień zarządzanych przy użyciu usługi DMS).
> Aby uzyskać więcej informacji dotyczących różnych metod migracji, zobacz artykuł [SQL Server instance migration to Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md) (Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:

- Wykorzystuje zasoby z przewodnika Szybki start [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Wymaga komputera z zainstalowanym najnowszym programem [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms).
- Wymaga użycia programu SSMS do połączenia z wystąpieniem zarządzanym. Zobacz te przewodniki Szybki start dotyczące nawiązywania połączenia:
  - [Włącz publiczny punkt końcowy](sql-database-managed-instance-public-endpoint-configure.md) w wystąpieniu zarządzanym — jest to zalecane podejście dla tego samouczka.
  - [Nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL Database z maszyny wirtualnej platformy Azure](sql-database-managed-instance-configure-vm.md)
  - [Skonfiguruj połączenie typu "punkt-lokacja" z wystąpieniem zarządzanym bazy danych SQL platformy Azure z lokalnego](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania bazy danych programu SQL Server przy użyciu usługi Azure Blob Storage i [klucza sygnatury dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1), zobacz artykuł [SQL Server Backup to URL (Tworzenie kopii zapasowej programu SQL Server pod określonym adresem URL)](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-the-database-from-a-backup-file"></a>Przywracanie bazy danych z pliku kopii zapasowej

W programie SSMS użyj następujących kroków, aby przywrócić bazę danych Wide World Importers do wystąpienia zarządzanego. Plik kopii zapasowej bazy danych znajduje na wstępnie skonfigurowanym koncie usługi Azure Blob Storage.

1. Otwórz ssms i połącz się z wystąpieniem zarządzanym.
2. W menu po lewej stronie, kliknij prawym przyciskiem myszy wystąpienie zarządzane i wybierz pozycję **Nowe zapytanie**, aby otworzyć okno nowego zapytania.
3. Uruchom poniższy skrypt SQL wykorzystujący wstępnie skonfigurowane konto magazynu i klucz SAS, aby [utworzyć poświadczenie](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) w wystąpieniu zarządzanym.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![tworzenie poświadczenia](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Aby sprawdzić swoje poświadczenie, uruchom następujący skrypt, który używa adresu URL [kontenera](https://azure.microsoft.com/services/container-instances/), aby uzyskać listę plików kopii zapasowej.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![lista plików](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Uruchom następujący skrypt, aby przywrócić bazę danych Wide World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Przywracanie](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Uruchom następujący skrypt, aby śledzić stan przywracania.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Po zakończeniu przywracania wyświetl bazę danych w Eksploratorze obiektów. Można sprawdzić, czy przywracanie bazy danych zostało zakończone przy użyciu [widoku sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)

> [!NOTE]
> Operacja przywracania bazy danych jest asynchronizalna i można go ponowić. Może pojawić się błąd jest SQL Server Management Studio, jeśli przerwy połączenia lub upośń upływ czasu wygasa. Usługa Azure SQL Database będzie próbować przywrócić bazę danych w tle i można śledzić postęp przywracania przy użyciu [widoków sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) i [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)
> W niektórych fazach procesu przywracania zobaczysz unikatowy identyfikator zamiast rzeczywistej nazwy bazy danych w widokach systemowych. Dowiedz `RESTORE` się więcej o różnicach w zachowaniu instrukcji [tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Następne kroki

- W celu rozwiązywania problemów dotyczących tworzenia kopii zapasowej do adresu URL, zobacz artykuł [SQL Server Backup to URL Best Practices and Troubleshooting](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting) (Kopia zapasowa programu SQL Server do adresu URL — najlepsze rozwiązania i rozwiązywanie problemów).
- Aby uzyskać omówienie opcji połączenia aplikacji, zobacz artykuł [Connect your applications to Managed Instance](sql-database-managed-instance-connect-app.md) (Łączenie aplikacji z wystąpieniem zarządzanym).
- Aby kwerendy przy użyciu ulubionych narzędzi lub języków, zobacz [Szybkie starty: Azure SQL Database Connect i Query](sql-database-connect-query.md).
