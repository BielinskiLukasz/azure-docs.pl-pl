<properties
   pageTitle="ExpressRoute の FAQ"
   description="ExpressRoute の FAQ には、サポートされている Azure サービス、料金、データと接続、SLA、プロバイダーと提供地域、帯域幅、およびその他の技術的な詳細に関する情報が記載されています。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/20/2015"
   ms.author="cherylmc"/>

# ExpressRoute の FAQ


## ExpressRoute とは何ですか。
ExpressRoute は、Microsoft のデータセンターとオンプレミスや共用施設にあるインフラストラクチャの間にプライベート接続を作成できる Azure サービスです。 ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

### ExpressRoute とプライベート ネットワーク接続を使用する利点は何ですか。
ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も一貫して短く、高速です。 オンプレミスのデバイスと Azure の間のデータ転送に ExpressRoute 接続を使用することで、大きなコスト上のメリットが得られる場合があります。

### ExpressRoute でサポートされる Microsoft クラウド サービスはどれですか。
現時点では、Office 365 を含むほとんどの Microsoft Azure サービスが ExpressRoute でサポートされています。  一般公開に関する最新情報をチェックしてください。

### このサービスはどこで使用できますか。
サービスの場所と可用性のためには、このページを参照してください: [ExpressRoute パートナーと提供地域](expressroute-locations.md)します。

### ExpressRoute のパートナー通信会社のいずれとも契約していない場合は、どのようにして ExpressRoute を使用して Microsoft に接続できますか。
地域の通信会社と地上イーサネット接続を選択して、Exchange プロバイダーの所在地のいずれかに接続します。 その後、このプロバイダーの所在地で Microsoft とピアリングできます。 最後のセクションを確認して [ExpressRoute パートナーと提供地域](expressroute-locations.md) を参照し、サービス プロバイダーが Exchange の場所のいずれかに存在します。 存在すれば、サービス プロバイダーを通じて ExpressRoute 回線を Azure に接続するように依頼できます。

