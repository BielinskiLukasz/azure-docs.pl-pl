<properties
   pageTitle="ExpressRoute の場所 | Microsoft Azure"
   description="この記事では、サービスが提供されている場所と Azure リージョンに接続する方法の詳細について説明します。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="cherylmc" />

# ExpressRoute パートナーとピアリングの場所

この記事の表では、ExpressRoute 接続プロバイダー、ExpressRoute の地理的範囲、ExpressRoute でサポートされる Microsoft クラウド サービス、ExpressRoute システム インテグレーター (SI) に関する情報を提供します。

## ExpressRoute 接続プロバイダー

ExpressRoute は、すべての Azure のリージョンと場所でサポートされています。 次のマップは Azure のリージョンと ExpressRoute の場所を一覧しています。 ExpressRoute の場所は、Microsoft がいくつかのサービス プロバイダーと接続している場所を示します。

![](./media/expressroute-locations/expressroute-locations-map.png)

地理的リージョン内の少なくとも 1 つの ExpressRoute の場所に接続している場合は、その地理的リージョン内のすべてのリージョンの Azure サービスにアクセスできます。 次の表は、地理的リージョン、ExpressRoute の場所、Azure リージョンをまとめたものです。

|**地理的リージョン**|**Azure Azure リージョン**|**ExpressRoute の場所**|
|---|---|---|
|**北米**|米国東部、米国西部、米国東部 2、米国中部、米国中南部、米国中北部、カナダ中部、カナダ東部|アトランタ、シカゴ、ダラス、ロサンゼルス、ニューヨーク、シアトル、シリコン バレー、ワシントン DC、モントリオール+、トロント+|
|**南アメリカ**|ブラジル南部|サンパウロ|
|**ヨーロッパ**|北ヨーロッパ、西ヨーロッパ|アムステルダム、ダブリン+、ロンドン|
|**アジア**|東アジア、東南アジア|香港、シンガポール|
|**日本**|西日本、東日本|大阪、東京|
|**オーストラリア**|オーストラリア東部、オーストラリア南東部|メルボルン、シドニー|
|**インド**|インド西部、インド中部、インド南部|チェンナイ、ムンバイ|



次の表は、国内のクラウドのリージョンと地政学的境界に関する情報をまとめたものです。

|**地理的リージョン**|**Azure リージョン**|**ExpressRoute の場所**|
|---|---|---|---|
|**米国政府クラウド**|米国政府アイオワ州、米国政府 Virginia|アイオワ州、Virginia|シカゴ、ワシントン州 DC|
|**中国のクラウド**|中国北部、中国 East|北京、Shanghai|


標準の ExpressRoute SKU では、地理的リージョン間の接続はサポートされていません。 グローバル接続に対応するには、ExpressRoute Premium アドオンを有効にする必要があります。 国内のクラウド環境には接続できません。 国内のクラウド環境に接続する必要がある場合、接続プロバイダーにご相談ください。


## 接続プロバイダーの場所

### 運用 Azure

