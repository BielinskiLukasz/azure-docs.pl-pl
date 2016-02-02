<properties 
    pageTitle="出力データをオンプレミスの SQL Server データベースにコピーする (Azure クラシック ポータル)" 
    description="ここでは、Azure クラシック ポータルで Data Factory Editor を使用してチュートリアルを拡張して、パイプラインで出力データを SQL Server データベースにコピーするようにします。"
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/12/2015" 
    ms.author="spelluru"/>



# チュートリアル: オンプレミスの SQL Server データベースへのキャンペーンの有効性のデータのコピー

このチュートリアルでは、パイプラインがオンプレミスのデータを扱えるようにするため、その環境を設定する方法を学びます。

パーティション -> 強化 -> 分析のワークフローを含む最初のチュートリアルにおいて、ログ処理のシナリオの最後の手順で、マーケティング キャンペーンの有効性のアウトプットが Azure SQL データベースにコピーされました。 分析のため、所属する組織内にあるオンプレミスの SQL Server にこのデータを移動することも可能です。

マーケティング キャンペーンの有効性データを Azure BLOB からオンプレミスの SQL Server にコピーするため、最初のチュートリアルで導入したものと同じコマンドレット一式を使って、オンプレミスのリンクされたサービス、テーブル、およびパイプラインを追加で作成する必要があります。

## 前提条件

**必要があります** でチュートリアルを実行、 [チュートリアル: 移動し、Data Factory ][datafactorytutorial] この記事のチュートリアルを実行する前にします。

**(推奨)** 確認と練習では、チュートリアル、 [パイプライン ][useonpremisesdatasources] からデータを移動するパイプラインの作成に関するチュートリアルについての記事内部設置型 SQL Server は Azure blob ストアにします。


このチュートリアルでは、次の手順に従います。