### ExpressRoute の料金はいくらですか。
確認 [料金の詳細](http://azure.microsoft.com/pricing/details/expressroute/) 料金情報についてです。

### 特定の帯域幅の ExpressRoute 回線に対して料金を支払っている場合、ネットワーク サービス プロバイダーから同じ速度の VPN 接続を購入する必要がありますか。
いいえ、できません。 任意の速度の VPN 接続は、サービス プロバイダーから購入できます。 ただし、Azure への接続は、購入した ExpressRoute 回線の帯域幅に制限されます。

### 特定の帯域幅の ExpressRoute 回線に対して料金を支払っている場合、必要に応じてより高速にバーストすることはできますか。
はい。 ExpressRoute 回線は、購入した帯域幅の 2 倍まで無料でバーストできるように構成されています。 サービス プロバイダーがこの機能をサポートしているかどうかをご確認ください。

### 同じプライベート ネットワーク接続を、Virtual Network や他の Azure サービスに同時に使用できますか。
はい。 ExpressRoute 回線のセットアップが完了すると、1 本の回線で Virtual Network 内のサービスや他の Azure サービスに同時にアクセスできるようになります。 Virtual Network にはプライベート ピアリング パス経由で接続し、他のサービスにはパブリック ピアリング パス経由で接続します。

### ExpressRoute ではサービス レベル アグリーメント (SLA) は提供されますか。
参照してください、 [ExpressRoute SLA ページ](http://azure.microsoft.com/support/legal/sla/) の詳細。

## サポートされているサービス
ほとんどの Azure サービスが、ExpressRoute でサポートされます。

- 仮想ネットワークにデプロイされている Virtual Machines と Cloud Services への接続は、プライベート ピアリング パス経由でサポートされます。
- Azure Websites は、パブリック ピアリング パス経由でサポートされます。
- Office 365 は、Microsoft ピアリング パス経由でサポートされます。
- その他のサービスには、パブリック ピアリング パス経由でアクセスできます。 ただし、次のような例外があります。

    **次のサービスはサポートされません。**

    - CDN
    - Visual Studio Team Services のロード テスト
    - Multi-factor Authentication

## データおよび接続

### ExpressRoute を使用して転送できるデータの量に制限はありますか。
データ転送量に対する制限はありません。 参照してください [料金の詳細](http://azure.microsoft.com/pricing/details/expressroute/) 帯域幅使用料についてです。

### ExpressRoute でサポートされる接続速度はどれくらいですか。
サポートされる帯域幅:

|50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 gbps、2 Gbps、5 Gbps、10Gbps|

### どのサービス プロバイダーを使用できますか。
参照してください [ExpressRoute パートナーと提供地域](expressroute-locations.md) サービス プロバイダーと場所の一覧です。

## 技術的な詳細

### オンプレミスの場所から Azure に接続するための技術的な要件は何ですか。
参照してください [ExpressRoute の前提条件ページ](expressroute-prerequisites.md) の要件です。

### ExpressRoute への接続は冗長化されていますか。
はい。 各 Express Route 回線にクロス接続の冗長ペアが設定され、高可用性を実現するように構成されています。

### ExpressRoute リンクのいずれかに障害が発生すると接続できなくなりますか。
クロス接続の一方に障害が発生しても、接続が失われることはありません。 ネットワークの負荷に対応できる冗長接続が用意されています。 さらに、耐障害性を実現するために、複数の回線を別のピアリング場所に作成することもできます。

### このサービスを機能させるには、両方のリンクを構成する必要がありますか。
レイヤー 3 サービスを提供するパートナー経由で接続している場合は、そのパートナーがお客様に代わって冗長リンクを構成します。 ただし、クラウド エクスチェンジ プロバイダーと同じ場所にいる場合は、クラウド エクスチェンジ プラットフォームへの 2 本の LAN リンクを構成する必要があります。 プライベート データセンターから単一の WAN リンク経由でクラウド プロバイダーに接続する場合は、独自のルーターの WAN リンクを終結させてから、クラウド エクスチェンジ プラットフォームへの 2 本の LAN リンクを構成する必要があります。  

### 自社の VLAN を、ExpressRoute を使用して Azure に拡張することはできますか。
いいえ、できません。 Azure にレイヤー 2 接続延長をサポートはされていません。

### 1 つのサブスクリプションで複数の ExpressRoute 回線を使用できますか。
はい。 1 つのサブスクリプションで、複数の ExpressRoute 回線をご利用いただけます。 専用回線の既定の上限は 10 に設定されています。 上限を増やす必要がある場合は、Microsoft サポートにご連絡ください。

### 別のサービス プロバイダーから ExpressRoute 回線を使用することはできますか。
はい。 多くのサービス プロバイダーで ExpressRoute 回線をご利用いただけます。 各 ExpressRoute 回線は、1 つのサービス プロバイダーのみに関連付けられます。

### ExpressRoute 回線に仮想ネットワークを接続するにはどうすればいいですか。
基本的な手順の概略は次のとおりです。

- ExpressRoute 回線を確立し、サービス プロバイダーに依頼してその回線を有効にします。
- お客様またはプロバイダーは、BGP ピアリングを構成する必要があります。
- 仮想ネットワークを ExpressRoute 回線に接続します。

参照してください [回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー](expressroute-workflows.md) の詳細。

### 当社の ExpressRoute 回線に接続境界はありますか。
はい。 [ExpressRoute パートナーと提供地域](expressroute-locations.md) ページは、ExpressRoute 回線の接続境界の概要を説明します。 ExpressRoute 回線の接続は、1 つの地理的リージョンに制限されます。 ExpressRoute Premium 機能を有効にすると、地理的リージョンを越えて接続を拡張できます。

### 複数の仮想ネットワークを ExpressRoute 回線に接続できますか。
はい。 最大 10 個の仮想ネットワークを ExpressRoute 回線に接続できます。

### 仮想ネットワークを含む複数の Azure サブスクリプションがあります。 個別のサブスクリプション内の仮想ネットワークを 1 つの ExpressRoute 回線に接続できますか。
はい。 最大 10 個の Azure サブスクリプションで、1 つの ExpressRoute 回線を使用することを承認できます。 ExpressRoute Premium 機能を有効にすると、この上限を増やすことができます。

詳細については、次を参照してください。 [複数のサブスクリプションで ExpressRoute 回線を共有](expressroute-share-circuit.md)します。

### 同じ回線に接続されている仮想ネットワークは、互いに分離されていますか。
いいえ、できません。 同じ ExpressRoute 回線にリンクされているすべての仮想ネットワークは、同じルーティング ドメインの一部であるし、ルーティングの観点から互いから分離されていません。 ルートの分離が必要な場合は、ExpressRoute 回線を別に作成する必要があります。

### 1 つの仮想ネットワークを複数の ExpressRoute 回線に接続できますか。
はい。 1 つの仮想ネットワークを最大 4 つの ExpressRoute 回線に接続できます。 これらは、4 つの異なる場所で注文する必要があります。

### ExpressRoute 回線に接続された仮想ネットワークから、インターネットにアクセスできますか。
はい。 BGP セッションを介して既定ルート (0.0.0.0/0) またはインターネット ルート プレフィックスをアドバタイズしていなければ、ExpressRoute 回線に接続された仮想ネットワークからインターネットに接続できます。

### ExpressRoute 回線に接続されている仮想ネットワークに対するインターネット接続をブロックできますか。
はい。 既定ルート (0.0.0.0/0) をアドバタイズして仮想ネットワーク内にデプロイされた仮想マシンへのインターネット接続をすべてブロックし、すべてのトラフィックが ExpressRoute 回線を経由しないようにルーティングすることができます。 ただし、既定ルートをアドバタイズすると、パブリック ピアリング経由で提供されるサービス (Azure Storage や SQL Database など) へのトラフィックが強制的にオンプレミスにルーティングされるので注意してください。 パブリック ピアリング パス経由またはインターネット経由でトラフィックを Azure に返すようにルーターを構成する必要があります。

### 同じ ExpressRoute 回線に接続されている仮想ネットワークは互いに通信できますか。
はい。 同じ ExpressRoute 回線に接続されている仮想ネットワークにデプロイされた仮想マシンは互いに通信できます。

### 仮想ネットワーク用のサイト間接続を、ExpressRoute と併用できますか。
はい。 ExpressRoute は、サイト間 VPN と共存できます。

### 仮想ネットワークを、サイト間/ポイント対サイト構成から ExpressRoute を使用する構成に移行できますか。
はい。 仮想ネットワーク内に ExpressRoute ゲートウェイを作成する必要があります。 このプロセスに関連して、わずかにダウンタイムが発生します。

### ExpressRoute を経由して Azure Storage に接続するには何が必要ですか。
ExpressRoute 回線を確立し、パブリック ピアリング用のルートを構成する必要があります。

### アドバタイズできるルートの数に上限はありますか。
はい。 プライベート ピアリングおよびパブリック ピアリング用に使用できるルート プレフィックスは最大 4,000 個です。 ExpressRoute Premium 機能を有効にすると、この上限を 10,000 ルートに増やすことができます。

### BGP セッションを介してアドバタイズできる IP 範囲に制限はありますか。
BGP を介してアドバタイズされるプレフィックスは /29 以上 (/28 ～ /8) である必要があります。

パブリック ピアリング BGP セッションでは、プライベート プレフィックス (RFC1918) は除外されます。

### BGP の上限を超えるとどうなりますか。
BGP セッションが切断されます。 プレフィックス数が上限未満になると、BGP セッションはリセットされます。

### ExpressRoute BGP ホールド時間とは何ですか。 それは調整できますか。
ホールド時間は 180 です。 キープ アライブ メッセージは、60 秒ごとに送信されます。 これらは Microsoft 側の固定設定であり、変更することはできません。

### 仮想ネットワークへの既定ルート (0.0.0.0/0) のアドバタイズ後に、Azure Virtual Network で実行する Windows をライセンス認証できません。 どうしたらいいですか。
次の手順を実行して、Azure にライセンス認証要求を認識させることができます。

1. ExpressRoute 回線用にパブリック ピアリングを確立します。
2. DNS 参照を実行しの IP アドレスを見つける **kms.core.windows.net**
3. 次の 2 つの項目のいずれかを実行すると、Azure からのライセンス認証要求がキー管理サービスによって認識され、要求が受け付けられます。
    - オンプレミス ネットワークで、(手順 2 で取得した) IP アドレス宛てのトラフィックをパブリック ピアリング経由で Azure にルーティングする。
    - NSP プロバイダーに依頼して、トラフィックをパブリック ピアリング経由で Azure に U ターンしてもらう。

### ExpressRoute 回線の帯域幅を変更できますか。
はい。 ExpressRoute 回線を切断せずに、帯域幅を増やすことができます。 接続プロバイダーに連絡して、帯域幅の増加をサポートするようにネットワーク内の調整を更新してもらう必要があります。 ただし、ExpressRoute 回線の帯域幅を減らすことはできなくなります。 帯域幅を減らす必要が生じた場合は、ExpressRoute 回線の切断と再作成が必要になります。

### ExpressRoute 回線の帯域幅を変更するには、どうすればいいですか。
専用回線の更新用の API や PowerShell コマンドレットを使用して、ExpressRoute 回線の帯域幅を更新できます。

## ExpressRoute premium

### ExpressRoute Premium とは何ですか。
ExpressRoute Premium は、次に示す機能で構成されたサービスです。

 - パブリック ピアリングおよびプライベート ピアリング用ルートの上限が 4,000 から 10,000 に増加されたルーティング テーブル。
 - ExpressRoute 回線に接続できる数が増加された VNET (既定は 10)。 詳細については、下の表を参照してください。
 - Microsoft のコア ネットワーク経由のグローバル接続。 ある地理的リージョンにある VNET を別のリージョン内の ExpressRoute 回線に接続できるようになります。 **例:** シリコン バレーで作成された ExpressRoute 回線に西ヨーロッパで作成された VNet をリンクすることができます。

### ExpressRoute Premium を有効にすると、どれくらいの VNET を ExpressRoute 回線に接続できますか。
ExpressRoute 回線に接続できる VNET 数の上限の増加分は、次の表のとおりです。 既定の上限は 10 です。

**回線の制限**

| **回線のサイズ** | **既定で設定される VNET リンクの数** | **ExpressRoute Premium の VNET リンクの数** |
|--------------|----------------------------------------|-----------------------------------------------|
| 50 Mbps      | 10                                     | サポートされていません                                 |
| 100 Mbps     | 10                                     | 20                                            |
| 200 Mbps     | 10                                     | 25                                            |
| 500 Mbps     | 10                                     | 40                                            |
| 1 Gbps       | 10                                     | 50                                            |
| 2 Gbps       | 10                                     | 60                                            |
| 5 Gbps       | 10                                     | 75                                            |
| 10 Gbps      | 10                                     |  100                                          |



### ExpressRoute Premium はどのようにして有効にしますか。
ExpressRoute Premium の機能は、ExpressRoute 機能を有効にする際に有効にでき、回線の状態を更新することで無効にできます。 ExpressRoute Premium の有効化は、回線の作成時のほか、専用回線の更新用の API や PowerShell コマンドレットを呼び出して行うこともできます。

### ExpressRoute Premium はどのようにして無効にしますか。
ExpressRoute Premium を無効にするには、専用回線の更新用の API や PowerShell コマンドレットを呼び出します。ExpressRoute Premium を無効にする前に、接続ニーズが既定の上限を超えて増大していないことを確認する必要があります。 使用量が既定の上限を超えて増大している場合は、ExpressRoute Premium を無効にする要求が失敗します。

### Premium の機能セットから必要な機能だけを選択できますか。
いいえ、できません。 必要な機能を選択することはできません。 ExpressRoute Premium を有効にすると、すべての機能が有効になります。

### ExpressRoute Premium の料金はいくらですか。
参照してください [料金の詳細](http://azure.microsoft.com/pricing/details/expressroute/) コストです。

### ExpressRoute Premium の料金は、標準の ExpressRoute 料金に追加して支払うのですか。
はい。 ExpressRoute Premium 料金は、ExpressRoute 回線の料金と接続プロバイダーに必要な料金に追加する形で適用されます。

## ExpressRoute と Office 365

### Office 365 サービスに接続する ExpressRoute 回線はどのようにして作成しますか。

1. レビュー、  [ExpressRoute の前提条件ページ](expressroute-prerequisites.md) ページの要件を満たしていることを確認します。
2. サービス プロバイダーとの場所の一覧を確認 [ExpressRoute パートナーと提供地域](expressroute-locations.md) を接続ニーズを満たしていることを確認します。
3. 確認することで、容量の要件を計画 [ネットワーク プランニングと Office 365 のパフォーマンス チューニング](http://aka.ms/tune/)します。
4. 以下のワークフローに示されている手順に従って、接続を設定します。
    [回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー](expressroute-workflows.md)します。

### 既存の ExpressRoute 回線で Office 365 サービスへの接続をサポートできますか。
はい。 既存の ExpressRoute 回線を、Office 365 サービスへの接続をサポートするように構成できます。 Office 365 サービスに接続するのに十分な容量があることを確認してください。 [ネットワーク プランニングと Office 365 のパフォーマンス チューニング](http://aka.ms/tune/) 、接続ニーズを計画するのに役立ちます。 参照してください [を作成し、ExpressRoute 回線を変更](expressroute-howto-circuit-classic.md)します。

### ExpressRoute 接続経由でアクセスできる Office 365 のサービスはどれですか。

**次の Office 365 サービスがサポートされます。**

- Exchange Online と Exchange Online Protection
- SharePoint Online
- Skype for Business Online
- Office Online
- Azure Active Directory と Azure AD Sync
- Office 365 ビデオ
- Power BI
- Delve
- Project Online

**次の Office 365 サービスはサポートされません。**

- Yammer
- Office 365 ProPlus クライアントのダウンロード
- オンプレミス ID プロバイダーのサインイン
- 中国での Office 365 (21 Vianet が運営) サービス

これらのサービスにはインターネット経由で接続できます。

### ExpressRoute for Office 365 の料金はいくらですか。
ExpressRoute 経由で Office 365 に接続するのに追加コストはかかりません。   [料金の詳細ページ](http://azure.microsoft.com/pricing/details/expressroute/) ExpressRoute のコストの詳細について説明します。

### ExpressRoute for Office 365 はどのリージョンでサポートされていますか。
参照してください [ExpressRoute パートナーと提供地域](expressroute-locations.md) パートナーと ExpressRoute がサポートされている地域の一覧についてです。

### 自社で ExpressRoute が構成されている場合でも、インターネット経由で Office 365 にアクセスできますか。
はい。 自社のネットワークで ExpressRoute が構成されている場合でも、インターネット経由で Office 365 サービスのエンドポイントにアクセスできます。 ExpressRoute 経由で Office 365 サービスに接続するように構成されている場所では、ExpressRoute 経由で接続します。
 

