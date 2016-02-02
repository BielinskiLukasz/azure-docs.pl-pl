<properties 
    pageTitle="Azure Redis Cache の FAQ" 
    description="Azure Redis Cache についてよく寄せられる質問の回答、パターンとベスト プラクティスについて説明します。" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/03/2015" 
    ms.author="sdanie"/>


# Azure Redis Cache の FAQ

Azure Redis Cache についてよく寄せられる質問の回答、パターンとベスト プラクティスについて説明します。

<a name="cache-size"></a>
## Redis Cache のサービス内容と適切なサイズの選択

Azure Redis Cache には、さまざまなレベルの**サイズ**、**帯域幅**、**高可用性**、**SLA** オプションが用意されています。

Cache のオプションを選択するときの考慮事項を次に示します。

-   **メモリ**: Basic レベルと Standard レベルでは、250 MB ～ 53 GB です。 Premium 階層で 530 GB までを提供する利用可能な [要求で](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)します。 詳細については、次を参照してください。 [Azure Redis Cache の料金](https://azure.microsoft.com/pricing/details/cache/)します。
-   **ネットワーク パフォーマンス**: 高いスループットを必要とするワークロードがある場合、Standard または Basic より Premium レベルの方が多くの帯域幅を提供します。 また、各レベル内では、キャッシュをホストする基盤の VM のため、キャッシュのサイズが大きいほど帯域幅も増えます。 詳細については後の表を参照してください。
-   **スループット**: Premium レベルでは、使用可能な最大のスループットが提供されます。 キャッシュ サーバーまたはクライアントが帯域幅の限界に達した場合、クライアント側でタイムアウトが発生します。 詳細については後の表を参照してください。
-   **高可用性/SLA**: Azure Redis Cache は、Standard/Premium キャッシュについて 99.9% の可用性を保証します。 SLA の詳細については、次を参照してください。 [Azure Redis Cache の料金](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)します。 SLA は、Cache エンドポイントへの接続のみをカバーします。 SLA は、データ損失からの保護には対応していません。 Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。
-   **Redis データの保持**: Premium レベルでは、Azure Storage アカウント内のキャッシュ データを永続化できます。 Basic/Standard のキャッシュでは、データはすべてメモリ内にのみ格納されます。 基盤となるインフラストラクチャに問題が発生した場合、データが失われる可能性があります。 Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。 Azure Redis Cache には、Redis の永続化の RDB オプションと AOF オプション (近日公開予定) があります。 詳細については、次を参照してください。 [Premium Azure Redis Cache 用の永続化を構成する方法](cache-how-to-premium-persistence.md)します。
-   **Redis クラスター**: 53 GB を超えるキャッシュを作成するか、複数の Redis ノード間でデータを共有する場合、Premium レベルで利用可能な Redis クラスタリングを使用することができます。 各ノードは、高可用性対応のプライマリ/レプリカ キャッシュのペアで構成されています。 詳細については、次を参照してください。 [Premium Azure Redis Cache のクラスタ リングを構成する方法](cache-how-to-premium-clustering.md)します。
-   **強化されたセキュリティとネットワーク分離**: Azure Virtual Network (VNET) のデプロイメントでは、Azure Redis Cache のための強化されたセキュリティと分離、およびサブネット、アクセス制御ポリシー、さらにアクセスを制限するためのその他の機能が提供されます。 詳細については、次を参照してください。 [Premium Azure Redis Cache 用の仮想ネットワークのサポートを構成する方法](cache-how-to-premium-vnet.md)します。
-   **Redis の構成**: Standard レベルと Premium レベルでは、Keyspace 通知用に Redis を構成できます。
-   **最大クライアント接続数**: Premium レベルでは、Redis に接続できるクライアントの最大数が提供されます。キャッシュのサイズが大きいほど、接続の数は多くなります。 [詳細については、料金のページを参照してください](https://azure.microsoft.com/pricing/details/cache/)します。
-   **Redis サーバー専用コア**: Premium レベルでは、すべてのキャッシュ サイズに Redis 専用のコアがあります。 Basic と標準の階層、C1 のサイズし、上記の Redis サーバーの専用のコアがあります。
-   **Redis はシングル スレッドです**。したがって、3 つ以上のコアを使用しても 2 つのコアを使用する場合と比べて追加のメリットはありません。ただし、一般に、VM のサイズが大きいほど、小さなサイズよりも多くの帯域幅を利用できます。 キャッシュ サーバーまたはクライアントが帯域幅の制限に達すると、クライアント側でタイムアウトが発生します。
-   **パフォーマンス向上**: Premium レベルのキャッシュは、高速プロセッサを備え、Basic/Standard レベルと比較して優れたパフォーマンスを発揮するハードウェア上にデプロイされます。 Premium レベルのキャッシュは、スループットが高く、待機時間が低くなっています。

次の表に、標準のさまざまなサイズをテストした結果得られた最大帯域幅とプレミアムのキャッシュを使用して `benchmark.exe` Azure Redis Cache のエンドポイントに対して Iaas VM からです。 これらの値は保証された値ではなく、これらの値の SLA もありません。これらの値は、標準的な値と考えてください。 アプリケーションに最適なキャッシュ サイズを特定するには、アプリケーションに対してロード テストを実行する必要があります。

この表からは次のような結論が得られます。

-   同じサイズのキャッシュでも Standard レベルと比べて Premium のスループットの方が高い。 例: 6 GB のキャッシュでは、P1 のスループットは 140 K RPS C3 の 49 K と比較しています。
-   Redis クラスタリングでは、クラスターのシャード (ノード) の数を増やすと、スループットもそれに比例して増加する。 例: 10 個のシャードの P4 クラスターを作成する場合は、使用可能なスループットの 250 K * 10 = 250万 RPS
-   キー サイズを大きくしたときのスループットは、Standard レベルより Premium レベルのほうが高い。

| [価格レベル]| サイズ| 使用可能な帯域幅| 1 KB のキーのサイズ|
|----------------------|--------|----------------------------|--------------------------------|
| **Standard のキャッシュ サイズ**| &nbsp;| **メガビット/秒 (Mbps)**| **1 秒あたりの要求数 (RPS)**|
| C0| 250 MB| 5| 600|
| C1| 1 GB| 100| 12200|
| C2| 2.5 GB| 200| 24000|
| C3| 6 GB| 400| 49000|
| C4| 13 GB| 500| 61000|
| C5| 26 GB| 1,000| 115000|
| C6| 53 GB| 2000| 150000|
| **Premium のキャッシュ サイズ**| &nbsp;| &nbsp;| **1 秒あたりの要求数 (RPS)、シャードあたり**|
| P1| 6 GB| 1,000| 140000|
| P2| 13 GB| 2000| 220000|
| P3| 26 GB| 2000| 220000|
| P4| 53 GB| 4000| 250000|


Redis ツールのダウンロードなどについて `benchmark.exe`, 、を参照してください [Redis コマンドを実行する方法ですか?](#cache-commands) section.

<a name="cache-region"></a>
## キャッシュを配置するリージョン

最適なパフォーマンスと最も短い待機時間を実現するには、キャッシュ クライアント アプリケーションと同じリージョンに Azure Redis Cache を配置します。

<a name="cache-billing"></a>
## Azure Redis Cache の課金方法を教えてください。

Azure Redis Cache の料金は [ここ](http://azure.microsoft.com/pricing/details/cache/)します。 価格ページには、1 時間単位の価格が表示されます。 キャッシュは、キャッシュが作成された時間から削除された時間までの期間に関して、分単位で課金されます。 キャッシュの課金を停止または一時停止するオプションはありません。

<a name="cache-timeouts"></a>
## タイムアウトが発生する理由

タイムアウトは、Redis との対話に使用されているクライアントで発生します。 ほとんどの場合、Redis サーバーでタイムアウトが発生することはありません。 Redis サーバーに送信されたコマンドは、キューに格納されます。コマンドは、最終的に Redis サーバーによって取得され、実行されます。 ただし、この処理中にクライアントがタイムアウトすることがあり、その場合は呼び出し元では例外が発生します。 タイムアウトの問題のトラブルシューティングに関する詳細については、次を参照してください。 [Azure Redis Cache の StackExchange.Redis のタイムアウトの例外の調査](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/)します。

<a name="cache-monitor"></a>
## キャッシュの正常性とパフォーマンスの監視方法

Microsoft Azure Redis Cache のインスタンスで監視できる、 [Azure ポータル](https://portal.azure.com)します。 メトリックの表示、メトリック グラフのスタート画面へのピン留め、監視グラフの日付と時刻の範囲のカスタマイズ、グラフのメトリックの追加と削除、特定の条件が満たされた場合のアラートの設定を行うことができます。 これらのツールによって、Azure Redis Cache インスタンスの正常性を監視し、キャッシュ アプリケーションを管理できます。 キャッシュの監視の詳細については、次を参照してください。 [Azure Redis Cache の監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)します。

<a name="cache-disconnect"></a>
## クライアントがキャッシュから切断される理由

キャッシュが切断される一般的な理由のいくつかを次に示します。

-   クライアント側の原因
    -   クライアント アプリケーションが再デプロイされた。
    -   クライアント アプリケーションがスケーリング操作を実行した。
        -   Cloud Services または Web Apps の場合、自動スケールが原因になっている場合があります。
    -   クライアント側のネットワーク レイヤーが変更された。
    -   クライアントで、またはクライアントとサーバー間のネットワーク ノードで一時的なエラーが発生した。
    -   帯域幅のしきい値制限に達した。
    -   CPU バインド型の操作の完了に時間がかかった。
-   サーバー側の原因
    -   Standard キャッシュ プランで、Azure Redis Cache Service がプライマリ ノードからセカンダリ ノードへのフェールオーバーを開始した。
    -   Azure により、キャッシュがデプロイされているインスタンスに修正プログラムが適用されていた。
        -   Redis サーバーの更新または VM の一般的なメンテナンスの場合もこれに該当します。

<a name="cache-configuration"></a>
## StackExchange.Redis 構成オプションについて

StackExchange.Redis には多くのオプションが用意されています。 ここでは、いくつかの一般的な設定について説明します。 StackExchange.Redis オプションについての詳細を参照してください。 [StackExchange.Redis の構成](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md)します。

 構成オプション| 説明| 推奨
---|---|---
 AbortOnConnectFail| true の場合、ネットワーク障害の後に再接続が行われません。| StackExchange.Redis が自動的に再接続するように、false に設定します。
 ConnectRetry| 初期接続中に接続試行を繰り返す回数。| |
 ConnectTimeout| 接続操作のタイムアウト (ミリ秒単位)。|

ほとんどの場合は、クライアントの既定値で十分です。 ワークロードに基づいてオプションを微調整できます。

-   再試行
    -   ConnectRetry と ConnectTimeout に関する一般的なガイダンスは、早く失敗して再試行することです。 これは、ワークロードと、クライアントが Redis コマンドを発行してから応答を受け取るまでに要する時間の平均に基づきます。
    -   自分で接続の状態を確認して再接続するのではなく、StackExchange.Redis が自動的に再接続するように設定します。 **ConnectionMultiplexer.IsConnected プロパティは使用しません**。
    -   問題の肥大化 - ある問題が発生し、再試行しても問題が解決しないことがあります。ここでする必要がある」の説明に従って、指数関数的バックオフ再試行アルゴリズムを使用して [一般的なガイダンスを再試行](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md) 、Microsoft Patterns & Practices グループ発行します。
-   タイムアウト値
    -   ワークロードを考慮したうえで値を適宜設定します。 大きな値を保存する場合は、タイムアウトを大きな値に設定します。
        -   StackExchange.Redis が自動的に再接続するように、ABortOnConnectFail を false に設定します。
-   アプリケーションに対して ConnectionMultiplexer インスタンスを 1 つ使用します。 ように、Connection プロパティから返される 1 つのインスタンスを作成する、LazyConnection を使用する [ConnectionMultiplexer クラスを使用してキャッシュに接続する](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect)します。
-   設定、 `ConnectionMultiplexer.ClientName` を診断用のアプリケーション インスタンス一意な名前のプロパティです。
-   複数回使用 `ConnectionMultiplexer` カスタム ワークロードに対してインスタンス。
    -   アプリケーションの負荷が変化する場合は、このモデルに従うことができます。 次に例を示します。
        -   1 つのマルチプレクサーを使用して、サイズの大きなキーを処理できます。
        -   1 つのマルチプレクサーを使用して、サイズの小さなキーを処理できます。
        -   使用する ConnectionMultiplexer ごとに、異なる接続タイムアウト値と再試行ロジックを設定できます。
        -   設定、 `ClientName` プロパティを各診断を支援するマルチプレクサーします。
        -   これにより詳細になりますあたりの待機時間の合理化 `ConnectionMultiplexer`します。

<a name="cache-redis-commands"></a>
## 一般的な Redis コマンドの使用に関するいくつかの考慮事項

-   処理に時間がかかる特定の Redis コマンドについては、その影響を理解せずに実行することは避けてください。
    -   たとえば、実行しないでください、 [キー](http://redis.io/commands/keys) 実稼働環境でコマンドは、キーの数によっては取得に時間がかかる場合があります。 Redis はシングル スレッド サーバーであり、一度に 1 つずつコマンドを処理します。 KEYS の後に他のコマンドが発行されている場合、それらのコマンドは Redis によって KEYS コマンドが処理されるまで処理されません。
-   キー サイズ - 小さなキー/値と大きなキー/値のどちらを使用するか。 一般に、それはシナリオしだいです。 サイズの大きなキーが必要となるシナリオでは、ConnectionTimeout 値、再試行回数、再試行ロジックを調整できます。 Redis サーバーの観点からは、小さな値を設定した方がパフォーマンスが高くなります。
    -   これは、サイズの大きな値を Redis に格納できないという意味ではありません。次の点に考慮する必要があります。 待機時間は長くなります。 サイズの大きなデータの 1 つのセットは小さくなりますがあれば、複数の ConnectionMultiplexer インスタンスを使用して、それぞれ構成された一連の異なるタイムアウトと再試行の値の前の手順に従って [StackExchange.Redis の構成オプションについては何](#cache-configuration) セクションです。


<a name="cache-ssl"></a>
## Redis への接続に非 SSL ポートを有効にする必要がある状況

Redis サーバーは既定で SSL をサポートしませんが、Azure Redis Cache では SSL がサポートされます。 Azure Redis Cache に接続するときに、クライアントが StackExchange.Redis のように SSL をサポートしている場合は、SSL を使用する必要があります。

既定では、新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています。 クライアントが、SSL をサポートしていないかどうかは、指示に従って、非 SSL ポートを有効にする必要があります、 [ポートへのアクセス](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) のセクションで、 [Azure Redis Cache でキャッシュを構成](https://msdn.microsoft.com/library/azure/dn793612.aspx) 記事です。

Redis ツール `redis cli` SSL ポートを持つことはできませんなどのユーティリティを使用する `stunnel` に安全に接続ツールを SSL ポートで説明された手順に従って、 [プレビュー リリースの ASP.NET セッション状態プロバイダーを発表](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) ブログの投稿です。

Redis ツールのダウンロード方法については、を参照してください [Redis コマンドを実行する方法ですか?](#cache-commands)。 section.

<a name="cache-benchmarking"></a>
## キャッシュのベンチマークを実行およびテストする方法

-   [キャッシュ診断を有効にする](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) できるように [モニター](https://msdn.microsoft.com/library/azure/dn763945.aspx) 、キャッシュの正常性。 Azure ポータルでメトリックを表示できるし、することもできます [をダウンロードして確認](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 好みのツールを使用しています。
-   redis-benchmark.exe を使用して Redis サーバーのロード テストを実行できます。
    -   ロード テスト クライアントと Redis Cache が同じリージョン内にあることを確認します。
-   redis-cli.exe を使用し、INFO コマンドを使用してキャッシュを監視します。
    -   負荷が高いことが原因でメモリの断片化が発生している場合は、キャッシュのサイズをスケールアップする必要があります。
-   Redis ツールのダウンロード方法については、を参照してください [Redis コマンドを実行する方法ですか?](#cache-commands)。 section.

<a name="cache-commands"></a>
## Redis コマンドの実行方法

記載されているコマンドのいずれかを使用する [Redis コマンド](http://redis.io/commands#) に記載されているコマンドを除く [Azure Redis Cache でサポートされない Redis コマンド](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache)します。 Redis コマンドを実行するには、いくつかのオプションがあります。

-   使用して Redis コマンドを実行する標準またはプレミアムのキャッシュがあれば、 [Redis コンソール](cache-configure.md#redis-console)します。 これは、Azure ポータルで Redis コマンドを安全に実行するための方法です。
-   Redis コマンド ライン ツールを使用することもできます。 これらを使用するには、次の手順に従います。
    -   ダウンロード、 [Redis コマンド ライン ツール](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip)します。
    -   使用して、キャッシュへの接続 `cli.exe`します。 次の例に示すように、-h スイッチを使用してキャッシュ エンドポイントを渡し、-a を使用してキーを渡します。
        -   `redis cli-h < キャッシュ名 >..redis.cache.windows.net という-a < キー >`
    -   Redis コマンド ライン ツールは SSL ポートで動作しないためですなどのユーティリティを使用する `stunnel` に安全に接続ツールを SSL ポートで説明された手順に従って、 [プレビュー リリースの ASP.NET セッション状態プロバイダーを発表](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) ブログの投稿です。

<a name="cache-common-patterns"></a>
## いくつかの一般的なキャッシュ パターンと考慮事項

-   Microsoft Patterns & Practices から次のガイダンスが公開されています。
    -   [キャッシュに関するガイダンス](https://github.com/mspnp/azure-guidance/blob/master/Caching.md)します。
    -   [Azure クラウド アプリケーションの設計および実装に関するガイダンス](https://github.com/mspnp/azure-guidance)
-   [Azure Redis Cache の一般的なキャッシュ パターン](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## 他のいくつかの Azure サービスと異なり Azure Redis Cache の MSDN クラス ライブラリ リファレンスが提供されない理由

Microsoft Azure Redis Cache は、広く普及しているオープン ソースの Redis Cache をベースに、Microsoft によって管理された、セキュリティで保護された専用の Redis Cache へのアクセスを提供します。 さまざまな [Redis クライアント](http://redis.io/clients) 多くのプログラミング言語を利用します。 各クライアントには、独自の API を使用して Redis キャッシュ インスタンスへの呼び出し [Redis コマンド](http://redis.io/commands)します。

クライアントはそれぞれ異なるため、MSDN には単独の一元的なクラス リファレンスは用意されていません。各クライアントで独自のリファレンス ドキュメントが管理されます。 リファレンス ドキュメントに加えていくつかのチュートリアルにある別の言語を使用して Azure Redis Cache の使用およびキャッシュ クライアントをする方法を示す Azure.com、 [Redis Cache のドキュメント](http://azure.microsoft.com/documentation/services/redis-cache/) ページです。


## どの Azure Cache を利用すればよいですか。

>[AZURE.IMPORTANT] すべての新規の開発に Azure Redis Cache を使用することをお勧めします。

現在、Azure Cache には 3 つのサービスがあります。

-   Azure Redis Cache
-   Azure Managed Cache Service
-   Azure In-Role Cache

>[AZURE.IMPORTANT]Azure Managed Cache Service と Azure In-Role Cache は、2016 年 11 月 30 日に提供を終了する予定です。 提供終了に備えて、Azure Redis Cache に移行することをお勧めします。 
>
>Azure Redis Cache は一般公開され、中国と米国政府を含むすべての Azure リージョンで使用できるようになったため、推奨されるキャッシュ ソリューションになりました。 この一般公開により、Managed Cache Service と In-Role Cache サービスは提供が終了されます。 
>
>今回のお知らせ (2015 年 11 月 30 日) から最長 12 か月間、Managed Cache Service と In-Role Cache サービスの既存のユーザーは引き続きサービスを利用できます。両サービスの提供終了日は、2016 年 11 月 30 日です。 その後は、Managed Cache Service はシャットダウンされ、In-Role Cache サービスはサポートされなくなります。 
>
>最初の Azure SDK リリースで In-Role Cache を新規作成する処理のサポートは削除されます (2016 年 2 月 1 日以降の予定)。 In-Role Cache を含む既存のプロジェクトを開くことはできます。 
>
>この期間中は、既存の Managed Cache Service と In-Role Cache サービス ユーザーをすべて Azure Redis Cache に移行することをお勧めします。 Azure Redis Cache は旧サービスよりも機能数が多く、全体的に優れたサービスです。 移行の詳細については、次を参照してください、 [Managed Cache Service から Azure Redis Cache への移行](cache-migrate-to-redis.md) ドキュメントの web ページです。 
>
>ご質問があれば、次のようにしてください。 [問い合わせ](https://azure.microsoft.com/support/options/?WT.mc_id=azurebg_email_Trans_933)します。 

### Azure Redis Cache

Azure Redis Cache は、最大 53 GB で一般公開されています。可用性の SLA は 99.9% です。 新しい [premium 階層](cache-premium-tier.md) 最大サイズは、530 GB とクラスタ リング、VNET、および 99.9% の sla の永続化のサポート。

Azure Redis Cache では、Microsoft が管理する安全な専用 Redis Cache を利用できます。 このサービスでは、Redis が提供する豊富な機能セットとエコシステムを利用し、Microsoft による信頼性の高いホスティングと監視を受けられます。

キーと値ペアのみを扱う従来のキャッシュとは異なり、Redis は高パフォーマンスな種類のデータに人気があります。 また、Redis は、このようなデータに対するアトミックな操作 (文字列の付加、ハッシュ内の値のインクリメント、リストへのプッシュ、積集合、和集合、および差集合の計算、並べ替えられた集合内で最高ランクのメンバーの取得など) の実行もサポートしています。 その他の機能として、トランザクションのサポート、パブリッシュ/サブスクライブ、Lua スクリプト、有効期限が制限されたキー、Redis を従来のキャッシュのように動作させるための構成設定があります。

Redis が正常に動作するために重要な点は、Redis を中心として正常に動作する強力なオープン ソース エコシステムが構築されていることです。 また、その環境を複数の言語で使用できる多様な Redis クライアントに反映します。 そうすることで、Azure 内に構築するほとんどすべてのワークロードに使用できるようになります。

Azure Redis Cache の概要の詳細については、次を参照してください。 [Azure Redis Cache の使用方法](cache-dotnet-how-to-use-azure-redis-cache.md) と [Azure Redis Cache のドキュメント](https://azure.microsoft.com/documentation/services/redis-cache/)します。

### Managed Cache Service

既存の Azure Managed Cache Service のお客様は、既存のサービスを引き続き利用することも、Azure Redis Cache へ移行して豊富な機能セットを利用することもできます。 また、Azure Managed Cache Service は一般公開されており、可用性の SLA は 99.9% です。

### インロール キャッシュ

In-role Cache を使用してキャッシュを自己ホストしている場合は、自己ホストを継続できます。 In-Role Cache は自己ホスト型コンポーネントであり、Microsoft ホスト型サービスではないので、SLA は用意されていません。 In-Role Cache ユーザーは、Azure Redis Cache に移行することで、豊富な機能を利用し、SLA を受けることができます。





