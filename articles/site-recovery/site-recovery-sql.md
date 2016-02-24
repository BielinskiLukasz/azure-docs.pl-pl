<properties 
    pageTitle="SQL Server 障害復旧および Azure Site Recovery を使用した SQL Server の保護 | Microsoft Azure" 
    description="Azure Site Recovery は、セカンダリ オンプレミス サイトまたは Azure への SQL Server のレプリケーション、フェールオーバー、および復旧を調整します。" 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor="tysonn"/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/14/2015" 
    ms.author="raynew"/>


# SQL Server 障害復旧および Azure Site Recovery を使用した SQL Server の保護 

Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、および復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略を支援する Azure サービスです。 Site Recovery では、いくつかのレプリケーション メカニズムをサポートし、常にマシンを Azure またはセカンダリ データセンターで保護し、Azure またはセカンダリ データセンターにレプリケートおよびフェールオーバーします。 内のすべての展開シナリオの概要、 [Azure Site Recovery の概要](site-recovery-overview.md)します。

 この記事では、SQL Server の BCDR テクノロジおよび Site Recovery の組み合わせを使用してアプリケーションの SQL Server バックエンドを保護する方法について説明します。 この記事で説明するシナリオをデプロイするには、SQL Server の BCDR 機能 (フェールオーバー クラスタリング、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布) および Site Recovery を十分に理解する必要があります。



## 概要

多くのワークロードは、SQL Server を基盤として使用します。 SharePoint、Dynamics、および SAP などのアプリケーションは、データ サービスを実装するのに SQL Server を使用します。 SQL Server 可用性グループなどの SQL Server の高可用性と障害復旧機能は、SQL Server データベースを保護し、復旧するために使用します。 アプリケーションでは、次の構成で SQL Server がデプロイされます


1. スタンドアロンの SQL Server: SQL Server とすべてのデータベースは、1 台のコンピューター (物理または仮想マシン) にホストされます。 仮想化される際には、ローカルの高可用性 (HA) にホスト クラスタリングが使用されます。ゲスト レベルの HA は実装されていません。 
2.  SQL Server フェールオーバー クラスタリング インスタンス (または、常時接続 FCI): この設定では、Windows フェールオーバー クラスターに、共有ディスクが使用された SQL Server インスタンスのノードを 2 つ以上構成します。 いずれかの SQL フェールオーバー クラスター インスタンスがダウンしている場合は、クラスターは SQL を別のインスタンスにフェールオーバーさせることができます。 この設定は、通常プライマリ サイトの HA で使用されます。 これは、共有記憶域層の障害や停止は保護しません。 共有ディスクは、ISCSI、ファイバー チャネル、または共有 VHDx を使用して実装できます。
3.  SQL Always ON 可用性グループ: 2 つのノードをシェアード ナッシング クラスターに設定します。このクラスターでは、同期レプリケーションと自動フェールオーバーを設定した可用性グループに SQL データベースを構成します。

SQL Server では、Enterprise エディションで、リモート サイトにデータベースを復元する復旧テクノロジもネイティブに提供しています。 該当する場合、Azure Site Recovery を使用した障害復旧計画の構築に、次のネイティブの SQL 障害復旧テクノロジを利用したり、統合したりすることができます。


1. Disaster Recovery for SQL 2012 または 2014 Enterprise の各エディションの SQL Always On 可用性グループ 
2.  SQL Server Standard (全バージョン) または SQL Server 2008 R2 用の高い安全性モードでの SQL Database ミラーリング



Site Recovery は、Hyper-V 仮想マシン、VMware 仮想マシン、または物理サーバーとして実行される SQL Server を保護できます。

 |**オンプレミスからオンプレミス** | **オンプレミスと Azure 間** 
