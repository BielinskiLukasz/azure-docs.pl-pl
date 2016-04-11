<properties 
    pageTitle="Application Insights からのテレメトリの連続エクスポート" 
    description="診断および利用状況データを Microsoft Azure のストレージにエクスポートし、そこからダウンロードします。" 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/15/2015" 
    ms.author="awills"/>
 
# Application Insights からのテレメトリのエクスポート

テレメトリに対してカスタマイズした分析を行う必要がありますか。 それとも、特定のプロパティを持つイベントに対して電子メール アラートを送信する必要がありますか。 そのようなケースには、連続エクスポートが最適です。 Application Insights ポータルに表示されるイベントは、JSON 形式で Microsoft Azure のストレージにエクスポートできます。 そこからデータをダウンロードしたり、データを処理するためのコードを自由に記述したりできます。  

連続エクスポートは、無料の試用期間と使用可能な [Standard と Premium 料金プラン](http://azure.microsoft.com/pricing/details/application-insights/)します。

(実行する場合、 [1 回限りのエクスポート](app-insights-metrics-explorer.md#export-to-excel) 表示される内容のメトリックや検索ブレードで、ブレードの上部にあるエクスポート] をクリックします。 Power BI でデータを表示する場合を使用して [アダプター](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx) で、これ *しない* 連続エクスポートを使用します)。

## ストレージ アカウントの作成

"クラシック" ストレージ アカウントがない場合は、ここで作成します。


1. サブスクリプションの「昔ながらの」ストレージ アカウントの作成、 [Azure ポータル](https://portal.azure.com)します。

    ![Azure ポータルで、[新規]、[データ]、[Storage] の順に選択します](./media/app-insights-export-telemetry/030.png)

2. コンテナーを作成します。

    ![新しいストレージで、[コンテナー] を選択し、[コンテナー] タイルをクリックし、[追加] を選択します](./media/app-insights-export-telemetry/040.png)

## <a name="setup"></a>連続エクスポートの設定

Application Insights ポータルのアプリケーションの概要ブレードで、[連続エクスポート] を開きます。 

![下へスクロールし、[連続エクスポート] をクリックします](./media/app-insights-export-telemetry/01-export.png)

エクスポートを追加して、 [Azure ストレージ アカウント](../storage-introduction.md) データを配置します。

![[追加]、[エクスポート先]、[ストレージ アカウント] の順にクリックし、新しいストアを作成するかまたは既存のストアを使用するかを選択します。](./media/app-insights-export-telemetry/02-add.png)

エクスポートするイベントの種類を選択します。

![[イベントの種類の選択] をクリックします](./media/app-insights-export-telemetry/03-types.png)


エクスポートが作成されると、処理が開始されます  (エクスポートを作成した後に到着したデータのみが取得されます)。 

BLOB でデータが表示されるまで、約 1 時間の遅延が発生する可能性があります。

後でイベントの種類を変更する場合は、単にエクスポートを編集します。

![[イベントの種類の選択] をクリックします](./media/app-insights-export-telemetry/05-edit.png)

ストリームを停止するには、[無効] をクリックします。 もう一度 [有効] をクリックすると、新しいデータでストリームが再開されます。 エクスポートが無効な場合、ポータルに到着したデータは取得されません。

ストリームを完全に停止するには、エクスポートを削除します。 エクスポートを削除しても、ストレージのデータは削除されません。

#### エクスポートを追加または変更できない

* エクスポートを追加または変更するには、所有者、共同作成者、または Application Insights 共同作成者のアクセス権が必要になります。 [ロールの概要を説明][roles]します。

## <a name="analyze"></a>取得されるイベント

エクスポートされたデータは、お客様のアプリケーションから受け取った未加工のテレメトリですが、クライアントの IP アドレスから計算された位置データが追加されます。 

他の計算メトリックは含まれません。 たとえば、平均 CPU 使用率はエクスポートされませんが、平均の計算に使用された未加工のテレメトリはエクスポートされます。

データは、いずれかの結果も含まれています。 [可用性 web テスト](app-insights-monitor-web-app-availability.md) を設定します。 

> [AZURE.NOTE] **をサンプリングします。** アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 [サンプリングの詳細については、こちらを参照してください。](app-insights-sampling.md)

## <a name="get"></a> データの確認

ポータルでストレージを直接検査することができます。 クリックして **参照**, 、ストレージ アカウントを選択して、開き **コンテナー**します。

Visual Studio での Azure のストレージを検査するには、開く **ビュー**, 、**クラウド エクスプ ローラー**します。 (メニュー コマンドを取得していない場合、Azure SDK をインストールする必要があります開いている、 **。新しいプロジェクト** ダイアログ ボックスで、Visual c# 順に展開/クラウドを選択して **Microsoft Azure SDK for .NET を取得**.)

BLOB ストアを開くと、BLOB ファイルのセットを含むコンテナーが表示されます。 各ファイルの URI は、Application Insights のリソース名、そのインストルメンテーション キー、テレメトリの種類/日付/時刻から派生します  (リソース名はすべて小文字になり、インストルメンテーション キーのダッシュは省略されます)。

![適切なツールで Blob ストアを調べます](./media/app-insights-export-telemetry/04-data.png)

日付と時刻は UTC 形式で表され、テレメトリが生成された時間ではなく、ストアに格納された日時を示します。 そのため、データをダウンロードするコードを記述する場合は、直線的にデータ内を移動できます。


## <a name="format"></a> データ形式

* それぞれの Blob は、"\n" で区切られた複数の行を含むテキスト ファイルです。
* それぞれの行は、書式設定されていない JSON ドキュメントです。 詳細を確認する場合は、Visual Studio でファイルを開き、[編集]、[詳細]、[フォーマット ファイル] の順に選択します。

![適切なツールでテレメトリを表示します](./media/app-insights-export-telemetry/06-json.png)

時間の長さはティック単位で表記されます。10,000 ティックが 1 ミリ秒です。 たとえば、これらの値は、ブラウザーから要求を送信するのに 10 ミリ秒、要求を受信するのに 30 ミリ秒、ブラウザーでページを処理するのに 1.8 秒の時間がかかったことを示しています。

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[データ モデルについては、プロパティの型と値のリファレンスで詳しく説明されています。](app-insights-export-data-model.md)

## データの処理

小規模な処理では、データを分解してスプレッドシートに読み込んだ後で他の処理を実行するコードを記述できます。 次に例を示します。

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

大規模なコード サンプルを参照してください。 [ワーカー ロールを使用して][exportasa]します。



## <a name="delete"></a>古いデータの削除
必要に応じて古いデータを削除するなどしてストレージ容量を管理する責任があることに注意してください。 

## ストレージ キーを再生成する場合

ストレージのキーを変更した場合、連続エクスポートは動作しなくなります。 Azure アカウントに通知が表示されます。 

[連続エクスポート] ブレードを開き、エクスポートを編集します。 エクスポート先の編集画面では、同じストレージを選択したままにします。 [OK] をクリックして確定します。

![連続エクスポートを編集し、3 つのエクスポート先を開いた後閉じます。](./media/app-insights-export-telemetry/07-resetstore.png)

連続エクスポートが再開されます。

## エクスポート サンプル


* [worker ロールを使用して SQL にエクスポートする][exportcode]
* [Stream Analytics を使用して SQL にエクスポートする][exportasa]
* [Stream Analytics を使用して Power BI にエクスポートする](app-insights-export-power-bi.md)
 * これは Power BI の標準的な利用方法ではないことに注意してください。 ある [アダプター](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx) 連続エクスポートを必要としません。


大規模なスケールを検討してください [HDInsight](http://azure.microsoft.com/services/hdinsight/) -クラウドの Hadoop クラスターです。 HDInsight は、ビッグ データを管理および分析するためのさまざまなテクノロジを提供します。



## Q & A

* *グラフを 1 回だけダウンロードしたいのですが。*  
 
    はい、できます。 ブレードの上部にあるをクリックして [データのエクスポート](app-insights-metrics-explorer.md#export-to-excel)します。

* *エクスポートを設定したのにストアにデータがありません。*

    エクスポートを設定した時点以降に Application Insights がアプリからテレメトリを受信していますか。 取得されるのは新しいデータのみです。

* *エクスポートを設定しようとしたところ、アクセスが拒否されました。*

    アカウントが組織によって所有されている場合は、所有者または共同作成者グループのメンバーである必要があります。

    <!-- Your account has to be either a paid-for account, or in the free trial period. -->

* *自分のオンプレミスのストアに直接エクスポートできますか。* 

    いいえ、できません。 現在のところ、エクスポート エンジンは、Azure Storage でのみ動作します。  

* *ストアに格納できるデータの量に制限はありますか。* 

    いいえ、できません。 おを保存データのプッシュ配信エクスポートを削除するまでです。 Blob Storage の制限に達した場合は配信が停止されますが、その制限には非常に大きな値が設定されています。 使用するストレージの量を管理するのはお客様です。  

* *ストレージに表示される BLOB の数を教えてください。*

 * エクスポートに選択した各データの種類ごとに、1 つの新しい BLOB が 1 分ごとに作成されます (データを使用できる場合)。 
 * さらに、トラフィック負荷の高いアプリケーションでは、追加のパーティション単位が割り当てられます。 この場合、各単位で 1 分ごとに 1 つの BLOB が作成されます。


* *ストレージのキーを再生成した後、またはコンテナーの名前を変更した後、エクスポートが動作しません。*

    エクスポートを編集し、[エクスポート先] ブレードを開きます。 以前と同じストレージが選択されていることを確認し、[OK] をクリックして確定します。 エクスポートが再開されます。 変更を加えたのが数日前のことであれば、データは失われません。

* *エクスポートを一時停止できますか。*

    はい。 [無効] をクリックします。

## コード サンプル

* [Power BI への Application Insights のストリーム](app-insights-export-power-bi.md)
* [worker ロールを使用してエクスポートされた JSON を解析する][exportcode]
* [Stream Analytics を使用して SQL にエクスポートする][exportasa]

* [データ モデルについては、プロパティの型と値のリファレンスで詳しく説明されています。](app-insights-export-data-model.md)

<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md

 


