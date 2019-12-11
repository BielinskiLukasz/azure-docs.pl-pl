---
title: Usuwanie funkcji ochrony TDE — PowerShell
description: Przewodnik, w którym można odpowiedzieć na potencjalnie złamaną ochronę TDE dla Azure SQL Database lub magazynu danych przy użyciu TDE z obsługą własnego klucza (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: d4c9b926afe93f52946c5f1adf40835f72812f2a
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995826"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Usuwanie funkcji ochrony Transparent Data Encryption (TDE) przy użyciu programu PowerShell

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji
- Musisz mieć Azure PowerShell zainstalowane i uruchomione.
- W tym przewodniku założono, że korzystasz już z klucza z Azure Key Vault jako funkcji ochrony TDE dla Azure SQL Database lub magazynu danych. Aby dowiedzieć się więcej, zobacz [transparent Data Encryption z obsługą BYOK](transparent-data-encryption-byok-azure-sql.md) .

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

 Aby uzyskać instrukcje dotyczące instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Aby poznać konkretne polecenia cmdlet, zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać więcej instalacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Przegląd

W tym przewodniku opisano, jak odpowiedzieć na potencjalnie złamaną ochronę TDE dla Azure SQL Database lub magazynu danych korzystającego z TDE z kluczami zarządzanymi przez klienta w ramach obsługi Azure Key Vault Bring Your Own Key (BYOK). Aby dowiedzieć się więcej o obsłudze BYOK dla TDE, zobacz [stronę przegląd](transparent-data-encryption-byok-azure-sql.md).

Poniższe procedury powinny być wykonywane tylko w sytuacjach skrajnych lub w środowiskach testowych. Uważnie zapoznaj się z przewodnikiem krok po kroku, ponieważ usunięcie aktywnie używanych funkcji ochrony TDE z Azure Key Vault może spowodować **utratę danych**.

Jeśli klucz jest kiedykolwiek zagrożony, w taki sposób, aby usługa lub użytkownik miał nieautoryzowany dostęp do klucza, najlepszym rozwiązaniem jest usunięcie klucza.

Należy pamiętać, że po usunięciu funkcji ochrony TDE w Key Vault **wszystkie połączenia z zaszyfrowanymi bazami danych na serwerze są blokowane, a te bazy danych przechodzą w tryb offline i zostaną usunięte w ciągu 24 godzin**. Stare kopie zapasowe zaszyfrowane za pomocą klucza złamanego nie są już dostępne.

Poniższe kroki przedstawiają sposób sprawdzania odcisków palca funkcji TDE, które są nadal używane przez wirtualne pliki dzienników (VLF) danej bazy danych.
Odcisk palca bieżącej funkcji ochrony TDE bazy danych i identyfikator bazy danych można znaleźć, uruchamiając polecenie:

```sql
SELECT [database_id], 
       [encryption_state], 
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ 
       [encryptor_thumbprint], 
 FROM [sys].[dm_database_encryption_keys]
```

Następujące zapytanie zwraca VLFs i szyfrujący odpowiednie odciski palców w użyciu. Każdy inny odcisk palca odwołuje się do innego klucza w Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Polecenie programu PowerShell **Get-AzureRmSqlServerKeyVaultKey** udostępnia odcisk palca funkcji ochrony TDE używanej w zapytaniu, dzięki czemu możesz zobaczyć, które klucze mają być zachowane i które klucze usunąć w AKV. Tylko klucze, które nie są już używane przez bazę danych, można bezpiecznie usunąć z Azure Key Vault.

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenie programu PowerShell **AZ SQL Server Key show** udostępnia odcisk palca funkcji ochrony TDE używany w zapytaniu, dlatego można zobaczyć, które klucze należy zachować i które klucze usunąć w AKV. Tylko klucze, które nie są już używane przez bazę danych, można bezpiecznie usunąć z Azure Key Vault.

* * *

Ten przewodnik przedstawia dwa podejścia w zależności od pożądanego wyniku po reakcji na zdarzenie:

- Aby zachować **dostęp** do baz danych Azure SQL Database/magazynów z danymi
- Aby uniemożliwić **dostęp** do baz danych Azure SQL Database/magazynów z danymi

## <a name="to-keep-the-encrypted-resources-accessible"></a>Aby zapewnić dostęp do zaszyfrowanych zasobów

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz [nowy klucz w Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Upewnij się, że ten nowy klucz jest tworzony w osobnym magazynie kluczy od potencjalnie naruszonej ochrony TDE, ponieważ kontrola dostępu jest inicjowana na poziomie magazynu.

2. Dodaj nowy klucz do serwera za pomocą poleceń cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) i [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) i zaktualizuj go jako nową funkcję ochrony TDE na serwerze.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Upewnij się, że serwer i wszystkie repliki zostały zaktualizowane do nowej funkcji ochrony TDE przy użyciu polecenia cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) .

   > [!NOTE]
   > Nowe zabezpieczenie TDE można propagować do wszystkich baz danych i pomocniczych baz danych na serwerze za kilka minut.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Utwórz [kopię zapasową nowego klucza](/powershell/module/az.keyvault/backup-azkeyvaultkey) w Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Usuń złamany klucz z Key Vault przy użyciu polecenia cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Aby przywrócić klucz do Key Vault w przyszłości przy użyciu polecenia cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać informacje dotyczące poleceń, zobacz [Magazyn kluczy interfejsu wiersza polecenia platformy Azure](/cli/azure/keyvault/key).

1. Utwórz [nowy klucz w Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Upewnij się, że ten nowy klucz jest tworzony w osobnym magazynie kluczy od potencjalnie naruszonej ochrony TDE, ponieważ kontrola dostępu jest inicjowana na poziomie magazynu.

2. Dodaj nowy klucz do serwera i zaktualizuj go jako nową funkcję ochrony TDE na serwerze.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Upewnij się, że serwer i wszystkie repliki zostały zaktualizowane do nowej funkcji ochrony TDE.

   > [!NOTE]
   > Nowe zabezpieczenie TDE można propagować do wszystkich baz danych i pomocniczych baz danych na serwerze za kilka minut.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Utwórz kopię zapasową nowego klucza w Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Usuń złamany klucz z Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Aby przywrócić klucz do Key Vault w przyszłości.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Aby uniemożliwić dostęp do zaszyfrowanych zasobów

1. Porzuć bazy danych, które są szyfrowane przez potencjalnie złamany klucz.

   Kopie zapasowe bazy danych i plików dziennika są tworzone automatycznie, dzięki czemu można wykonać przywracanie do punktu w czasie w dowolnym momencie (o ile podano klucz). Przed usunięciem aktywnej funkcji ochrony TDE należy porzucić bazy danych, aby zapobiec utracie danych do 10 minut od najnowszych transakcji.

2. Wykonaj kopię zapasową najważniejszych materiałów ochrony TDE w Key Vault.
3. Usuń potencjalnie złamany klucz z Key Vault

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak obrócić funkcję ochrony TDE na serwerze, aby zachować zgodność z wymaganiami dotyczącymi zabezpieczeń: [obróć transparent Data Encryption funkcję ochrony przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Wprowadzenie do obsługi Bring Your Own Key TDE: [Włączanie TDE przy użyciu własnego klucza z Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
