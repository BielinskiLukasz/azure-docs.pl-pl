<properties
    pageTitle="BizTalk Services のエディションに含まれる機能の詳細情報 | Microsoft Azure"
    description="BizTalk Services のエディション (Free、Developer、Basic、Standard、および Premium) の機能を比較します。MABS、WABS。"
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="12/08/2015"
    ms.author="mandia"/>



# BizTalk Services: エディション チャート

Azure BizTalk Services には複数のエディションがあります。 この記事では、シナリオとビジネス ニーズに合ったエディションを決定する方法について説明します。


## 各エディションを比較する

**Free (プレビュー)**

ハイブリッド接続を作成して管理できます。 ハイブリッド接続は、Azure Web サイトとオンプレミスのシステム (たとえば SQL Server) を簡単に接続する方法です。

**開発者**

ハイブリッド接続、EAI および EDI メッセージ処理 (使いやすい取引先管理ポータルあり)、一般的な EDI スキーマのサポート、X12 および AS2 でのリッチな EDI 処理などの機能が含まれます。 HTTP/S、REST、FTP、WCF、および SFTP プロトコルを使用してメッセージの読み取りと書き込みを行うクラウド内のサービスを接続する一般的な EAI シナリオを作成できます。 オンプレミスの LOB システムへの接続が可能であり、SAP、Oracle eBusiness、Oracle DB、Siebel、および SQL Server のためのすぐに使えるアダプターが用意されています。 開発とデプロイを容易にする Visual Studio ツールによる開発者中心の環境を使用します。 目的は開発とテストに限定されており、サービス レベル アグリーメント (SLA) はありません。

**Basic**

開発者エディションのほとんどの機能が含まれており、ハイブリッド接続、EAI ブリッジ、EDI 契約、BizTalk Adapter Pack 接続の数が増えています。 高可用性と拡張オプションに加えてサービス レベル アグリーメント (SLA) も提供されます。

**Standard**

Basic エディションのすべての機能が含まれており、ハイブリッド接続、EAI ブリッジ、EDI 契約、BizTalk Adapter Pack 接続の数が増えています。 高可用性と拡張オプションに加えてサービス レベル アグリーメント (SLA) も提供されます。

**Premium**

Standard エディションのすべての機能が含まれており、ハイブリッド接続、EAI ブリッジ、EDI 契約、BizTalk Adapter Pack 接続の数が増えています。 さらに、アーカイブと高可用性の機能も含まれており、サービス レベル アグリーメント (SLA) に基づくスケールも可能です。


## エディションのチャート

次の表に、エディション間の違いを示します。

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (プレビュー)</th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>プレミアム</th>
</tr>
<tr>
<td><strong>開始価格</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk Services の価格</a> <br /><br /> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full"> Azure 料金計算ツール</a></td>
</tr>
<tr>
<td><strong>既定の最小構成</strong></td>
<td>1 Free ユニット</td>
<td>1 Developer ユニット</td>
<td>1 Basic ユニット</td>
<td>1 Standard ユニット</td>
<td>1 プレミアム ユニット</td>
</tr>
<tr>
<td><strong>スケール</strong></td>
<td>該当なし</td>
<td>該当なし</td>
<td>あり (1 Basic ユニットずつ増加)</td>
<td>あり (1 Standard ユニットずつ増加)</td>
<td>あり (1 Premium ユニットずつ増加)</td>
</tr>
<tr>
<td><strong>許容される最大スケール アウト</strong></td>
<td>該当なし</td>
<td>該当なし</td>
<td>最大 8 ユニット</td>
<td>最大 8 ユニット</td>
<td>最大 8 ユニット</td>
</tr>
<tr>
<td><strong>EAI ブリッジ/ユニット</strong></td>
<td>なし</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI、AS2</strong>
<br /><br />
TPM 契約の有無</td>
<td>なし</td>
<td>あり。10 契約/ユニット。</td>
<td>あり。50 契約/ユニット。</td>
<td>あり。250 契約/ユニット。</td>
<td>あり。1000 契約/ユニット。</td>
</tr>
<tr>
<td><strong>ユニットあたりのハイブリッド接続数</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>ユニットあたりのハイブリッド接続データ転送量 (GB)</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>オンプレミス LOB システムへの BizTalk Adapter サービス接続</strong></td>
<td>なし</td>
<td>1 接続</td>
<td>2 接続</td>
<td>5 接続</td>
<td>25 接続</td>
</tr>
<tr>
<td align="left"><strong>サポートされるプロトコル/システム:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure BLOB</li>
<li>REST API</li>
</ul>
</td>
<td>なし</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>高可用性</strong>
<br /><br />
サービス レベル アグリーメント (SLA) を参照してください。 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk Services の価格</a>.
</td>
<td>なし</td>
<td>なし</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>バックアップと復元</strong></td>
<td>なし</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>追跡</strong></td>
<td>なし</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>アーカイブ</strong><br /><br />
NRR (Non-repudiation of Receipt) と追跡されたメッセージのダウンロードが含まれます。</td>
<td>なし</td>
<td>あり</td>
<td>なし</td>
<td>なし</td>
<td>あり</td>
</tr>
<tr>
<td><strong>カスタム コードの使用</strong></td>
<td>なし</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>変換の使用 (カスタム XSLT を含む)</strong></td>
<td>なし</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
</table>
> [AZURE.NOTE] ハードウェアの障害に対する復元性の場合、高可用性は 1 つの BizTalk ユニット内に複数の VM を備えていることを意味します。


## FAQ

#### BizTalk ユニットとは何ですか。

