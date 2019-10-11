---
title: Tworzenie kopii zapasowych i przywracanie baz danych SQL na maszynach wirtualnych platformy Azure za pomocą programu PowerShell — Azure Backup
description: Tworzenie kopii zapasowych i przywracanie baz danych SQL na maszynach wirtualnych platformy Azure przy użyciu Azure Backup i programu PowerShell.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Azure Backup; Server
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: dacurwin
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 242eaf06b9cd0b3783a626ab13eb0cb92300652f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249067"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Tworzenie kopii zapasowych i przywracanie baz danych SQL na maszynach wirtualnych platformy Azure przy użyciu programu PowerShell

W tym artykule opisano sposób użycia Azure PowerShell do tworzenia kopii zapasowych i odzyskiwania bazy danych SQL w ramach maszyny wirtualnej platformy Azure przy użyciu magazynu [Azure Backup](backup-overview.md) Recovery Services.

W tym samouczku wyjaśniono:

> [!div class="checklist"]
> * Skonfiguruj program PowerShell i zarejestruj dostawcę usługi Azure Recovery Services.
> * Utwórz magazyn usługi Recovery Services.
> * Skonfiguruj kopię zapasową bazy danych SQL na maszynie wirtualnej platformy Azure.
> * Uruchom zadanie tworzenia kopii zapasowej.
> * Przywracanie kopii zapasowej bazy danych SQL.
> * Monitoruj zadania tworzenia kopii zapasowych i przywracania.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) o magazynach Recovery Services.
* Zapoznaj się z funkcjami funkcji [tworzenia kopii zapasowych programu SQL baz danych na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md#before-you-start).
* Zapoznaj się z hierarchią obiektów programu PowerShell dla Recovery Services.

### <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów Recovery Services

Hierarchia obiektów jest podsumowana na poniższym diagramie.

![Hierarchia obiektów Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zapoznaj się z dokumentacją [polecenia cmdlet](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** w bibliotece platformy Azure.

### <a name="set-up-and-install"></a>Skonfiguruj i zainstaluj

Skonfiguruj program PowerShell w następujący sposób:

1. [Pobierz najnowszą wersję polecenia AZ PowerShell](/powershell/azure/install-az-ps). Minimalna wymagana wersja to 1.5.0.

2. Znajdź Azure Backup poleceń cmdlet programu PowerShell za pomocą tego polecenia:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zapoznaj się z aliasami i poleceniami cmdlet dla Azure Backup i magazynu Recovery Services. Oto przykład tego, co może być widoczne. Nie jest to kompletna lista poleceń cmdlet.

    ![Lista poleceń cmdlet Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Zaloguj się do konta platformy Azure za pomocą programu **Connect-AzAccount**.
5. Na wyświetlonej stronie sieci Web zostanie wyświetlony monit o wprowadzenie poświadczeń konta.

    * Alternatywnie można uwzględnić poświadczenia konta jako parametr w poleceniu cmdlet **Connect-AzAccount** z poleceniem **-Credential**.
    * Jeśli jesteś partnerem programu CSP działającym dla dzierżawy, określ klienta jako dzierżawcę przy użyciu nazwy domeny głównej dzierżawy tenantID lub. Przykładem jest **Connect-AzAccount-dzierżawca** fabrikam.com.

6. Skojarz subskrypcję, której chcesz używać z kontem, ponieważ konto może mieć kilka subskrypcji.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Jeśli używasz Azure Backup po raz pierwszy, użyj polecenia cmdlet **register-AzResourceProvider** , aby zarejestrować dostawcę Recovery Services platformy Azure w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Sprawdź, czy dostawcy zarejestrowali się pomyślnie:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. W danych wyjściowych polecenia Sprawdź, czy **RegistrationState** zmiany zostały **zarejestrowane**. Jeśli nie, uruchom ponownie polecenie cmdlet **register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Wykonaj następujące kroki, aby utworzyć magazyn Recovery Services.

Magazyn Recovery Services jest zasobem Menedżer zasobów, dlatego należy umieścić go w grupie zasobów. Można użyć istniejącej grupy zasobów lub można utworzyć grupę zasobów za pomocą polecenia cmdlet **New-AzResourceGroup** . Podczas tworzenia grupy zasobów należy określić nazwę i lokalizację grupy zasobów.

1. Magazyn znajduje się w grupie zasobów. Jeśli nie masz istniejącej grupy zasobów, Utwórz nową aplikację przy użyciu [nowej AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). W tym przykładzie utworzymy nową grupę zasobów w regionie zachodnie stany USA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Użyj polecenia cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) , aby utworzyć magazyn. Określ tę samą lokalizację dla magazynu, który został użyty dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Określ typ nadmiarowości, która ma być używana w magazynie magazynu.

    * Można użyć magazynu [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) lub [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md).
    * W poniższym przykładzie ustawiono opcję **-BackupStorageRedundancy** dla polecenia[Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd dla **testvault** jako **geomiarowy**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w subskrypcji, użyj polecenie [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Dane wyjściowe są podobne do następujących. Podano skojarzoną grupę zasobów i lokalizację.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

Zapisz obiekt magazynu w zmiennej i ustaw kontekst magazynu.

* Wiele Azure Backup poleceń cmdlet wymaga obiektu magazynu Recovery Services jako danych wejściowych, dlatego warto przechowywać obiekt magazynu w zmiennej.
* Kontekst magazynu to typ danych chronionych w magazynie. Ustaw go za pomocą [opcji Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po ustawieniu kontekstu ma zastosowanie do wszystkich kolejnych poleceń cmdlet.

Poniższy przykład ustawia kontekst magazynu dla **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pobierz identyfikator magazynu

Planujemy zaniechanie ustawienia kontekstu magazynu zgodnie z zaleceniami Azure PowerShell. Zamiast tego można zapisać lub pobrać identyfikator magazynu i przekazać go do odpowiednich poleceń w następujący sposób:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają harmonogram tworzenia kopii zapasowych i czas przechowywania punktów odzyskiwania kopii zapasowych:

* Zasady tworzenia kopii zapasowej są skojarzone z co najmniej jedną zasadą przechowywania. Zasady przechowywania określają czas przechowywania punktu odzyskiwania przed jego usunięciem.
* Wyświetl domyślne przechowywanie zasad kopii zapasowych za pomocą polecenia [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Wyświetlanie domyślnego harmonogramu zasad tworzenia kopii zapasowych za pomocą polecenia [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Aby utworzyć nowe zasady tworzenia kopii zapasowych, należy użyć polecenia cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) . Wprowadzasz obiekty zasad harmonogramu i przechowywania.

Domyślnie w obiekcie zasad harmonogramu jest zdefiniowany czas rozpoczęcia. Użyj poniższego przykładu, aby zmienić godzinę rozpoczęcia na żądaną godzinę rozpoczęcia. Wymagana godzina rozpoczęcia powinna być również w formacie UTC. W poniższym przykładzie przyjęto założenie, że wymagany czas rozpoczęcia to 01:00 czasu UTC dla codziennych kopii zapasowych.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Musisz podać godzinę rozpoczęcia tylko w ciągu 30 minut. W powyższym przykładzie może to być tylko "01:00:00" lub "02:30:00". Godzina rozpoczęcia nie może być "01:15:00"

W poniższym przykładzie są przechowywane zasady harmonogramu i zasady przechowywania w zmiennych. Następnie używa tych zmiennych jako parametrów dla nowych zasad (**NewSQLPolicy**). **NewSQLPolicy** pobiera codziennie "pełną" kopię zapasową, zachowuje ją przez 180 dni i pobiera kopię zapasową dziennika co 2 godziny

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Dane wyjściowe są podobne do następujących.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Włącz tworzenie kopii zapasowej

### <a name="registering-the-sql-vm"></a>Rejestrowanie maszyny wirtualnej SQL

W przypadku kopii zapasowych maszyn wirtualnych platformy Azure i udziałów plików platformy Azure usługa Backup może połączyć się z tymi Azure Resource Manager zasobami i pobrać odpowiednie szczegóły. Ponieważ SQL jest aplikacją na maszynie wirtualnej platformy Azure, usługa Backup wymaga uprawnień dostępu do aplikacji i pobrania niezbędnych informacji. Aby to zrobić, należy *"zarejestrować"* maszynę wirtualną platformy Azure zawierającą aplikację SQL z magazynem usługi Recovery Services. Po zarejestrowaniu maszyny wirtualnej SQL z magazynem możesz chronić baz danych SQL tylko do tego magazynu. Użyj polecenia cmdlet [register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS, aby zarejestrować maszynę wirtualną.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Polecenie zwróci "kontener kopii zapasowych" tego zasobu, a stan zostanie zarejestrowana

> [!NOTE]
> Jeśli parametr Force nie zostanie określony, użytkownik zostanie poproszony o potwierdzenie z tekstem "czy chcesz wyłączyć ochronę dla tego kontenera". Zignoruj ten tekst i powiedz "Y", aby potwierdzić. Jest to znany problem i pracujemy nad usunięciem tekstu i wymaganiem parametru Force.

### <a name="fetching-sql-dbs"></a>Pobieranie baz danych SQL

Po zakończeniu rejestracji usługa Backup będzie w stanie wyświetlić listę wszystkich dostępnych składników SQL w ramach maszyny wirtualnej. Aby wyświetlić wszystkie składniki SQL, których kopie zapasowe mają być tworzone w tym magazynie, użyj polecenia cmdlet [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

W danych wyjściowych zostaną wyświetlone wszystkie niechronione składniki SQL między wszystkimi maszynami wirtualnymi SQL zarejestrowanymi w tym magazynie z typem elementu i ServerName. Można kontynuować filtrowanie do określonej maszyny wirtualnej SQL przez przekazanie parametru "-Container" lub użycie kombinacji wartości "name" i "ServerName" wraz z flagą ItemType, aby dotrzeć do unikatowego elementu SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Konfigurowanie kopii zapasowej

Teraz, gdy wymagana jest baza danych SQL i zasady, których kopia zapasowa ma zostać utworzona, można użyć polecenia cmdlet [enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) w celu skonfigurowania kopii zapasowej tej bazy danych SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Polecenie czeka na zakończenie konfigurowania kopii zapasowej i zwraca następujące dane wyjściowe.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Pobieranie nowych baz danych SQL

Po zarejestrowaniu maszyny usługa Backup pobierze w ten sposób szczegóły baz danych. Jeśli użytkownik doda wystąpienia SQL baz danych/SQL do zarejestrowanej maszyny później, musi ręcznie wyzwolić usługę kopii zapasowej, aby wykonać świeżą instrukcję "zapytania", aby uzyskać wszystkie niechronione baz danych (w tym nowo dodane). Użyj polecenia cmdlet [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS na maszynie wirtualnej SQL, aby wykonać nowe zapytanie. Polecenie czeka, aż operacja zostanie ukończona. Następnie użyj polecenia cmdlet [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS, aby uzyskać listę najnowszych niechronionych składników SQL

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Po pobraniu odpowiednich elementów podlegających ochronie Włącz kopie zapasowe zgodnie z [powyższą sekcją](#configuring-backup).
Jeśli nikt nie chce ręcznie wykryć nowych baz danych, może wybrać opcję autoochrony, jak wyjaśniono [poniżej](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Włącz ochronę autoprotection

Użytkownik może skonfigurować tworzenie kopii zapasowej, tak aby wszystkie baz danych dodane w przyszłości były automatycznie chronione przy użyciu określonych zasad. Aby włączyć ochronę autoprotection, należy użyć polecenia cmdlet [enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

Ponieważ instrukcja ma tworzyć kopie zapasowe wszystkich przyszłych baz danych, operacja odbywa się na poziomie SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Po podaniu opcji autoochrony zapytanie do maszyny w celu pobrania nowo dodanej baz danych odbywa się jako zaplanowane zadanie w tle co 8 godzin.

## <a name="restore-sql-dbs"></a>Przywróć SQL baz danych

Azure Backup można przywrócić SQL Server baz danych uruchomionych na maszynach wirtualnych platformy Azure w następujący sposób:

1. Przywracanie do określonej daty lub czasu (w drugim) przy użyciu kopii zapasowych dziennika transakcji. Azure Backup automatycznie określa odpowiednią pełną, różnicową kopię zapasową i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia w oparciu o wybrany czas.
2. Przywróć określoną pełną lub różnicową kopię zapasową w celu przywrócenia do określonego punktu odzyskiwania.

Przed przywróceniem programu SQL baz danych Sprawdź wymagania wstępne wymienione w [tym miejscu](restore-sql-database-azure-vm.md#prerequisites) .

Najpierw Pobierz odpowiednie kopie zapasowe bazy danych SQL przy użyciu polecenia cmdlet [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Pobierz odpowiedni czas przywracania

Jak opisano powyżej, użytkownik może przywrócić kopię zapasową bazy danych SQL w trybie pełny/różnicowy **lub** do punktu dziennika w czasie.

#### <a name="fetch-distinct-recovery-points"></a>Pobierz odrębne punkty odzyskiwania

Użyj [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) , aby pobrać różne (pełne/różnicowe) punkty odzyskiwania dla kopii zapasowej bazy danych SQL.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

Dane wyjściowe są podobne do poniższego przykładu

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Użyj filtru "RecoveryPointId" lub filtra tablicy, aby pobrać odpowiedni punkt odzyskiwania.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Pobierz punkt odzyskiwania punktu w czasie

Jeśli użytkownik chce przywrócić bazę danych do określonego punktu w czasie, należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS. Polecenie cmdlet zwraca listę dat reprezentujących czasy rozpoczęcia i zakończenia nieuszkodzonego ciągłego łańcucha dzienników dla tego elementu kopii zapasowej SQL. Żądany punkt w czasie powinien znajdować się w tym zakresie.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Dane wyjściowe będą podobne do poniższego przykładu.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Powyższe dane wyjściowe oznacza, że użytkownik może przywrócić wszystkie punkty w czasie między wyświetlaną godziną rozpoczęcia a czasem zakończenia. Czasy są w formacie UTC. Skonstruuj wszystkie punkty w czasie w PS, które znajdują się w zakresie pokazanym powyżej.

> [!NOTE]
> Po wybraniu punktu dziennika na potrzeby przywracania użytkownik nie musi określać punktu początkowego, tj., pełnej kopii zapasowej, z której jest przywracana baza danych. Usługa Azure Backup zajmie cały plan odzyskiwania, tj., którą pełną kopię zapasową wybrać, jakie kopie zapasowe dzienników mają być stosowane.

### <a name="determine-recovery-configuration"></a>Określanie konfiguracji odzyskiwania

W przypadku przywracania bazy danych SQL obsługiwane są następujące scenariusze przywracania.

1. Zastępowanie kopii zapasowej bazy danych SQL z innym punktem odzyskiwania — OriginalWorkloadRestore
2. Przywracanie bazy danych SQL jako nowej bazy danych w tym samym wystąpieniu SQL — AlternateWorkloadRestore
3. Przywracanie bazy danych SQL jako nowej bazy danych w innym wystąpieniu SQL w innej maszynie wirtualnej SQL — AlternateWorkloadRestore

Po pobraniu odpowiedniego punktu odzyskiwania (oddzielnego lub dziennika w czasie) Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS, aby pobrać obiekt konfiguracji odzyskiwania zgodnie z żądanym planem odzyskiwania.

#### <a name="original-workload-restore"></a>Oryginalne przywracanie obciążenia

Aby zastąpić kopię zapasową bazy danych z punktu odzyskiwania, po prostu Określ prawą flagę i odpowiedni punkt odzyskiwania, jak pokazano w poniższych przykładach.

##### <a name="original-restore-with-distinct-recovery-point"></a>Przywracanie oryginalne z odrębnym punktem odzyskiwania

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Przywracanie oryginalne z punktem dziennika w czasie

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternatywne przywracanie obciążenia

> [!IMPORTANT]
> Kopię zapasową bazy danych SQL można przywrócić jako nową bazę danych do innego wystąpienia SQLInstance, w maszynie wirtualnej platformy Azure zarejestrowanej w tym magazynie.

Jak opisano powyżej, jeśli docelowe wystąpienie SQLInstance znajduje się na innej maszynie wirtualnej platformy Azure, upewnij się, że jest ono [zarejestrowane w tym magazynie](#registering-the-sql-vm) , a odpowiednie wystąpienie SQLInstance jest wyświetlane jako element objęty ochroną.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Następnie po prostu Przekaż odpowiedni punkt odzyskiwania, docelowe wystąpienie serwera SQL z prawą flagą, jak pokazano poniżej.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Przywracanie alternatywne z odrębnym punktem odzyskiwania

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Przywracanie alternatywne z punktem dziennika w czasie

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Ostatni obiekt konfiguracji punktu odzyskiwania uzyskany z polecenia cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS zawiera wszystkie informacje dotyczące przywracania i jest jak pokazano poniżej.

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

Można edytować pola przywróconej bazy danych, OverwriteWLIfpresent, norecoverymode i targetPhysicalPath. Uzyskaj więcej szczegółowych informacji na temat docelowych ścieżek plików, jak pokazano poniżej.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Ustaw odpowiednie właściwości PS jako wartości ciągów, jak pokazano poniżej.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> Upewnij się, że końcowy obiekt konfiguracji odzyskiwania ma wszystkie wymagane i odpowiednie wartości, ponieważ operacja przywracania będzie oparta na obiekcie konfiguracji.

### <a name="restore-with-relevant-configuration"></a>Przywróć z odpowiednią konfiguracją

Po uzyskaniu i sprawdzeniu odpowiedniego obiektu konfiguracji odzyskiwania Użyj polecenia cmdlet [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS, aby rozpocząć proces przywracania.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Operacja przywracania zwraca zadanie, które ma być śledzone.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Zarządzanie kopiami zapasowymi SQL

### <a name="on-demand-backup"></a>Kopia zapasowa na żądanie

Po włączeniu tworzenia kopii zapasowej bazy danych użytkownik może również wyzwolić kopię zapasową na żądanie dla bazy danych za pomocą polecenia cmdlet [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS. Poniższy przykład wyzwala pełną kopię zapasową bazy danych SQL z włączoną kompresją, a pełna kopia zapasowa powinna zostać zachowana przez 60 dni.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Polecenie kopii zapasowej ad hoc zwraca zadanie, które ma być śledzone.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Jeśli dane wyjściowe zostaną utracone lub chcesz uzyskać odpowiedni identyfikator zadania, [Pobierz listę zadań](#track-azure-backup-jobs) z usługi Azure Backup, a następnie Śledź ją i jej szczegóły.

### <a name="change-policy-for-backup-items"></a>Zmień zasady dla elementów kopii zapasowej

Użytkownik może zmodyfikować istniejące zasady lub zmienić zasady kopii zapasowej elementu z Policy1 na Policy2. Aby przełączyć zasady dla elementu kopii zapasowej, po prostu Pobierz odpowiednie zasady i wykonaj kopię zapasową, a następnie użyj polecenia [enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) z elementem kopii zapasowej jako parametru.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Polecenie czeka na zakończenie konfigurowania kopii zapasowej i zwraca następujące dane wyjściowe.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Ponowne rejestrowanie maszyn wirtualnych SQL

> [!WARNING]
> Pamiętaj o zapoznaniu się z tym [dokumentem](backup-sql-server-azure-troubleshoot.md#re-registration-failures) , aby zrozumieć objawy błędów i przyczyny przed próbą ponownego zarejestrowania

Aby wyzwolić ponowną rejestrację maszyny wirtualnej SQL, Pobierz odpowiedni kontener kopii zapasowych i przekaż go do polecenia cmdlet register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Zatrzymaj ochronę

#### <a name="retain-data"></a>Zachowaj dane

Jeśli użytkownik chce zatrzymać ochronę, może użyć polecenia cmdlet [disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Spowoduje to zatrzymanie zaplanowanych kopii zapasowych, ale dane, których kopia zapasowa została utworzona, dopóki nie będzie teraz zachowywane.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Aby całkowicie usunąć przechowywane dane kopii zapasowej w magazynie, po prostu Dodaj flagę "-RemoveRecoveryPoints"/Przełącz do [polecenia "Disable" ochrony](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Wyłącz ochronę autoprotection

W przypadku skonfigurowania autoochrony w wystąpieniu SQLInstance użytkownik może ją wyłączyć przy użyciu polecenia cmdlet [disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Wyrejestruj maszynę wirtualną SQL

Jeśli wszystkie baz danych programu SQL Server [nie są już chronione i nie istnieją żadne dane kopii zapasowej](#delete-backup-data), użytkownik może wyrejestrować maszynę wirtualną SQL z tego magazynu. Tylko użytkownik może chronić baz danych w innym magazynie. Aby wyrejestrować maszynę wirtualną SQL, użyj polecenia cmdlet [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Śledzenie zadań Azure Backup

Należy pamiętać, że Azure Backup śledzi tylko zadania wyzwalane przez użytkownika w usłudze Kopia zapasowa SQL. Zaplanowane kopie zapasowe (w tym kopie zapasowe dzienników) nie są widoczne w portalu/programie PowerShell. Jednak w przypadku niepowodzenia zaplanowanych zadań zostanie wygenerowany [alert dotyczący tworzenia kopii zapasowej](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) , który będzie wyświetlany w portalu. [Użyj Azure monitor](backup-azure-monitoring-use-azuremonitor.md) , aby śledzić wszystkie zaplanowane zadania i inne istotne informacje.

Użytkownicy mogą śledzić operacje wyzwalane przez użytkownika z identyfikatorem JobID, które są zwracane w [danych wyjściowych](#on-demand-backup) zadań asynchronicznych, takich jak kopia zapasowa. Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS, aby śledzić zadanie i jego szczegóły.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Aby uzyskać listę zadań ad hoc i ich Stanów z usługi Azure Backup, należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS. Poniższy przykład zwraca wszystkie zadania SQL w toku.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Aby anulować zadanie w toku, należy użyć polecenia cmdlet [stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>Zarządzanie zawsze włączonymi grupami dostępności SQL

W przypadku zawsze dostępnych grup dostępności SQL upewnij się, że [zarejestrowano wszystkie węzły](#registering-the-sql-vm) grupy dostępności (AG). Po zakończeniu rejestracji dla wszystkich węzłów obiekt grupy dostępności SQL jest logicznie tworzony w obszarze elementy objęte ochroną. Bazy danych w ramach programu SQL AG będą wyświetlane jako "SQLDatabase". Węzły będą wyświetlane jako wystąpienia autonomiczne, a domyślne bazy danych SQL znajdujące się w nich są również wyświetlane jako bazy danych SQL.

Załóżmy na przykład, że w przypadku usługi SQL AG istnieją dwa węzły: "SQL-Server-0" i "SQL-Server-1" oraz 1 SQL AG DB. Po zarejestrowaniu obu tych węzłów, jeśli użytkownik [wyświetla listę elementów podlegających ochronie](#fetching-sql-dbs), wyświetla następujące składniki

1. Typ elementu, który umożliwia ochronę obiektów SQL AG jako element sqlavailability
2. Typ elementu chronionego za pomocą bazy danych SQL w formacie SQLDatabase
3. SQL-Server-0 — typ elementu do ochrony jako SQLInstance
4. Typ elementu z ochroną SQL-Server-1 jako SQLInstance
5. Wszystkie domyślne baz danych języka SQL (Master, model, msdb) w ramach typu "SQL-Server-0" objętego ochroną jako SQLDatabase
6. Wszystkie domyślne baz danych języka SQL (Master, model, msdb) w ramach typu "SQL-Server-1" objętego ochroną jako SQLDatabase

SQL-Server-0, SQL-Server-1 również będzie wyświetlana jako "AzureVMAppContainer", gdy [są wyświetlane kontenery kopii zapasowych](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Po prostu pobierz odpowiednią bazę danych SQL, aby [włączyć tworzenie kopii zapasowych](#configuring-backup) i [wykonywanie kopii zapasowych](#on-demand-backup) i [przywracanie](#restore-sql-dbs) danych ad hoc są identyczne.
