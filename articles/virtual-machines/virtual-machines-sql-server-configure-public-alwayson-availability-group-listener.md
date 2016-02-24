<properties 
    pageTitle="AlwaysOn 可用性グループの外部リスナーを構成する | Microsoft Azure"
    description="このチュートリアルでは、関連付けられているクラウド サービスのパブリック仮想 IP アドレスを使用して外部からアクセスできる、AlwaysOn 可用性グループ リスナーを Azure で作成する手順について説明します。"
    services="virtual-machines"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="11/13/2015"
    ms.author="jroth" />

# Azure での AlwaysOn 可用性グループの外部リスナーの構成

> [AZURE.SELECTOR]
- [内部リスナー](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [外部リスナー](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

このトピックでは、インターネット上の外部からアクセス可能な、AlwaysOn 可用性グループのリスナーを構成する方法について説明します。 これを実行するクラウド サービスの関連付け **パブリック仮想 IP (VIP)** アドレス リスナーを使用します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。
 

可用性グループには、オンプレミスのみ、Azure のみ、またはオンプレミスと Azure の両方にまたがるハイブリッド構成のレプリカを含めることができます。 Azure レプリカは、同じリージョン内に配置することも、複数の仮想ネットワーク (VNet) を使用して複数のリージョンに配置することもできます。 次の手順では、既にあると仮定 [可用性グループに構成](virtual-machines-sql-server-alwayson-availability-groups-gui.md) しますが、リスナーは構成されていません。 

## 外部リスナーのガイドラインと制限事項

クラウド サービスのパブリック VIP アドレスを使用してデプロイしている場合は、Azure での可用性グループ リスナーに関する次のガイドラインを確認してください。

- 可用性グループ リスナーは、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 でサポートされます。

- クライアント アプリケーションは、可用性グループ VM が含まれるクラウド サービスとは異なるクラウド サービスに存在する必要があります。 Azure では、同じクラウド サービスに存在するクライアントとサーバーによる Direct Server Return はサポートされません。

- 既定では、この記事の手順により、クラウド サービスの仮想 IP (VIP) アドレスを使用するリスナーが 1 つ構成されます。 ただし、クラウド サービスに対して複数の VIP アドレスを予約して作成することができます。 これにより、この記事の手順を使用して、別の VIP にそれぞれ関連付けられている複数のリスナーを作成することができます。 複数の VIP アドレスを作成する方法については、次を参照してください。 [クラウド サービスごとの複数の Vip](load-balancer-multivip.md)します。

- ハイブリッド環境でのリスナーを作成する場合、オンプレミスのネットワークは、Azure 仮想ネットワークを使用したサイト間 VPN への接続に加えて、パブリック インターネットへの接続が必要です。 Azure のサブネットでは、可用性グループ リスナーは個々 のクラウド サービスのパブリック IP アドレスによってのみ到達可能です。

- 内部ロード バランサー (ILB) を使用する内部リスナーも含まれるクラウド サービスに外部リスナーを作成することはできません。

## リスナーのアクセシビリティを決定する

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

この記事では使用するリスナーの作成に **外部負荷分散**します。 仮想ネットワークにプライベートのリスナーを設定する場合を設定するための手順は、この記事のバージョンを参照してください、 [ILB とともにリスナー](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)

## Direct Server Return を使用して負荷分散 VM エンドポイントを作成する

外部負荷分散では、VM をホストするクラウド サービスのパブリック仮想 IP アドレスを使用します。 そのため、この場合はロード バランサーを作成または構成する必要はありません。 

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. 次の PowerShell スクリプトをテキスト エディターにコピーし、環境に合わせて変数の値を設定します (一部のパラメーターには既定値が指定されています)。 可用性グループが複数の Azure リージョンにまたがっている場合、各データセンターにおいて、そのデータセンターに存在するクラウド サービスおよびノードごとにスクリプトを 1 回ずつ実行する必要があることに注意してください。

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. 変数の設定後、スクリプトを、テキスト エディターからそれを実行する Azure PowerShell セッションにコピーします。 プロンプトにまだ >> が表示される場合、スクリプトの実行が確実に開始されるようにするため、Enter キーを再度押します。

## KB2854082 がインストールされていることを確認する (必要に応じて)

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## 可用性グループ ノードでファイアウォール ポートを開く

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## 可用性グループ リスナーを作成する

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. 外部負荷分散の場合、レプリカが含まれるクラウド サービスのパブリック仮想 IP アドレスを取得する必要があります。 Azure クラシック ポータルにログインします。 可用性グループの VM が含まれるクラウド サービスに移動します。 開いている、 **ダッシュ ボード** 表示します。 

3. 下に表示されたアドレスに注意してください **パブリック仮想 IP (VIP) アドレス**です。 ソリューションが複数の VNet にまたがっている場合は、レプリカをホストする VM が含まれるクラウド サービスごとにこの手順を繰り返します。

4. VM のいずれかで、次の PowerShell スクリプトをテキスト エディターにコピーし、変数を先ほどメモした値に設定します。

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name 
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
        
        Import-Module FailoverClusters
        
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code. 
        
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. 変数の設定後、管理者特権で Windows PowerShell ウィンドウを開いて、スクリプトをテキスト エディターからコピーし、それを実行する Azure PowerShell セッションに貼り付けます。 プロンプトにまだ >> が表示される場合、スクリプトの実行が確実に開始されるようにするため、Enter キーを再度押します。 

1. 各 VM で、これを繰り返します。 このスクリプトでは、クラウド サービスの IP アドレスを使用して IP アドレス リソースを構成し、プローブ ポートなどの他のパラメーターを設定します。 IP アドレス リソースがオンラインになると、プローブ ポートにおいて、このチュートリアルで先ほど作成した負荷分散エンドポイントからのポーリングに応答できます。

## リスナーをオンラインにする

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## フォローアップ項目

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## 可用性グループ リスナー(同一 VNet 内) をテストする

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## 可用性グループ リスナー(インターネット経由) をテストする

仮想ネットワークの外部からリスナーにアクセスするには、同じ VNet 内のみアクセス可能な ILB ではなく、外部/公開負荷分散 (このトピックで説明) を使用している必要があります。 接続文字列で、クラウド サービス名を指定します。 たとえば、名前のクラウド サービスを作成する必要がある *mycloudservice*, 、sqlcmd ステートメントは次のようになります。

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

呼び出し元はインターネット経由で windows 認証を使用できないため、前の例とは異なり SQL 認証を使用する必要があります。 詳細については、次を参照してください。 [Azure VM 内の AlwaysOn 可用性グループ: クライアント接続のシナリオ](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)します。 SQL 認証を使用する場合は、両方のレプリカで必ず同じログインを作成します。 可用性グループでのログインのトラブルシューティングの詳細については、次を参照してください。 [ログインをマップおよび使用する方法には、他のレプリカに接続して、可用性データベースにマップする SQL データベースのユーザーが含まれている](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)します。

AlwaysOn レプリカが異なるサブネットのかどうか、クライアントは指定する必要があります **MultisubnetFailover = True** 接続文字列にします。 これにより、別のサブネット内のレプリカへのパラレル接続が試行されます。 このシナリオには、AlwaysOn 可用性グループのリージョンを越えたデプロイが含まれていることに注意してください。

## 次のステップ

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]


