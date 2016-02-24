<properties
    pageTitle="PowerShell を使用した Azure VM のバックアップのデプロイおよび管理 | Microsoft Azure"
    description="PowerShell を使用して Microsoft Azure Backup をデプロイおよび管理する手順の説明"
    services="backup"
    documentationCenter=""
    authors="aashishr"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload=「記憶域のバックアップ回復」
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2015"
    ms.author="aashishr";"trinadhk"/>


# PowerShell を使用した Azure VM のバックアップのデプロイおよび管理
この記事では、Azure の IaaS の VM をバックアップおよび回復するために Azure PowerShell を使用する方法を示します。

## 概念
取得 [Azure IaaS VM のバックアップに導入された](backup-azure-vms-introduction.md) Azure Backup のドキュメントにします。

> [AZURE.WARNING] 開始する前に、essentials をカバーすることを確認、 [の前提条件](backup-azure-vms-prepare.md) Azure Backup を使用するために必要な [制限](backup-azure-vms-prepare.md#limitations) 現在 VM のバックアップ ソリューションです。

PowerShell を効果的に使用するには、オブジェクトの階層および開始地点を理解しておく必要があります。

![オブジェクト階層](./media/backup-azure-vms-automation/object-hierarchy.png)

2 つの最も重要なフローは、VM の保護の有効化と、復旧ポイントからのデータの復元です。 この記事は、これらの 2 つのシナリオを実現するために、PowerShell コマンドレットの操作に熟達することを目的としています。


## セットアップと登録
開始するには

1. [最新の PowerShell のダウンロード](https://github.com/Azure/azure-powershell/releases) (必要な最小バージョン: 1.0.0)

2. 以下のコマンドを入力して、使用可能な Azure Backup の PowerShell コマンドレットを検索します。

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

PowerShell を使用して次のセットアップおよび登録タスクを自動化できます。

- バックアップ コンテナーを作成していること
- Microsoft Azure Backup サービスを使用した VM の登録

### バックアップ コンテナーを作成していること

> [AZURE.WARNING] お客様は、最初に Azure Backup を使用して、サブスクリプションで使用される Azure Backup プロバイダーを登録する必要があります。 これは、Register-AzureProvider -ProviderNamespace "Microsoft.Backup" コマンドを実行して行うことができます。

使用して新しいバックアップ資格情報コンテナーを作成することができます、 **新規 AzureRMBackupVault** 内線番号です。 バックアップ コンテナーは ARM リソースであるため、リソース グループ内に配置する必要があります。 管理者特権の Azure PowerShell コンソールで、次のコマンドを実行します。

```
PS C:\> New-AzureRMResourceGroup –Name “test-rg” –Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

使用して指定されたサブスクリプション内のすべてのバックアップ コンテナーの一覧を取得できる、 **Get AzureRMBackupVault** 内線番号です。

> [AZURE.NOTE] バックアップ資格情報コンテナー オブジェクトを変数に保存すると便利です。 コンテナー オブジェクトは、多くの Azure Backup コマンドレットの入力に必要です。


### VM の登録
Azure Backup のバックアップを構成するには、まず Azure Backup コンテナーにコンピューターまたは VM を登録します。  **レジスタ AzureRMBackupContainer** コマンドレット Azure IaaS 仮想マシンの入力情報を受け取り、指定した資格情報コンテナーに登録します。 登録操作は、Azure virtual machine とバックアップ コンテナーを関連付け、VM をバックアップのライフサイクルを通じて追跡します。

Azure Backup サービスを VM に登録する場合、最上位のコンテナー オブジェクトが作成されます。 通常コンテナーには、バックアップ可能な項目が複数ありますが、VM の場合、コンテナーのバックアップ項目は 1 つのみとなります。

```
PS C:\> $registerjob = Register-AzureRMBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## Azure VM のバックアップ

### 保護ポリシーの作成
VM のバックアップを開始する際、新しい保護ポリシーを作成する必要はありません。 コンテナーには、後ですぐに正しい詳細に編集できる、保護をすぐに有効にするために使用できる '既定のポリシー' が含まれます。 使用して、資格情報コンテナーで使用可能なポリシーの一覧を取得する、 **Get AzureRMBackupProtectionPolicy** コマンドレット。

```
PS C:\> Get-AzureRMBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [AZURE.NOTE] PowerShell で BackupTime フィールドのタイム ゾーンは UTC です。 ただし、Azure ポータルでバックアップ時刻が表示されるときには、UTC オフセットとローカル システムに合わせてタイムゾーンが調整されます。

バックアップ ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられています。 アイテム保持ポリシーには、Azure Backup で復旧ポイントを保持する期間が定義されています。  **新規 AzureRMBackupRetentionPolicy** コマンドレットは、アイテム保持ポリシー情報を保持する PowerShell オブジェクトを作成します。 入力としてこれらの保有期間ポリシー オブジェクトが使用される、 *新規 AzureRMBackupProtectionPolicy* コマンドレットと直接、または、 *有効にする AzureRMBackupProtection* 内線番号です。

バックアップ ポリシーには、アイテムのバックアップが実行されるタイミングと方法を定義します。  **新規 AzureRMBackupProtectionPolicy** コマンドレットは、バックアップ ポリシーの情報を保持する PowerShell オブジェクトを作成します。 バックアップ ポリシーがへの入力として使用される、 *有効にする AzureRMBackupProtection* 内線番号です。

```
PS C:\> $Daily = New-AzureRMBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRMBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy ($Daily) -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### 保護を有効にする
保護を有効にするには、同じコンテナーに属する、アイテムとポリシーの 2 つのオブジェクトが必要です。 ポリシーとアイテムの関連付けが完了すると、バックアップのワークフローが定義されたスケジュールで開始されます。

```
PS C:\> Get-AzureRMBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRMBackupItem | Enable-AzureRMBackupProtection -Policy $newpolicy
```

### 初回バックアップ
バックアップのスケジュールによって、アイテムの完全な初回コピーと以降のバックアップの増分コピーが行われます。 ただし、または一定の時間に発生する可能性にもすぐに最初のバックアップを強制する場合、使用、 **バックアップ AzureRMBackupItem** コマンドレット。

```
PS C:\> $container = Get-AzureRMBackupContainer -Vault $backupvault -type AzureVM -name "testvm"
PS C:\> $backupjob = Get-AzureRMBackupItem -Container $container | Backup-AzureRMBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [AZURE.NOTE] PowerShell で表示されるフィールドに StartTime と EndTime のタイム ゾーンは UTC です。 ただし、Azure ポータルで同様の情報が表示されるときには、ローカル システム クロックに合わせてタイムゾーンが調整されます。

### バックアップ ジョブの監視
Azure Backup で長時間実行される多くの操作は、ジョブとしてモデル化されています。 これにより、Azure ポータルを常に開いていなくても進捗を簡単に追跡できるようになります。

進行中のジョブの最新の状態を取得する、 **Get AzureRMBackupJob** 内線番号です。

```
PS C:\> $joblist = Get-AzureRMBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

これらのジョブにある不要な追加のコードの完了をポーリングではなくが使いやすく、 **待機 AzureRMBackupJob** 内線番号です。 スクリプトで使用する場合、ジョブが完了するまで、または指定したタイムアウト値に到達するまでコマンドレットは実行を停止します。

```
PS C:\> Wait-AzureRMBackupJob -Job $joblist[0] -Timeout 43200
```


## Azure VM の復元

バックアップ データを復元するには、バックアップ項目と復元する特定の時点のデータを保持する復元ポイントを特定する必要があります。 この情報は、コンテナーから顧客のアカウントにデータの復元を開始するために、Restore-AzureRMBackupItem コマンドレットに提供されます。

### VM の選択

バックアップする正しい項目を特定する PowerShell オブジェクトを取得するには、コンテナー内のコンテナーから開始し、次いでオブジェクト階層の上から下に進みます。 VM を表すコンテナーを選択するには、使用、 **Get AzureRMBackupContainer** コマンドレットおよびパイプを使用する、 **Get AzureRMBackupItem** 内線番号です。

```
PS C:\> $backupitem = Get-AzureRMBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRMBackupItem
```

### 復元ポイントの選択

使用して、バックアップ項目のすべての回復ポイントを表示できるようになりました、 **Get AzureRMBackupRecoveryPoint** コマンドレットを復元する回復ポイントを選択します。 通常のユーザーの選択、最新 *AppConsistent* 一覧にポイントします。

```
PS C:\> $rp =  Get-AzureRMBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

```$rp``` 変数は、選択したバックアップ アイテムの復旧ポイントの配列であり、時間の逆順に並べ替えられています。つまり、最新の復旧ポイントがインデックス 0 の位置になります。 標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。 例: ```$rp[0]``` は、最新の復旧ポイントを選択します。

### データの復元

Azure ポータルと Azure PowerShell を使用して実行する復元処理には決定的な違いがあります。 Powershell では、復元操作は、復元ポイントからディスクと構成情報を復元すると停止します。 仮想マシンは作成されません。

> [AZURE.WARNING] 復元 AzureRMBackupItem は、VM を作成できません。 指定したストレージ アカウントにディスクを復元するのみです。 これは、Azure ポータルの動作とは異なります。

```
PS C:\> $restorejob = Restore-AzureRMBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

復元の操作の使用方法の詳細を取得できます、 **Get AzureRMBackupJobDetails** 復元ジョブが完了したら、内線番号です。  *ErrorDetails* プロパティを VM を再構築するために必要な情報が表示されます。

```
PS C:\> $restorejob = Get-AzureRMBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRMBackupJobDetails -Job $restorejob
```

### VM の構築

復元したディスクから VM を構築するには、Azure の古い ServiceManager PowerShell コマンドレット、新しい Azure ResourceManager テンプレート、または Azure ポータルを使用して実行できます。 Azure ServiceManager コマンドレットを使用して、これを実行する簡単な例を示します。

```
 $properties  = $details.Properties

 $storageAccountName = $properties["TargetStorageAccountName"]
 $containerName = $properties["TargetContainerName"]
 $blobName = $properties["TargetBlobName"]

 $keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
 $storageAccountKey = $keys.Primary
 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


 $destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
 Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


 $obj = [xml](Get-Content $destination_path)
 $pvr = $obj.PersistentVMRole
 $os = $pvr.OSVirtualHardDisk
 $dds = $pvr.DataVirtualHardDisks
 $osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
 $vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

 if (!($dds -eq $null))
 {
     foreach($d in $dds.DataVirtualHardDisk)
     {
         $lun = 0;
         if(!($d.Lun -eq $null))
         {
             $lun = $d.Lun
         }
         $name = "panbhadataDisk" + $lun
     Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
     $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
    }
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

復元したディスクから VM を構築する方法の詳細については、次のコマンドレットに関する説明を参照してください。

- [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
- [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
- [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## コード サンプル

### 1.ジョブのサブタスクの完了状態の取得

個々 のサブタスクの完了状態を追跡するために使用することができます、 **Get AzureRMBackupJobDetails** コマンドレット。

```
PS C:\> $details = Get-AzureRMBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### 2.バックアップ ジョブの日次/週次レポートの作成

通常、管理者は過去 24 時間に実行されたバックアップ ジョブと、それらのバックアップ ジョブの状態を知りたいと考えています。 また、転送されたデータの量によって、管理者は毎月のデータ使用量を推定できます。 次のスクリプトは、Azure Backup サービスから生データを取得し、PowerShell コンソールに情報を表示します。

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRMBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRMBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -type NoteProperty -name Date -value $startdate
        $newstatsobj | Add-Member -type NoteProperty -name VMName -value $job.WorkloadName
        $newstatsobj | Add-Member -type NoteProperty -name Duration -value $job.Duration
        $newstatsobj | Add-Member -type NoteProperty -name Status -value $job.Status

        $details = Get-AzureRMBackupJobDetails -Job $job
        $newstatsobj | Add-Member -type NoteProperty -name BackupSize -value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

このレポートの出力にグラフ作成機能を追加する場合から学ぶ TechNet ブログで [PowerShell を使用したグラフ作成](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

