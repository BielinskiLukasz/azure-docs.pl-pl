<properties 
    pageTitle="基幹業務アプリケーションのフェーズ 3 | Microsoft Azure" 
    description="コンピューターと SQL Server クラスターを作成し、可用性グループを有効にします。" 
    documentationCenter=""
    services="virtual-machines" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="Windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2015" 
    ms.author="josephd"/>


# 基幹業務アプリケーションのワークロード フェーズ 3: SQL Server インフラストラクチャを構成する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 従来のデプロイ モデルです。

高可用な基幹業務アプリケーションを Azure インフラストラクチャ サービスにデプロイするこのフェーズでは、SQL Server を実行する 2 台のコンピューターとクラスター マジョリティ ノード コンピューターを構成し、Windows Server クラスターに統合します。

進むには、このフェーズを完了する必要があります [フェーズ 4](virtual-machines-workload-high-availability-LOB-application-phase4.md)します。 参照してください [、可用性の高い基幹業務アプリケーションで、Azure のデプロイ](virtual-machines-workload-high-availability-LOB-application-overview.md) の全フェーズ。
> [AZURE.NOTE] 次の手順では、Azure イメージ ギャラリーの SQL Server イメージを使用するため、SQL Server のライセンスを使用するための継続的な費用が発生します。 Azure で仮想マシンを作成し、独自の SQL Server のライセンスをインストールすることもできますが、Azure 仮想マシンを含む、仮想マシンで SQL Server のライセンスを使用するには、ソフトウェア アシュアランスおよびライセンス モビリティが必要になります。 仮想マシンに SQL Server をインストールする方法の詳細については、次を参照してください。 [SQL Server のインストール](https://msdn.microsoft.com/library/bb500469.aspx)します。

## Azure での SQL Server クラスターの仮想マシンの作成

SQL サーバーを実行する仮想マシンは 2 つあります。 最初の仮想マシンには、可用性グループのプライマリ データベースのレプリカが含まれています。2 番目の仮想マシンには、セカンダリ (バックアップ) レプリカが含まれています。 高可用性をサポートするために、バックアップが存在しています。 追加の仮想マシンは、クラスター マジョリティ ノード用です。

PowerShell コマンドの次のブロックを使用して、3 つのサーバーの仮想マシンを作成します。削除して、変数の値を指定します < と > 文字です。この PowerShell コマンド セットでは、次の表の値を使用します。

- 表 M (仮想マシン)
- 表 V (仮想ネットワーク設定)
- 表 S (サブネット)
- 表 ST (ストレージ アカウント)
- 表 A (可用性セット)

表 M が定義されていることを思い出してください [フェーズ 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) し、表 V、S、ST、および A [フェーズ 1](virtual-machines-workload-high-availability-LOB-application-phase1.md)します。
> [AZURE.NOTE] 次のコマンド セットは、Azure PowerShell 1.0 以降を使用します。 詳細については、次を参照してください。 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)します。

適切な値をすべて指定したら、Azure PowerShell プロンプトでそのブロックを実行します。

    # Set up key variables
    $rgName="<your resource group name>"
    $locName="<Azure location of your resource group>"
    # Change to the premium storage account
    $saName="<Table ST – Item 1 – Storage account name column>"
    $vnetName="<Table V – Item 1 – Value column>"
    $avName="<Table A – Item 2 – Availability set name column>"
    
    # Create the first SQL server
    $vmName="<Table M – Item 3 - Virtual machine name column>"
    $vmSize="<Table M – Item 3 - Minimum size column>"
    $vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
    $avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
    $vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
    
    $diskSize=<size of the extra disk for SQL data in GB>
    $diskLabel="<the label on the disk>"
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the first SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
    
    # Create the second SQL Server virtual machine
    $vmName="<Table M – Item 4 - Virtual machine name column>"
    $vmSize="<Table M – Item 4 - Minimum size column>"
    $nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
    $vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
    
    $diskSize=<size of the extra disk for SQL data in GB>
    $diskLabel="<the label on the disk>"
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
    
    # Change to the standard storage account
    $saName="<Table ST – Item 2 – Storage account name column>"
    
    # Create the cluster majority node server
    $vmName="<Table M – Item 5 - Virtual machine name column>"
    $vmSize="<Table M – Item 5 - Minimum size column>"
    $nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
    $vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the cluster majority node server." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] これらの仮想マシンはイントラネット アプリケーション用であるため、パブリック IP アドレスや DNS ドメイン名ラベルは割り当てられず、インターネットに公開されません。 ただし、これは Azure ポータルから接続できないことを意味します。 仮想マシンのプロパティを表示する際に、**[接続]** ボタンは使用できません。 プライベート IP アドレスまたはイントラネット DNS 名を使用して仮想マシンに接続する場合は、リモート デスクトップ接続のアクセサリまたは別のリモート デスクトップ ツールを使用します。

