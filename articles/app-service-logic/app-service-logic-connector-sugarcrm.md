<properties
   pageTitle="Logic Apps での SugarCRM コネクタの使用 | Microsoft Azure App Service"
   description="SugarCRM コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
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
   ms.author="sameerch"/>



# SugarCRM コネクタの使用開始とロジック アプリへの追加

SugarCRM コネクタでは、取引先企業や潜在顧客、連絡先などのさまざまなエンティティを作成および変更できます。 SugarCRM に関連する一般的な統合シナリオを次に示します。

- SugarCRM と ERP システム (SAP など) 間のアカウントの同期
- Marketo と SugarCRM 間のアカウント、連絡先、潜在顧客の同期
- SugarCRM から ERP システムへの受注から入金までのフロー

コネクタ パッケージの設定の一部として、コネクタで管理できるエンティティを指定できます。アクション、入力パラメーター、および出力パラメーターは、動的に設定されます。

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 この SugarCRM コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。



## SugarCRM コネクタのアクション

次に、SugarCRM コネクタで使用できるさまざまなアクションを示します。

- モジュールの作成 - このアクションを使用して、アカウント、潜在顧客、連絡先などの SugarCRM モジュールの新しいレコードを作成します。

- モジュールの更新 - このアクションを使用して、SugarCRM モジュールの既存のレコードを更新します。

- モジュールの削除 - このアクションを使用して、SugarCRM モジュールの既存のレコードを削除します。

- モジュールの一覧 - このアクションを使用して、フィルター処理されたレコードを一覧します。 クエリが指定されていない場合は、すべてのレコードが返されます。

- モジュールの取得 - このアクションを使用して、指定したモジュールから単一レコードを取得します。

- レコード数の取得 - このアクションを使用して、クエリに一致するモジュール内のレコード数を取得します。 クエリが指定されていない場合は、モジュール内のレコードの合計数が返されます。

- 重複するモジュールの確認 - このアクションを使用して、モジュール内で重複するレコードを確認します。

*注*: クエリでサポートされている引数の詳細についてを参照してください [SugarCRM REST API](https://msdn.microsoft.com/library/dn705870) ドキュメントです。

## SugarCRM コネクタの API App を作成する

1.  portal.azure.com に移動します。 Azure ポータルの左上隅にある [+ 新規] を使用して Azure Marketplace を開きます。
2.  [Marketplace]、[すべて] の順に移動し、"SugarCRM" を検索します。
3.  リソース グループに App Service プランの詳細を指定し、API アプリの名前を入力して、SugarCRM コネクタを構成します。
4. SugarCRM コネクタのパッケージ設定を構成します。 次に、コネクタを作成するために入力する必要があるパッケージ設定を示します。

    名前| 必須| 説明
--- | --- | ---
 サイトの URL| あり| SugarCRM インスタンスの URL を入力します。たとえば、https://abcde1234.sugarcrm.com を入力します。
 クライアント ID| あり| SugarCRM で OAUTH 2.0 キーのコンシューマー キーを入力します。
 クライアント シークレット| あり| OAUTH のコンシューマー シークレットを入力します。
 ユーザー名| あり| SugarCRM ユーザーのユーザー名を入力します。
 パスワード| あり| SugarCRM ユーザーのパスワードを入力します。
 モジュール名| はい| 操作を実行する SugarCRM モジュール (アカウント、連絡先、製品) などの入力<br><br>例: アカウント、潜在顧客、連絡先


![][9]



## ロジック アプリの作成

SugarCRM にアカウントを作成し、このアカウントの請求先住所の詳細を更新する単純なロジック アプリを作成してみましょう。

1.  Azure ポータルの [新規]、[Web + モバイル]、[ロジック アプリ] をクリックしてログインします。  
![][1]

2.  ロジック アプリの作成] ページで、名前、app service プラン、場所など、必要な情報を提供します。  
![][2]

3.  [トリガーとアクション] をクリックすると、Logic App エディターの画面が表示されます。 [このロジックを手動で実行] を選択します。これは、このロジック アプリを手動でのみ起動できることを意味します。

4.  ギャラリーで [このリソース グループの API Apps] を展開し、使用可能なすべての API Apps を表示します。 ギャラリーから [SugarCRM] を選択し、[SugarCRM コネクタ] が、フローに追加されます。  
![][3]

5.  アカウントの作成] アクションを選択、入力パラメーターが表示されます。  
![][4]

6.  Microsoft Account というとして名を指定し、[✓] をクリックします。  
![][5]

7.  ギャラリーの [最近使用した項目] セクションから [SugarCRM コネクタ] を選択すると、新しい SugarCRM アクションが追加されます。

8.  アクション アカウントの更新の操作が表示されますの入力パラメーターの一覧からアカウントの更新 (これは [高度な操作 '…' されます) を選択します。  
![][6]

9.  アカウントの作成] アクションの出力から"id"値を選択する [レコード Id] の横にある [...] をクリックします。  
![][7]

10. 課金アドレス情報の値を指定し、[✓] をクリックします。  
![][8]

11. ロジック アプリ エディターの画面で [OK] をクリックし、[作成] をクリックします。 作成が完了するまで約 30 秒かかります。

12. 新しく作成されたロジック アプリを参照し、[今すぐ実行] をクリックして実行します。

13. SugarCRM アカウントに Microsoft Account という名前の新しいアカウントが作成され、さらに、このアカウントの請求先住所の情報が更新されたことを確認できます。

## コネクタでできること

コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。 を参照してください [Logic Apps とは?](app-service-logic-what-are-logic-apps.md).
>[AZURE.NOTE] Azure Logic Apps と Azure アカウントにサインアップする前に開始する場合に、 [ロジック アプリを実行してください](https://tryappservice.azure.com/?appservice=logic), 、App Service で有効期間の短いスターター ロジック アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

表示、Swagger の REST API リファレンス [コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)します。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。 参照してください [の管理し、監視、組み込みの API Apps とコネクタ](app-service-logic-monitor-your-connectors.md)します。



[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png 
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png 
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png 
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png 
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png 
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png 
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png 
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png 
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png 