1. [手順 1: data management gateway を作成する](#OnPremStep1)します。 Data Management Gateway は、クラウドから、組織内の内部設置型データ ソースへのアクセスを提供するためのクライアント エージェントです。 このゲートウェイによって、オンプレミスの SQL Server と Azure データ ストアの間でデータ転送が可能になります。

    オンプレミスの SQL Server データベースをリンクされたサービスとして Azure データ ファクトリに追加する前に、企業環境内に少なくとも 1 つのゲートウェイがインストールされており、それが Azure Data Factory に登録されている必要があります。

2. [手順 2: 内部設置型 SQL Server のリンクされたサービスを作成する](#OnPremStep2)します。 この手順では、まずオンプレミスの SQL Server コンピューター上にデータベースとテーブルを作成し、その後リンクされたサービスの **OnPremSqlLinkedService** を作成します。
3. [手順 3: テーブルとパイプラインの作成](#OnPremStep3)します。 この手順では、**MarketingCampaignEffectivenessOnPremSQLTable** テーブルと **EgressDataToOnPremPipeline** パイプラインを作成します。

4. [手順 4: パイプラインを監視して結果を確認](#OnPremStep4)します。 この手順では、Azure ポータルを使用して、パイプライン、テーブル、およびデータ スライスを監視します。


## <a name="OnPremStep1"></a> 手順 1: Data Management Gateway を作成します。

Data Management Gateway は、所属する組織内のオンプレミスのデータ ソースに、クラウドからのアクセスを提供するクライアント エージェントです。 このゲートウェイによって、オンプレミスの SQL Server と Azure データ ストアの間でデータ転送が可能になります。

オンプレミスの SQL Server データベースをリンクされたサービスとして Azure データ ファクトリに追加する前に、企業環境内に少なくとも 1 つのゲートウェイがインストールされており、それが Azure Data Factory に登録されている必要があります。

すでに使用できるデータ ゲートウェイがある場合は、この手順をスキップします。

1.  論理データ ゲートウェイを作成します。 **Azure ポータル**で、Data Factory の **[Data Factory]** ブレードの **[関連付けられているサービス]** をクリックします。
2.  コマンド バーの **[+ データ ゲートウェイ]** をクリックします。
3.  **[新しいデータ ゲートウェイ]** ブレードで、**[作成]** をクリックします。
4.  **[作成]** ブレードで、データ ゲートウェイの **[名前]** に「**MyGateway**」と入力します。
5.  **[地域を選択]** をクリックし、必要があれば変更します。
6.  **[作成]** ブレードで **[OK]** をクリックします。
7.  **[構成]** ブレードが表示されます。
8.  **[構成]** ブレードで、**[このコンピューターに直接インストール]** をクリックします。 これによりゲートウェイのダウンロード、インストール、およびコンピューター上での構成が行われ、サービスに登録されます。 すでにコンピューターにインストール済みのゲートウェイがあり、それをポータル上に作成したこの論理ゲートウェイにリンクさせたい場合、このブレード上のキーを使用して Data Management Gateway Manager (プレビュー) ツールでゲートウェイの再登録をします。

    ![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. **[OK]** をクリックして **[構成]** ブレードを閉じ、**[OK]** をクリックして **[作成]** ブレードを閉じます。 **[関連付けられているサービス]** ブレードの **MyGateway** の状態が **[良好]** に変わるまで待ちます。 また、**Data Management Gateway Configuration Manager (プレビュー)** ツールを起動し、ゲートウェイの名前がポータル上の名前と一致すること、および**状態**が **[登録済み]** であることを確認することもできます。 最新の状態を確認するため、場合によっては [リンクされたサービス] ブレードをいったん閉じて再度開く必要があります。 画面が最新の状態に更新されるまで、数分かかる場合があります。

## <a name="OnPremStep2"></a> 手順 2: 内部設置型 SQL Server 用のリンクされたサービスを作成します。

この手順では、まずオンプレミスの SQL Server コンピューター上にデータベースとテーブルを作成し、その後リンクされたサービスを作成します。

### オンプレミスのデータベースとテーブルを準備する

最初に、SQL Server データベース、テーブル、ユーザー定義型、およびストアド プロシージャを作成する必要があります。 これらは **MarketingCampaignEffectiveness** の結果を Azure BLOB から SQL Server データベースに移動するために使用されます。

1.  **エクスプローラー**で、**C:\ADFWalkthrough** (またはサンプルを展開した場所) にある **OnPremises** サブフォルダーに移動します。
2.  お好みのエディターで **prepareOnPremDatabase&Table.ps1** を開き、強調表示部分を自分の SQL Server 情報に置き換え、ファイルを保存します (**SQL 認証**の詳細を入力してください)。このチュートリアルのために、データベースの SQL 認証を有効にします。

        $dbServerName = "<servername>"
        $dbUserName = "<username>"
        $dbPassword = "<password>"

3. **Azure PowerShell** で、**C:\ADFWalkthrough\OnPremises** フォルダーに移動します。
4.  **prepareOnPremDatabase&Table.ps1** を実行します **(& を二重引用符で囲むか、以下のようにします)**。

        & '.\prepareOnPremDatabase&Table.ps1'

5. スクリプトが正常に実行されると、以下が表示されます。

        PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
        6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
        6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
        6/10/2014 10:12:33 PM Connecting as user [sa]
        6/10/2014 10:12:33 PM Summary:
        6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
        6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 



### リンクされたサービスの作成

1.  **Azure ポータル**で、**LogProcessingFactory** の **[Data Factory]** ブレードにある **[作成者とデプロイ]** タイルをクリックします。
2.  **Data Factory エディター**で、ツール バーの **[新しいデータ ストア]** クリックして、**[オンプレミスの SQL Server データベース]** を選択します。
3.  JSON スクリプトで、次の手順に従います。
    1.  置換 * *<servername>* * SQL Server データベースをホストするサーバーの名前に置き換えます。
    2.  置換 * *<databasename>* * と **MarketingCampaigns**します。
    3.  **SQL 認証**を使用している場合は、次の手順に従います。
        1.  指定 * *<username>** と **<password>* * で、 **connectionString**します。
        2.  最後の 2 行を削除します (JSON の **username** プロパティと **password** プロパティが必要なのは、Windows 認証を使用している場合のみです)。
        3.  削除 **, 、(コンマ) * * の末尾に * * gatewayName** 行です。

        **Windows 認証を使用する: 場合**
        1. **connectionString** の**統合セキュリティ**の値を **True** に設定します。削除する"* * ユーザー ID =<username>です。パスワード =<password>; * *"connectionString からです。
        2. **username** プロパティに、データベースへのアクセス権を持つユーザーの名前を指定します。
        3. ユーザー アカウントの **password** を指定します。
    4. gatewayName プロパティに、ゲートウェイの名前 (**MyGateway**) を指定します。
3.  ツール バーの **[デプロイ]** をクリックして、リンクされたサービスをデプロイします。

## <a name="OnPremStep3"></a> 手順 3: テーブルとパイプラインを作成します。

### オンプレミスの論理テーブルを作成する

1.  **Data Factory エディター**で、ツール バーの **[新しいデータ セット]** をクリックして、**[オンプレミスの SQL]** を選択します。
2. 右側のウィンドウにある JSON を、**C:\ADFWalkthrough\OnPremises** フォルダーにある **MarketingCampaignEffectivenessOnPremSQLTable.json** の JSON スクリプトに置き換えます。
3. リンクされたサービスの名前を変更 (**linkedServiceName** プロパティ) から **OnPremSqlServerLinkedService** に  **SqlServerLinkedService**します。
4. ツール バーの **[デプロイ]** をクリックして、テーブルをデプロイします。

#### パイプラインを作成して Azure BLOB から SQL Server へデータをコピーする

1.  1. **Data Factory エディター**で、ツール バーの **[新しいパイプライン]** をクリックします。 Click **...(省略記号)** ツールバー ボタンが表示されない場合。 または、ツリー ビューの **[パイプライン]** を右クリックして、**[新しいパイプライン]** をクリックする方法もあります。
2. 右側のウィンドウにある JSON を、**C:\ADFWalkthrough\OnPremises** フォルダーにある **EgressDataToOnPremPipeline.json** の JSON スクリプトに置き換えます。
3. JSON の**閉じ角かっこ (']')** の最後に**コンマ (',')** を追加してから、その閉じ角かっこの後に次の 3 行を追加します。

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

    このチュートリアルのサンプル データが 2014 年 5 月 1 日から 2014 年 5 月 5 日であるため、**開始**時刻と**終了**時刻が 2014 年 5 月 1 日および 2014 年 5 月 5 日に設定されていることに注意してください。

3. ツール バーの **[デプロイ]** クリックして、パイプラインを作成してデプロイします。 エディターのタイトル バーに "**パイプラインが正常に作成されました**" というメッセージが表示されていることを確認します。

## <a name="OnPremStep4"></a> 手順 4: パイプラインを監視して結果を確認

導入された同じ手順を使用できます、 **パイプラインとデータ スライスを監視する** のセクションで、 [メインのチュートリアル ][datafactorytutorial] 新しいパイプラインと、新しい内部設置型の ADF テーブル用のデータ スライスを監視します。

**MarketingCampaignEffectivenessOnPremSQLTable** テーブルのスライスの状態が [準備完了] に変われば、パイプラインがスライスの実行を完了したことを意味します。 結果を表示するには、SQL Server 内の **MarketingCampaigns** データベースの **MarketingCampaignEffectiveness** テーブルにクエリを実行します。

ご利用ありがとうございます。 オンプレミスのデータ ソースを使用するためのチュートリアルを無事に終了しました。



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md 
[use-custom-activities]: data-factory-use-custom-activities.md 
[troubleshoot]: data-factory-troubleshoot.md 
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456 
[datafactorytutorial]: data-factory-tutorial.md 
[adfgetstarted]: data-factory-get-started.md 
[adfintroduction]: data-factory-introduction.md 
[useonpremisesdatasources]: data-factory-move-data-between-onprem-and-cloud.md 
[azure-portal]: http://portal.azure.com 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/ 
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/ 
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580 
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx 
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell 
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495 
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908 
[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises/DataManagementGatewayConfigurationManager.png 