## SQL Server を実行するコンピューターの構成

SQL Server を実行する仮想マシンのそれぞれについて、好みのリモート デスクトップ クライアントを使用して、リモート デスクトップ接続を作成します。 仮想マシンのイントラネット DNS 名またはコンピューター名と、ローカル管理者アカウントの資格情報を使用します。

Windows PowerShell プロンプトで次のコマンドを実行し、SQL Server を実行する各仮想マシンを適切な AD DS ドメインに参加させます。

    $domName="<AD DS domain name to join, such as corp.contoso.com>"
    Add-Computer -DomainName $domName
    Restart-Computer

Add-Computer コマンドを入力した後、ドメイン アカウントの資格情報を指定する必要があることに注意してください。

仮想マシンの再起動後、ローカル管理者特権を持つアカウントを使用して再接続します。


次の手順を 2 回 (SQL Server を実行する仮想マシンごとに 1 回) 実行し、データ ディスクを追加して、新しいボリュームにフォルダーを作成します。

### 空のディスクを初期化してフォルダーを追加するには

1. サーバー マネージャーの左側のウィンドウで、**[ファイル サービスと記憶域サービス]** をクリックし、**[ディスク]** をクリックします。
2. コンテンツ ウィンドウで、**[ディスク]** グループの (**[パーティション]** が **[不明]** に設定されている) **[ディスク 2]** をクリックします。
3. **[タスク]** をクリックし、**[ボリューム]** をクリックします。
4. 新しいボリューム ウィザードの [開始する前に] ページで **[次へ]** をクリックします。
5. [サーバーとディスクの選択] ページで、**[ディスク 2]** をクリックし、**[次へ]** をクリックします。 メッセージが表示されたら、**[OK]** をクリックします。
6. [ボリュームのサイズの指定] ページで、**[次へ]** をクリックします。
7. [ドライブ文字またはフォルダーへの割り当て] ページで、**[次へ]** をクリックします。
8. [ファイル システム形式の選択] ページで、**[次へ]** をクリックします。
9. [選択内容の確認] ページで、**[作成]** をクリックします。
10. 完了したら、**[閉じる]** をクリックします。
11. Windows PowerShell プロンプトで次のコマンドを実行します。
    - md f:\Data
    - md f:\Log
    - md f:\Backup

次の手順を 2 回 (SQL Server を実行する仮想マシンごとに 1 回) 実行して、新しいデータベースと、アカウントおよびアクセス許可に F: ドライブを使用するように SQL Server を構成します。

1. スタート画面で「**SQL Studio**」と入力し、**[SQL Server 2014 Management Studio]** をクリックします。
2. **[サーバーに接続]** で、**[接続]** をクリックします。
3. 左ウィンドウで、最上位ノード (コンピューターの名前が付けられた既定のインスタンス) を右クリックし、**[プロパティ]** をクリックします。
4.  **[サーバーのプロパティ]** で、**[データベースの設定]** をクリックします。
5.  **[データベースの既定の場所]** で、次の値を設定します。
    - **[データ]** では、パスを **f:\Data** に設定します。
    - **[ログ]** では、パスを **f:\Log** に設定します。
    - **[バックアップ]** では、パスを **f:\Backup** に設定します。
    - 新しいデータベースでのみ、これらの場所が使用されます。
