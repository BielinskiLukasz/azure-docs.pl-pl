---
title: Migrowanie certyfikatu TDE — wystąpienie zarządzane usługi Azure SQL Database | Microsoft Docs
description: Migrowanie certyfikatu chroniącego klucz szyfrowania bazy danych za pomocą funkcji Transparent Data Encryption do wystąpienia zarządzanego usługi Azure SQL
keywords: sql database tutorial, sql database managed instance, migrate TDE certificate
services: sql-database
author: MladjoA
ms.reviewer: carlrab, jovanpop
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 08/09/2018
ms.author: mlandzic
manager: craigg
ms.openlocfilehash: afecd69cdf9832e1c6dc294ca01968ee50a3eabd
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "41921043"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-managed-instance"></a>Migrowanie certyfikatu bazy danych chronionej przez funkcję TDE do wystąpienia zarządzanego usługi Azure SQL

W przypadku migracji bazy danych chronionej przez funkcję [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) do wystąpienia zarządzanego usługi Azure SQL przy użyciu natywnej opcji przywracania, odpowiedni certyfikat z lokalnego programu SQL Server lub programu SQL Server IaaS musi zostać migrowany przed przywróceniem bazy danych. Ten artykuł przeprowadzi Cię przez proces ręcznej migracji certyfikatu do wystąpienia zarządzanego usługi Azure SQL Database:

> [!div class="checklist"]
> * Eksportowanie certyfikatu do pliku wymiany informacji osobistych (pfx)
> * Wyodrębnianie certyfikatu z pliku do ciągu Base-64
> * Przekazywanie go przy użyciu polecenia cmdlet programu PowerShell

Alternatywną możliwość bezproblemowej migracji zarówno bazy danych chronionej przez funkcję TDE, jak i odpowiedniego certyfikatu, korzystającą z w pełni zarządzanej usługi opisano w artykule [Jak przeprowadzić migrację lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Funkcja Transparent Data Encryption dla wystąpienia zarządzanego usługi Azure SQL działa w trybie zarządzanym przez usługę. Migrowany certyfikat jest używany tylko do przywracania bazy danych chronionej przez funkcję TDE. Wkrótce po zakończeniu przywracania migrowany certyfikat jest zastępowany innym certyfikatem zarządzanym przez system.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne jest spełnienie następujących wymagań wstępnych:

- Narzędzie wiersza polecenia [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) zainstalowane na serwerze lokalnym lub innym komputerze z dostępem do certyfikatu wyeksportowanego jako plik. Narzędzie Pvk2Pfx stanowi część [Zestawu sterowników systemu Windows dla przedsiębiorstw](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk) — autonomicznego, samodzielnego środowiska wiersza polecenia.
- Zainstalowany program [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) w wersji 5.0 lub nowszej.
- [Zainstalowany i zaktualizowany](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) moduł AzureRM PowerShell.
- [Moduł AzureRM.Sql](https://www.powershellgallery.com/packages/AzureRM.Sql) w wersji 4.10.0 lub nowszej.
  Aby zainstalować/zaktualizować moduł programu PowerShell, w programie PowerShell uruchom następujące polecenia:

   ```powershell
   Install-Module -Name AzureRM.Sql
   Update-Module -Name AzureRM.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Eksportowanie certyfikatu TDE do pliku wymiany informacji osobistych (pfx)

Certyfikat można wyeksportować bezpośrednio ze źródłowego programu SQL Server lub z magazynu certyfikatów, jeśli jest tam przechowywany.

### <a name="export-certificate-from-the-source-sql-server"></a>Eksportowanie certyfikatu ze źródłowego programu SQL Server

Wykonaj następujące kroki, aby wyeksportować certyfikat przy użyciu programu SQL Server Management Studio i przekonwertować go w format pfx. W tych krokach dla nazw plików i certyfikatów oraz ścieżek są używane ogólne nazwy *TDE_Cert* i *full_path*. Należy je zastąpić rzeczywistymi nazwami.

1. W programie SSMS otwórz nowe okno zapytania i połącz się ze źródłowym programem SQL Server.
2. Za pomocą następującego skryptu wyświetl listę baz danych chronionych przez funkcję TDE i pobierz nazwę certyfikatu chroniącego szyfrowanie bazy danych, który wymaga migracji:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Lista certyfikatów TDE](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

3. Wykonaj następujący skrypt, aby wyeksportować certyfikat do pary plików (cer i pvk), w których znajdują się informacje o kluczu publicznym i prywatnym:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Tworzenie kopii zapasowej certyfikatu TDE](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

4. Korzystając z konsoli programu PowerShell, skopiuj informacje o certyfikacie z pary nowo utworzonych plików do pliku wymiany informacji osobistych (pfx) za pomocą narzędzia Pvk2Pfx:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Eksportowanie certyfikatu z magazynu certyfikatów

Jeśli certyfikat jest przechowywany w magazynie certyfikatów komputera lokalnego programu SQL Server, można go wyeksportować, wykonując następujące kroki:

1. Otwórz konsolę programu PowerShell i uruchom następujące polecenie, aby otworzyć przystawkę Certyfikaty programu Microsoft Management Console:

   ```powershell
   certlm
   ```

2. W przystawce Certyfikaty programu MMC rozwiń ścieżkę Osobiste -> Certyfikaty, aby wyświetlić listę certyfikatów

3. Kliknij prawym przyciskiem myszy certyfikat, a następnie kliknij polecenie Eksportuj…

4. Postępuj zgodnie z poleceniami kreatora, aby wyeksportować certyfikat i klucz prywatny do formatu wymiany informacji osobistych

## <a name="upload-certificate-to-azure-sql-managed-instance-using-azure-powershell-cmdlet"></a>Przekazywanie certyfikatu do wystąpienia zarządzanego usługi Azure SQL przy użyciu polecenia cmdlet programu Azure PowerShell

1. Rozpocznij od kroków przygotowawczych w programie PowerShell:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module AzureRM
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzureRmAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzureRmSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzureRmSubscription Guid_Subscription_Id
   ```

2. Po ukończeniu wszystkich kroków przygotowawczych uruchom następujące polecenia, aby przekazać certyfikat zakodowany w formacie Base-64 do docelowego wystąpienia zarządzanego:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzureRmSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

Certyfikat jest teraz dostępny dla określonego wystąpienia zarządzanego i można pomyślnie przywrócić kopię zapasową odpowiedniej bazy danych chronionej przez funkcję TDE.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób migracji certyfikatu chroniącego klucz szyfrowania bazy danych za pomocą funkcji Transparent Data Encryption z lokalnego programu SQL Server lub programu SQL Server IaaS do wystąpienia zarządzanego usługi Azure SQL.

Zobacz [Przywracanie kopii zapasowej bazy danych do wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-restore-from-backup-tutorial.md), aby dowiedzieć się, jak przywrócić kopię zapasową bazy danych do wystąpienia zarządzanego usługi Azure SQL Database.
