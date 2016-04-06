<properties 
    pageTitle="BizTalk Services での [ダッシュボード]、[監視]、[スケール]、[構成]、および [ハイブリッド接続] | Microsoft Azure" 
    description="BizTalk Services のクラシック ポータルのタブのコントロールについて説明し、パフォーマンスを監視します: [ダッシュボード]、[監視]、[スケール]、[構成]、および [ハイブリッド接続]。 MABS、WABS" 
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
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="mandia"/>




# [ダッシュボード]、[監視]、[スケール]、[構成]、および [ハイブリッド接続] タブの確認

BizTalk サービスを作成してアプリケーションを配置した後は、BizTalk サービスの設定の一部を変更し、アプリケーションのパフォーマンスを監視できます。 

自動的に配置される Azure クラシック ポータルを開くと、 **すべてのアイテム** ] タブをクリックします。 表示するには、BizTalk サービスで BizTalk サービスを選択して、 **すべてのアイテム** ] タブを選択するか、 **BIZTALK サービス** タブし、BizTalk サービスの名前を選択します。

新しいウィンドウが開き、次のタブが表示されます。 このトピックでは、これらのタブについて説明します。

## クイック スタート (![クイック スタート][QuickStart])
BizTalk サービスのエディションによっては、ここに示したオプションの一部が表示されないこともあります。 
<table border="1">
    <tr>
        <td><strong>ツールの入手</strong></td>

        <td>Download the BizTalk Services SDK to install the Visual Studio project templates on your on-premises development computer. These templates create the <strong>BizTalk Services</strong> (bridge) and the <strong>BizTalk Service Artifacts</strong> (Transform) Visual Studio projects that are deployed to your BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> How do I Start Using the Azure BizTalk Services SDK </a> and <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installing the Azure BizTalk Services SDK</a> lists the steps to get started.
        </td>
    </tr>

    <tr>
        <td><strong>Create partner agreements</strong></td>

        <td>Opens the Azure BizTalk Services Portal hosted on Azure where you add partners and create X12, AS2, and EDIFACT EDI agreements.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal</a> lists the steps to get started.
        </td>
    </tr>

<tr>
        <td><strong>BizTalk サービスの詳細</strong></td>
        <td>移動して、 <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">学習センター</a> を Azure BizTalk サービスの詳細を参照してください。</td>
</tr>
</table>


一番下にあるタスク バーには、次のオプションがあります。

<table border="1">

<tr>
<td><strong>管理</strong> 、アプリケーションの配置</td>
<td>Azure BizTalk サービス ポータルが開きます。 BizTalk サービス ポータルは、パートナーの追加や X12 契約、AS2 契約および EDIFACT 契約の作成などの EDI 構成を行うためのスタート地点です。
<br/><br/>
これは、同じ <strong>パートナー アグリーメントを作成</strong> 上、 <strong>クイック スタート</strong> ] タブをクリックします。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk サービス ポータルにおける EDI メッセージングのコンポーネントを構成する</a> BizTalk サービス ポータルについて詳しく説明します。</td>
</tr>

<tr>
<td><strong>接続情報</strong> の Access Control 名前空間</td>
<td>[接続情報] を選択すると、[Access Control 名前空間]、[既定の発行者]、および [既定のキー] が表示されます。 この値はコピーできます。
<br/><br/>
Access Control ポータルを開くこともできます。 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">アクセス制御名前空間を作成</a> Access Control ポータルについて詳しく説明します。</td>
</tr>

<tr>
<td><strong>キーの同期</strong> 、ストレージ アカウントに</td>
<td>ストレージ アカウントを作成すると、プライマリ キーおよびセカンダリ キーが自動的に作成されます。 これらの暗号キーは、ストレージ アカウントへのアクセスを制御します。 BizTalk サービスは自動的にプライマリ キーを使用します。 <strong>キーの同期</strong> BizTalk サービスを中断せず、主キーとセカンダリ キー間を切り替えるユーザーを有効にします。
<br/><br/>
たとえば、BizTalk サービスでストレージ アカウント用に新しいプライマリ キーを使用することができます。 これを行うには、次の手順を実行します。
<br/><br/>
<ol>
<li>BizTalk サービスを選択し、選択 <strong>キーの同期</strong>します。 セカンダリ キーを選択します。 これを行うと、BizTalk サービスはセカンダリ キーの使用を開始します。</li>
<li>Azure クラシック ポータルで、ストレージ アカウントを選択してプライマリ キーを再生成します。 BizTalk サービスはセカンダリ キーを使用していることに注意してください。</li>
<li>BizTalk サービスを選択し、選択 <strong>キーの同期</strong>します。 プライマリ キーを選択します。 これは、再生成した新しいプライマリ キーです。</li>
<li>Azure クラシック ポータルで、ストレージ アカウントを選択してセカンダリ キーを再生成します。</li>
</ol>
<br/>
このプロセスを "キーのロールオーバー" といいます。 その目的は、BizTalk サービスを中断せずにプライマリ キーとセカンダリ キーの間で切り替えを行うことです。</td>
</tr>

