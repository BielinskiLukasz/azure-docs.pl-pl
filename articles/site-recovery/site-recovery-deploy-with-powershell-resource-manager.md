<properties
    pageTitle="Azure PowerShell と Azure リソース マネージャーを使用して Azure にサーバーをレプリケートして保護する | Microsoft Azure"
    description="PowerShell と Azure リソース マネージャーを使用して Azure に対するサーバーの保護を Azure Site Recovery で自動化します。"
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="12/09/2015"
    ms.author="bsiva"/>


# PowerShell と Azure リソース マネージャーを使った Azure Site Recovery

## 概要

Azure Site Recovery は、さまざまなデプロイ シナリオでの仮想マシンのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。 シナリオの「展開の完全な一覧については、 [Azure Site Recovery の概要](site-recovery-overview.md)します。

Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。 これを 2 種類のモジュール (Azure Profile モジュールまたは Azure リソース マネージャー (ARM) モジュール) と共に使用できます。

Azure 内のサーバーに対する保護と復旧は、Azure PowerShell for ARM に用意されている Azure Site Recovery (ASR) の PowerShell コマンドレットを使って行うことができます。

この記事では、Windows PowerShell® と ARM を使って Azure Site Recovery をデプロイし、Azure に対してサーバーの保護を構成し、調整する方法を、例を交えながら説明します。 この記事で紹介している例は、Azure PowerShell と ARM を使って Hyper-V ホスト上の仮想マシンを保護し、Azure にフェールオーバーして復旧させる方法を示しています。
> [AZURE.NOTE] Azure Site Recovery PowerShell コマンドレットを使用すると、現在、VMM サイト対 VMM サイト、VMM サイト対 Azure、Hyper-V サイト対 Azure のシナリオを構成できます。 その他の ASR シナリオについても近々サポートされる予定です。 

