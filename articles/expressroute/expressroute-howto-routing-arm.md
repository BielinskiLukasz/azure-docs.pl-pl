<properties
   pageTitle="ExpressRoute 回線のルーティングを構成する方法 |Microsoft Azure"
   description="この記事では、ExpressRoute 回線のプライベート、パブリックおよび Microsoft ピアリングを作成し、プロビジョニングする手順について説明します。 この記事では、回線のピアリングの状態確認、更新、または削除の方法も示します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="cherylmc"/>

# Azure リソース マネージャーと PowerShell を使用した ExpressRoute 回線のルーティングの作成および変更

> [AZURE.SELECTOR]
[PowerShell - クラシック](expressroute-howto-routing-classic.md)
[リソース マネージャーの PowerShell](expressroute-howto-routing-arm.md)

この記事では、PowerShell コマンドレットと Azure リソース マネージャーのデプロイメント モデルを使用して、ExpressRoute 回線のルーティング構成を作成して管理する手順について説明します。  以下の手順では、ExpressRoute 回線の状態確認、ピアリングの更新、または削除およびプロビジョニング解除の方法も示します。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)] 

## 構成の前提条件

- Azure PowerShell モジュールの最新バージョン (バージョン 1.0 以降) が必要です。 
- 参照ことを確認、 [の前提条件](expressroute-prerequisites.md) ] ページで、 [ルーティング要件を満たす](expressroute-routing.md) ページおよび [ワークフロー](expressroute-workflows.md) 構成を開始する前に] ページします。
- アクティブな ExpressRoute 回線が必要です。 指示に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-classic.md) を続行する前に、接続プロバイダーが有効になっている回線があるとします。 ExpressRoute 回線をプロビジョニングされ、有効になっている状態にする必要があります。そうすれば、以下で説明されているコマンドレットを実行できます。

>[AZURE.IMPORTANT] 次の手順は、レイヤー 2 接続サービスを提供するサービス プロバイダーを作成する回線ののみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IPVPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。 そのような場合は、ユーザーがピアリングを作成したり、管理したりすることはできません。 

ExpressRoute 回線用に 1 つ、2 つ、または 3 つすべてのピアリング (Azure プライベート、Azure パブリックおよび Microsoft) を構成することができます。 ピアリングは任意の順序で構成することができます。 ただし、各ピアリングの構成は必ず一度に 1 つずつ完了するようにしてください。 

## Azure プライベート ピアリング

このセクションでは、ExpressRoute 回線用の Azure プライベート ピアリング構成を作成、取得、更新、および削除する方法について説明します。 

### Azure プライベート ピアリングを作成するには

1. **ExpressRoute 用の PowerShell モジュールをインポートします。**
    
    最新の PowerShell インストーラーをインストールする必要があります [PowerShell Gallery](http://www.powershellgallery.com/) し ExpressRoute コマンドレットを使用するために、Azure リソース マネージャー モジュールを PowerShell セッションにインポートします。 管理者として PowerShell を実行する必要があります。

        Install-Module AzureRM

        Install-AzureRM

    既知のセマンティック バージョン範囲内の AzureRM.* モジュールをすべてインポートします。

        Import-AzureRM

    既知のセマンティック バージョン範囲内の選択したモジュールのみをインポートすることもできます。 
        
        Import-Module AzureRM.Network 

    ご使用のアカウントにログオンします。

        Login-AzureRmAccount

    ExpressRoute 回線を作成するサブスクリプションを選択します。
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **ExpressRoute 回線を作成します。**
    
    作成するための指示に従って、 [ExpressRoute 回線](expressroute-howto-circuit-arm.md) 接続プロバイダーから提供されたことがあるとします。 

    接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに要求できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、以下の手順に従います。 

3. **ExpressRoute 回線がプロビジョニングされていることを確認します。**

    まず、ExpressRoute 回線がプロビジョニングされており、有効にもなっているかどうかを確認する必要があります。 次の例を見てください。

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    応答は、以下の例のようになります。

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. **回線用に Azure プライベート ピアリングを構成します。**

    次の手順に進む前に、以下のものがそろっていることを確認します。

    - プライマリ リンク用の /30 サブネット。 これを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。
    - セカンダリ リンク用の /30 サブネット。 これを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。
    - このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
    - ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。 このピアリングではプライベート AS 番号を使用できます。 65515 を使用しないようにしてください。
    - いずれかを使用する場合は、MD5 ハッシュ。 **これは省略可能な**です。
    
    次のコマンドレットを実行して、回線用に Azure プライベート ピアリングを構成することができます。

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    MD5 ハッシュを使用する場合は、以下のコマンドレットを使用できます。

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] ピアリング ASN、ASN 顧客としての AS 番号を指定することを確認します。

