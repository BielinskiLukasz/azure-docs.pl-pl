<properties
 pageTitle="HDInsight での一定期間に発生したイベントの Storm および HBase との関連付け"
 description="HDInsight で Storm と HBase を使用して、別々の時間に到着するイベントを関連付ける方法について説明します。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="12/04/2015"
 ms.author="larryfr"/>

# HDInsight での一定期間に発生したイベントの Storm および HBase との関連付け

Apache Storm の永続的なデータ ストアを使用して、別々の時刻に到着するデータのエントリを関連付けることができます。 たとえば、ユーザー セッションのログイン イベントとログアウト イベントをリンクして、セッションの継続期間を計算します。

このドキュメントでは、ユーザー セッションのログイン イベントとログアウト イベントを追跡し、セッションの期間を計算する基本的な C# Storm トポロジを作成する方法を学習します。 このトポロジは、永続的なデータ ストアとして HBase を使用します。 HBase では、履歴データに対してバッチのクエリを実行し、特定の期間に開始または終了したユーザー セッションの数などの追加の情報を生成することもできます。

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

## 前提条件

-   HDInsight tools for Visual Studio の: を参照してください [Visual Studio の HDInsight ツールを使い始める](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) インストールについてです。

-   HDInsight クラスターでの Apache Storm

-   HDInsight クラスターでの Apache HBase

## アーキテクチャ

![トポロジ経由のデータ フロー図](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

イベントを関連付けるには、イベント ソースの共通の識別子が必要です。 たとえば、ユーザー ID、セッション ID、または a) 一意で、b) Storm に送信されたすべてのデータに含まれている他のデータが必要です。 この例では、GUID 値を使用してセッション ID を表します。

この例は、次の 2 つの HDInsight クラスターで構成されます。

-   HBase: 履歴データの永続的なデータ ストア

-   Storm: 受信データの取り込みに使用

データは、Storm トポロジによってランダムに生成され、次の項目で構成されます。

-   セッション ID: 各セッションを一意に識別する GUID

-   イベント: START または END イベント。 この例では、START は必ず END の前に発生します。

-   時刻: イベントの時刻。

このデータは、処理されて HBase に格納されます。

### Storm トポロジ

セッションの開始時に、 **開始** イベントは、トポロジによって受信され、HBase に記録します。 ときに、 **エンド** イベントを受け取った場合、トポロジを取得、 **開始** イベントし、2 つのイベントまでの時間を計算します。 これは、 **期間** 値がと共に HBase に格納し、 **エンド** イベント情報です。

> [AZURE.IMPORTANT] このトポロジは、基本的なパターンを示した例、運用ソリューションは、次のシナリオのための設計を行う必要があります。
>
> - 順序に関係なく到着するイベント
> - 重複イベント
> - 削除されたイベント

サンプルのトポロジは、次のコンポーネントで構成されています。

-   Session.cs: ランダムなセッション ID、開始時刻、セッションの継続時間を作成することで、ユーザー セッションをシミュレートします。

-   Spout.cs: 100 個のセッションを作成し、START イベントを出力します。セッションごとにランダムなタイムアウトを発生させて待機し、END イベントを出力します。 その後、終了したセッションをリサイクルして、新しいセッションを生成します。

-   HBaseLookupBolt.cs: セッション ID を使用して HBase からセッション情報を検索します。 END イベントが処理されると、対応する START イベントを検索して、セッションの期間を計算します。

-   HBaseBolt.cs: HBase に情報を格納します。

-   TypeHelper.cs: HBase からの読み取り/HBase への書き込み時の型の変換で役立ちます。

### HBase のスキーマ

HBase では、データは、次のスキーマと設定を使用してテーブルに格納されます。

-   Row key: セッション ID が、このテーブルの行キーとして使用されます。

-   Column family: ファミリ名は 'cf' です。 このファミリに格納される列は、次のとおりです。

    -   event: START または END

    -   time: イベントが発生した時刻 (ミリ秒)

    -   duration: START と END イベントの間の時間

-   VERSIONS: 各行の 5 つのバージョンを保持するよう 'cf' ファミリを設定

    > [AZURE.NOTE] バージョンは、特定の行キーの格納されている以前の値のログです。 既定では、HBase により、行の最新バージョンの値のみが返されます。 この場合は、同じ行がすべてのイベント (START、END) に使用されます。行の各バージョンは、タイムスタンプの値によって識別されます。 これにより、ログに記録された、特定の ID のイベント履歴を表示できます。

## プロジェクトのダウンロード

