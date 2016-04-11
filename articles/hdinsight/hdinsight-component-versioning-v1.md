<properties
    pageTitle="HDInsight で提供される Hadoop クラスター バージョンの新機能 | Microsoft Azure"
    description="HDInsight は、デプロイできる Hadoop クラスター バージョンを複数サポートしています。 サポートされている Hadoop および Hortonworks Data Platform (HDP) ディストリビューションのバージョンを確認します。"
    services="hdinsight"
    editor="cgronlun"
    manager="paulettm"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/09/2015"
    ms.author="jgao"/>


#HDInsight で提供される Hadoop クラスター バージョンの新機能

##HDInsight のバージョンと Hadoop コンポーネント
Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。 各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントがプロビジョニングされます。 HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。 Azure HDInsight で使用される既定のクラスター バージョンは、2014 年 11 月 7 日現在、HDP 2.1.7 を基盤とした 3.1 です。


<table border="1">
<tr><th>コンポーネント</th><th>HDInsight Version 3.2</th><th>HDInsight Version 3.1 (既定)</th><th>HDInsight Version 3.0</th><th>HDInsight Version 2.1</th></tr>
<tr><td>Hortonworks Data Platform</td><td>2.2</td><td>2.1.7</td><td>2.0</td><td>1.3</td></tr>
<tr><td>Apache Hadoop & YARN</td><td>2.6.0</td><td>2.4.0</td><td>2.2.0</td><td>1.2.0</td></tr>
<tr><td>Apache Tez</td><td>0.5.2</td><td>0.4.0</td><td></td><td></td></tr>
<tr><td>Apache Pig</td><td>0.14.0</td><td>0.12.1</td><td>0.12.0</td><td>0.11.0</td></tr>
<tr><td>Apache Hive & HCatalog</td><td>0.14.0</td><td>0.13.1</td><td>0.12.0</td><td>0.11.0</td></tr>
<tr><td>Apazhe HBase </td><td>0.98.4</td><td>0.98.0</td><td></td><td></td></tr>
<tr><td>Apache Sqoop</td><td>1.4.5</td><td>1.4.4</td><td>1.4.4</td><td>1.4.3</td></tr>
<tr><td>Apache Oozie</td><td>4.1.0</td><td>4.0.0</td><td>4.0.0</td><td>3.3.2</td></tr>
<tr><td>Apache Zookeeper</td><td>3.4.6</td><td>3.4.5</td><td>3.4.5</td><td></td></tr>
<tr><td>Apache Storm</td><td>0.9.3</td><td>0.9.1</td><td></td><td></td></tr>
<tr><td>Apache Mahout</td><td>0.9.0</td><td>0.9.0</td><td></td><td></td></tr>
<tr><td>Apache Phoenix</td><td>4.2.0</td><td>4.0.0.2.1.7.0-2162</td><td></td><td></td></tr>
<tr><td>Apache Spark</td><td>1.3.1</td><td></td><td></td><td></td></tr>
</table>


**現在のコンポーネントのバージョンの取得**

HDInsight クラスターのバージョンに関連付けられたコンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。 利用可能なコンポーネントを特定し、どのバージョンがクラスターに使用されているかどうかを確認する 1 つの方法は、Ambari REST API を使用することです。  **GetComponentInformation** コマンドを使用して、サービス コンポーネントに関する情報を取得することです。 詳細については、「、 [Ambari のドキュメント][ambari-docs]します。 この情報を取得する別の方法は、リモート デスクトップを使用してクラスターにログインし、"C:\apps\dist\"ディレクトリの内容を直接にです。


**リリース ノート**

参照してください [HDInsight リリース ノート](hdinsight-release-notes.md) HDInsight の最新バージョンに関する追加のリリース ノートにします。

### HDInsight クラスターのプロビジョニング時にバージョンを選択

HDInsight Windows PowerShell コマンドレットまたは HDInsight .NET SDK を使用してクラスターを作成するときは、"Version" パラメーターを使用して、HDInsight Hadoop クラスターのバージョンを選択することができます。

使用する場合、 **簡易作成** オプション、既定で Hadoop クラスターを作成する HDInsight のバージョン 3.1 が取得されます。 使用する場合、 **カスタム作成** オプション、Azure 旧ポータルからデプロイすることは、クラスターのバージョンを選択できる、 **HDInsight Version** ] ボックスで、 **クラスターの詳細** ページです。

##機能概要
HDInsight プラットフォームの重要な機能には、次のようなものがあります。

