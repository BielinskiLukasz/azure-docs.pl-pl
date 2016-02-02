<properties
   pageTitle="Azure リソース マネージャーを使用したインターネットに接続するロード バランサーの構成の開始 | Microsoft Azure "
   description="ロード バランサー ルール、NAT ルール、Azure リソース マネージャーのプローブを作成する方法。ロード バランサーのリソースを作成するエンド ツー エンドのプロセスを示す手順。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/26/2015"
   ms.author="joaoma" />


# Azure リソース マネージャーを使用したインターネットに接続するロード バランサーの構成の開始

> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internet-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-arm-powershell.md)



以下の手順では、PowerShell で Azure リソース マネージャーを使用して、インターネットに接続するロード バランサーを作成する方法を示します。 Azure リソース マネージャーでは、インターネットに接続するロード バランサーを作成するための項目は、個別に構成されてから、リソースを作成するためにまとめられます。

このページでは、ロード バランサーを作成するために実行する必要のある一連の作業を個別に取り上げ、ロード バランサーを作成するという目的を達成するために実行する事柄を詳細に説明します。


## インターネットに接続するロード バランサーを作成するために必要な項目

ロード バランサーを作成する前に、次の項目を構成する必要があります。

- フロントエンド IP 構成 - 受信ネットワーク トラフィックを負荷分散するために、フロントエンド IP プールにパブリック IP アドレスを追加します。

- バックエンド アドレス プール: フロントエンド IP プールからの負荷分散されたトラフィックを受信するネットワーク インターフェイスを構成します。

- 負荷分散規則 - ロード バランサーのソースおよびローカル ポート構成。

- プローブ - 仮想マシン インスタンスの正常性状態のプローブを構成します。

- 受信 NAT 規則 - 仮想マシン インスタンスのいずれかに直接アクセスするためのポート規則を構成します。

Azure リソース マネージャーであるの詳細については、ロード バランサー コンポーネントを取得できます [ロード バランサーに対応する Azure リソース マネージャー](load-balancer-arm.md)します。

次の手順は、2 台の仮想マシン間でロード バランサーを構成する方法を示しています。


## PowerShell を使用した手順

### ロード バランサーのリソース グループの作成

### 手順 1.

ARM コマンドレットを使用するように PowerShell モードを切り替えてください。 詳細については、使用 [リソース マネージャーでの Windows Powershell](powershell-azure-resource-manager.md)します。


    PS C:\> Switch-AzureMode -Name AzureResourceManager

### 手順 2.

Azure アカウントにログインします。


    PS C:\> Add-AzureAccount

資格情報を使用して認証を行うよう求められます。


### 手順 3.

使用する Azure サブスクリプションを選択します。

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

使用できるサブスクリプションのリストを表示するには、Get-AzureSubscription コマンドレットを使用します。


### 手順 4.

新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 ロード バランサーを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、「NRP RG」という名前のリソース グループと「West US」という名前の場所を作成しました。

## Virtual Network と、フロント エンド IP プールのパブリック IP アドレスの作成

### 手順 1.

仮想ネットワークを作成します。

    $backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

仮想ネットワークのサブネットを作成し、$backendSubnet に割り当てます。

    New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

仮想ネットワークを作成し、サブネット lb-subnet-be を仮想ネットワーク NRPVNet に追加します。

### 手順 2.

フロントエンド IP プールによって使用されるパブリック IP アドレスを作成します。

    $publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Dynamic -DomainNameLabel lbip 

>[AZURE.NOTE]パブリック IP アドレスのドメイン名のラベル プロパティは、ロード バランサーの FQDN のプレフィックスになります。 

## フロント エンド IP プールとバックエンド アドレス プールの作成

受信ロード バランサー ネットワーク トラフィック用のフロント エンド IP プールと、負荷が分散されたトラフィックを受け取るためのバックエンド アドレス プールを設定します。

### 手順 1.

パブリック IP 変数 ($publicIP) を使用して、フロント エンド IP プールを作成します。

    $frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### 手順 2.

フロント エンド IP プールから受信トラフィックを受け取るために使用するバック エンド アドレス プールを設定します。

    $beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## LB ルール、NAT ルール、プローブ、およびロード バランサーの作成

フロント エンド IP プールとバックエンド アドレス プールを作成した後、ロード バランサーのリソースに属するルールを作成する必要があります。

### 手順 1.

    $inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389
    
    $inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    
    $healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    
    $lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

上記の例では、次の項目が作成されます。

- ポート 3441 へのすべての受信トラフィックがポート 3389 に向かう NAT ルール。
- ポート 3442 へのすべての受信トラフィックがポート 3389 に向かう 2 番目の NAT ルール。
- パブリック ポート 80 上のすべての受信トラフィックをバック エンド アドレス プールのローカル ポート 80 に負荷分散するロード バランサー ルール。
- パス "HealthProbe.aspx" の正常性状態を確認するプローブ ルール。



### 手順 2.

すべてのオブジェクト (NAT ルール、ロード バランサー ルール、プローブ構成) を一緒に追加してロード バランサーを作成します。

    $NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## ネットワーク インターフェイスの作成

ロード バランサーを作成した後、受信で負荷分散されたネットワーク トラフィック、NAT ルール、およびプローブを受信するネットワーク インターフェイスを定義する必要があります。 この場合、ネットワーク インターフェイスは個別に構成され、後で仮想マシンに割り当てることができます。


### 手順 1.

リソースの仮想ネットワークとサブネットを取得し、ネットワーク インターフェイスを作成します。

    $vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    
    $backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

このステップでは、ロード バランサーのバック エンド プールに属し、このネットワーク インターフェイスの RDP に対して最初の NAT ルールを関連付けるネットワーク インターフェイスを作成します。

    $backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 手順 2.

LB-Nic2-BE という名前の 2 番目のネットワーク インターフェイスを作成します。

このステップでは、同じロード バランサーのバック エンド プールに割り当て、RDP 用に作成された 2 番目の NAT ルールを関連付けて、2 番目のネットワーク インターフェイスを作成します。

    $backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

最終的に、結果として次の情報が表示されます。


PS C:\ > $ backendnic1


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

### 手順 3.

コマンド Add-AzureVMNetworkInterface を使用して、NIC を仮想マシンに割り当てます。

手順に従って仮想マシンを作成し、ドキュメント、NIC に割り当てるを検索する [の作成とリソース マネージャーと Azure PowerShell で Windows 仮想マシンを事前構成する](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) オプション 4 または 5 です。

## 既存のロード バランサーの更新

### 手順 1

上の例のロード バランサーを使用し、ロード バランサーのオブジェクトを変数 $slb using Get-AzureLoadBalancer に割り当てます。

    $slb=get-azureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

### 手順 2

次の例では、フロント エンドでポート 81、バック エンド プールでポート 8181 を使用し、既存のロード バランサーに新しい受信 NAT 規則を追加します。

    $slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp

### 手順 3.

Set-AzureLoadBalancer を使用して、新しい構成を保存します。

    $slb | Set-AzureLoadBalancer

## ロード バランサーの削除

削除 AzureLoadBalancer コマンドを使用して、名前"NRP LB"リソース グループに「NRP RG」と呼ばれる以前に作成したロード バランサーの削除

    Remove-AzureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

>[AZURE.NOTE] オプションのスイッチ -Force を使用することで、削除のためのプロンプトを回避できます。


## 関連項目

[ロード バランサー分散モードを構成します。](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定を構成します。](load-balancer-tcp-idle-timeout.md)