| **サービス プロバイダー**  |**Microsoft Azure** | **Office 365 と CRM Online** | **場所** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka ネットワーク]( http://www.aryaka.com/)** | サポートされています | サポートされています | アムステルダム、シリコン バレー、シンガポール、ワシントン DC |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | サポートされています | サポートされています | アムステルダム、ダラス、ロンドン、シリコン バレー、シンガポール、ワシントン DC |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | サポートされています | サポートされています | アムステルダム、香港、ロンドン、シリコン バレー、シンガポール、東京、ワシントン DC |
|**China Telecom Global** | サポートされています | サポートされていません | 香港 |
|**Cologix** | 近日対応予定 | サポートされていません | モントリオール+、トロント+ |
| **[コルト]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  サポートされています | サポートされています | アムステルダム、ロンドン |
| **Comcast** | サポートされています | サポートされていません | シリコン バレー、ワシントン DC |
| **CoreSite** | サポートされています | サポートされています | ロサンゼルス | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | サポートされています | サポートされています | アムステルダム、アトランタ、シカゴ、ダラス、香港、ロンドン、ロサンゼルス、メルボルン、ニューヨーク、大阪、サンパウロ、シアトル、シリコン バレー、シンガポール、シドニー、東京、トロント+、ワシントン DC |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** |  サポートされています | サポートされていません | 大阪、東京 |
| **[InterCloud]( https://www.intercloud.com/)** | サポートされています | サポートされています | アムステルダム、ロンドン、シンガポール、ワシントン DC |
| **Internet Solutions - Cloud Connect** | サポートされています | サポートされています | アムステルダム、ロンドン |
| **Interxion** | サポートされています | サポートされています | アムステルダム |
| **[レベル 3 の通信]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | サポートされています | サポートされています | アムステルダム、シカゴ、ダラス、ロンドン、シアトル、シリコン バレー、ワシントン DC |
| **Megaport** | サポートされています | サポートされています | メルボルン、シドニー |
| **MTN** | サポートされています | サポートされています | ロンドン |
| **NEXTDC** | サポートされています | サポートされています | メルボルン、シドニー |
| **NTT Communications** | サポートされています | サポートされていません | ロンドン+、東京 |
| **[Orange]( http://www.orange-business.com/)** | サポートされています | サポートされていません | アムステルダム、香港、ロンドン、シリコン バレー、シンガポール、ワシントン DC |
| **PCCW Global Limited** | サポートされています | サポートされていません | 香港 |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  サポートされています | サポートされていません | シンガポール |
| **ソフトバンク** | 近日対応予定 | サポートされていません | 大阪、東京 | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | サポートされています | サポートされています | アムステルダム、チェンナイ、香港、ロンドン、ムンバイ、シンガポール、ワシントン DC |
| **[TeleCity Group]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | サポートされています | サポートされています | アムステルダム、ロンドン |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | サポートされています | サポートされていません | メルボルン、シドニー |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | サポートされています | サポートされています | 香港、ロンドン、シリコン バレー、シドニー、シンガポール、ワシントン DC |
| **Vodafone** | サポートされています | サポートされていません | ロンドン | 
| **[Zayo Group]( http://www.zayo.com/)** | サポートされています | サポートされています | シカゴ、ロサンゼルス、ニューヨーク、シリコン バレー、ワシントン DC |

 **+** 近日対応予定を表します

### 国内のクラウド環境

#### 米国政府のクラウド

| **サービス プロバイダー**  |**Microsoft Azure** | **Office 365** | **場所** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | サポートされています | サポートされていません | シカゴ+、ワシントン DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 近日対応予定 | サポートされていません | シカゴ +、ワシントン DC + |
| **[3 つの通信に IPVPN をレベルします。]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | サポートされています | サポートされていません | シカゴ+、ワシントン DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | サポートされています | サポートされていません | シカゴ、ワシントン DC |

#### 中国のクラウド

| **サービス プロバイダー**  |**Microsoft Azure** | **Office 365** | **場所** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | サポートされています | サポートされていません | 北京、上海+|
詳細については、次を参照してください。 [中国での ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)します。

## 一覧にないサービス プロバイダー経由の接続

接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

- 接続プロバイダーが上の表に記載されているいずれかの Exchange に接続されているかどうかをその接続プロバイダーに確認します。 次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。 一部の接続プロバイダーは既にイーサネット Exchange に接続されています。

    - [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
- その接続プロバイダーに、選択したピアリングの場所までネットワークを拡張してもらいます。
    - 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
- Microsoft に接続するために、接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。
    - 」の手順に従って [ExpressRoute 回線を作成](expressroute-howto-circuit-classic.md) 接続を設定します。

|**接続プロバイダー**|**Exchange**|**ピアリングの場所**|
|---|---|---|
|**[XO 通信](http://www.xo.com/)**|Equinix|シリコン バレー|

## ExpressRoute システム インテグレーター

ネットワークの規模によっては、ニーズに合わせてプライベート接続を有効にするのは難しい場合があります。 次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。

|**システム インテグレーター**|**大陸**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|米国||
|**[Dotnet ソリューション](http://www.dotnetsolutions.co.uk/)**|ヨーロッパ・中東・アフリカ|

## 次のステップ

- ExpressRoute の詳細については、次を参照してください。、 [ExpressRoute の FAQ](expressroute-faqs.md)します。
- すべての前提条件を満たしていることを確認します。 参照してください [ExpressRoute の前提条件](expressroute-prerequisites.md)します。