6.  **[OK]** をクリックしてウィンドウを閉じます。
7.  左ウィンドウで、**[セキュリティ]** フォルダーを展開します。
8.  **[ログイン]** を右クリックし、**[新しいログイン]** をクリックします。
9.  **ログイン名**, 、型 *ドメイン*で \sqladmin (を *ドメイン* で sqladmin アカウントが作成したドメインの名前を指定 [フェーズ 2](virtual-machines-workload-high-availability-LOB-application-phase2.md))します。
10. **[ページの選択]** で、**[サーバー ロール]**、**[sysadmin]** の順にクリックし、**[OK]** をクリックします。
11. SQL Server 2014 Management Studio を閉じます。

次の手順を 2 回 (SQL サーバーごとに 1 回) 実行して、sqladmin アカウントを使用したリモート デスクトップ接続を許可します。

1.  スタート画面で **[PC]** を右クリックし、**[プロパティ]** をクリックします。
2.  **[システム]** ウィンドウで、**[リモートの設定]** をクリックします。
3.  **[リモート デスクトップ]** セクションで **[ユーザーの選択]** をクリックし、**[追加]** をクリックします。
4.  **選択するオブジェクト名を入力**, 、「[domain]**\sqladmin**, 順にクリック **OK** 3 回です。

SQL Server サービスには、クライアントがデータベース サーバーへのアクセスに使用するポートが必要です。 また、SQL Server Management Studio に接続するためのポートと、高可用性グループを管理するためのポートも必要です。 次に、管理者レベルの Windows PowerShell プロンプトで次のコマンドを 2 回 (SQL Server 仮想マシンごとに 1 回) 実行して、この種類の受信トラフィックを許可するファイアウォール規則を追加します。

    New-NetFirewallRule -DisplayName "SQL Server ports 1433, 1434, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

SQL Server 仮想マシンごとに、ローカル管理者としてサインアウトします。

Azure で SQL Server のパフォーマンスを最適化する方法の詳細については、次を参照してください。 [Azure 仮想マシンにおける SQL Server のパフォーマンスのベスト プラクティス](virtual-machines-sql-server-performance-best-practices.md)します。 基幹業務アプリケーションのストレージ アカウントの 地理冗長ストレージ (GRS) を無効にし、記憶域を使用して IOP を最適化することもできます。

## クラスター マジョリティ ノード サーバーの構成

好みのリモート デスクトップ クライアントを使用し、クラスター マジョリティ ノード サーバー仮想マシンへのリモート デスクトップ接続を作成します。 仮想マシンのイントラネット DNS 名またはコンピューター名と、ローカル管理者アカウントの資格情報を使用します。

Windows PowerShell プロンプトで次のコマンドを実行し、クラスター マジョリティ ノードを適切な AD DS ドメインに参加させます。

    $domName="<AD DS domain name to join, such as corp.contoso.com>"
    Add-Computer -DomainName $domName
    Restart-Computer

**Add-Computer** コマンドを実行するときに、ドメイン アカウントの資格情報を指定する必要があることに注意してください。

仮想マシンの再起動後、ローカル管理者特権を持つアカウントを使用して再接続します。

## Windows Server フェールオーバー クラスタリング クラスターの作成

SQL Server AlwaysOn 可用性グループでは、Windows Server の Windows Server フェールオーバー クラスタリング (WSFC) 機能を利用します。 この機能により、複数のコンピューターが 1 つのグループとしてクラスターに参加できるようになります。 1 台のコンピューターで障害が発生すると、別のコンピューターがすぐに機能を引き継ぎます。 したがって、最初の作業として、参加するすべてのコンピューターでフェールオーバー クラスタリング機能を有効にします。次のコンピューターで作業を行います。

- SQL Server を実行するプライマリ仮想マシン
- SQL Server を実行するセカンダリ仮想マシン
- クラスター マジョリティ ノード

フェールオーバー クラスターには少なくとも 3 つの仮想マシンが必要です。 2 つのマシンで SQL Server をホストします。セカンダリ仮想マシンは同期セカンダリ レプリカであり、プライマリ マシンで障害が発生した場合にデータの損失を防ぎます。 3 つ目の仮想マシンでは、SQL Server をホストする必要はありません。 クラスター マジョリティ ノードの機能が、WSFC でクォーラムを提供します。 WSFC クラスターではクォーラムを利用して正常性を監視するため、WSFC クラスターがオンラインであるためには、常に過半数存在する必要があります。 クラスターにコンピューターが 2 台しかない場合、2 台のうち 1 台で障害が発生すると、過半数に届かなくなります。 詳細については、次を参照してください。 [WSFC クォーラム モードと投票の構成 (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx)します。

両方の SQL Server 仮想マシンとクラスター マジョリティ ノードに対して、管理者レベルの Windows PowerShell プロンプトで次のコマンドを実行します。

    Install-WindowsFeature Failover-Clustering -IncludeManagementTools

現在、Azure の DHCP によって RFC に準拠していない動作が発生するため、Windows Server フェールオーバー クラスタリング (WSFC) クラスターの作成に失敗する可能性があります。 詳細については、「Azure Virtual Machines 内の SQL Server の高可用性と障害復旧」で、「Azure ネットワークでの WSFC クラスターの動作」を検索してください。 ただし、回避策があります。 次の手順に従って、クラスターを作成します。

1.  プライマリ SQL Server 仮想マシンで作成した sqladmin アカウントにログオン [フェーズ 2](virtual-machines-workload-high-availability-LOB-application-phase2.md)します。
2.  スタート画面で「**フェールオーバー**」と入力し、**[フェールオーバー クラスター マネージャー]** をクリックします。
3.  左ウィンドウで、**[フェールオーバー クラスター マネージャー]** を右クリックし、**[クラスターの作成]** をクリックします。
4.  **[開始する前に]** ページで **[次へ]** をクリックします。
5.  **[サーバーの選択]** ページで、プライマリ SQL Server コンピューターの名前を入力して **[追加]** をクリックし、**[次へ]** をクリックします。
6.  **検証の警告** ] ページで [* * 残念ですが、このクラスターでは、マイクロソフトのサポートは必要ありませんしたがってしないように検証テストを実行します。 次をクリックすると、クラスターの作成を続行します。**, 、] をクリックし、* * 次**します。
7.  **[クラスター管理用のアクセス ポイント]** ページで、**[クラスター名]** ボックスにクラスターの名前を入力し、**[次へ]** をクリックします。
8.  **[確認]** ページで、**[次へ]** をクリックしてクラスターの作成を開始します。
9.  **[概要]** ページで **[完了]** をクリックします。
10. 左ウィンドウで新しいクラスターをクリックします。 コンテンツ ウィンドウの **[クラスター コア リソース]** セクションで、サーバー クラスター名を開きます。 **[IP アドレス]** リソースが **[失敗]** 状態で表示されます。 クラスターにコンピューター自体と同じ IP アドレスが割り当てられているため、IP アドレス リソースをオンラインにすることができません。 その結果、アドレスの重複が発生します。
11. 失敗した **IP アドレス** リソースを右クリックし、**[プロパティ]** をクリックします。
12. **[IP アドレスのプロパティ]** ダイアログ ボックスで、**[静的 IP アドレス]** をクリックします。
13. SQL サーバーが配置されているサブネットに対応するアドレス範囲の未使用の IP を入力し、**[OK]** をクリックします。
14. 失敗した IP アドレス リソースを右クリックし、**[オンラインにする]** をクリックします。 両方のリソースがオンラインになるまで待ちます。 クラスター名リソースがオンラインになると、新しい Active Directory (AD) コンピューター アカウントでドメイン コントローラーが更新されます。 この AD アカウントは、後で可用性グループのクラスター化サービスを実行する際に使用されます。
15. AD アカウントが作成されたので、クラスター名をオフラインにします。 **[クラスター コア リソース]** でクラスター名を右クリックし、**[オフラインにする]** をクリックします。
16. クラスターの IP アドレスを削除するには、**[IP アドレス]** を右クリックして **[削除]** をクリックし、メッセージが表示されたら **[はい]** をクリックします。 クラスター リソースは、IP アドレス リソースに依存しているため、オンラインにすることはできなくなります。 ただし、可用性グループは、適切に機能するために、クラスター名と IP アドレスのどちらにも依存しません。 そのため、クラスター名をオフラインのままにしておくことができます。
17. 残りのノードをクラスターに追加するには、左ウィンドウでクラスター名をクリックし、**[ノードの追加]** をクリックします。
18. **[開始する前に]** ページで **[次へ]** をクリックします。
19. **[サーバーの選択]** ページで名前を入力し、**[追加]** をクリックして、セカンダリ SQL Server とクラスター マジョリティ ノードの両方をクラスターに追加します。 2 台のコンピューターを追加したら、**[次へ]** をクリックします。
コンピューターを追加できず、"リモート レジストリは実行されていません" というエラー メッセージが表示された場合は、次の操作を行います。 コンピューターにログオンし、サービス スナップイン (services.msc) を開いて、リモート レジストリを有効にします。 詳細については、次を参照してください。 [リモート レジストリ サービスに接続できない](http://technet.microsoft.com/library/bb266998.aspx)します。
20. **検証の警告** ] ページで [* * 残念ですが、このクラスターでは、マイクロソフトのサポートは必要ありませんしたがってしないように検証テストを実行します。 次をクリックすると、クラスターの作成を続行します。**, 、] をクリックし、* * 次**します。
21. **[確認]** ページで **[次へ]** をクリックします。
22. **[概要]** ページで **[完了]** をクリックします。
23. 左ウィンドウで **[ノード]** をクリックします。 3 台のコンピューターがすべて表示されます。

