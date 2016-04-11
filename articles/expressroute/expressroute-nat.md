<properties
   pageTitle="ExpressRoute 回線の NAT 要件 | Microsoft Azure"
   description="このページでは、ExpressRoute 回線の NAT を構成および管理するための詳細な要件について説明します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2015"
   ms.author="cherylmc"/>

# ExpressRoute NAT の要件

ExpressRoute を使用して Microsoft クラウド サービスに接続するには、NAT をセットアップして管理する必要があります。 一部の接続プロバイダーでは、NAT のセットアップと管理が管理されたサービスとして提供されています。 このようなサービスが提供されているかどうか、接続プロバイダーに問い合わせてください。 このようなサービスが提供されていない場合は、次に示す要件に従う必要があります。 

レビュー、 [ExpressRoute 回路とルーティング ドメイン](expressroute-circuit-peerings.md) ルーティング ドメインは、さまざまな概要を表示するページです。 Azure パブリックと Microsoft ピアリングのパブリック IP アドレス要件を満たすために、ネットワークと Microsoft の間に NAT をセットアップすることをお勧めします。 このセクションでは、セットアップする NAT インフラストラクチャに関して説明します。

## Azure パブリック ピアリングの NAT 要件

Azure パブリック ピアリング パスを利用すれば、パブリック IP アドレスで Azure にホストされているすべてのサービスに接続できます。 すべてのサービスが一覧にサービスが含まれます、 [ExpessRoute FAQ](expressroute-faqs.md) と Microsoft Azure での Isv によってホストされているすべてのサービスです。 パブリック ピアリングでの Microsoft Azure への接続は常にネットワークから Microsoft ネットワークに対して開始されます。 パブリック ピアリングで Microsoft Azure に向かうトラフィックは有効な IPv4 アドレスに SNAT 変換しないと、Microsoft ネットワークに入れません。 下の図は、上記の要件を満たすように NAT をセットアップするしくみを上のレベルで示しています。

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### NAT IP プールとルート アドバタイズ

トラフィックが有効なパブリック IPv4 アドレスで Azure パブリック ピアリング パスに入っていることを確認する必要があります。 Microsoft はリージョンのルーティング インターネット レジストリ (RIR) またはインターネット ルーティング レジストリ (IRR) に対して IPv4 NAT アドレスの所有権を検証する必要があります。 ピアリングされている AS 番号と NAT に使用されている IP アドレスに基づいて確認されます。 参照してください、 [ExpressRoute ルーティング要件を満たす](expressroute-routing.md) ルーティング レジストリでは、ページです。
 
このピアリングでアドバタイズされる NAT IP プレフィックスの長さには制約がありません。 NAT プールを監視し、NAT セッションが不足していないことを確認する必要があります。

>[AZURE.IMPORTANT] インターネットに Microsoft に提供される NAT IP プールを提供しない必要があります。 他の Microsoft サービスへの接続が切断されます。

## Microsoft ピアリングの NAT 要件

Microsoft ピアリング パスにより、Azure パブリック ピアリング パスでサポートされていない Microsoft クラウド サービスに接続できます。 そのようなサービスには、Exchange Online、SharePoint Online、Skype for Business、CRM Online のような Office 365 サービスがあります。 Microsoft は今後、Microsoft ピアリングで双方向の接続をサポートする予定です。 Microsoft クラウド サービスに向かうトラフィックは有効な IPv4 アドレスに SNAT 変換しないと、Microsoft ネットワークに入れません。 Microsoft クラウド サービスからご利用のネットワーク方向のトラフィックは SNAT 変換しないと、ネットワークに入れません。 下の図は、Microsoft ピアリングのために NAT をセットアップするしくみを上のレベルで示しています。
 
![](./media/expressroute-nat/expressroute-nat-microsoft.png) 


#### ご利用のネットワークから Microsoft に送信されるトラフィック

- トラフィックが有効なパブリック IPv4 アドレスで Microsoft ピアリング パスに入っていることを確認する必要があります。 Microsoft はリージョンのルーティング インターネット レジストリ (RIR) またはインターネット ルーティング レジストリ (IRR) に対して IPv4 NAT アドレスの所有者を検証する必要があります。 ピアリングされている AS 番号と NAT に使用されている IP アドレスに基づいて確認されます。 参照してください、 [ExpressRoute ルーティング要件を満たす](expressroute-routing.md) ルーティング レジストリでは、ページです。

- Azure パブリック ピアリング セットアップと他の ExpressRoute 回線に使用する IP アドレスは BGP セッションで Microsoft にアドバタイズしないでください。 このピアリングでアドバタイズされる NAT IP プレフィックスの長さには制約がありません。

    >[AZURE.IMPORTANT] インターネットに Microsoft に提供される NAT IP プールを提供しない必要があります。 他の Microsoft サービスへの接続が切断されます。

#### Microsoft からあなたのネットワークに送信されるトラフィック

- 一部のシナリオでは、あなたのネットワーク内でホストされているサービス エンドポイントへの接続を Microsoft が開始する必要があります。 そのようなシナリオの典型的な例は、Office 365 からあなたのネットワークでホストされている ADFS サービスに接続する場合です。 そのような場合は、ネットワークから Microsoft ピアリングに適切なプレフィックスをリークする必要があります。 

- Microsoft からあなたのネットワーク内にある IP アドレスに送信されるトラフィックを SNAT 変換する必要があります。 

## 次のステップ

- 要件を参照して [ルーティング](expressroute-routing.md) と [QoS](expressroute-qos.md)します。
- ワークフローの詳細については、次を参照してください。 [ExpressRoute 回線のプロビジョニング ワークフローと状態を回線](expressroute-workflows.md)します。
- ExpressRoute 接続を構成します。

    - [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md)
    - [ルーティングの構成](expressroute-howto-routing-classic.md)
    - [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-classic.md)