### Azure プライベート ピアリングの詳細を取得するには

次のコマンドレットを使用して、構成の詳細を取得することができます。

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### Azure プライベート ピアリングの構成を更新するには

次のコマンドレットを使用して、構成のどの部分でも更新することができます。 次の例では、回路の VLAN ID が 100 から 500 に更新されています。

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Azure プライベート ピアリングを削除するには

以下のコマンドレットを実行して、ピアリング構成を削除することができます。

>[AZURE.WARNING] このコマンドレットを実行する前にすべての仮想ネットワークが ExpressRoute 回線のリンクでないことを確認する必要があります。 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## Azure パブリック ピアリング

このセクションでは、ExpressRoute 回線用の Azure パブリック ピアリング構成を作成、取得、更新および削除する方法について説明します。

### Azure パブリック ピアリングを作成するには

1. **ExpressRoute 用の PowerShell モジュールをインポートします。**
    
    最新の PowerShell インストーラーをインストールする必要があります [PowerShell Gallery](http://www.powershellgallery.com/) し ExpressRoute コマンドレットを使用するために、Azure リソース マネージャー モジュールを PowerShell セッションにインポートします。 管理者として PowerShell を実行する必要があります。

        Install-Module AzureRM

        Install-AzureRM

    既知のセマンティック バージョン範囲内の AzureRM.* モジュールをすべてインポートします。

        Import-AzureRM

    既知のセマンティック バージョン範囲内の選択したモジュールのみをインポートすることもできます。 
        
        Import-Module AzureRM.Network 

    ご使用のアカウントにログオンします。

        Login-AzureRmAccount

    ExpressRoute 回線を作成するサブスクリプションを選択します。
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **ExpressRoute 回線の作成**
    
    作成するための指示に従って、 [ExpressRoute 回線](expressroute-howto-circuit-arm.md) 接続プロバイダーから提供されたことがあるとします。 

    接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに要求できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、以下の手順に従います。

3. **ExpressRoute 回線がプロビジョニングされていることを確認します。**

    まず、ExpressRoute 回線がプロビジョニングされており、有効にもなっているかどうかを確認する必要があります。 次の例を見てください。

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    応答は、以下の例のようになります。

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. **回線用に Azure パブリック ピアリングを構成します。**

    作業を続行する前に、次の情報がそろっていることを確認します。

    - プライマリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。
    - セカンダリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。
    - このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
    - ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。 このピアリングにはパブリック AS 番号を使用する必要があります。
    - いずれかを使用する場合は、MD5 ハッシュ。 **これは省略可能な**です。
    
    次のコマンドレットを実行して、回線用に Azure プライベート ピアリングを構成することができます。

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    MD5 ハッシュを使用する場合は、次のコマンドレットを使用することができます。

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] ピアリング ASN あり、ASN お客様として AS 番号を指定することを確認します。

### Azure パブリック ピアリングの詳細を取得するには

次のコマンドレットを使用して、構成の詳細を取得することができます。

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### Azure パブリック ピアリング構成を更新するには

次のコマンドレットを使用して、構成のどの部分も更新することができます。

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

上記の例では、回線の VLAN ID は 200 から 600 に更新されています。

