<properties 
   pageTitle="リソース マネージャーで PowerShell を使用してインターネットに接続するロード バランサーを作成する | Microsoft Azure"
   description="リソース マネージャーで PowerShell を使用してインターネットに接続するロード バランサーを作成する方法について説明します"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/24/2015"
   ms.author="joaoma" />

# リソース マネージャーで PowerShell を使用して、インターネットに接続するロード バランサーを作成を開始する

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] この記事では、リソース マネージャーの配置モデルについて説明します。 こともできます [インターネットへの従来のデプロイ モデルを使用してロード バランサーを作成する方法について](load-balancer-get-started-internet-classic-cli.md)します。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

以下の手順では、PowerShell で Azure リソース マネージャーを使用して、インターネットに接続するロード バランサーを作成する方法を示します。 Azure リソース マネージャーでは、インターネットに接続するロード バランサーを作成するための項目は、個別に構成されてから、リソースを作成するためにまとめられます。 

ここでは、ロード バランサーを作成するために実行する必要のある一連の作業を個別に取り上げ、目的を達成するために実行する事柄を詳しく説明します。

## インターネットに接続するロード バランサーを作成するために必要な項目

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります。

- フロント エンド IP 構成 - 受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。 

- バック エンド アドレス プール - ロード バランサーからネットワーク トラフィックを受信する、仮想マシンのネットワーク インターフェイス (NIC) が含まれます。 

- 負荷分散規則 - ロード バランサーのパブリック ポートをバック エンド アドレス プール内のポートにマッピングする規則が含まれます。

- 受信 NAT 規則 - ロード バランサーのパブリック ポートをバック エンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。

- プローブ - バック エンド アドレス プール内の仮想マシン インスタンスの可用性を確認するために使用する正常性プローブが含まれます。

Azure リソース マネージャーであるの詳細については、ロード バランサー コンポーネントを取得できます [ロード バランサーに対応する Azure リソース マネージャー](load-balancer-arm.md)します。


## リソース マネージャーを使用するように PowerShell をセットアップする

PowerShell 用 Azure モジュールが最新の製品版であり、Azure サブスクリプションにアクセスできるように PowerShell が正しく設定されていることを確認します。

### 手順 1

        PS C:\> Login-AzureRmAccount



### 手順 2.

アカウントのサブスクリプションを確認する 

        PS C:\> get-AzureRmSubscription 

資格情報を使用して認証を行うよう求められます。<BR>

### 手順 3. 

使用する Azure サブスクリプションを選択します。 <BR>


        PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 手順 4.

新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## 仮想ネットワークと、フロント エンド IP プールのパブリック IP アドレスの作成

### 手順 1

サブネットと仮想ネットワークを作成します。

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### 手順 2.

名前付きパブリック IP アドレス (PIP) を作成する *PublicIP* DNS 名を持つフロント エンド IP プールによって使用される *loadbalancernrp.westus.cloudapp.azure.com*します。 次のコマンドでは、静的な割り当てタイプが使用されます。

    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT] ロード バランサーに、FQDN のプレフィックスとしてパブリック IP のドメインのラベルを使用します。 これは、ロード バランサー FQDN としてクラウド サービスを使用するクラシック デプロイ モデルからの変更点です。 
>この例では、FQDN がなります *loadbalancernrp.westus.cloudapp.azure.com*します。

## フロント エンド IP プールとバック エンド アドレス プールの作成

### 手順 1. 

という名前のフロント エンド IP プールを作成する *LB フロント エンド* を使用して、 *PublicIp* PIP です。

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### 手順 2. 

という名前のバック エンド アドレス プールを作成 *LB バックエンド*します。 

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## LB ルール、NAT 規則、プローブ、およびロード バランサーの作成

次の例では、以下の項目が作成されます。

- ポート 3441 のすべての受信トラフィックをポート 3389 に転送する NAT 規則。
- ポート 3442 のすべての受信トラフィックをポート 3389 に転送する NAT 規則。
- バックエンド プールのアドレスでポート 80 ～ 80 に入ってくるすべてのトラフィックを分散するロード バランサー規則。
- という名前のページ上のヘルス状態を確認するプローブ ルール *HealthProbe.aspx*します。
- 上記のオブジェクトをすべて使用するロード バランサー。



### 手順 1

NAT 規則を作成します。

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### 手順 2

ロード バランサー規則を作成します。

    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### 手順 3.

正常性プローブを作成します。

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### 手順 4.

上記で作成したオブジェクトを使用し、ロード バランサーを作成します。

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## NIC の作成

NIC を作成し (あるいは、既存の NIC を変更し)、それを NAT 規則、ロード バランサー規則、プローブに関連付ける必要があります。

### 手順 1 

NIC を作成する必要がある VNet とサブネットを取得します。

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### 手順 2.

という名前の NIC を作成する *lb nic1 する*, 、し、最初の NAT ルール、および最初の (そして唯一の) のバック エンド アドレス プールに関連付けます。
    
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 手順 3.

という名前の NIC を作成する *lb nic2 する*, 、し、2 番目の NAT ルール、および最初の (そして唯一の) のバック エンド アドレス プールに関連付けます。 

    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### 手順 4.

NIC を確認します。


    PS C:\> $backendnic1

予想される出力:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### 手順 5.

`Add-AzureRmVMNetworkInterface` コマンドレットを使用し、NIC をさまざまな VM に割り当てます。

仮想マシンを作成する方法のガイダンスを検索して内の NIC に割り当てる [の作成とリソース マネージャーと Azure PowerShell で Windows 仮想マシンを事前構成する](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example), の例では、オプション 5 を使用します。 

## 既存のロード バランサーの更新


### 手順 1

上の例のロード バランサーを使用し、ロード バランサーのオブジェクトを変数 $slb using Get-AzureLoadBalancer に割り当てます。

    $slb=get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### 手順 2

次の例では、フロント エンドでポート 81、バック エンド プールでポート 8181 を使用し、既存のロード バランサーに新しい受信 NAT 規則を追加します。

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### 手順 3.

Set-AzureLoadBalancer を使用して、新しい構成を保存します。 

    $slb | Set-AzureRmLoadBalancer

## ロード バランサーの削除

削除 AzureLoadBalancer コマンドを使用して、名前"NRP LB"リソース グループに「NRP RG」と呼ばれる以前に作成したロード バランサーの削除 

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] 省略可能なを使用するスイッチ - 強制的に削除のプロンプトを回避します。

## 次のステップ

[内部ロード バランサーの構成の開始](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