<tr>
<td><strong>削除</strong> アプリケーション</td>
<td>[削除] を選択すると、この BizTalk サービスと、この BizTalk サービスにデプロイされたすべてのアイテムが削除されます。</td>
</tr>
</table>


## Dashboard
BizTalk サービスのエディションによっては、ここに示したオプションの一部が表示されないこともあります。 

BizTalk サービス名を選択すると、[ダッシュボード] タブが表示されます。 ダッシュボードで、次の操作を実行できます。

##### 使用状況の概要: 使用されているハイブリッド接続の数が表示されます。
データ使用量 (GB 単位) も表示されます。 

##### メトリック グラフ: 所定のパフォーマンス メトリックの一覧が表示されます。
これらのメトリックは、BizTalk サービスの正常性状態に関するリアルタイムの値を提供します。 選択することもできます、 **相対** または **絶対** 値および時間範囲 **間隔** 、グラフに表示するメトリックのです。 

これらのパフォーマンス メトリックの説明を参照してください [使用可能なメトリック](#Metrics) 」を参照します。


##### 概要: BizTalk サービスのプロパティが一覧表示されます。

<table border="1">

<tr>
<td><strong>トラッキング データベース資格情報の更新</strong></td>
<td>トラッキング データベースにログインするためのユーザー名とパスワードを変更します。</td>
</tr>
<tr>
<td><strong>SSL 証明書の更新</strong></td>
<td>BizTalk サービスで使用される SSL 証明書を別のものに更新できます。 自己署名 SSL 証明書が自動的に作成した <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk サービスを作成する</a>です。</td>
</tr>
<tr>
<td><strong>証明書のダウンロード</strong></td>
<td>BizTalk サービスで使用される SSL 証明書をローカル コンピューターにダウンロードできます。</td>
</tr>
<tr>
<td><strong>ステータス</strong></td>
<td>BizTalk サービスの現在の状態が表示されます。 参照してください <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk サービス: サービスの状態のチャート</a>します。 </td>
</tr>
<tr>
<td><strong>サービス URL</strong></td>
<td>この BizTalk サービスの URL です。 これと同じ、 <strong>ドメイン URL</strong> 、BizTalk サービスの作成時に入力します。</td>
</tr>
<tr>
<td><strong>パブリック仮想 IP (VIP) アドレス</strong></td>
<td>この BizTalk サービスに割り当てられた IP アドレスです。 これはすべての入力エンドポイントで使用され、送信トラフィックのソース アドレスとなります。 この IP アドレスは、作成される限り BizTalk サービスに属します。 BizTalk サービスを削除すると、IP アドレスは別の BizTalk サービスに割り当てられます。</td>
</tr>
<tr>
<td><strong>ACS 名前空間</strong></td>
<td>BizTalk サービスで認証を行います。</td>
</tr>
<tr>
<td><strong>エディション</strong></td>
<td>BizTalk サービスの作成時に入力されたエディションが表示されます。</td>
</tr>
<tr>
<td><strong>場所</strong></td>
<td>BizTalk サービスをホストするリージョンが表示されます。</td>
</tr>
<tr>
<td><strong>作成日時</strong></td>
<td>BizTalk サービスが作成された日時が表示されます。</td>
</tr>
<tr>
<td><strong>トラッキング データベース</strong></td>
<td>BizTalk サービスで使用される追跡テーブルを格納する Azure SQL データベース名です。 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">要件の説明の項</a>  、トラッキング データベースの詳細情報を提供します。</td>
</tr>
<tr>
<td><strong>ストレージの監視/アーカイブ</strong></td>
<td>BizTalk サービスの監視出力を格納する Azure ストレージ アカウント名です。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">要件の説明の項</a>  ストレージ アカウントについて詳しく説明します。</td>
</tr>
<tr>
<td><strong>サブスクリプション名</strong></td>
<td>この BizTalk サービスをホストしているサブスクリプションの名前が表示されます。 サブスクリプションは、Azure クラシック ポータルへのアクセスを管理します。</td>
</tr>
<tr>
<td><strong>サブスクリプション ID</strong></td>
<td>サブスクリプションが作成されると、サブスクリプション ID は自動的に生成されます。 REST API を使用するときに、サブスクリプション ID の入力が必要になる場合があります。</td>
</tr>
</table>

[BizTalk サービス: Azure を使用して従来のポータルをプロビジョニングする](http://go.microsoft.com/fwlink/p/?LinkID=302280) BizTalk サービスを作成する手順について説明します。


##### タスク バーの [管理]、[接続情報]、[キーの同期]、[削除]:

<table border="1">

<tr>
<td><strong>管理</strong> 、アプリケーションの配置</td>
<td>Azure BizTalk サービス ポータルが開きます。 BizTalk サービス ポータルは、パートナーの追加や X12 契約、AS2 契約および EDIFACT 契約の作成などの EDI 構成を行うためのスタート地点です。
<br/><br/>
これは、同じ <strong>パートナー アグリーメントを作成</strong> 上、 <strong>クイック スタート</strong> ] タブをクリックします。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk サービス ポータルにおける EDI メッセージングのコンポーネントを構成する</a> BizTalk サービス ポータルについて詳しく説明します。</td>
</tr>
<tr>
<td><strong>接続情報</strong> の Access Control 名前空間</td>
<td>[Access Control 名前空間]、[既定の発行者]、[既定のキー] が表示されます。これらの値はコピー可能です。
<br/><br/>
Access Control ポータルを開くこともできます。 Access Control ポータルは、左のナビゲーション ウィンドウで [Active Directory] オプションを使用するのと同じです。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">ACS 名前空間の管理</a> Access Control ポータルについて詳しく説明します。</td>
</tr>
<tr>
<td><strong>キーの同期</strong> 、ストレージ アカウントに</td>
<td>ストレージ アカウントを作成すると、プライマリ キーおよびセカンダリ キーが自動的に作成されます。 これらの暗号キーは、ストレージ アカウントへのアクセスを制御します。 BizTalk サービスは自動的にプライマリ キーを使用します。 <strong>キーの同期</strong> BizTalk サービスを中断せず、主キーとセカンダリ キー間を切り替えるユーザーを有効にします。
<br/><br/>
たとえば、BizTalk サービスでストレージ アカウント用に新しいプライマリ キーを使用することができます。 これを行うには、次の手順を実行します。
<br/><br/>
<ol>
<li>BizTalk サービスを選択し、選択 <strong>キーの同期</strong>します。 セカンダリ キーを選択します。 これを行うと、BizTalk サービスはセカンダリ キーの使用を開始します。</li>
<li>Azure クラシック ポータルで、ストレージ アカウントを選択してプライマリ キーを再生成します。 BizTalk サービスはセカンダリ キーを使用していることに注意してください。</li>
<li>BizTalk サービスを選択し、選択 <strong>キーの同期</strong>します。 プライマリ キーを選択します。 これは、再生成した新しいプライマリ キーです。</li>
<li>Azure クラシック ポータルで、ストレージ アカウントを選択してセカンダリ キーを再生成します。</li>
</ol>
<br/>
このプロセスを "キーのロールオーバー" といいます。 その目的は、BizTalk サービスを中断せずにプライマリ キーとセカンダリ キーの間で切り替えを行うことです。</td>
</tr>

<tr>
<td><strong>削除</strong> アプリケーション</td>
<td>この BizTalk サービスと、この BizTalk サービスにデプロイされたすべてのアイテムが削除されます。</td>
</tr>
</table>


## 監視
無料エディションには適用されません。

BizTalk サービス名を選択すると、[監視] タブが選択可能になり、次の情報が表示されます。

##### メトリック グラフ: 選択されたパフォーマンス メトリックが表示されます。
これらのメトリックは、BizTalk サービスの正常性状態に関するリアルタイムの値を提供します。 表示されるパフォーマンス メトリックを選択します。 最大で 6 つのパフォーマンス メトリックを同時に表示できます。 

選択することもできます、 **相対** または **絶対** 値および時間範囲 **間隔** 表示されるメトリックのです。 

##### グラフ内のメトリックを削除または表示するには:
1. 選択、 **モニター** ] タブをクリックします。
2. 選択 **メトリックの追加** タスク バーで。  
![メトリックの追加] を選択][AddMetrics]
3. 表示したいパフォーマンス メトリックのチェック ボックスをオンにします。
4. 戻るには、チェック マークを選択して、 **モニター** ] タブをクリックします。
5. グラフにメトリック値を表示するメトリックの横にある円を選択します。  

    たとえば、 **CPU 使用率** メトリックは淡色、その出力はグラフに表示されません。  
