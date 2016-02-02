<properties 
   pageTitle="Logic Apps での Azure Storage Blob コネクタの使用 | Microsoft Azure App Service" 
   description="Azure Storage Blob コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="11/30/2015"
   ms.author="rajram"/>


# Azure Storage Blob コネクタの使用開始とロジック アプリへの追加

BLOB をアップロード、ダウンロード、BLOB コンテナーから削除するには、Azure Storage BLOB に接続します。 コネクタは、"ワークフロー" の一部として Logic Apps で使用されます。

## トリガーとアクション

*トリガー*とは、発生するイベントを指します。 たとえば、注文が更新された、新しい顧客が追加された、といったイベントがあります。 *アクション*は、トリガーの結果です。 たとえば、注文が更新されたときに、営業担当者にアラートを送信します。 または、新しい顧客が追加されたときに、ウェルカム メールを新しい顧客に送信します。

Storage BLOB コネクタは、ロジック アプリでアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。 現時点では、Storage BLOB コネクタにはトリガーはありません。

Storage BLOB コネクタでは、次のトリガーとアクションを使用できます。

 トリガー| アクション
--- | ---
 なし| <ul><li>Get Blob: コンテナーから特定の Blob を取得する</li><li>Blob のアップロード: 新しい Blob をアップロードまたは既存の Blob を更新する</li><li>Blob の削除: コンテナーから特定の Blob を削除する</li><li>の Blob の一覧: ディレクトリ内のすべての blob を一覧表示</li><li>Snapshot Blob: 特定の Blob の読み取り専用スナップショットを作成</li><li>Copy Blob: 別の Blob をコピーして新しい Blob を作成します。ソース Blob は、同じアカウントで、または別のアカウントの場合もあります。</li></ul>


## Azure Storage BLOB コネクタの作成

コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。 Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "Blob"を検索します。  
    ![Azure Storage BLOB コネクタの選択][2]

3. 検索した BLOB を選択し、**[作成]** を選択します。
4. 名前、App Service プラン、その他のプロパティを入力します。
5. 次のパッケージの設定を入力します。

    名前| 必須| 説明
--- | --- | ---
 コンテナー/SAS URI| あり| BLOB コンテナーの URI を入力します。URI には SAS トークンを含めることもできます。たとえば、入力 http://*storageaccountname*.blob.core.windows.net/containername または http://*storageaccountname*.blob.core.windows.net/containername?sr=c & si mypolicy & sig = = signatureblah
 アクセス キー| いいえ| 有効なプライマリまたはセカンダリ ストレージ アカウントのアクセス キーを入力します。認証に SAS トークンを使用する場合は、このフィールドは空白のままにしてください。

    ![Azure Storage BLOB コネクタの作成][3]

6. **[作成]** をクリックします。

## ロジック アプリでの Azure Storage BLOB コネクタの使用

Azure Storage BLOB コネクタを作成すると、ワークフローに追加できます。

1. 新しいロジック アプリを作成するには、[新規]、[Web + モバイル]、[ロジック アプリ] の順に選択します。 ロジック アプリのプロパティを入力します。  
    ![ロジック アプリの作成][4]

2. **[トリガーとアクション]** をクリックします。 次のようなワークフロー デザイナーが開きます。  
    ![ロジック アプリの空のフロー デザイナー][5]

3. 右側のウィンドウから [Azure Storage BLOB コネクタ] を選択します。 コネクタには、実行可能なアクションが一覧表示されます。  
    ![Azure Storage BLOB アクションの一覧][10]

4. このシナリオでは、**Upload Blob** アクションを使用します。  
    ![BLOB のアップロード アクションの入力][11]

5. 入力値を入力し、チェック マークを選択して構成を完了します。

    入力| 説明
--- | ---
 BLOB パス| アップロードする BLOB のパスを指定します。パスは構成済みのコンテナーのパスを基準として解釈されます。
 BLOB の書き込みコンテンツ| アップロードする BLOB のコンテンツとプロパティを入力します。
 コンテンツ転送エンコード| [なし] または [Base64] を指定します。
 上書き| true に設定すると、既存の BLOB が上書きされます。false に設定すると、同じパスに BLOB が既に存在する場合にエラーが返されます。


構成済みの Azure Storage BLOB の BLOB のアップロードという BLOB アクションにより、入力パラメーターと出力パラメーターの両方が表示されます。

#### Azure Storage BLOB アクションへの入力として前のアクションの出力を使用する

前のスクリーン ショットで、**[コンテンツ]** の値は、式にすることができます。

    @triggers().outputs.body.Content

任意の値に設定できます。 式はロジック アプリのトリガーの出力を取得し、アップロードするファイルのコンテンツとして使用します。 たとえば、変換の出力を使用することができます。 このシナリオでは、次の式になります。

    @actions('transformservice').outputs.body.OutputXML

## コネクタでできること

コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。 を参照してください [Logic Apps とは?](app-service-logic-what-are-logic-apps.md).
>[AZURE.NOTE] Azure Logic Apps と Azure アカウントにサインアップする前に開始する場合に、 [ロジック アプリを実行してください](https://tryappservice.azure.com/?appservice=logic), 、App Service で有効期間の短いスターター ロジック アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

表示、Swagger の REST API リファレンス [コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)します。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。 参照してください [の管理し、監視、組み込みの API Apps とコネクタ](app-service-logic-monitor-your-connectors.md)します。



[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG 
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG 
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG 
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG 
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG 
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG 
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG 
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG 
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG 
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG 