- **Spark** -Apache Spark はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内処理をサポートするオープン ソースの並列処理のフレームワークです。 Spark のメモリ内の計算能力により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。

    Spark を使用して、従来のディスク ベースのデータ処理を実行することもできます。 Spark は、中間段階でのディスクへの書き込みを回避することで、従来の MapReduce フレームワークを向上しています。 また、Spark は Hadoop Distributed File System (HDFS) と Azure BLOB ストレージと互換性があるため、既存のデータは Spark を通じて簡単に処理できます。

    Spark はスクリプト アクションを使用して追加することもできます。  スクリプト アクションは、Spark 1.2.0 を HDInsight 3.2 クラスターに、または Spark 1.0.2 を HDInsight 3.1 クラスターに追加します。 詳細については、次を参照してください。 [HDInsight Hadoop での Spark クラスターをインストールして使用する](hdinsight-hadoop-spark-install.md)です。


- **Storm** - Azure HDInsight の Storm をすばやく一般的に使用できるようになりましたしする簡単な方法を展開し、数回クリックするだけで、内のリアルタイムの分析 (分) です。 Azure HDInsight の Apache Storm は、Apache Hadoop エコシステムのオープン ソース プロジェクトの 1 つで、何百万ものイベントを信頼性の高い方法で処理できる分析プラットフォームへのアクセスを提供します。 これにより、Hadoop ユーザーは、イベントが発生したときだけでなく、過去のイベントからも洞察を得ることができます。 Microsoft では、Visual Studio とのビルトイン統合も提供しており、開発者は Storm を簡単に操作できます。 Storm トポロジの開発、デプロイ、およびデバッグを、Visual Studio 内から実行できます。

- **Linux での HDInsight** -Azure HDInsight は、Linux (Ubuntu) 仮想マシン (Vm) で実行されるクラスターの Hadoop のプロビジョニングのオプションを提供します。 Linux または Unix に詳しい場合、既存の Linux ベースの Hadoop ソリューションから移行する場合、または Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、このオプションを使用して実行できます。 Windows または Linux が実行されているクライアント コンピューターから、Azure クラシック ポータル、Azure CLI、または HDInsight .NET SDK (Windows のみ) を使用して Linux 上に HDInsight クラスターをプロビジョニングできます。

- **追加の VM サイズ** -HDInsight クラスターがより多くの VM の種類とサイズで利用できるようになりました。 HDInsight クラスターは、汎用目的でビルドされた A2 ～ A7 サイズ、ソリッド ステート ドライブ (SSD) と 60% 高速のプロセッサを特徴とする D シリーズ ノード、高速ネットワーク用 InfiniBand をサポートする A8 と A9 サイズを利用できます。 Azure HDInsight で Apache HBase を使用するお客様は、D シリーズの大容量メモリ構成を利用してパフォーマンスを向上させることができます。 また、Azure HDInsight で Apache Storm を使用するお客様は、追加メモリを活用して大規模な参照データ セットを読み込んだり、高速な CPU によってスループットを向上させたりできます。

- **クラスターのスケール設定** -クラスターのスケール設定を削除または再作成しなくても実行する HDInsight クラスターのノードの数を変更できます。 現在この機能があるのは Hadoop クエリと Apache Storm のみですが、Apache HBase も近日中にサポートされます。

- **スクリプト操作** -このクラスター カスタマイズ機能は、カスタム スクリプトを使用して任意の方法で Hadoop クラスターの変更を有効します。 この新機能により、ユーザーは、Apache Hadoop エコシステムから入手できるプロジェクトを検証し、Azure HDInsight クラスターにデプロイできます。 このカスタマイズ機能は、Hadoop、HBase、Storm など、すべての種類の HDInsight クラスターで使用できます。

- **HBase** -HBase はビッグ データのオンライン トランザクション処理を可能にする遅延時間の短い NoSQL データベースです。 HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。 クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。 そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよびテレメトリのデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。

- **Apache Phoenix** -Apache Phoenix は HBase 上の構造化照会言語 (SQL) クエリ レイヤーです。 セカンダリ インデックスのレポートなど、SQL クエリ言語仕様のサブセットを限定的にサポートします。 また、HBase データ上で遅延時間が短いクエリを対象とする、クライアント内蔵の Java Database Connectivity (JDBC) ドライバーとして配信されます。 Apache Phoenix は SQL クエリを引き継ぎ、一連の HBase スキャンおよびコプロセッサの呼び出しにコンパイルし、通常の JDBC 結果セットを生成します。 Apache Phoenix は HBase 上のリレーショナル データベース レイヤーです。 また、HBase データ上で遅延時間が短いクエリを対象とする、クライアント内蔵の JDBC ドライバーとして配信されます。 Apache Phoenix は SQL クエリを引き継ぎ、一連の HBase スキャンにコンパイルし、それらのスキャンの実行を調整し、通常の JDBC 結果セットを生成します。