![CPU 使用率] メトリックが淡色します。][GrayedMetric]  

    淡色表示を有効にする円を選択、 **CPU 使用率** グラフにその出力を表示するメトリック。  
![CPU 使用率] メトリックが有効になっています。][EnabledMetric]

6. グラフの表示とリストからメトリックを削除する選択 **メトリックの削除]** タスク バーにします。 メトリックを再びを一覧に追加する選択 **メトリックの追加** タスク バーで、メトリックを確認しに戻るには、チェック マークを選択して、 **モニター** ] タブをクリックします。 淡色表示されている円を選択すると、そのメトリックが有効な状態になります。

## <a name="Metrics"></a>使用可能なメトリック
次のパフォーマンス カウンター/メトリックを使用できます。

<table border="1">

<tr>
<td><strong>ラウンドトリップ遅延</strong></td>
<td>メッセージ 1 件の処理にかかる時間 (そのメッセージが受信された時点から、BizTalk サービスによる処理が完了するまでの時間) の、すべてのブリッジの平均値がミリ秒 (ms) 単位で表示されます。 正常に処理されたメッセージのみがカウントされます。<br/><br/>
次のイベントが発生した場合は、タイムスタンプが作成されます。
<ul>
<li>メッセージがゲートウェイに入る</li>
<li>メッセージが送信先にルーティングされる</li>
<li>送信先の応答が受信される</li>
<li>送信先の確認応答がゲートウェイに送信される</li>
</ul>
<br/>
このメトリックは、次の計算結果を表示します。
<br/><br/>
[送信先の確認応答がゲートウェイに送信される] - [メッセージがゲートウェイに入る]</td>
</tr>
<tr>
<td><strong>ソースのエラー</strong></td>
<td>BizTalk サービスがソース エンドポイントからメッセージを取り出すときにエラーとなったメッセージの合計数が表示されます。</td>
</tr>
<tr>
<td><strong>CPU 使用率</strong></td>
<td>すべてのロール インスタンスの平均処理時間の割合を表示します。</td>
</tr>
<tr>
<td><strong>処理の遅延時間</strong></td>
<td>メッセージ 1 件の処理にかかる時間 (BizTalk サービスによる処理が完了するまでの時間から送信先で費やした時間を差し引いた値) の、すべてのブリッジの平均値がミリ秒 (ms) 単位で表示されます。 正常に処理されたメッセージのみがカウントされます。<br/><br/>
次の各イベントが発生した場合は、タイムスタンプが作成されます。