この記事の内容を使用するには PowerShell の専門家である必要はありませんが、モジュール、コマンドレット、セッションなどの基本的な概念を理解していることを前提としています。 Windows PowerShell の詳細については、次を参照してください。 [Windows PowerShell の概要](http://technet.microsoft.com/library/hh857337.aspx)します。
- 詳細について [Azure リソース マネージャーで Azure PowerShell を使用して](powershell-azure-resource-manager.md)します。


## 主要な機能

- Azure に対してサーバーをレプリケートして保護し、Azure IaaS(ARM) または Azure IaaS(クラシック) にフェールオーバーして復旧させる。
- クラウド ソリューション プロバイダー (CSP) プログラムに参加しているマイクロソフト パートナーは、顧客のサーバーの保護を、その CSP サブスクリプション (テナント サブスクリプション) に対して構成、管理することができます。

## 開始する前に

次の前提条件を満たしていることを確認してください。

- 必要があります、 [Microsoft Azure](http://azure.microsoft.com/) アカウントです。 必要があります、 [Microsoft Azure](http://azure.microsoft.com/) アカウントです。 まず、 [無料評価版](pricing/free-trial/)します。 さらに、知っておくことができます [Azure Site Recovery Manager の料金](http://azure.microsoft.com/pricing/details/site-recovery/)します。
- Azure PowerShell 1.0 が必要です。 今回のリリースとそのインストール方法については、を参照してください [Azure PowerShell 1.0。](http://azure.microsoft.com/)。
- 権が必要です、 [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) と [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) インストールされているモジュールです。 これらのモジュールの最新バージョンは、から取得できます [PowerShell ギャラリー](https://www.powershellgallery.com/)

この記事では、Azure Powershell と ARM を使ってサーバーの保護を構成、管理する方法とその例を紹介しています。 この記事で用いている例は、Hyper-V ホスト上で動作する仮想マシンを Azure にレプリケートして保護する方法を紹介したものであり、以下に示した前提条件もその例に固有のものです。 各種 ASR シナリオごとの詳細な要件については、そのシナリオに関連したドキュメントを参照してください。

- 1 つ以上の仮想マシンを含んだ Windows Server 2012 R2 を実行する Hyper-V ホストが必要です。
- Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続します。
- 保護する仮想マシンに準拠する必要があります [仮想マシンの前提条件](site-recovery-best-practices.md#virtual-machines)します。


## 手順 1: Azure アカウントにログインする

1. PowerShell コンソールを開いて次のコマンドを実行し、Azure アカウントにログインします。 コマンドレットを実行すると Web ページが表示され、アカウントの資格情報を入力するように求められます。

        Login-AzureRmAccount

    または、含めることも、アカウント資格情報へのパラメーターとして、 `ログイン AzureRmAccount` コマンドレットを使用して、 `-資格情報` パラメーター。

    CSP パートナーがテナントの代理としてサインインする場合は、その顧客をテナントとして指定する必要があります。該当するテナント ID またはテナントのプライマリ ドメイン名で指定してください。

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. 1 つのアカウントで複数のサブスクリプションを持つことができるため、アカウントで使用するサブスクリプションを関連付ける必要があります。

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  該当するサブスクリプションに対して Recovery Services 用と Site Recovery 用の Azure プロバイダーが登録され、使用できる状態になっていることを次のコマンドで確認します。

    - `Get AzureRmResourceProvider-ProviderNamespace Microsoft.RecoveryServices`
    -  `Get AzureRmResourceProvider-ProviderNamespace Microsoft.SiteRecovery`

    上の 2 つのコマンドの出力で RegistrationState が Registered に設定されている場合は、手順 2. に進んでください。 設定されていない場合は、登録されていないプロバイダーを該当するサブスクリプションに登録する必要があります。

    Site Recovery 用の Azure プロバイダーを登録するには、次のコマンドを実行します。

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery

    同様に、Recovery Services のコマンドレットを自分のサブスクリプションで初めて使用する場合、Recovery Services 用の Azure プロバイダーを登録する必要があります。 そのためにはまず、該当するサブスクリプションで Recovery Services プロバイダーへのアクセスを有効にする必要があります。次のコマンドを実行してください。

        Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

    >[AZURE.TIP] 上のコマンドが正常に実行されてから、サブスクリプションで Recovery Services プロバイダーにアクセスできるようになるまでに最大 1 時間かかる場合があります。 サブスクリプションを使用して、[復旧サービス プロバイダーを登録しようとしています、 `レジスタ AzureRmResourceProvider ProviderNamespace Microsoft.RecoveryServices` までの間のコマンドが失敗します。 その場合は 1 時間待ってから再試行してください。

    サブスクリプションで Recovery Services プロバイダーへのアクセスを有効にしたら、次のコマンドを実行して、サブスクリプションにプロバイダーを登録します。

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    正しく使用して、プロバイダーが登録されていることを確認、 `Get AzureRmResourceProvider ProviderNamespace Microsoft.RecoveryServices` と `Get AzureRmResourceProvider ProviderNamespace Microsoft.SiteRecovery` コマンド



## 手順 2: Recovery Services の資格情報コンテナーを設定する

1. 資格情報コンテナーの作成先となる ARM リソース グループを作成するか、既存のリソース グループを使用します。 新しい ARM リソース グループを作成するには、次のコマンドを使用します。

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    $ResourceGroupName 変数には、作成する ARM リソース グループの名前が格納され、$Geo 変数には、リソース グループの作成先となる Azure リージョン (例: ブラジル南部) が格納されます。

    ARM リソース グループの一覧を取得するには、サブスクリプションを使用して、 `Get AzureRmResourceGroup` コマンドレットです。

2. 次のコマンドを使用して、Azure Recovery Services の新しい資格情報コンテナーを作成します。

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResouceGroupName <string> -Location <string>

    使用して既存のコンテナーの一覧を取得する、 `Get AzureRmRecoveryServicesVault` コマンドレットです。

> [AZURE.NOTE] 使用してこのようなコンテナーの一覧を取得する従来のポータルまたは Azure サービス管理 PowerShell モジュールを使用して作成された ASR コンテナーに対して操作を実行する場合、 `Get AzureRmSiteRecoveryVault` コマンドレットです。 新たに実行するすべての操作について、Recovery Services の資格情報コンテナーを新しく作成することをお勧めします。 過去に作成された Site Recovery の資格情報コンテナーも引き続きサポートされますが、最新の機能が備わっていません。

## ステップ 3: コンテナー登録キーを生成する

1. 資格情報コンテナー登録キーを生成してダウンロードします。

        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -Path $path

2. ダウンロードした資格情報コンテナーの設定ファイルをインポートしてそのコンテキストを設定します。

        Import-AzureRmSiteRecoveryVaultSettingsFile -Path $path 


## 手順 4: Hyper-V サイトを作成し、そのサイト用に新しい資格情報コンテナー登録キーを生成する

1. 次のコマンドを使用して、新しい Hyper-V サイトを作成します。

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    このコマンドレットは ASR ジョブを起動してサイトを作成し、ASR ジョブ オブジェクトを返します。 ジョブの完了を待って、ジョブが正常に完了したことを確認します。

    Get-AzureRmSiteRecoveryJob コマンドレットを使用して、ASR ジョブ オブジェクトを取得し、現在のジョブ ステータスをチェックしてください。
2. サイトの登録キーを生成してダウンロードします。

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    ダウンロードしたキーを Hyper-V ホストにコピーします。 このキーは、Hyper-V ホストをサイトに登録するときに必要になります。


## 手順 5: Azure Site Recovery プロバイダーと Azure Recovery Services エージェントを Hyper-V ホストにインストールする

1. からプロバイダーの最新バージョンのインストーラーをダウンロード [ここ](https://aka.ms/downloaddra)
2. Hyper-V ホストでインストーラーを実行し、インストールの完了時に、そのまま登録手順に進みます。
3. サイト登録キーを求められたら、ダウンロードしたキーを指定し、サイトに対する Hyper-V ホストの登録を完了します。
4. 次のコマンドを使用して、Hyper-V ホストがサイトに登録されたことを確認します。

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname 


## 手順 6: レプリケーション ポリシーを作成して保護コンテナーに関連付ける

1. レプリケーション ポリシーを作成します。

     $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
     $PolicyName = “replicapolicy”
     $Recoverypoints = 6                 #specify the number of recovery points
     $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id 
    
     $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

 返されたジョブをチェックし、レプリケーション ポリシーが正常に作成されたことを確認します。
 >[AZURE.IMPORTANT] 指定するストレージ アカウントは、Recovery Services の資格情報コンテナーと同じ Azure リージョンに存在すること、また geo レプリケーションが有効になっていることが必要です。
 >
 > -保護されたマシンのフェールオーバーが Azure IaaS (クラシック) にコンピューターを回復指定した回復のストレージ アカウントが Azure Storage(Classic) の型の場合
 > -保護されたマシンのフェールオーバーがコンピューターに、Azure IaaS (ARM) を回復指定した回復のストレージ アカウントが Azure Storage(ARM) の型の場合

2. サイトに対応する保護コンテナーを取得します。

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer

3.  保護コンテナーとレプリケーション ポリシーの関連付けを開始します。

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    関連付けジョブの完了を待って、正常に完了したことを確認します。

## ステップ 7: 仮想マシンの保護を有効化する

1. 保護する VM に対応する保護エンティティを取得します。

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM 
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. 仮想マシンの保護を開始します。

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] 指定するストレージ アカウントは、Recovery Services の資格情報コンテナーと同じ Azure リージョンに存在すること、また geo レプリケーションが有効になっていることが必要です。
    >
    > -保護されたマシンのフェールオーバーが Azure IaaS (クラシック) にコンピューターを回復指定した回復のストレージ アカウントが Azure Storage(Classic) の型の場合
    > -保護されたマシンのフェールオーバーがコンピューターに、Azure IaaS (ARM) を回復指定した回復のストレージ アカウントが Azure Storage(ARM) の型の場合

   > 保護対象の VM に複数のディスクが接続されている場合は、OSDiskName パラメーターを使ってオペレーティング システム ディスクを指定する必要があります。

3. 初回レプリケーション後、仮想マシンが保護された状態になるまで待ちます。 その所要時間は、レプリケートするデータの量や Azure へのアップストリーム方向で利用できる帯域幅など、さまざまな要因によって決まります。VM が保護された状態になると、次のように、ジョブの State と StateDescription が更新されます。

     PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob
    
     PS C:\> $DRjob | Select-Object -ExpandProperty State
     Succeeded
    
     PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
     Completed

4. フェールオーバーの発生時に VM の NIC の接続先となる Azure ネットワークや VM ロール サイズなど、復旧のプロパティを更新します。

     PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"
    
     PS C:\> $VMFriendlyName = "Fabrikam-App"
    
     PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    
     PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name
    
     PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob
    
     PS C:\> $UpdateJob
    
    
     Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
     ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                        s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
     Type             : Microsoft.RecoveryServices/vaults/replicationJobs
     JobType          : UpdateVmProperties
     DisplayName      : Update the virtual machine
     ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
     State            : Succeeded
     StateDescription : Completed
     StartTime        : 10-12-2015 17:55:53 +00:00
     EndTime          : 10-12-2015 17:55:54 +00:00
     TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
     TargetObjectType : ProtectionEntity
     TargetObjectName : Fabrikam-App
     AllowedActions   : {Restart}
     Tasks            : {UpdateVmPropertiesTask}
     Errors           : {}




## ステップ 8: テスト フェールオーバーを実行する

1. テスト フェールオーバー ジョブを実行します。

     $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group
    
     $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer
    
     $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Azure にテスト VM が作成されていることを確認します (テスト フェールオーバー ジョブは、Azure にテスト VM を作成した後に中断されます。 次の手順に示すようにジョブを再開すると、作成されたアーティファクトがクリーンアップされてジョブが完了します)。

3. テスト フェールオーバーを完了します。

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob





