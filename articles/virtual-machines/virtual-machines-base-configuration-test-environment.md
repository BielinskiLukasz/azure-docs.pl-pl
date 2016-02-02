<properties
    pageTitle="基本構成テスト環境"
    description="イントラネットをシミュレーションする単純な開発/テスト環境を Microsoft Azure で作成する方法について説明します。"
    documentationCenter=""
    services="virtual-machines"
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="Windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="josephd"/>


# 基本構成テスト環境

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [リソース マネージャー モデル](virtual-machines-base-configuration-test-environment-resource-manager.md)します。

この記事では、Azure Virtual Network 内に基本構成テスト環境を作成する手順について説明します。

完成したテスト環境は、次の用途に使うことができます。

- アプリケーションの開発とテストを行う。
- [シミュレートされたハイブリッド クラウド環境](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)します。
- 仮想マシンや Azure サービスを追加して拡張し、独自のテスト環境を設計する。

基本構成テスト環境は、TestLab という、クラウドのみの仮想ネットワーク内の Corpnet サブネットから成ります。インターネットに接続された単純なプライベート イントラネットが TestLab によってシミュレーションされます。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

その構成要素を次に示します。

- Azure 仮想マシン (×1)。イントラネットのドメイン コントローラーおよびドメイン ネーム システム (DNS) サーバーとして構成された、DC1 という名前の Windows Server 2012 R2 を実行します。
- Azure 仮想マシン (×1)。一般的なアプリケーション/Web サーバーとして構成された、APP1 という名前の Windows Server 2012 R2 を実行します。
- Azure 仮想マシン (×1)。CLIENT1 という名前の Windows Server 2012 R2 を実行し、イントラネットのクライアントとして機能します。

この構成では、DC1、APP1、CLIENT1 をはじめとする、Corpnet サブネットに属しているコンピューターに対して、次のことを実行できます。

- インターネットに接続して更新プログラムをインストールする、インターネット リソースにリアルタイムでアクセスする、パブリック クラウド テクノロジ (Microsoft Office 365、各種 Azure サービスなど) を利用する。
- インターネットや社内のネットワークに接続されている遠隔コンピューターからリモート デスクトップ接続を介して管理する。

Windows Server 2012 R2 基本構成テスト環境の Corpnet サブネットを Azure にセットアップする手順は、次の 4 つのフェーズから成ります。

1.  仮想ネットワークを作成する。
2.  DC1 を構成する。
3.  APP1 を構成する。
4.  CLIENT1 を構成する。