---|---|---
**Hyper V** |[はい |[はい]
**VMware** |[はい |[はい] 
**物理サーバー** |[はい |[はい]


## サポートと統合

この記事に該当する SQL Server のエディションとバージョンは次のとおりです。


- SQL Server 2014 Enterprise および Standard
- SQL Server 2012 Enterprise および Standard
- SQL Server 2008 R2 Enterprise および Standard


Site Recovery は、次の表に要約したネイティブの SQL Server の BCDR テクノロジと統合して、障害復旧ソリューションを提供できます。

**機能** |**詳細** | **SQL Server のバージョン** 
---|---|---
**AlwaysOn 可用性グループ** | <p>SQL Server の複数のスタンドアロン インスタンスであり、複数のノードを持つフェールオーバー クラスターでそれぞれが実行されます。</p> <p>データベースは、SQL Server インスタンス上でコピー (ミラー化) が可能なフェールオーバー グループにグループ化できるため、共有記憶域は必要ありません</p> <p>プライマリ サイトと 1 つまたは複数のセカンダリ サイトの間で障害復旧を実現します。 2 つのノードをシェアード ナッシング クラスターに設定できます。このクラスターでは、同期レプリケーションと自動フェールオーバーを設定した可用性グループに SQL Server データベースを構成しておきます。</p> | SQL Server 2014/2012 Enterprise エディション
**フェールオーバー クラスタリング (AlwaysOn FCI)** | <p>SQL Server は、Windows のフェールオーバー クラスタリングを利用して、オンプレミスの SQL Server ワークロードの高可用性を実現しています。</p><p>共有ディスクを備えた、SQL Server のインスタンスを実行しているノードは、フェールオーバー クラスター内に構成されます。 インスタンスがダウンした場合、クラスターは別のクラスターにフェールオーバーします。</p> <p>クラスターは、共有記憶域のエラーまたは障害からは保護しません。 共有ディスクは、iSCSI、ファイバー チャネル、または共有 VHDX を使用して実装できます。</p> | SQL Server Enterprise エディション</p> <p>SQL Server Standard エディション (2 つのノードのみに制限)
**データベース ミラーリング (高い安全性モード)** | 1 つのセカンダリ コピーで 1 つのデータベースを保護します。 高い安全性 (同期) と高パフォーマンス (非同期) の両方のレプリケーション モードで使用できます。 フェールオーバー クラスターは必要はありません。 | <p>SQL Server 2008 R2</p><p>SQL Server Enterprise のすべてのエディション</p>
**スタンドアロンの SQL Server** | SQL Server とデータベースは、単一のサーバー (物理または仮想) でホストされます。 サーバーが仮想である場合、ホスト クラスタリングが高可用性のために使用されます。 ゲストレベルの高可用性はありません。 | Enterprise または Standard エディション




次の表は、Site Recovery デプロイメントに SQL Server の BCDR テクノロジを統合するための推奨事項を示しています。

**バージョン** |**エディション** | **デプロイ** | **オンプレミスからオンプレミス** | **オンプレミスから Azure** 
---|---|---|---|---
SQL Server 2014 または 2012 | Enterprise | フェールオーバー クラスター インスタンス | AlwaysOn 可用性グループ | AlwaysOn 可用性グループ
 | Enterprise | 高可用性のための AlwaysOn 可用性グループ | AlwaysOn 可用性グループ | AlwaysOn 可用性グループ
 | Standard | フェールオーバー クラスター インスタンス (FCI) | ローカルのミラーを使用した Site Recovery レプリケーション | ローカルのミラーを使用した Site Recovery レプリケーション
 | Enterprise または Standard | スタンドアロン | Site Recovery レプリケーション | Site Recovery レプリケーション 
SQL Server 2008 R2 | Enterprise または Standard | フェールオーバー クラスター インスタンス (FCI) | ローカルのミラーを使用した Site Recovery レプリケーション | ローカルのミラーを使用した Site Recovery レプリケーション
 | Enterprise または Standard | スタンドアロン | Site Recovery レプリケーション | Site Recovery レプリケーション
SQL Server (全バージョン) | Enterprise または Standard | フェールオーバー クラスター インスタンス - DTC アプリケーション | Site Recovery レプリケーション | サポートされていません

## デプロイメントの前提条件

ここでは、開始する前に必要な項目を示します。


- サポートされている SQL Server のバージョンを実行しているオンプレミスの SQL Server デプロイメント 通常、SQL Server には Active Directory も必要です。
- デプロイするシナリオの前提条件。 前提条件は、各デプロイメントの記事に記載されています。 これら一覧しなっていてでリンクされている、 [Site Recovery の概要](site-recovery-overview.md)します。
- Azure での復旧を設定する場合は、実行する必要があります、 [Azure 仮想マシン準備状態評価](http://www.microsoft.com/download/details.aspx?id=40898) ツールで、SQL Server の仮想マシンを Azure および Site Recovery との互換性があるかどうかを確認します。


## AD の保護の設定

Active Directory を正しく実行する SQL Server のセカンダリ復旧サイトで必要になります。 2 つのオプションがあります。

- **小規模な企業**-少数のアプリケーションと内部設置型サイトの 1 つのドメイン コント ローラーがあり、サイト全体をフェールオーバーする場合、Site Recovery レプリケーションを使用して、セカンダリ データ センターまたは Azure にドメイン コント ローラーをレプリケートすることが勧めします。

- **中規模および大規模な企業**-追加のドメイン コント ローラーをセカンダリ データ センターまたは Azure を設定する場合があるアプリケーションの数が多く、Active Directory フォレストを実行しているアプリケーションまたはワークロード単位でフェールオーバーする、お勧めします。 AlwaysOn 可用性グループを使用してリモート サイトに復旧する場合は、もう 1 つ追加のドメイン コント ローラーをセカンダリ サイトまたは Azure にセットアップして、復旧した SQL Server インスタンスに対して使用することをお勧めします。

このドキュメントに記載された手順は、2 番目の拠点でドメイン コントローラーが使用できることを前提としています。  AD の災害復旧ソリューションのガイダンスを参照することができます [ここ](http://aka.ms/asr-ad)します。

## Azure への SQL AlwaysOn の統合

### オンプレミスと Azure 間

Azure Site Recovery (ASR) は SQL AlwaysOn をネイティブでサポートします。 Azure の仮想マシンを "セカンダリ" として設定した状態で SQL 可用性グループを作成した場合、ASR を使用して可用性グループのフェールオーバーを管理できます。 

この機能は現時点でプレビュー段階であり、プライマリ データセンターが System Center Virtual Machine Manager (VMM) で管理される際に使用可能です。 

#### VMM Server で管理される環境
ASR コンテナー内に入ると、[保護された項目] タブの下に SQL Servers のタブが表示されます。 

![保護された項目](./media/site-recovery-sql/protected-items.png)

ASR への SQL AlwaysON の統合の手順を以下に示します。

##### 前提条件
- スタンドアロン サーバーまたはフェールオーバー クラスターでのオンプレミスの SQL Server 
- SQL Server がインストールされている 1 つまたは複数の Azure Virtual Machines
- オンプレミスの SQL Server と Azure で実行されている SQL Server 間の SQL 可用性グループ セットアップ
- PowerShell リモート処理をオンプレミスの SQL Server で有効にする必要があります。 VMM Server から SQL Server へのリモート PowerShell 呼び出しが可能になっている必要があります。
- オンプレミスの SQL Server では、SQL ユーザー グループにユーザー アカウントを追加する際に、少なくとも次のアクセス権を付与する必要があります。
    - ALTER AVAILABILITY GROUP の [1 を参照して](https://msdn.microsoft.com/library/hh231018.aspx), 、[2 を参照](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER DATABASE - [1 を参照](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- そのアカウントの実行アカウントは事前に VMM Server 上で作成しておく必要があります。
- SQL PS モジュールをオンプレミスで実行している SQL Servers と Azure Virtual Machines 上にインストールする必要があります。
- Azure で実行している仮想マシン上に VM Agent をインストールする必要があります。
- Azure の仮想マシン上で実行している SQL Server でNTAUTHORITY\System に次のアクセス権が付与されている必要があります。
    - ALTER AVAILABILITY GROUP の [1 を参照して](https://msdn.microsoft.com/library/hh231018.aspx), 、[2 を参照](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER DATABASE - [1 を参照](https://msdn.microsoft.com/library/ff877956.aspx#Security)

##### 1.SQL Server の追加

[SQL の追加] をクリックして、新規の SQL Server を追加します。 

![Add SQL](./media/site-recovery-sql/add-sql.png)

SQL Server の管理に使用する SQL Server、VMM、資格情報を詳しく指定します。

![Add SQL Dialog](./media/site-recovery-sql/add-sql-dialog.png)

###### パラメーター
 - 名前: この SQL Server を表すフレンドリ名
 - SQL Server (FQDN): 追加するソースの SQL Server の完全修飾ドメイン名 (FQDN)。 SQL Server をフェールオーバー クラスターにインストールする場合、クラスターの FQDN を指定し、クラスター ノードの FQDN には指定しないでください。 
 - SQL Server インスタンス: デフォルトの SQL インスタンスを選択するか、カスタム SQL インスタンス名を指定します。
 - VMM Server: Azure Site Recovery (ASR) ですでに登録済みの VMM Server のいずれかを選択します。 ASR は SQL Server との通信にこの VMM Server を使用します。
 - 実行アカウント: 上で選択した VMM Server 上で作成した実行アカウント名のいずれかを指定します。 この実行アカウントは SQL Server へのアクセスに使用され、SQL Server の可用性グループで読み取りおよびフェールオーバーのアクセス権を持っている必要があります。 

SQL Server を追加すると、SQL Server タブの下に表示されるようになります。 

![SQL Server List](./media/site-recovery-sql/sql-server-list.png)

##### 2.SQL 可用性グループの追加

SQL Server を追加したら、次の手順は ASR への可用性グループの追加になります。 それを行うには、前の手順で追加した SQL Server の中をドリルダウンし、[SQL 可用性グループの追加] をクリックします。 

![Add SQL AG](./media/site-recovery-sql/add-sqlag.png)

SQL 可用性グループは Azure の 1 つまたは複数の仮想マシンへのレプリケートが可能です。 SQL 可用性グループの追加の際、ASR によって可用性グループをフェールオーバーさせる Azure 仮想マシンの名前およびサブスクリプションを指定する必要があります。

![Add SQL AG Dialog](./media/site-recovery-sql/add-sqlag-dialog.png)

上の例では、Availability Group (可用性グループ) DB1-AG がフェールオーバーの際に、サブスクリプション DevTesting2 内で実行している仮想マシン SQLAGVM2 でプライマリとなります。 

>[AZURE.NOTE] 前の手順で追加した SQL サーバー上のプライマリである可用性グループのみが ASR に追加できるようにします。 SQL Server に可用性グループ プライマリを作成した場合、もしくは SQL Server を追加したあとに SQL Server にさらに可用性グループを追加した場合は、SQL Server 上で使用可能な [更新] オプションで更新をかけてください。

#### 3.復旧計画の作成

次の手順は仮想マシンと可用性グループの両方を使用した復旧計画の作成です。 
手順 1 で使用した VMM Server と同じものをソースとして、Microsoft Azure をターゲットとして選択してください。

![復旧計画の作成](./media/site-recovery-sql/create-rp1.png)

![復旧計画の作成](./media/site-recovery-sql/create-rp2.png)

例では、Sharepoint アプリケーションはバックエンドに SQL 可用性グループを使用している 3 つの仮想マシンで構成されています。 この復旧計画では、アプリケーションを構成する可用性グループと仮想マシンの両方を選択できます。 

仮想マシンを他のフェールオーバー グループに移動して、フェールオーバーの実行順序を指定することで復旧計画をカスタマイズすることもできます。 可用性グループはどのアプリケーションでもバックエンドとして使用されるので、常に一番最初にフェールオーバーされます。 

![復旧計画のカスタマイズ](./media/site-recovery-sql/customize-rp.png)

#### 4.フェールオーバー

可用性グループを復旧計画に追加した後は、他のフェールオーバー オプションも使用可能になります。

##### 計画されたフェールオーバー

計画されたフェールオーバーとはデータ損失のないフェールオーバーを意味します。 そのためには、SQL 可用性グループの可用性モードを最初に "同期" に設定し、フェールオーバーがトリガーされることで、ASR に可用性グループを追加した際に指定した仮想マシンに対して可用性グループが "プライマリ" となります。 フェールオーバーが完了すると、可用性モードは計画されたフェールオーバーがトリガーされる前と同じ値に設定されます。 

##### 計画されていないフェールオーバー

計画されていないフェールオーバーではデータの損失が生じることがあります。 計画されていないフェールオーバーがトリガーされても、可用性グループの可用性モードは変更されず、ASR に可用性グループを追加した際に指定した仮想マシンに対して "プライマリ" となっています。 計画されていないフェールオーバーが完了すると、SQL Server で実行しているオンプレミスのサーバーが再び使用可能となり、レプリケーションの反転が可用性グループに対してトリガーされる必要があります。 この操作は復旧計画では使用できず、[SQL Servers] タブの下の SQL 可用性グループにて行われることに注意してください。

##### テスト フェールオーバー
SQL 可用性グループのテスト フェールオーバーはサポートされていません。 SQL 可用性グループを含む復旧計画のテスト フェールオーバーをトリガーしたとしても、可用性グループに対するフェールオーバーはスキップされます。

別の方法として、以下のオプションについて検討します。

###### 方法 1



1. アプリケーション層とフロントエンド層のテスト フェールオーバーを実行します。

2. 読み取り専用モードでレプリカのコピーにアクセスするようアプリケーション層を更新し、アプリケーションの読み取り専用テストを実行します。

###### 方法 2

1.  (サイト間の VMM の複製または Azure Backup を使用して) レプリカの SQL Server 仮想マシン インスタンスのコピーを作成し、テスト ネットワークで起動します。
2.  復旧計画を使用して、テスト フェールオーバーを実行します。

#### フェールバック

可用性グループをオンプレミスの SQL Server で再度 "プライマリ" にする場合、復旧計画で計画性フェールオーバーをトリガーし、方向を Microsoft Azure からオンプレミスの VMM Server に選択することで行うことができます。

#### 反転レプリケーション

計画されていないフェールオーバーの後、可用性グループで反転レプリケーションをトリガーしてレプリケーションを再開させる必要があります。 これが行われるまでは、レプリケーションが中断されたままです。


### VMM Server で管理されない環境

VMM Server で管理されない環境については、Azure Automation Runbooks を使用して、SQL 可用性グループのスクリプト化されたフェールオーバーを構成できます。 以下が構成手順です。


1.  可用性グループをフェールオーバーするスクリプトのローカル ファイルを作成します。 このサンプル スクリプトでは、Azure レプリカ上の可用性グループへのパスを指定し、そのレプリカ インスタンスに可用性グループをフェールオーバーします。 このスクリプトは、カスタム スクリプトの拡張を使用して可用性グループを渡すことによって、SQL Server レプリカ仮想マシン上で実行されます。



        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  スクリプトを Azure ストレージ アカウント内の BLOB にアップロードします。 次の例を使用します。

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  Azure の SQL Server レプリカ仮想マシンでスクリプトを呼び出す Azure Automation Runbook を作成します。 これを行うには、次のサンプル スクリプトを使用します。 [詳細については](site-recovery-runbook-automation.md) に関する復旧計画で automation の runbook を使用します。 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  アプリケーションの復旧計画を作成するときに、可用性グループをフェールオーバーする Automation Runbook を呼び出す「pre-Group 1 boot」というスクリプト化した手順を追加します。

###オンプレミス間
SQL Server が可用性グループまたはフェールオーバー クラスター インスタンスを使用して高可用性を実現している場合は、復旧サイトでも可用性グループを使用することをお勧めします。 このガイダンスは、分散トランザクションを使用しないアプリケーション向けであることに注意してください。


1. [データベースを構成する](https://msdn.microsoft.com/library/hh213078.aspx) 可用性グループにします。
2. セカンダリ サイトに、新しい仮想ネットワークを作成します。
3. 新しい仮想ネットワークとプライマリ サイトの間に、サイト間 VPN を設定します。
4. 復旧サイトに仮想マシンを作成し、SQL Server を仮想マシンにインストールします。
5. 新しい SQL Server 仮想マシンに、既存の AlwaysOn 可用性グループを拡張します。 この SQL Server インスタンスは、非同期レプリカ コピーとして構成します。
6. 可用性グループ リスナーを作成するか、または既存のリスナーを更新して、非同期のレプリカ仮想マシンを含めます。
7. アプリケーション ファームがリスナーを使用してセットアップされていることを確認します。 データベース サーバー名を使用してセットアップされている場合は、リスナーを使用するように更新して、フェールオーバー後に再構成する必要がないようにしてください。

分散トランザクションを使用するアプリケーションで推奨設定を使用する [Site Recovery と SAN レプリケーション](site-recovery-vmm-san.md) または [VMWare サイト間レプリケーション](site-recovery-vmware-to-vmware.md)します。

#### 復旧計画に関する考慮事項

1. このサンプル スクリプトは、プライマリ サイトおよびセカンダリ サイト上の VMM ライブラリに追加します。

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. アプリケーションの復旧計画を作成するときに、可用性グループをフェールオーバーするスクリプトを呼び出す「pre-Group 1 boot」というスクリプト化した手順を追加します。



## スタンドアロンの SQL Server に対する保護の設定


この構成では、Site Recovery レプリケーションを使用して、SQL Server マシンを保護することをお勧めします。 正確な手順は、SQL Server が仮想マシンまたは物理サーバーのどちらとして設定されているか、および Azure またはセカンダリ オンプレミス サイトのどちらにレプリケートするかによって異なります。 すべての展開シナリオのための手順、 [Site Recovery の概要](site-recovery-overview.md)します。


## SQL Server クラスター (Standard または 2008 R2) の保護の設定

SQL Server Standard エディション または SQL Server 2008 R2 を実行するクラスターでは、Site Recovery レプリケーションを使用して SQL Server を保護することをお勧めします。

#### オンプレミス間

- 展開する場合は、アプリケーションの分散トランザクションを使用してお勧め [Site Recovery と SAN レプリケーション](site-recovery-vmm-san.md) HYPER-V 環境用と [VMware 間](site-recovery-vmware-to-vmware.md) VMware 環境のです。

- DTC 以外のアプリケーションの場合、前述のアプローチで、ローカルの安全性の高い DB ミラーを使用し、スタンドアロン サーバーとしてクラスターを復旧します。

#### オンプレミスと Azure 間

Site Recovery は、Azure にレプリケートするときに、ゲスト クラスター サポートをサポートしていません。 SQL Server も Standard エディション用に低コストの障害復旧ソリューションを提供しません。 スタンドアロンの SQL Server でオンプレミスの SQL Server クラスターを保護し、Azure で復旧することをお勧めします。


1. オンプレミスのサイトで、追加のスタンドアロン SQL Server インスタンスを構成します。
2. このインスタンスを、保護が必要なデータベースのミラーとして機能するように構成します。 高い安全性モードでのミラーリングの構成
3.  環境に基づいて、内部設置型サイトで Site Recovery を構成 ([HYPER-V](site-recovery-hyper-v-site-to-azure.md) または [VMware](site-recovery-vmware-to-azure.md)します。
4.  Azure に新しい SQL Server インスタンスをレプリケートするには、Site Recovery レプリケーションを使用します。 これは高い安全性のミラー コピーなので、プライマリ クラスターと同期されますが、Site Recovery レプリケーションを使用して Azure にレプリケートされます。

次の図は、この設定を示しています。

![Standard クラスター](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### フェールバックに関する考慮事項

SQL Standard のクラスターの場合、計画外のフェールオーバー後のフェールバックには、SQL のバックアップ、ミラー インスタンスから元のクラスターへの復元、およびミラーの再確立が必要になります。










 