### Azure パブリック ピアリングを削除するには

次のコマンドレットを実行して、ピアリング構成を削除することができます。

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Microsoft ピアリング

このセクションでは、ExpressRoute 回線の Microsoft ピアリング構成を作成、取得、更新および削除する方法について説明します。 

### Microsoft ピアリングを作成するには

1. **ExpressRoute 用の PowerShell モジュールをインポートします。**
    
    最新の PowerShell インストーラーをインストールする必要があります [PowerShell Gallery](http://www.powershellgallery.com/) し ExpressRoute コマンドレットを使用するために、Azure リソース マネージャー モジュールを PowerShell セッションにインポートします。 管理者として PowerShell を実行する必要があります。

        Install-Module AzureRM

        Install-AzureRM

    既知のセマンティック バージョン範囲内の AzureRM.* モジュールをすべてインポートします。

        Import-AzureRM

    既知のセマンティック バージョン範囲内の選択したモジュールのみをインポートすることもできます。 
        
        Import-Module AzureRM.Network 

    ご使用のアカウントにログオンします。

        Login-AzureRmAccount

    ExpressRoute 回線を作成するサブスクリプションを選択します。
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **ExpressRoute 回線の作成**
    
    作成するための指示に従って、 [ExpressRoute 回線](expressroute-howto-circuit-arm.md) 接続プロバイダーから提供されたことがあるとします。 

    接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに要求できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、以下の手順に従います。

3. **ExpressRoute 回線がプロビジョニングされていることを確認します。**

    まず、ExpressRoute 回線がプロビジョニングされており、有効にもなっているかどうかを確認する必要があります。 次の例を見てください。

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    応答は、以下の例のようになります。

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. **回線用に Microsoft ピアリングを構成します。**

    続行する前に、次の情報を確認してください。

    - プライマリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。
    - セカンダリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。
    - このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
    - ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。 パブリック AS 番号のみを使用する必要があります。 AS 番号を所有する必要があります。
    - アドバタイズされたプレフィックス: BGP セッションを介してアドバタイズする予定のすべてのプレフィックスのリストを指定する必要があります。 パブリック IP アドレス プレフィックスのみが受け入れられます。 一連のプレフィックスを送信する予定の場合は、コンマ区切りのリストを送信できます。 これらのプレフィックスは、RIR/IRR に登録する必要があります。
    - 顧客 ASN: ピアリング AS 番号に登録されていないプレフィックスをアドバタイズする場合は、そのプレフィックスが登録されている AS 数を指定できます。 **これは省略可能な**です。
    - ルーティング レジストリ名: AS 番号とプレフィックスを登録する RIR/IRR を指定することができます。
    - いずれかを使用する場合は、MD5 ハッシュ。 **これは省略可能です。**
    
    次のコマンドレットを実行して、回線用に Microsoft ピアリングを構成することができます。

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MircosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MircosoftConfigCustomerAsn 23 -MircosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Microsoft ピアリングの詳細を取得するには

次のコマンドレットを使用して、構成の詳細を取得できます。

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt


### Microsoft ピアリング構成を更新するには

次のコマンドレットを使用して、構成のどの部分でも更新することができます。

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MircosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MircosoftConfigCustomerAsn 23 -MircosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### Microsoft ピアリングを削除するには

以下のコマンドレットを実行して、ピアリング構成を削除することができます。

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## 次のステップ

次の手順では、ExpressRoute 回線に VNet をリンクします。 使用する [このテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) Azure リソース マネージャーの配置モードを使用する場合。 PowerShell の手順は現在作成中です。


-  ExpressRoute のワークフローの詳細については、次を参照してください。 [ExpressRoute ワークフロー](expressroute-workflows.md)します。

-  回路のピアリングの詳細については、次を参照してください。 [ExpressRoute 回路とルーティング ドメイン](expressroute-circuit-peerings.md)します。

-  仮想ネットワークの使用方法の詳細については、次を参照してください。 [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)します。


