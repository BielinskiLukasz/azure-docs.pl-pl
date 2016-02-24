<properties 
    pageTitle="Stream Analytics を利用し、Application Insights のデータを Power BI にエクスポートする" 
    description="Stream Analytics を利用し、エクスポートしたデータを処理する方法について説明します。" 
    services="application-insights" 
    documentationCenter=""
    authors="noamben" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/25/2015" 
    ms.author="awills"/>
 
# Stream Analytics を利用し、Application Insights のデータを Power BI に入力する

この記事は、使用する方法を示しています。 [Stream Analytics](http://azure.microsoft.com/services/stream-analytics/) データを処理する [エクスポート](app-insights-export-telemetry.md) から [Visual Studio Application Insights](app-insights-overview.md)します。 データを送信して、例のターゲットとして [Microsoft Power BI](https://powerbi.microsoft.com/)します。 


> [AZURE.NOTE] Application Insights から Power BI にデータを取得するための最も簡単な方法は [アダプターを使用して](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-application-insights/) に [サービス] で Power BI ギャラリーにあります。 この記事で説明する方法は、現在のところ、より多面的なものですが、Application Insights で Stream Analytics を利用する方法を紹介するものでもあります。

[Microsoft Power BI](https://powerbi.microsoft.com/) 複数のソースから情報をまとめる機能豊富で多様なビジュアルでデータが表示されます。 


![Application Insights の使用状況データの Power BI ビュー サンプル](./media/app-insights-export-power-bi/010.png)

[Stream Analytics](http://azure.microsoft.com/services/stream-analytics/) はアダプターを継続的に、データの処理として機能しますが Application Insights からエクスポートされる Azure サービスです。

![Application Insights の使用状況データの Power BI ビュー サンプル](./media/app-insights-export-power-bi/020.png)




## ビデオ

Noam Ben Zeev で、この記事で説明する内容を確認できます。

> [AZURE.VIDEO export-to-power-bi-from-application-insights]


**サンプリングできます。**アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 [サンプリングについて説明します。](app-insights-sampling.md)

## Application Insights によるアプリの監視

まだ試していないなら、今からはじめましょう。 Application Insights では、さまざまなプラットフォーム (Windows、iOS、Android、J2EE など) のすべてのデバイスまたは Web アプリを監視できます。 [開始](app-insights-overview.md)します。

## Azure でのストレージの作成

連続エクスポートでは、常に Azure のストレージ アカウントにデータが出力されるため、まずストレージを作成する必要があります。

1. サブスクリプションの「昔ながらの」ストレージ アカウントの作成、 [Azure ポータル](https://portal.azure.com)します。

    ![Azure ポータルで、[新規]、[データ]、[Storage] の順に選択します](./media/app-insights-export-power-bi/030.png)

2. コンテナーを作成する

    ![新しいストレージで、[コンテナー] を選択し、[コンテナー] タイルをクリックし、[追加] を選択します](./media/app-insights-export-power-bi/040.png)

3. ストレージ アクセス キーのコピー

    これは、ストリーム分析サービスへの入力のセットアップのためにすぐに必要になります。

    ![ストレージで、[設定]、[キー] の順に開き、プライマリ アクセス キーのコピーを取ります](./media/app-insights-export-power-bi/045.png)

## Azure Storage への連続エクスポートの開始

[連続エクスポート](app-insights-export-telemetry.md) Azure ストレージに Application Insights からデータを移動します。

1. Azure ポータルで、アプリケーション用に作成した Application Insights リソースを参照します。

    ![[参照]、[Application Insights]、アプリケーションの順に選択します](./media/app-insights-export-power-bi/050.png)

2. 連続エクスポートを作成します。

    ![[設定]、[連続エクスポート]、[追加] の順に選択します](./media/app-insights-export-power-bi/060.png)


    Select the storage account you created earlier:

    ![Set the export destination](./media/app-insights-export-power-bi/070.png)
    
    Set the event types you want to see:

    ![Choose event types](./media/app-insights-export-power-bi/080.png)

3. データを蓄積します。 しばらく待機し、ユーザーにアプリケーションを使用してもらいます。 テレメトリが開始し、統計グラフが表示されます [メトリックス エクスプ ローラー](app-insights-metrics-explorer.md) と個々 のイベントが [診断検索](app-insights-diagnostic-search.md)します。 

    また、データはストレージにもエクスポートされます。 

4. エクスポートされたデータを検査します。 Visual Studio で、次のように選択します。 **表示/クラウド エクスプ ローラー**, を開き、Azure/ストレージです。 (このメニュー オプションがない場合は、Azure SDK をインストールする必要があります。[新しいプロジェクト] ダイアログを開き、[Visual c#]、[クラウド]、[Microsoft Azure SDK for .NET の取得] の順に開きます)。

    ![](./media/app-insights-export-power-bi/04-data.png)

    パス名の共通部分を書き留めます。共通部分はアプリケーションの名前とインストルメンテーション キーから派生します。 

イベントが JSON 形式で BLOB ファイルに書き込まれます。 各ファイルに 1 つ以上のイベントが含まれる場合があります。 このため、イベント データを読み取って必要なフィールドをフィルター処理します。 データの処理に関して実行できることは多数ありますが、今日の計画は、Stream Analytics を使用してデータを Power BI に移動することです。

## Azure Stream Analytics インスタンスの作成

 [従来の Azure ポータル](https://manage.windowsazure.com/), Azure Stream Analytics サービスを選択して、新しい Stream Analytics ジョブを作成します。


![](./media/app-insights-export-power-bi/090.png)



![](./media/app-insights-export-power-bi/100.png)

新しいジョブが作成された後、その詳細を展開します。

![](./media/app-insights-export-power-bi/110.png)


#### BLOB の場所の設定

連続エクスポート BLOB から入力を取るよう設定します。

![](./media/app-insights-export-power-bi/120.png)

ここで、ストレージ アカウントからのプライマリ アクセス キーが必要になります。これは前にメモしておいたものです。 ストレージ アカウント キーとしてこれを設定します。

![](./media/app-insights-export-power-bi/130.png)

#### パスのプレフィックス パターンの設定 

![](./media/app-insights-export-power-bi/140.png)


日付の書式は YYYY-MM-DD (ダッシュ付き) に設定してください。

パスのプレフィックス パターンは、Stream Analytics がストレージ内の入力ファイルを検索する場所を指定します。 連続エクスポートによるデータ格納方法と一致するように設定する必要があります。 次のように設定します。

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

次の点に注意してください。

* `webapplication27` Application Insights リソースの名前を指定 **すべて小文字に変換**します。
* `1234...` Application Insights リソースのインストルメンテーション キーは、 **ダッシュ記号を省略すると**です。 
* `PageViews` は分析するデータの種類です。 使用可能な種類は、連続エクスポートで設定するフィルターによって異なります。 エクスポートされたデータを他の利用可能な型を参照して調べる、 [データ モデルをエクスポート](app-insights-export-data-model.md)します。
* `/{date}/{time}` はそのまま書き込まれるパターンです。

> [AZURE.NOTE] パスを正しく理解することを確認するストレージを検査します。

#### 初期セットアップの完了

シリアル化の書式を確認します。

![[確認]をクリックしてウィザードをして閉じます](./media/app-insights-export-power-bi/150.png)

ウィザードを閉じ、セットアップが完了するまで待機します。

> [AZURE.TIP] 一部のデータをダウンロードするのにには、サンプルのコマンドを使用します。 クエリをデバッグするために、それをテスト サンプルとして保存します。

## 出力の設定

では、ジョブを選択し、出力を設定しましょう。

![新しいチャネルを選択して、[出力]、[追加]、[Power BI] をクリックします。](./media/app-insights-export-power-bi/160.png)

提供、 **職場または学校のアカウント** Stream Analytics Power BI リソースへのアクセスを承認するためにします。 次に、出力に名前を付け、ターゲット Power BI データセットと表に名前を付けます。

![3 つの名前を作成します。](./media/app-insights-export-power-bi/170.png)

## クエリの設定

クエリでは、入力から出力への変換を制御します。

![ジョブを選択し、[クエリ] をクリックします。 以下のサンプルを貼り付けます。](./media/app-insights-export-power-bi/180.png)


テスト機能を使用し、出力が正しいことを確認します。 入力ページから取得したサンプル データを指定します。 

#### イベントの数を表示するためのクエリ

このクエリを貼り付けます。

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input は、ストリーム入力に付けたエイリアスです。
* pbi-output は、定義した出力エイリアスです。
* 使用して [外部適用 GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) イベント名が入れ子になった JSON 配列であるためです。 Select でイベント名と、期間内にその名前を持つインスタンスの個数を取得します。  [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) 句は、1 分の期間内に要素をグループ化します。


#### メトリックの値を表示するためのクエリ


```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* このクエリはメトリックス テレメトリをドリルダウンし、イベント時刻とメトリック値を取得します。 メトリックの値は、外側の適用 GetElements パターンを使用して行を抽出するため、配列内にあります。 "myMetric"は、ここでは、メトリックの名前です。 

#### ディメンション プロパティの値を追加するクエリ

```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* このクエリは、次元配列の固定インデックスにある特定のディメンションに依存せず、ディメンション プロパティの値を追加します。

## ジョブを実行する

ジョブを開始する過去の日付を選択できます。 

![ジョブを選択し、[クエリ] をクリックします。 以下のサンプルを貼り付けます。](./media/app-insights-export-power-bi/190.png)

ジョブが実行されるまで待ちます。

## Power BI で結果を確認します。

職場または学校のアカウントで Power BI を開き、Stream Analytics ジョブの出力として定義したデータセットとテーブルを選択します。

![Power BI で、データセットとフィールドを選択します。](./media/app-insights-export-power-bi/200.png)

このデータセットをレポートやダッシュ ボードを使用すれば [Power BI](https://powerbi.microsoft.com)します。


![Power BI で、データセットとフィールドを選択します。](./media/app-insights-export-power-bi/210.png)

## ビデオ

Noam Ben Zeev で、Power BI にエクスポートする方法を確認できます。

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## 関連項目

* [連続エクスポート](app-insights-export-telemetry.md)
* [データ モデルについては、プロパティの型と値のリファレンスで詳しく説明されています。](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [その他のサンプルとチュートリアル](app-insights-code-samples.md)