無料評価版にサインアップすることができます、Azure アカウントがあるない場合 [無料評価版の 1 か月](http://azure.microsoft.com/pricing/free-trial/)します。 MSDN サブスクリプションがある場合は、次を参照してください。 [MSDN サブスクライバー向けの Azure 特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)します。
> [AZURE.NOTE] Azure で仮想マシンを実行しているときは継続的に料金コストが発生します。 その費用は、無料試用版、MSDN サブスクリプション、または有料のサブスクリプションに対して請求されます。 Azure の仮想マシンの実行の詳細については、コストは、次を参照してください。 [Virtual Machines 料金](http://azure.microsoft.com/pricing/details/virtual-machines/) と [Azure 料金計算ツール](http://azure.microsoft.com/pricing/calculator/)します。 コストを抑えるを参照してください。 [Azure でのテスト環境の仮想マシンのコストを最小限に抑える](#costs)します。

## フェーズ 1: 仮想ネットワークを作成する

まず、基本構成の Corpnet サブネットをホストすることになる TestLab という仮想ネットワークを作成します。

1.  タスク バーで、 [Azure クラシック ポータル](https://manage.windowsazure.com), 、クリックして **新規 > ネットワーク サービス > 仮想ネットワーク > カスタム作成**します。
2.  [仮想ネットワークの詳細] ページの**[名前]** に「**TestLab**」と入力します。
3.  **[場所]** から適切なリージョンを選択します。
4.  次へ進む矢印をクリックします。
5.  [DNS サーバーおよび VPN 接続] ページで、**[DNS サーバー]** の **[名前の選択または入力]** に「**DC1**」と入力し、**[IP アドレス]** に「**10.0.0.4**」と入力して、次へ進む矢印をクリックします。
6.  [仮想ネットワーク アドレス空間] ページの **[サブネット]** で **[Subnet-1]** をクリックし、名前を「**Corpnet**」に置き換えます。
7.  Corpnet サブネットの **[CIDR (アドレス数)]** 列で、**[/24 (256)]** をクリックします。
8.  [完了] アイコンをクリックします。 仮想ネットワークが作成されるまで待ってから、次に進みます。

」の手順を次に、に従って [をインストールして、Azure PowerShell を構成する方法](../install-configure-powershell.md) 、ローカル コンピューターに Azure PowerShell をインストールします。 Azure PowerShell コマンド プロンプトを開きます。

まず以下のコマンドで、適切な Azure サブスクリプションを選択します。置換を含む引用符内のすべての < と > 適切な名前の文字。

    $subscr="<Subscription name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current

サブスクリプション名は、**Get-AzureSubscription** コマンドで表示される **SubscriptionName** プロパティから取得できます。

次に、Azure クラウド サービスを作成します。 クラウド サービスは、仮想ネットワーク内に配置された仮想マシンのセキュリティの境界および論理上のコンテナーとして機能します。 また、クラウド サービスを通じてリモートから Corpnet サブネット上の仮想マシンに接続し、管理することができます。

クラウド サービスには一意の名前を選ぶ必要があります。 * クラウド サービス名には、文字、数字、およびハイフンのみを含めることができます。 フィールドの最初と最後の文字が文字または number.* にする必要があります。

たとえば、クラウド サービスに TestLab-*UniqueSequence* (*UniqueSequence* は組織の略称) という名前を付けることができます。 たとえば、社名が Tailspin Toys であれば、クラウド サービスの名前を TestLab-Tailspin とします。

次の Azure PowerShell コマンドで名前の一意性を確認することができます。

    Test-AzureName -Service <Proposed cloud service name>

このコマンドで "False" が返された場合は、提案した名前は一意です。 以下のコマンドでクラウド サービスを作成してください。

    $loc="<the same location (region) as your TestLab virtual network>"
    New-AzureService -Service <Unique cloud service name> -Location $loc

実際のクラウド サービスの名前をメモしておいてください。

次に、仮想マシンのディスクとそれ以外のデータ ディスクの従属先となるストレージ アカウントを構成します。 *小文字と数字のみから成る一意の名前を選んでください。*ストレージ アカウント名の一意性は、次の Azure PowerShell コマンドで確認することができます。

    Test-AzureName -Storage <Proposed storage account name>

このコマンドで "False" が返された場合は、提案した名前は一意です。 さらに、ストレージ アカウントを作成し、それを使用するための設定をサブスクリプションに対して行います。以下のコマンドを使用してください。

    $stAccount="<your storage account name>"
    New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

現在の構成は次のようになります。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG01.png)

## フェーズ 2: DC1 を構成する

DC1 は、Active Directory ドメイン サービス (AD DS) のドメイン corp.contoso.com のドメイン コントローラーとして、また、TestLab 仮想ネットワークの仮想マシンに使用される DNS サーバーとして機能します。

まず、DC1 の Azure 仮想マシンを作成します。ローカル コンピューターから Azure PowerShell のコマンド プロンプトでクラウド サービスの名前を入力し、これらのコマンドを実行してください。

    $serviceName="<your cloud service name>"
    $cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
    $image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
    $vm1 | Set-AzureSubnet -SubnetNames Corpnet
    $vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
    New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

続けて、DC1 仮想マシンに接続します。

1.  Azure クラシック ポータルの左側のウィンドウで **[Virtual Machines]** をクリックし、DC1 仮想マシンの **[状態]** 列で **[開始済み]** をクリックします。
2.  タスク バーで、**[接続]** をクリックします。
3.  DC1.rdp を開くように求められたら、**[開く]** をクリックします。
4.  リモート デスクトップ接続のメッセージ ボックスが表示されたら、**[接続]** をクリックします。
5.  資格情報の入力を求められたら、次の情報を使用します。
- [名前]: **DC1\\**[ローカル管理者アカウント名]
- パスワード: [ローカル管理者アカウントのパスワード]
6.  証明書に関するリモート デスクトップ接続のメッセージ ボックスが表示されたら、**[はい]** をクリックします。

次に、余っているデータ ディスクを新しいボリュームとして追加し、ドライブ文字 F: を割り当てます。

1.  サーバー マネージャーの左側のウィンドウで、**[ファイル サービスと記憶域サービス]** をクリックし、**[ディスク]** をクリックします。
2.  コンテンツ ウィンドウの **[ディスク]** グループで、(**[パーティション]** が **[不明]** に設定されている) **[ディスク 2]** をクリックします。
3.  **[タスク]** をクリックし、**[ボリューム]** をクリックします。
4.  新しいボリューム ウィザードの [開始する前に] ページで **[次へ]** をクリックします。
5.  [サーバーとディスクの選択] ページで、**[ディスク 2]** をクリックし、**[次へ]** をクリックします。 メッセージが表示されたら、**[OK]** をクリックします。
6.  [ボリュームのサイズの指定] ページで、**[次へ]** をクリックします。
7.  [ドライブ文字またはフォルダーへの割り当て] ページで、**[次へ]** をクリックします。
8.  [ファイル システム形式の選択] ページで、**[次へ]** をクリックします。
9.  [選択内容の確認] ページで、**[作成]** をクリックします。
10. 作成されたら、**[閉じる]** をクリックします。

次に、corp.contoso.com ドメインのドメイン コントローラー兼 DNS サーバーとして DC1 を構成します。 管理者レベルの Windows PowerShell コマンド プロンプトで以下のコマンドを実行します。

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

DC1 を再起動した後、DC1 の仮想マシンに再接続します。

1.  Azure クラシック ポータルの [Virtual Machines] ページで、DC1 仮想マシンの **[状態]** 列の **[実行中]** をクリックします。
2.  タスク バーで、**[接続]** をクリックします。
3.  DC1.rdp を開くように求められたら、**[開く]** をクリックします。
4.  リモート デスクトップ接続のメッセージ ボックスが表示されたら、**[接続]** をクリックします。
5.  資格情報の入力を求められたら、次の情報を使用します。
- [名前]: **CORP\\**[ローカル管理者アカウント名]
- パスワード: [ローカル管理者アカウントのパスワード]
6.  証明書に関するリモート デスクトップ接続のメッセージ ボックスが表示されたら、**[はい]** をクリックします。

次に、CORP ドメインのメンバー コンピューターにログインする際に使用するユーザー アカウントを Active Directory に作成します。 管理者レベルの Windows PowerShell コマンド プロンプトで以下のコマンドを 1 つずつ実行します。

    New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
    Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

1 つ目のコマンドを実行すると、User1 アカウントのパスワードを入力するよう求められます。 このアカウントは、CORP ドメインに属しているすべてのコンピューターのリモート デスクトップ接続に使用されるため、強力なパスワードを選んでください。 強度を確認するを参照してください。 [パスワード チェッカー: 強力なパスワードを使用して](https://www.microsoft.com/security/pc-security/password-checker.aspx)します。 User1 アカウントのパスワードをメモし、安全な場所に保管してください。

CORP\User1 アカウントを使用して DC1 仮想マシンに再接続します。

今度は、Ping ツールのトラフィックを許可します。管理者レベルの Windows PowerShell コマンド プロンプトで以下のコマンドを実行してください。

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

現在の構成は次のようになります。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG02.png)

## フェーズ 3: APP1 を構成する

APP1 は、Web サービスとファイル共有サービスを提供します。

まず、APP1 の Azure 仮想マシンを作成します。ローカル コンピューターから Azure PowerShell のコマンド プロンプトでクラウド サービスの名前を入力し、これらのコマンドを実行してください。

    $serviceName="<your cloud service name>"
    $cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
    $cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
    $image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
    $vm1 | Set-AzureSubnet -SubnetNames Corpnet
    New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

次に、CORP\User1 の資格情報で APP1 仮想マシンに接続し、管理者レベルの Windows PowerShell コマンド プロンプトを開きます。

名前解決が正しく実行されることと、APP1 と DC1 間のネットワーク通信を確認するために、「**ping dc1.corp.contoso.com**」コマンドを実行し、応答が 4 回返されることを確認します。

次に、APP1 に Web サーバーをインストールします。Windows PowerShell コマンド プロンプトで以下のコマンドを実行してください。

    Install-WindowsFeature Web-WebServer –IncludeManagementTools

次に、以下のコマンドを実行して、APP1 上に共有フォルダーを作成し、そのフォルダー内にテキスト ファイルを作成します。

    New-Item -path c:\files -type directory
    Write-Output "This is a shared file." | out-file c:\files\example.txt
    New-SmbShare -name files -path c:\files -changeaccess CORP\User1

現在の構成は次のようになります。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG03.png)

## フェーズ 4: CLIENT1 を構成する

CLIENT1 は、Contoso イントラネット上の標準的なノート PC、タブレット、またはデスクトップ コンピューターとなります。

まず、CLIENT1 の Azure 仮想マシンを作成します。ローカル コンピューターから Azure PowerShell のコマンド プロンプトでクラウド サービスの名前を入力し、これらのコマンドを実行してください。

    $serviceName="<your cloud service name>"
    $cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
    $cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
    $image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
    $vm1 | Set-AzureSubnet -SubnetNames Corpnet
    New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

次に、CORP\User1 の資格情報を使用して、CLIENT1 仮想マシンに接続します。

名前解決が正しく実行されることと、CLIENT1 と DC1 間のネットワーク通信を確認するために、Windows PowerShell コマンド プロンプトから「**ping dc1.corp.contoso.com**」コマンドを実行し、応答が 4 回返されることを確認します。

次に、APP1 上にある Web リソースとファイル共有リソースに CLIENT1 からアクセスできることを確認します。

1.  サーバー マネージャーのツリー ウィンドウで、**[ローカル サーバー]** をクリックします。
2.  **CLIENT1 のプロパティ**で、**[IE セキュリティ強化の構成]** の横にある **[有効]** をクリックします。
3.  **[Internet Explorer セキュリティ強化の構成]** で、**[Administrators]** と **[Users]** の **[オフ]** をクリックし、**[OK]** をクリックします。
4.  スタート画面で、**[Internet Explorer]** をクリックして、**[OK]** をクリックします。
5.  アドレス バーに「 **http://app1.corp.contoso.com/**, 、ENTER キーを押します。 APP1 の既定のインターネット インフォメーション サービス Web ページが表示されます。
6.  デスクトップのタスク バーからエクスプローラー アイコンをクリックします。
7.  アドレス バーに「 **\\\app1\Files**, 、ENTER キーを押します。
8.  フォルダー ウィンドウに、Files 共有フォルダー内容が表示されます。
9.  **[Files]** 共有フォルダー ウィンドウで、**Example.txt** ファイルをダブルクリックします。 Example.txt ファイルの内容が表示されます。
10. **[example.txt - メモ帳]** ウィンドウと **[Files]** 共有フォルダー ウィンドウを閉じます。

最終的な構成は次のようになります。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Azure の基本構成の準備ができました各種テスト環境、またはアプリケーションの開発やテストなど、 [シミュレートされたハイブリッド クラウド環境](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)します。

## その他のリソース

[Azure のテスト ラボ](http://social.technet.microsoft.com/wiki/contents/articles/24092.azure-test-lab.aspx)

[ハイブリッド クラウド テスト環境](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

[基本構成テスト環境では、Azure リソース マネージャー](virtual-machines-base-configuration-test-environment-resource-manager.md)

## <a id="costs"></a>Azure でのテスト環境の仮想マシンのコストを最小限に抑える

テスト環境の仮想マシンを実行する際に発生するコストは、次のいずれかの方法で最小限に抑えることができます。

- テスト環境の作成および必要なテストとデモンストレーションをできるだけ短時間で実行する。 完了したらテスト環境の仮想マシンを削除してください。
- テスト環境の仮想マシンをシャットダウンして割り当て解除済みの状態にする。

Azure PowerShell で仮想マシンをシャットダウンするには、クラウド サービスの名前を入力し、以下のコマンドを実行します。

    $serviceName="<your cloud service name>"
    Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
    Stop-AzureVM -ServiceName $serviceName -Name "APP1"
    Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned

停止 (割り当て解除済み) 状態のすべての仮想マシンを起動して、正しく動作させるためには、次の順序で起動する必要があります。

1.  DC1
2.  APP1
3.  CLIENT1

Azure PowerShell で順に仮想マシンを起動するには、クラウド サービスの名前を入力し、以下のコマンドを実行します。

    $serviceName="<your cloud service name>"
    Start-AzureVM -ServiceName $serviceName -Name "DC1"
    Start-AzureVM -ServiceName $serviceName -Name "APP1"
    Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"