サンプル プロジェクトはからダウンロードできます [hdinsight storm-eventcorrelation](https://github.com/Blackmist/hdinsight-storm-eventcorrelation)します。

このダウンロードには、次の C# プロジェクトが含まれています。

-   CorrelationTopology: ユーザー セッションの開始イベントと終了イベントをランダムに生成する C# Storm トポロジ。 各セッションは、1 ～ 5 分間継続します。

-   SessionInfo: HBase テーブルを作成し、格納されているセッション データに関する情報を返すクエリの例を提供する C# コンソール アプリケーション。

## テーブルを作成する

1. 開いている、 **SessionInfo** Visual Studio のプロジェクトです。

2.  **ソリューション エクスプ ローラー**, を右クリックし、 **SessionInfo** プロジェクトし、選択 **プロパティ**します。

    ![メニューとプロパティが選択されたスクリーンショット](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. 選択 **設定**, 、次の値を設定します。

    -   HBaseClusterURL: HBase クラスターの URL。 たとえば、https://myhbasecluster.azurehdinsight.net

    -   HBaseClusterUserName: クラスターの管理者/HTTP ユーザー アカウント

    -   HBaseClusterPassword: 管理者/HTTP ユーザー アカウントのパスワード

    -   HBaseTableName: この例で使用するテーブルの名前

    -   HBaseTableColumnFamily: 列ファミリ名

    ![[設定] ダイアログの画像](./media/hdinsight-storm-correlation-topology/settings.png)

5. ソリューションを実行する メッセージが表示されたら、'c' キーを選択し、HBase クラスター上でテーブルを作成します。

## Storm トポロジをビルドおよびデプロイする

1.  開いている、 **[correlationtopology]** Visual Studio でソリューションです。

2.   **ソリューション エクスプ ローラー**, を右クリックして、 **[correlationtopology]** プロジェクトのプロパティ] を選択します。

3.  [プロパティ] ウィンドウで [ **設定** し、次の情報を提供します。 最初の 5 で使用される同じ値を指定する必要があります、 **SessionInfo** プロジェクト。

    -   HBaseClusterURL: HBase クラスターの URL。 たとえば、https://myhbasecluster.azurehdinsight.net

    -   HBaseClusterUserName: クラスターの管理者/HTTP ユーザー アカウント

    -   HBaseClusterPassword: 管理者/HTTP ユーザー アカウントのパスワード

    -   HBaseTableName: この例で使用するテーブルの名前。 これは、SessionInfo プロジェクトで使用するのと同じテーブル名を含める必要があります。

    -   HBaseTableColumnFamily: 列ファミリ名。 これは、SessionInfo プロジェクトで使用するのと同じ列ファミリ名を含める必要があります。

    > [AZURE.IMPORTANT] デフォルトで使用される名前では、HBaseTableColumnNames は変更しないで **SessionInfo** データを取得します。

4.  プロパティを保存し、プロジェクトをビルドします。

5.   **ソリューション エクスプ ローラー**, プロジェクトを右クリックして、選択 **HDInsight での Storm に送信**します。 メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。

    ![[Storm に送信] メニュー項目の画像](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6.   **トポロジの送信** ダイアログ ボックスで、このトポロジを実行する Storm クラスターを選択します。

    > [AZURE.NOTE] 初めてのトポロジを送信する HDInsight クラスターの名前を取得するために数秒かかることがあります。

7.  トポロジがアップロードおよび、クラスターに送信されたら、 **Storm トポロジ ビュー** が開き、実行中のトポロジを表示します。 選択、 **[correlationtopology]** 上部にある [更新] ボタンを使用して、トポロジ情報を更新するページの右です。

    ![トポロジ ビューの画像](./media/hdinsight-storm-correlation-topology/topologyview.png)

    トポロジがデータの値の生成が開始すると、 **Emitted** 列の値が増加します。

    > [AZURE.NOTE] 場合、 **Storm トポロジ ビュー** は自動的に開くではなく、次の手順を使用して、開いて。
    >
    > 1.  **ソリューション エクスプ ローラー**, 、展開 **Azure**, 、順に展開 **HDInsight**します。
    >
    > 2. トポロジで実行されている Storm クラスターを右クリックし、[ **View Storm Topologies**

## データを照会する

データが出力されたら、次の手順を使用してデータを照会します。

1. 戻り、 **SessionInfo** プロジェクトです。 実行されていない場合は、トポロジの新しいインスタンスを開始します。

2. メッセージが表示されたら、[ **s** 開始イベントを検索します。 時間範囲を定義するために、開始時刻および終了時刻を入力するように求められます。これら 2 つの時刻の間のイベントのみが返されます。

    開始時刻と終了時刻を入力するときには、HH:MM と 'am' または 'pm' の形式を使用します。 たとえば、11:20pm です。

    トポロジが開始されたので、開始時刻にはデプロイするよりも以前の時刻、終了時刻には現在の時刻を使用します。 これにより、トポロジの開始時に生成された大部分の START イベントが取り込まれます。 クエリの実行時に、次のようなエントリの一覧が表示されます。

        Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

START イベントと同じ方法で END イベントを検索できます。 ただし、END イベントは、START イベントの後に、1 ～ 5 分の間隔でランダムに生成されます。 よって、END イベントを検索するために、いくつかの時間範囲を試す必要がある場合があります。 END イベントには、セッションの期間も含まれます。これは、START イベントの時刻と END イベントの時刻の差です。 次に、END イベントのデータの例を示します。

    Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [AZURE.NOTE] 入力した時刻の値は、現地時刻では、クエリから返される時刻は UTC になります。

##トポロジを停止する

トポロジを停止する準備ができたらに戻り、 **[correlationtopology]** Visual Studio のプロジェクトです。  **Storm トポロジ ビュー**, 、トポロジを選択し、使用、 **Kill** 、トポロジ ビューの上部にあるボタンをクリックします。

##次のステップ

Storm 例については、次を参照してください。 [HDInsight での Storm に関するトポロジ例](hdinsight-storm-example-topology.md)します。
 
