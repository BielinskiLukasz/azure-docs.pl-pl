---
title: Tworzenie kopii zapasowej systemu Windows Server na platformie Azure przy użyciu programu PowerShell
description: Dowiedz się, jak wdrażać i zarządzać usługi Azure Backup przy użyciu programu PowerShell
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: saurse
ms.openlocfilehash: 154d4fbb265a6abb57bc25b4e364983ffe7e399b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237761"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Wdrażanie kopii zapasowych systemu Windows Server/Windows Client na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell
W tym artykule przedstawiono sposób konfigurowania usługi Azure Backup w systemie Windows Server lub klienta Windows oraz zarządzania nimi i odzyskiwania kopii zapasowych przy użyciu programu PowerShell.

## <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Ten artykuł koncentruje się na usłudze Azure Resource Manager (ARM) i poleceń cmdlet programu PowerShell kopii zapasowej Online MS, które umożliwiają korzystanie z magazynu usługi Recovery Services w grupie zasobów.

W października 2015 r. programu Azure PowerShell 1.0 został wydany. W tej wersji 0.9.8 zakończyło się pomyślnie wydawania oraz temat wprowadzono istotne zmiany, szczególnie w przypadku wzorca nazewnictwa z poleceń cmdlet. Polecenia cmdlet 1.0 mają wzorzec nazewnictwa {czasownik}-AzureRm{rzeczownik}; natomiast nazwy 0.9.8 nie zawierają elementu **Rm** (np. New-AzureRmResourceGroup zamiast New-AzureResourceGroup). W przypadku korzystania z programu Azure PowerShell 0.9.8 trzeba najpierw włączyć tryb usługi Resource Manager przez uruchomienie polecenia **Switch-AzureMode AzureResourceManager**. To polecenie nie jest konieczne w wersji 1.0 lub nowszej.

Jeśli chcesz użyć w skryptach napisanych dla 0.9.8 środowiska, w środowisku w wersji 1.0 lub nowszej, należy starannie aktualizacji i przetestować skrypty w środowisku przedprodukcyjnym przed ich użyciem w środowisku produkcyjnym, aby uniknąć nieoczekiwanych wpływu.

