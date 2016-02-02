<properties 
    pageTitle="出力データをオンプレミスの SQL Server データベースにコピーする (Azure PowerShell)" 
    description="ここでは、Azure PowerShell を使用してチュートリアルを拡張して、パイプラインで出力データを SQL Server データベースにコピーするようにします。"
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
    ms.date="10/29/2015" 
    ms.author="spelluru"/>



# チュートリアル: オンプレミスの SQL Server データベースへのキャンペーンの有効性のデータのコピー

このチュートリアルでは、パイプラインがオンプレミスのデータを扱えるようにするため、その環境を設定する方法を学びます。

パーティション -> 強化 -> 分析のワークフローを含む最初のチュートリアルにおいて、ログ処理のシナリオの最後の手順で、マーケティング キャンペーンの有効性のアウトプットが Azure SQL データベースにコピーされました。 分析のため、所属する組織内にあるオンプレミスの SQL Server にこのデータを移動することも可能です。

マーケティング キャンペーンの有効性データを Azure BLOB からオンプレミスの SQL Server にコピーするため、最初のチュートリアルで導入したものと同じコマンドレット一式を使って、オンプレミスのリンクされたサービス、テーブル、およびパイプラインを追加で作成する必要があります。

**必要があります** でチュートリアルを実行、 [チュートリアル: 移動し、Data Factory ][datafactorytutorial] この記事のチュートリアルを実行する前にします。

**(推奨)** 確認と練習では、チュートリアル、 [パイプライン ][useonpremisesdatasources] からデータを移動するパイプラインの作成に関するチュートリアルについての記事内部設置型 SQL Server は Azure blob ストアにします。


このチュートリアルでは、次の手順に従います。

1. [手順 1: data management gateway を作成する](#OnPremStep1)します。 Data Management Gateway は、クラウドから、組織内の内部設置型データ ソースへのアクセスを提供するためのクライアント エージェントです。 このゲートウェイによって、オンプレミスの SQL Server と Azure データ ストアの間でデータ転送が可能になります。

    オンプレミスの SQL Server データベースをリンクされたサービスとして Azure データ ファクトリに追加する前に、企業環境内に少なくとも 1 つのゲートウェイがインストールされており、それが Azure Data Factory に登録されている必要があります。

2. [手順 2: 内部設置型 SQL Server のリンクされたサービスを作成する](#OnPremStep2)します。 この手順では、まずオンプレミスの SQL Server コンピューター上にデータベースとテーブルを作成し、その後リンクされたサービスの **OnPremSqlLinkedService** を作成します。
3. [手順 3: テーブルとパイプラインの作成](#OnPremStep3)します。 この手順では、**MarketingCampaignEffectivenessOnPremSQLTable** テーブルと **EgressDataToOnPremPipeline** パイプラインを作成します。

4. [手順 4: パイプラインを監視して結果を確認](#OnPremStep4)します。 この手順では、Azure クラシック ポータルを使用して、パイプライン、テーブル、データ スライスを監視します。


## <a name="OnPremStep1"></a> 手順 1: Data Management Gateway を作成します。

Data Management Gateway は、所属する組織内のオンプレミスのデータ ソースに、クラウドからのアクセスを提供するクライアント エージェントです。 このゲートウェイによって、オンプレミスの SQL Server と Azure データ ストアの間でデータ転送が可能になります。

オンプレミスの SQL Server データベースをリンクされたサービスとして Azure データ ファクトリに追加する前に、企業環境内に少なくとも 1 つのゲートウェイがインストールされており、それが Azure Data Factory に登録されている必要があります。

すでに使用できるデータ ゲートウェイがある場合は、この手順をスキップします。

1.  論理データ ゲートウェイを作成します。 **Azure ポータル**で、**[Data Factory]** ブレードの **[リンクされたサービス]** をクリックします。
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

1.  **Azure ポータル**で、**[LogProcessingFactory]** の **[Data Factory]** ブレードにある **[リンクされたサービス]** タイルをクリックします。
2.  **[リンクされたサービス]** ブレードで、**[+ データ ストア]** をクリックします。
3.  **[新しいデータ ストア]** ブレードで、**[名前]** に「**OnPremSqlLinkedService**」と入力します。
4.  **[種類 (設定が必要)]** をクリックし、**[SQL Server]** を選択します。 これで、**[新しいデータ ストア]** ブレードに、**[データ ゲートウェイ]**、**[サーバー]**、**[データベース]**、**[資格情報]** の設定が表示されます。
5.  **[データ ゲートウェイ (必要な設定を構成する)]** をクリックし、以前に作成した **[MyGateway]** を選択します。
6.  **MarketingCampaigns** データベースをホストするデータベース サーバーの**名前**を入力します。
7.  データベースには「**MarketingCampaigns**」と入力します。
8.  **[資格情報]** をクリックします。
9.  **[資格情報]** ブレードで、**[ここをクリックして安全に資格情報を設定する]** をクリックします。
10. 初めてクリック 1 回でアプリケーションをインストールして起動して、 ** 資格情報の設定 **] ダイアログ ボックス。
11. **[資格情報の設定]** ダイアログ ボックスで、**[ユーザー名]** と **[パスワード]** を入力し、**[OK]** をクリックします。 ダイアログ ボックスが閉じるまで待ちます。
12. **[新しいデータ ストア]** ブレードで、**[OK]** をクリックします。
13. **[リンクされたサービス]** ブレードで、**OnPremSqlLinkedService** が一覧に表示され、リンク サービスの **[状態]** が **[良好]** であることを確認します。

## <a name="OnPremStep3"></a> 手順 3: テーブルとパイプラインを作成します。

### オンプレミスの論理テーブルを作成する

1.  **Azure PowerShell** で、**C:\ADFWalkthrough\OnPremises** フォルダーに移動します。
2.  **New-AzureRmDataFactoryDataset** コマンドレットを使用して、次のように **MarketingCampaignEffectivenessOnPremSQLTable.json** のテーブルを作成します。

        New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json


#### パイプラインを作成して Azure BLOB から SQL Server へデータをコピーする

1.  **New-AzureRmDataFactoryPipeline** コマンドレットを使用して、次のように **EgressDataToOnPremPipeline.json** のパイプラインを作成します。

        New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json

2. **Set-AzureRmDataFactoryPipelineActivePeriod** コマンドレットを使用して、**EgressDataToOnPremPipeline** の有効期間を指定します。

        Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

    **Y** キーを押して続行します。

## <a name="OnPremStep4"></a> 手順 4: パイプラインを監視して結果を確認

導入された同じ手順を使って、 [手順 6: テーブルとパイプラインを監視](#MainStep6)  新しいパイプラインと、新しい内部設置型の ADF テーブル用のデータ スライスを監視します。

**MarketingCampaignEffectivenessOnPremSQLTable** テーブルのスライスの状態が [準備完了] に変われば、パイプラインがスライスの実行を完了したことを意味します。 結果を表示するには、SQL Server 内の **MarketingCampaigns** データベースの **MarketingCampaignEffectiveness** テーブルにクエリを実行します。

ご利用ありがとうございます。 オンプレミスのデータ ソースを使用するためのチュートリアルを無事に終了しました。



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md 
[use-custom-activities]: data-factory-use-custom-activities.md 
[troubleshoot]: data-factory-troubleshoot.md 
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456 
[datafactorytutorial]: data-factory-tutorial-using-powershell.md 
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
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx 
[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png 