- **クラスター ダッシュ ボード** -HDInsight クラスターにデプロイする新しい web アプリケーション。 Hive クエリの実行、ジョブ ログの確認、Azure BLOB ストレージの表示に使用します。 Web アプリケーションにアクセスするための URL は <*ClusterName*>. >.azurehdinsight.net です。

- **Microsoft Avro ライブラリ** -このライブラリは、Microsoft .NET 環境向けに Apache Avro データ シリアル化システムを実装します。 Apache Avro は、シリアル化のためのコンパクトなバイナリ データ交換形式を提供します。 Apache Avro は、JavaScript Object Notation (JSON) を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。 ある言語でシリアル化されたデータは、別の言語で読むことができます。 現在、C、C++、C#、Java、PHP、Python、および Ruby がサポートされています。 Apache Avro シリアル化形式は、Hadoop MapReduce ジョブ内の複雑なデータ構造を表すために Azure HDInsight で広く使用されています。

- **YARN** の従来の Apache Hadoop MapReduce フレームワークでの Hadoop クラスターのデータ処理に代わる、新しい汎用的な分散アプリケーション管理フレームワークです。 Hadoop オペレーティング システムとして効果的に機能し、単一の目的のバッチ処理用データ プラットフォームであった Hadoop を、バッチ、インタラクティブ、オンライン、ストリームの各処理が可能な多目的プラットフォームにします。 この新しい管理フレームワークにより、容量の保証、公平性、サービス レベル アグリーメント (SLA) などの基準に従って、スケーラビリティおよびクラスター使用率が向上します。

- **Tez (HDInsight 3.1 以降のみ)** -Hadoop で小規模および大規模なワークロード間で簡単なデータ処理タスクを作成する、カスタマイズ可能な汎用フレームワーク。 1 つのジョブでタスクの複雑な無閉路有効グラフ (DAG) を実行できるため、Apache Hive や Apache Pig などの Apache Hadoop エコシステムのプロジェクトで、人間が介在する応答時間やペタバイト規模の高速スループットに関する要件を満たすことができます。 Hive 0.13 では、MapReduce ではなく Tez 上で Hive クエリを実行できます。

- **高可用性 (HA)** -サービスの可用性を高めるために、HDInsight によってデプロイされた Hadoop クラスターに 2 つ目のヘッドノードが追加されました。 通常 Hadoop クラスターの標準的な実装には、ヘッド ノードは 1 つしかありません。 HDInsight はセカンダリ ヘッドノードを追加することで、この単一障害点をなくします。 顧客が既定の L サイズ ノードを使わず、XL サイズのヘッドノードでクラスターをプロビジョニングしない限り、新しい HA クラスター構成に切り替えてもクラスターの価格は変わりません。

- **Hive のパフォーマンス** -Hive クエリの応答時間 (最大 40 x) とデータ圧縮率の順序の絶対値による機能強化 (最大 80%) を使用して、 **Optimized Row Columnar** (ORC) 形式です。

- **Pig、Sqoop、Oozie、Ambari** -HDInsight クラスター version 2.1 (HDP 1.3/Hadoop 1.2) でのパリティを提供する HDInsight クラスター version 3.0 (HDP 2.0/Hadoop 2.2) のコンポーネントのバージョン アップグレードします。 詳細については、以下のバージョンの各表を参照してください。

- **Mahout** -このスケーラブルな機械学習アルゴリズムのライブラリは HDInsight 3.1 (以降) にプレインストールされている Hadoop クラスターです。 そのため、他のクラスター構成を追加することなく、Mahout ジョブを実行できます。

- **Virtual Network のサポート** -HDInsight クラスターは、クラウド リソースまたはデータ センターで使用したクラウド リソースをリンクするハイブリッド シナリオの分離をサポートするために Azure の仮想ネットワークで使用できます。


## サポートされているバージョン
次の表に、現在使用できる HDInsight のバージョン、該当する Hortonworks Data Platform のバージョン、そのリリース日を示します。 非推奨となった場合は、サポート有効期限とその日付も記載されます。 以下の点に注意してください。

* ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight 2.1 以降では既定でデプロイされています。 HDInsight 1.6 クラスターでは利用できません。
* 特定のバージョンのサポート期限が切れると、Azure クラシック ポータルから利用できなくなる可能性があります。 次の表に、Azure クラシック ポータルで利用できるバージョンを示します。 クラスター バージョンは引き続きご利用可能なを使用して、 `Version` Windows PowerShell でのパラメーター [新規 AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) コマンドと、廃止日までの .NET SDK です。

<table border="1">
<tr><th>HDInsight のバージョン</th><th>HDP のバージョン</a><th>高可用性</th></th><th>リリース日</th><th>Azure クラシック ポータルでの利用</th><th>サポート有効期限</th><th>非推奨となる日</th></tr>
<tr><td>HDI 3.2</td><td>HDP 2.2</td><td>あり</td><td>2015 年 2 月 18 日</td><td>あり</td><td></td><td></td></tr>
<tr><td>HDI 3.1</td><td>HDP 2.1</td><td>あり</td><td>2014 年 6 月 24 日</td><td>あり</td><td></td><td></td></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>あり</td><td>02/11/2014</td><td>あり</td><td>09/17/2014</td><td>06/30/2015</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>あり</td><td>10/28/2013</td><td>いいえ</td><td>05/12/2014</td><td>05/31/2015</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>いいえ</td><td>10/28/2013</td><td>いいえ</td><td>04/26/2014</td><td>05/31/2015</td></tr>
</table><br>

**既定以外のクラスターのデプロイメント**

HDInsight 3.1 クラスターが既定では Hadoop 2.4 で作成されるため、ユーザーが使用する必要があります、 **カスタム作成** 、ポータルのオプションを作成する必要がある場合は、他の HDInsight クラスター バージョンを指定します。

### HDInsight クラスター バージョンのサービス レベル アグリーメント

SLA は、"サポート ウィンドウ" の条件で定義されます。 サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サービス & サポートによってサポートされる期間を指しています。 HDInsight クラスターは、そのバージョンがある場合、サポート対象外は、 **サポート有効期限** 現在の日付を過ぎています。 サポートされている HDInsight クラスターのバージョンの一覧は上記のテーブルに示されています。 所定の HDInsight Version X (新しい X+1 バージョンが利用可能なった後) のサポート有効期限は、次の数式で計算した日付のうち、遅い方とされます。  

- 数式 1: HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
- 数式 2: HDInsight クラスター バージョン X+1 (X の次のバージョン) が Azure クラシック ポータルで使用可能になった日付に 90 日を加える。

 **非推奨となる日付** 日付を HDInsight でその後、クラスター バージョンを作成できません。

> [AZURE.NOTE] HDInsight 2.1 と 3.0 クラスターの両方が Azure ゲスト OS で実行 [ファミリ 4](../cloud-services-guestos-update-matrix.md), を 64 ビット版の Windows Server 2012 R2 を使用し、.NET Framework 4.0、4.5 をサポートしています。 および 4.5.1 を選択します。

## HDInsight バージョンに対応する Hortonworks リリース ノート##

* HDInsight クラスター version 3.2 に基づく Hadoop ディストリビューションを使用して [Hortonworks Data Platform 2.2][hdp-2-2]します。

    * 特定の Apache コンポーネントのリリース ノート [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), 、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), 、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), 、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), 、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), 、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), 、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), 、[共通](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), 、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), 、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), 、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), 、[Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)します。


* HDInsight クラスター version 3.1 に基づく Hadoop ディストリビューションを使用して [Hortonworks Data Platform 2.1.7][hdp-2-1-7]します。 これは、 **既定** Hadoop クラスターの 2014 年 11 月 7 日後に、Azure HDInsight ポータルを使用するときに作成します。 HDInsight 3.1 クラスターに基づいて作成された 2014 年 11 月 7 日前に、作成、 [Hortonworks Data Platform 2.1.1][hdp-2-1-1]します。

* HDInsight クラスター version 3.0 に基づく Hadoop ディストリビューションを使用して [Hortonworks Data Platform 2.0][hdp-2-0-8]します。

* HDInsight クラスター version 2.1 に基づいている Hadoop ディストリビューションを使用して [Hortonworks Data Platform 1.3][hdp-1-3-0]します。

* HDInsight クラスター version 1.6 に基づく Hadoop ディストリビューションを使用して [Hortonworks Data Platform 1.1][hdp-1-1-0]します。


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/