「ユニット」は、Azure BizTalk Services デプロイのアトミックなレベルです。 各エディションには、計算能力とメモリが異なるユニットが用意されています。 たとえば、Basic ユニットは Developer ユニットより計算能力が高く、Standard ユニットは Basic ユニットより計算能力が高くなります。 BizTalk Service のスケールを変更する場合は、ユニット単位で行います。

#### BizTalk Services と Azure BizTalk VM の違いは何ですか。

BizTalk Services は、クラウドでの統合ソリューションの構築のために真のサービスとしてのプラットフォーム (PaaS) を提供します。 PaaS モデルでは、完全にアプリケーション ロジックに集中し、次のようなインフラストラクチャ管理はすべて Microsoft に任せることができます。

- 仮想マシンの管理やパッチ適用の必要がありません。
- 可用性は Microsoft により確保されます。
- Azure ポータルを通じて容量の増減を要求するだけで、スケールをオンデマンドで管理できます。

Azure Virtual Machines 上の BizTalk Server は、サービスとしてのインフラストラクチャ (IaaS) アーキテクチャを提供します。 仮想マシンを作成および構成します、内部設置型環境とほぼ同じコードを変更せずに、クラウド内の既存のアプリケーションを実行しやすくなります。 Iaas での仮想マシンの構成、バーチャル マシン (たとえば、ソフトウェアのインストールおよび OS 修正プログラムなど) の管理および高可用性のアプリケーションを設計も担当しています。

インフラストラクチャの管理作業を最小化する新しい統合ソリューションの構築を検討している場合は、BizTalk Services をご利用ください。 既存の BizTalk ソリューションをすばやく移行することを検討している場合、または BizTalk Server アプリケーションを開発しテストするためのオンデマンドの環境を探している場合は、Azure の仮想マシンで BizTalk Server をご利用ください。

#### BizTalk Adapter サービスとハイブリッド接続の違いは何ですか。

BizTalk Adapter サービスは、Azure BizTalk サービスで使用されます。 BizTalk Adapter サービスでは、オンプレミスの基幹業務 (LOB) システムへの接続を目的として BizTalk Adapter Pack が使用されます。 ハイブリッド接続は、Azure アプリケーション (Azure App Service と Azure Mobile Services 内の Web Apps 機能など) をオンプレミスのリソースに接続するための簡単で便利な方法を提供します。

#### 「ユニットあたりのハイブリッド接続データ転送量 (GB)」とはどのような意味ですか。単位は分/時間/日/週/月のいずれですか。制限に達するとどうなりますか。

ユニットあたりのハイブリッド接続コストは、BizTalk Services のエディションによって異なります。 コストの依存を簡単に言えば、転送するデータの量。 たとえば、毎日 10 GB のデータを転送する方が、毎日 100 GB を転送するより低コストです。 使用して、 [料金計算ツール](http://azure.microsoft.com/pricing/calculator/?scenario=full) 具体的なコストを決定するための BizTalk サービス用です。 通常、制限は 1 日単位で適用されます。 制限を超えた場合、超過分には GB あたり $1 の料金がかかります。

#### BizTalk Services で契約を作成すると、ブリッジの数が 1 つではなく 2 つ増えるのはなぜですか。

各契約は、送信側の通信ブリッジと受信側の通信ブリッジの 2 つの異なるブリッジで構成されます。

#### ブリッジ数または契約数のクォータの上限に達した場合はどうなりますか。

新しいブリッジをデプロイしたり、新しい契約を作成したりすることができなくなります。 詳細を展開するには、BizTalk サービスのユニット数をスケール アップまたは上位のエディションにアップグレードする必要があります。

#### BizTalk Services の 1 つのレベルから別のレベルに移行するにはどのようにしますか。

Free エディションは、別のレベルに移行 ("スケールアップ") することはできません。また、バックアップして別のレベルに復元することもできません。 別のレベルが必要な場合は、新しいレベルを使用して BizTalk サービスを新規作成してください。 ハイブリッド接続など、Free エディションを使用して作成されたアーティファクトがある場合は、新しい BizTalk サービスで再度作成する必要があります。

他のエディションでは、バックアップと復元を使用して、レベル間でアーティファクトを移行できます。 たとえば、Standard レベルでアーティファクトをバックアップし、Premium レベルに復元できます。 [BizTalk サービス: バックアップし、復元](biztalk-backup-restore.md) 、サポートされている移行パスについて、どのような成果物のバックアップを一覧表示します。 ハイブリッド接続はバックアップされないので注意してください。 バックアップして新しいレベルに復元した後で、もう一度ハイブリッド接続を作成してください。


#### BizTalk Adapter サービスはサービスに含まれていますか。ソフトウェアはどのようにして取得しますか。

はい、BizTalk Adapter Pack 付きの BizTalk Adapter サービスは Azure BizTalk サービス SDK に付属 [ダウンロード](http://www.microsoft.com/download/details.aspx?id=39087)します。

## 次のステップ

Azure ポータルで Azure BizTalk サービスを作成するには [BizTalk サービス: Azure ポータルを使用して](biztalk-provision-services.md)します。 アプリケーションの作成を開始するには [Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=235197)します。

## その他のリソース

- [Azure ポータルを使用して BizTalk サービス: サービスのプロビジョニング](biztalk-provision-services.md)<br/>
- [BizTalk サービス: プロビジョニング状態チャート](biztalk-service-state-chart.md)<br/>
- [BizTalk サービス: ダッシュ ボード、モニター、スケール タブ](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [BizTalk サービス: バックアップと復元](biztalk-backup-restore.md)<br/>
- [BizTalk サービス: 調整](biztalk-throttling-thresholds.md)<br/>
- [BizTalk サービス: 発行者名および発行者キー](biztalk-issuer-name-issuer-key.md)<br/>
- [Azure BizTalk サービス SDK の操作使用開始方法](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>