[Pobieranie najnowszej wersji programu PowerShell](https://github.com/Azure/azure-powershell/releases) (minimalna wymagana wersja to: 1.0.0)

[!INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
Następujące kroki przeprowadzą Cię przez proces tworzenia magazynu usługi Recovery Services. Magazyn usługi Recovery Services jest inny niż magazynu kopii zapasowych.

1. Jeśli używasz usługi Azure Backup po raz pierwszy, musisz użyć **Register-AzureRMResourceProvider** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Magazyn usługi Recovery Services jest zasobu usługi ARM, dlatego należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub Utwórz nową. Podczas tworzenia nowej grupy zasobów, określ nazwę i lokalizację grupy zasobów.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "WestUS"
    ```
3. Użyj **New-AzureRmRecoveryServicesVault** polecenia cmdlet, aby utworzyć nowy magazyn. Pamiętaj określić lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```
4. Określenie typu nadmiarowości magazynu, które mają być używane; Możesz użyć [magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). Poniższy przykład pokazuje, że ustawiono opcję - BackupStorageRedundancy testVault GeoRedundant.

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji
Użyj **Get-AzureRmRecoveryServicesVault** Aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Można użyć tego polecenia, aby sprawdzić, czy został utworzony nowy magazyn lub aby zobaczyć, jakie magazyny są dostępne w ramach subskrypcji.

Uruchom polecenie **Get-AzureRmRecoveryServicesVault**, i są wymienione wszystkie magazyny w ramach subskrypcji.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalowanie agenta usługi Azure Backup
Przed zainstalowaniem agenta usługi Azure Backup, musisz mieć Instalatora pobrane i są obecne w systemie Windows Server. Możesz pobrać najnowszą wersję Instalatora z [Microsoft Download Center](https://aka.ms/azurebackup_agent) lub ze strony pulpitu nawigacyjnego magazynu usługi Recovery Services. Zapisanie Instalatora, aby łatwo dostępnej lokalizacji, takich jak * C:\Downloads\*.

Można również użyć programu PowerShell można pobrać narzędzie do pobierania:
 
 ```
 $MarsAURL = 'Http://Aka.Ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Aby zainstalować agenta, uruchom następujące polecenie w konsoli programu PowerShell z podwyższonym poziomem uprawnień:

```
PS C:\> MARSAgentInstaller.exe /q
```

Spowoduje to zainstalowanie agenta z opcjami domyślnymi. Instalacja potrwa kilka minut w tle. Jeśli nie określisz */nu* opcji, a następnie **Windows Update** zostanie otwarte okno po zakończeniu instalacji sprawdź, czy jakiekolwiek aktualizacje. Po zainstalowaniu agenta pojawi się na liście zainstalowanych programów.

Aby wyświetlić listę zainstalowanych programów, przejdź do **Panelu sterowania** > **programy** > **programy i funkcje**.

![Agent jest zainstalowany](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opcje instalacji
Aby wyświetlić wszystkie opcje dostępne za pośrednictwem wiersza polecenia, użyj następującego polecenia:

```
PS C:\> MARSAgentInstaller.exe /?
```

Dostępne opcje to:

| Opcja | Szczegóły | Domyślne |
| --- | --- | --- |
| /q |Instalację cichą |- |
| / p: "Lokalizacja" |Ścieżka do folderu instalacji agenta usługi Azure Backup. |Agent usług C:\Program Files\Microsoft Azure Recovery Services |
| / s: "Lokalizacja" |Ścieżka do folderu pamięci podręcznej dla agenta usługi Kopia zapasowa Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Zoptymalizowany pod kątem w usłudze Microsoft Update |- |
| /nu |Nie sprawdzaj aktualizacje po ukończeniu instalacji |- |
| /d |Odinstalowuje agenta usług odzyskiwania Microsoft Azure |- |
| /pH |Adres hosta proxy |- |
| /Po |Numer portu hosta serwera proxy |- |
| /pu |Nazwa użytkownika hosta serwera proxy |- |
| /PW |Hasło serwera proxy |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Rejestracja systemu Windows Server lub Windows komputer kliencki do magazynu usługi Recovery Services
Po utworzeniu magazynu usługi Recovery Services, Pobierz najnowszą wersję agenta i poświadczenia magazynu i zapisz go w dogodnym miejscu, takich jak C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
```

W systemie Windows Server lub Windows, komputer kliencki, uruchom [Start OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) polecenia cmdlet, aby zarejestrować maszynę w magazynie.
Są to i inne polecenia cmdlet używane do tworzenia kopii zapasowej z modułu MSONLINE, który Mars AgentInstaller dodany jako część procesu instalacji. 

Instalatora agenta nie powoduje aktualizacji $Env: PSModulePath zmiennej. Oznacza to, że automatyczne ładowanie modułu zakończy się niepowodzeniem. Aby rozwiązać ten problem można wykonaj następujące czynności:

```
PS C:\>  $Env:psmodulepath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Alternatywnie można ręcznie załadować moduł w skrypcie w następujący sposób:

```
PS C:\>  Import-Module  'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'

```

Po załadowaniu polecenia cmdlet kopii zapasowych Online, zarejestruj się poświadczenia magazynu:


```

PS C:\> Start-OBRegistration -VaultCredentials $credsfilename.FilePath -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Aby określić plik poświadczeń magazynu nie należy używać ścieżek względnych. Jako dane wejściowe do polecenia cmdlet, należy podać ścieżkę bezwzględną.
>
>

## <a name="networking-settings"></a>Ustawienia sieci
W przypadku łączności maszyny Windows do Internetu za pośrednictwem serwera proxy, ustawienia serwera proxy może być również dostarczona do agenta. W tym przykładzie istnieje żaden serwer proxy, dzięki czemu możemy są jawnie wyczyścić wszystkie informacje dotyczące serwera proxy.

Można także kontrolować użycie przepustowości z opcjami ```work hour bandwidth``` i ```non-work hour bandwidth``` dla danego zestawu dni tygodnia.

Szczegóły serwera proxy i przepustowość ustawienia jest wykonywane przy użyciu [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) polecenia cmdlet:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Ustawienia szyfrowania
Wysyłane do usługi Azure Backup dane kopii zapasowej jest szyfrowany do ochrony poufności danych. Hasło szyfrowania jest "password" do odszyfrowania danych w czasie przywracania.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
PS C:\> $PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force 
PS C:\> $PassCode   = 'AzureR0ckx'
PS C:\> Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
Server properties updated successfully
```

> [!IMPORTANT]
> Zachowaj informacje hasła bezpieczny, po ustawieniu. Nie można przywrócić dane z platformy Azure, bez tego hasła.
>
>

## <a name="back-up-files-and-folders"></a>Tworzenie kopii zapasowych plików i folderów
Wszystkie kopie zapasowe z klientami i serwerów Windows w usłudze Azure Backup są zarządzane przez zasady. Zasady składa się z trzech części:

1. A **harmonogram tworzenia kopii zapasowych** , który określa podczas tworzenia kopii zapasowych muszą być wykonane i synchronizowane z usługą.
2. A **harmonogram przechowywania** określająca czas przechowywania punktów odzyskiwania na platformie Azure.
3. A **Specyfikacja uwzględniania/wykluczania plików** które nakazują, co powinna zostać utworzona kopia zapasowa.

W tym dokumencie ponieważ firma Microsoft automatyzujesz kopii zapasowej, firma Microsoft będzie przyjęto założenie, że nic nie został skonfigurowany. Zaczniemy, tworząc nowe zasady tworzenia kopii zapasowej, używając [New OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) polecenia cmdlet.

```
PS C:\> $newpolicy = New-OBPolicy
```

W tej chwili zasady jest pusta, i inne polecenia cmdlet są wymagane do zdefiniowania, jakie elementy będą dołączone lub wykluczone, kiedy kopii zapasowych zostanie wykonane, a w przypadku, gdy będą przechowywane kopie zapasowe.

### <a name="configuring-the-backup-schedule"></a>Konfigurowanie harmonogramu tworzenia kopii zapasowych
Pierwsza z 3 części zasad jest harmonogram tworzenia kopii zapasowych, który jest tworzony przy użyciu [New OBSchedule](https://technet.microsoft.com/library/hh770401) polecenia cmdlet. Harmonogram tworzenia kopii zapasowych określa, kiedy należy wykonać kopie zapasowe. Podczas tworzenia harmonogramu, należy określić parametry wejściowe 2:

* **Dni tygodnia** uruchamianą kopii zapasowej. Można uruchomić zadania tworzenia kopii zapasowej na jeden dzień lub każdego dnia, tygodnia lub dowolnej kombinacji pomiędzy.
* **Porach dnia** uruchamiania kopii zapasowej. Można zdefiniować maksymalnie 3 różnych porach dnia, gdy zostanie wyzwolony kopii zapasowej.

Na przykład można skonfigurować zasad kopii zapasowych, obsługująca 16: 00 każdego soboty i niedziele.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

Harmonogram tworzenia kopii zapasowych musi być skojarzone z zasadami i można to osiągnąć przy użyciu [OBSchedule zestaw](https://technet.microsoft.com/library/hh770407) polecenia cmdlet.

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Konfigurowanie zasad przechowywania
Zasady przechowywania Określa, ile punktów odzyskiwania tworzonych na podstawie zadania tworzenia kopii zapasowej zostaną zachowane. Podczas tworzenia nowych zasad przechowywania, przy użyciu [New OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) polecenia cmdlet, można określić liczbę dni, które punkty odzyskiwania kopii zapasowych muszą być przechowywane w usłudze Azure Backup. Poniższy przykład ustawia zasady przechowywania, 7 dni.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

Zasady przechowywania musi być skojarzony z zasadami głównej, przy użyciu polecenia cmdlet [OBRetentionPolicy zestaw](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### <a name="including-and-excluding-files-to-be-backed-up"></a>Uwzględnianie i wykluczanie plików do wykonania kopii zapasowej
```OBFileSpec``` Obiektu definiuje plików włączone i wyłączone w kopii zapasowej. Jest to zestaw reguł, które zakresu chronionych plików i folderów na komputerze. Może mieć wiele plików dołączania lub wykluczania reguły, zgodnie z wymaganiami i skojarzenie ich z zasadami. Podczas tworzenia nowego obiektu OBFileSpec, możesz wykonywać następujące czynności:

* Określ pliki i foldery do uwzględnienia
* Określ pliki i foldery do wykluczenia
* Określ cyklicznego tworzenia kopii zapasowych danych w folderze (lub) czy tylko najwyższego poziomu pliki we wskazanym folderze należy utworzyć kopię zapasową.

Drugim jest osiągane przy użyciu flagi - nierekurencyjnego w poleceniu New-OBFileSpec.

W poniższym przykładzie możemy utworzyć kopię zapasową woluminu C: i D: i wykluczanie plików binarnych systemu operacyjnego Windows i w folderze żadnych folderów tymczasowych. W tym celu utworzymy dwóch plików przy użyciu specyfikacji [New OBFileSpec](https://technet.microsoft.com/library/hh770408) polecenia cmdlet: jeden dla dołączania i jeden do wykluczenia. Po utworzeniu ze specyfikacjami plików, są one skojarzone z zasadami, za pomocą [OBFileSpec Dodaj](https://technet.microsoft.com/library/hh770424) polecenia cmdlet.

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Zastosowanie zasad
Teraz obiektu zasad jest ukończona i ma skojarzone harmonogram tworzenia kopii zapasowych, zasady przechowywania i uwzględniania/wykluczania listę plików. Te zasady można teraz zatwierdzone dla usługi Azure Backup do użycia. Przed zastosowaniem nowo utworzone zasady upewnij się, że nie istnieją żadne z istniejących zasad tworzenia kopii zapasowej skojarzony z serwerem przy użyciu [OBPolicy Usuń](https://technet.microsoft.com/library/hh770415) polecenia cmdlet. Usunięcie zasad wyświetli monit o potwierdzenie. Aby pominąć użycie potwierdzenia ```-Confirm:$false``` flagę za pomocą polecenia cmdlet.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Zatwierdzanie obiektu zasad jest wykonywane przy użyciu [OBPolicy zestaw](https://technet.microsoft.com/library/hh770421) polecenia cmdlet. Spowoduje to również monituje o potwierdzenie. Aby pominąć użycie potwierdzenia ```-Confirm:$false``` flagę za pomocą polecenia cmdlet.

```
PS C:> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Można wyświetlić szczegółowe informacje o istniejących zasad tworzenia kopii zapasowej przy użyciu [Get OBPolicy](https://technet.microsoft.com/library/hh770406) polecenia cmdlet. Możesz przejść za pomocą [Get OBSchedule](https://technet.microsoft.com/library/hh770423) polecenie cmdlet służące do harmonogramu tworzenia kopii zapasowych i [Get OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) polecenia cmdlet dla zasad przechowywania

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-ad-hoc-backup"></a>Wykonywanie kopii zapasowej usługi ad-hoc
Gdy ustawiono zasad tworzenia kopii zapasowej kopie zapasowe zostanie przeprowadzona na harmonogram. Wyzwolenie tworzenia kopii zapasowej ad-hoc jest także możliwe przy użyciu [Start OBBackup](https://technet.microsoft.com/library/hh770426) polecenia cmdlet:

```
PS C:> Get-OBPolicy | Start-OBBackup
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Przywróć dane z usługi Azure Backup
Ta sekcja przeprowadzi Cię przez kroki do automatyzowania odzyskiwanie danych z usługi Azure Backup. Ten sposób obejmuje następujące czynności:

1. Wybierz wolumin źródłowy
2. Wybieranie punktu kopii zapasowej do przywrócenia
3. Wybierz element do przywrócenia
4. Wyzwolenie procesu przywracania

### <a name="picking-the-source-volume"></a>Pobrania woluminu źródłowego
Aby przywrócić elementu z usługi Azure Backup, należy najpierw zidentyfikować źródło elementu. Ponieważ firma Microsoft jest wykonywanie polecenia w kontekście systemu Windows Server lub klienta Windows, komputer jest już zidentyfikowany. Następnym krokiem w identyfikacji źródła jest do identyfikowania wolumin zawierający go. Listę woluminów lub źródeł, w których powstaje kopia zapasowa z tego komputera mogą być pobierane, wykonując [Get OBRecoverableSource](https://technet.microsoft.com/library/hh770410) polecenia cmdlet. To polecenie zwraca tablicę wszystkich źródeł kopię zapasową z tego serwera/klienta.

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Wybieranie punktu kopii zapasowej, z którym mają zostać przywrócone
Pobierz listę punktów kopii zapasowej, wykonując [Get OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) polecenia cmdlet z odpowiednimi parametrami. W tym przykładzie Wybraliśmy najnowszego punktu kopii zapasowej dla woluminu źródłowego *D:* i użyć go do odzyskania określonego pliku.

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```
Obiekt ```$rps``` jest tablicą punktów kopii zapasowej. Pierwszy element jest najnowszy punkt i n-tego elementu jest najstarszy punkt. Aby wybrać najnowszego punktu, użyjemy ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Wybranie elementu do przywrócenia
Aby zidentyfikować dokładną plik lub folder do przywrócenia cyklicznie, użycia [Get OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) polecenia cmdlet. W ten sposób, w hierarchii folderów można przeglądać wyłącznie przy użyciu ```Get-OBRecoverableItem```.

W tym przykładzie chcemy przywrócić plik *finances.xls* firma Microsoft może odwoływać się przy użyciu obiektu ```$filesFolders[1]```.

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Możesz również wyszukać elementy, aby przywrócić dane przy użyciu ```Get-OBRecoverableItem``` polecenia cmdlet. W tym przykładzie, aby wyszukać *finances.xls* firma Microsoft można uzyskać dojścia do pliku, uruchamiając następujące polecenie:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Wyzwolenie procesu przywracania
Aby wyzwolić procesu przywracania, najpierw należy Określ opcje odzyskiwania. Można to zrobić za pomocą [New OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) polecenia cmdlet. W tym przykładzie załóżmy, że chcemy przywrócić pliki do *C:\temp*. Załóżmy również, że chcemy pominąć pliki, które już istnieją w folderze docelowym *C:\temp*. Aby utworzyć opcję odzyskiwania, użyj następującego polecenia:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Teraz wyzwolić proces przywracania przy użyciu [Start OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) polecenia na wybranym ```$item``` z danych wyjściowych ```Get-OBRecoverableItem``` polecenia cmdlet:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Odinstalowywanie programu agent usługi Kopia zapasowa Azure
Odinstalowywanie agenta usługi Azure Backup może odbywać się przy użyciu następującego polecenia:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

Odinstalowywanie plików binarnych agenta na komputerze ma pewne skutki wziąć pod uwagę:

* Usuwa filtr plików z komputera i śledzenie zmian jest zatrzymana.
* Wszystkie informacje o zasadach jest usuwany z komputera, ale informacje o zasadach nadal mają być przechowywane w usłudze.
* Wszystkie harmonogramy tworzenia kopii zapasowych zostaną usunięte, a żadne dodatkowe kopie zapasowe są wykonywane.

Jednak dane przechowywane w pozostaje platformy Azure i są przechowywane zgodnie z ustawień zasad przechowywania przez użytkownika. Starsze punkty automatycznie są przestarzałe w.

## <a name="remote-management"></a>Zdalne zarządzanie
Całe zarządzanie całym agenta usługi Azure Backup, zasady i źródłami danych mogą to robić zdalnie za pomocą programu PowerShell. Komputer, na którym będzie można zarządzać zdalnie, musi być przygotowane poprawnie.

Domyślnie Usługa WinRM jest skonfigurowana do uruchamiania ręcznego. Typ uruchamiania musi być równa *automatyczne* i można uruchomić usługi. Aby sprawdzić, czy Usługa WinRM jest uruchomiona, wartość właściwości stanu powinien być *systemem*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Program PowerShell, należy skonfigurować dla niego komunikację zdalną.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

Maszyny można teraz zarządzać zdalnie — począwszy od instalacji agenta. Na przykład poniższy skrypt kopiuje agenta na maszynie zdalnej i instaluje go.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat usługi Azure Backup dla Windows Server/kliencie, zobacz

* [Wprowadzenie do usługi Azure Backup](backup-introduction-to-azure-backup.md)
* [Tworzenie kopii zapasowej serwerów Windows](backup-configure-vault.md)