<ul>
<li>メッセージがゲートウェイに入る</li>
<li>メッセージが送信先にルーティングされる</li>
<li>送信先の応答が受信される</li>
<li>送信先の確認応答がゲートウェイに送信される</li>
</ul>
<br/>このメトリックは、次の計算結果を表示します。<br/><br/>
[送信先の確認応答がゲートウェイに送信される] - [メッセージがゲートウェイに入る] - [送信先の応答が受信される] + [メッセージが送信先にルーティングされる]</td>
</tr>
<tr>
<td><strong>プロセス内のエラー</strong></td>
<td>BizTalk サービスによる処理が完了する前にエラーとなったメッセージの、すべてのブリッジの合計数を所定の時間範囲内で集計したものが表示されます。</td>
</tr>
<tr>
<td><strong>送信されたメッセージ</strong></td>
<td>BizTalk サービスによって送信されたメッセージの、すべてのブリッジの合計数を所定の時間範囲内で集計したものが表示されます。 このメトリックは、パイプラインから送信されたメッセージがルートの送信先に到達したときに 1 増えます。 このメトリックは、メッセージが正常に処理されたことを示すものではありません。<br/><br/>
要求 - 応答シナリオでは、ルート送信先からパイプラインに受信確認が返送されたときにこのメトリックに 1 が加算されます。</td>
</tr>
<tr>
<td><strong>受信したメッセージ</strong></td>
<td>BizTalk サービスによって受信されたメッセージの、すべてのブリッジの合計数を所定の時間範囲内で集計したものが表示されます。 パイプラインによって新しいメッセージが受信されたときに、このメトリックに 1 が加算されます。</td>
</tr>
<tr>
<td><strong>処理中のメッセージ</strong></td>
<td>BizTalk サービスによる処理中のメッセージの合計数を所定の時間範囲内で集計したものが表示されます。</td>
</tr>
<tr>
<td><strong>処理されたメッセージ</strong></td>
<td>BizTalk サービスによる処理が正常に完了したメッセージの、すべてのブリッジの合計数を所定の時間範囲内で集計したものが表示されます。 メッセージがパイプラインによって正常に受信されて送信先に正常にルーティングされたときに、このメトリックに 1 が加算されます。</td>
</tr>
</table>