## AlwaysOn 可用性グループの有効化

次に、SQL Server 構成マネージャーを使用して、AlwaysOn 可用性グループを有効にします。 SQL Server の可用性グループは、Azure 可用性セットとは異なります。 可用性グループには、可用性が高く回復可能なデータベースが含まれます。 Azure 可用性セットでは、さまざまな障害ドメインに仮想マシンを割り当てます。 障害ドメインの詳細については、次を参照してください。 [仮想マシンの可用性を管理](virtual-machines-manage-availability.md)します。

SQL Server の AlwaysOn 可用性グループを有効にするには、次の手順に従います。

1.  プライマリ SQL Server 仮想マシンで作成した sqladmin アカウントにログオン [フェーズ 2](virtual-machines-workload-high-availability-LOB-application-phase2.md)します。
2.  スタート画面で「**SQL Server 構成**」と入力し、**[SQL Server 構成マネージャー]** をクリックします。
3.  左ウィンドウで、**[SQL Server のサービス]** をクリックします。
4.  コンテンツ ウィンドウで、**[SQL Server (MSSQLSERVER)]** をダブルクリックします。
5.  **[SQL Server (MSSQLSERVER) のプロパティ]** で、**[AlwaysOn 高可用性]** タブをクリックします。**[AlwaysOn 可用性グループを有効にする]** をオンにして **[適用]** をクリックし、メッセージが表示されたら、**[OK]** をクリックします。 プロパティ ウィンドウはまだ閉じないでください。
6.  仮想マシンの管理-可用性] タブをクリックし、[ドメイン] を入力**\sqlservice** で **アカウント名**します。 **[パスワード]** と **[パスワードの確認]** で sqlservice アカウントのパスワードを入力し、**[OK]** をクリックします。
7.  メッセージ ウィンドウで **[はい]** をクリックして、SQL Server サービスを再起動します。
8.  sqladmin アカウントを使用してセカンダリ SQL Server 仮想マシンにログオンし、手順 2 ～ 7 を繰り返します。

次の図は、このフェーズが問題なく完了したときの構成を示しています。プレースホルダーにコンピューター名が示されています。

![](./media/virtual-machines-workload-high-availability-LOB-application-phase3/workload-lobapp-phase3.png)

## 次のステップ

- 使用 [フェーズ 4](virtual-machines-workload-high-availability-LOB-application-phase4.md) このワークロードの構成を続行します。






