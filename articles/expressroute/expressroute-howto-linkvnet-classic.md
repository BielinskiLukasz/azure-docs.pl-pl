<properties 
   pageTitle="ExpressRoute 回線への仮想ネットワークのリンク |Microsoft Azure"
   description="このドキュメントでは、ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要を示します。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/02/2015"
   ms.author="cherylmc" />


# ExpressRoute 回線への仮想ネットワークのリンク

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)


- [PowerShell のリソース マネージャー](expressroute-howto-linkvnet-arm.md)
- [テンプレート - Azure リソース マネージャー](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

この記事では、ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要を示します。 仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。 この記事は、クラシック デプロイ モデルを使用してデプロイされた VNet に適用されます。 Azure リソース マネージャーのデプロイメント方法を使用してデプロイされた仮想ネットワークをリンクする場合は、テンプレートを使用して行うことができます。 テンプレートに移動する場合は、上のタブを参照してください。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 構成の前提条件

- Azure PowerShell モジュールの最新バージョンが必要になります。 PowerShell セクションから最新の PowerShell モジュールをダウンロードすることができます、 [Azure のダウンロード ページ](http://azure.microsoft.com/downloads)します。 上の指示に従って、 [をインストールして、Azure PowerShell を構成する方法](../powershell-install-configure.md) 手順については、Azure PowerShell モジュールを使用するようコンピューターを構成する方法に関するページ。
- 参照ことを確認、 [の前提条件](expressroute-prerequisites.md) ] ページで、  [ルーティング要件を満たす](expressroute-routing.md) ページおよび [ワークフロー](expressroute-workflows.md) 構成を開始する前に] ページします。
- アクティブな ExpressRoute 回線が必要です。
    - 指示に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-classic.md) 回線の接続プロバイダーが有効になっているとします。
    - 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 参照してください、 [ルーティングを構成する](expressroute-howto-routing-classic.md) ルーティング情報の記事です。
    - Azure プライベート ピアリングを構成する必要があります。また、ネットワークと Microsoft 間の BGP ピアリングを起動して、エンド ツー エンド接続を有効にする必要があります。

最大 10 個の VNet を ExpressRoute 回線にリンクできます。 ExpressRoute 回線はすべて同じ地理的リージョンにある必要があります。 ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線に多数の仮想ネットワークをリンクできます。 チェック アウト、 [FAQ](expressroute-faqs.md) premium アドオンの詳細についてです。

## 同じ Azure サブスクリプションの VNet を ExpressRoute 回線にリンクする

次のコマンドレットを使用して、ExpressRoute 回線に仮想ネットワークをリンクすることができます。 コマンドレットを実行する前に、仮想ネットワーク ゲートウェイが作成されており、リンクの準備ができていることを確認してください。

    C:\> New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## 異なる Azure サブスクリプションの VNet を ExpressRoute 回線にリンクする

複数のサブスクリプションで ExpressRoute 回線を共有することができます。 下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。 大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。 組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。 1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。 組織内の他のサブスクリプションも ExpressRoute 回線を使用できます。
>[AZURE.NOTE] 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。 すべての仮想ネットワークが同じ帯域幅を共有します。

![サブスクリプション間接続](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### 管理

*回線所有者*とは、ExpressRoute 回線が作成されたサブスクリプションの管理者または共同管理者です。 回線所有者は、他のサブスクリプションの管理者または共同管理者 (ワークフロー図では*回線ユーザー*と呼ぶ) が所有する専用回線を使用することを承認できます。 組織の ExpressRoute 回線の使用を承認される回線ユーザーは、いったん承認されると、サブスクリプションの VNet を ExpressRoute 回線にリンクできるようになります。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。 承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンクが削除されます。

### 回線所有者の操作

#### 承認の作成

回線所有者は、その他のサブスクリプションの管理者が指定された回線を使用することを承認します。 次の例では、回線 (Contoso IT) の管理者は、Microsoft ID を指定して、別のサブスクリプション (Dev-Test) の管理者が最大 2 個の VNET を回線にリンクできるようにします。 コマンドレットは、指定された Microsoft ID に電子メールを送信しません。 回線所有者が、承認が完了したことをその他のサブスクリプション所有者に明示的に通知する必要があります。

        PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
    
        Description         : Dev-Test Links 
        Limit               : 2 
        LinkAuthorizationId : ********************************** 
        MicrosoftIds        : devtest@contoso.com 
        Used                : 0

#### 承認の確認

回線所有者は、次のコマンドレットを実行して、特定の回線で発行されるすべての承認を確認できます。

    PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
    
    Description         : EngineeringTeam 
    Limit               : 3 
    LinkAuthorizationId : #################################### 
    MicrosoftIds        : engadmin@contoso.com 
    Used                : 1 
    
    Description         : MarketingTeam 
    Limit               : 1 
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
    MicrosoftIds        : marketingadmin@contoso.com 
    Used                : 0 
    
    Description         : Dev-Test Links 
    Limit               : 2 
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    MicrosoftIds        : salesadmin@contoso.com 
    Used                : 2 

#### 承認の更新

回線所有者は、次のコマンドレットを使用して承認を変更できます。

    PS C:\> set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
    
    Description         : Dev-Test Links 
    Limit               : 5 
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    MicrosoftIds        : devtest@contoso.com 
    Used                : 0

#### 承認の削除

回線所有者は、次のコマンドレットを実行して、ユーザーに対する承認を取り消したり削除したりすることができます。

    PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"

### 回線ユーザーの操作

#### 承認の確認

回線ユーザーは、次のコマンドレットを使用して承認を確認できます。

    PS C:\> Get-AzureAuthorizedDedicatedCircuit
    
    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

#### リンク承認の適用

回線ユーザーは以下のコマンドレットを実行して、リンク承認を適用することができます。

    PS C:\> New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
    
    State VnetName 
    ----- -------- 
    Provisioned SalesVNET1

## 次のステップ

ExpressRoute の詳細については、次を参照してください。、 [ExpressRoute の FAQ](expressroute-faqs.md)します。