## スケール
[スケール] タブで、BizTalk サービスで使用されるユニット数を追加または削減できます。 既定では、1 つのユニットが構成されています。 ユニットを追加して BizTalk サービスをスケールできます。 スケールを増やすと、スループットが増えます。 また、デプロイされるブリッジ、契約、LOB 接続、処理能力などのリソース量も増えます。 たとえば、ユニットを 1 から 2 に増やすとします。 この場合、2 倍のブリッジ数をデプロイでき、契約数、LOB 接続数、処理能力をそれぞれ 2 倍にできます。

一部の BizTalk エディションではスケール オプションが提供されません。 この場合、許容されるユニットは 1 つです。 エディションでスケールできるユニット数を確認するのを参照してください [BizTalk サービス: エディションのチャート](biztalk-editions-feature-chart.md)します。

ユニット数を増やすと、料金に影響する可能性があります。 ユニットを増やす場合に選択すると **保存** かどうか、料金に影響を示すメッセージが表示されます。 [OK] をクリックして続行します。 ユニット数を増やすと、BizTalk サービスのステータスがアクティブから更新中に代わります。 更新中というステータスで、BizTalk サービスの実行が続行されます。

[BizTalk サービス: エディションのチャート](biztalk-editions-feature-chart.md) 「単位」を定義します。


## 構成
ハイブリッド接続には適用されません。

[バックアップ ステータス] を [なし] または [自動] に設定します。 [なし] に設定すると、バックアップが自動的には作成されなくなります。 [自動] に設定するときは、管理者がバックアップの場所、バックアップの頻度、バックアップ ファイルの保持期間を構成します。 

[BizTalk サービス: バックアップし、復元](biztalk-backup-restore.md) 詳細を提供します。 


## <a name="HybridConnections"></a>ハイブリッド接続
"ハイブリッド接続" を使用すると、Azure のアプリケーション (たとえば Web サイトや Mobile Services) と、静的 TCP ポートを使用する内部設置型リソース (たとえば SQL Server、MySQL、HTTP Web API、ほとんどのカスタム Web サービス) とを接続できます。 ハイブリッド接続は、Azure クラシック ポータルで BizTalk サービスで管理されます。

Azure Websites でハイブリッド接続を作成するを参照してください。 [ハイブリッド接続: Azure の Web サイトを内部設置型リソースに接続](http://go.microsoft.com/fwlink/p/?LinkId=397538)します。

Azure Mobile Services でハイブリッド接続を使用する、次を参照してください。 [Azure Mobile Services とハイブリッド接続](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)します。

作成または Azure BizTalk Services でハイブリッド接続の管理は、次を参照してください。 [ハイブリッド接続](integration-hybrid-connection-overview.md)します。



## 次へ
ここでは、各種のタブについて学びました。Azure BizTalk サービスの機能についてさらに学習できます。

- [BizTalk Services: 調整](biztalk-throttling-thresholds.md)  
- [BizTalk サービス: 発行者名および発行者キーに関するページ](biztalk-issuer-name-issuer-key.md)  
- [BizTalk サービス: バックアップと復元に関するページ](biztalk-backup-restore.md)

## 関連項目
- [ハイブリッド接続と](integration-hybrid-connection-overview.md)  
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャートに関するページ](biztalk-editions-feature-chart.md)  
- [BizTalk サービス: Azure クラシック ポータルを使用したプロビジョニング](biztalk-provision-services.md)  
- [BizTalk サービス: BizTalk サービスの状態のグラフに関するページ](biztalk-service-state-chart.md)  
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 


