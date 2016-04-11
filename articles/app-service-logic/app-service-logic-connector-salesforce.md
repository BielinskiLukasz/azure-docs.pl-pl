<properties
   pageTitle="Logic Apps での Salesforce コネクタの使用 | Microsoft Azure App Service"
   description="Salesforce コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
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


# Salesforce コネクタの使用開始とロジック アプリへの追加
Salesforce に接続して、取引先企業、潜在顧客などのエンティティを作成、変更します。 Salesforce に関連する一般的な統合シナリオを次に示します。

- Salesforce と ERP システム (SAP、QuickBooks など) との間のアカウントの同期
- Salesforce から ERP システムへの受注から入金までのフロー

コネクタ パッケージの設定の一部として、ユーザーは、コネクタで管理できるエンティティを指定できます。アクション、入力パラメーター、および出力パラメーターは、動的に設定されます。 Salesforce コネクタで使用できるさまざまなアクションを次に示します。

- [エンティティの作成] - アカウント、事例、カスタム オブジェクトなどの新しい Salesforce エンティティを作成するには、この操作を使用します。
- [エンティティの更新] - Salesforce の既存のエンティティを更新するには、このアクションを使用します。
- [エンティティの Upsert] - 既存の Salesforce エンティティを更新するか、または既存の Salesforce エンティティが存在しない場合にエンティティを作成するには、このアクションを使用します。
- [エンティティの削除] - 既存の Salesforce エンティティを削除するには、このアクションを使用します。
- [クエリの実行] - Salesforce オブジェクト クエリ言語 (SOQL) で記述された SELECT クエリを実行するには、このアクションを使用します。

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 この Salesforce コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。 


## SalesForce コネクタの API アプリの作成
1.  Azure ポータルの右下にある [+ 新規] オプションを使用して Azure Marketplace を開きます。
2.  [Web + モバイル]、[API Apps] の順に移動し、"SalesForce" を検索します。
3.  リソース グループにホスティング プランの詳細を提供する、API アプリの名前をクリックして Salesforce コネクタを構成します。  
![][15]
4. [パッケージ設定] で、読み取りと書き込みを行う SalesForce エンティティを構成します。

これで、SalesForce コネクタの API アプリを作成できます。


## ロジック アプリの作成
Salesforce にアカウントを作成し、このアカウントの請求先住所の詳細を更新する単純なロジック アプリを作成してみましょう。

1.  Azure ポータルの [新規]、[Web + モバイル]、[ロジック アプリ] をクリックしてログインします。  
![][1]

2.  ロジック アプリの作成] ページで、名前、app service プラン、場所など、必要な情報を提供します。  
![][2]

3.  [トリガーとアクション] をクリックします。 ロジック アプリ エディターを開きます。  
![][3]

4.  選択 'このロジックを手動で実行する' このロジック アプリを手動でのみ起動できることを意味します。  
![][4]

5.  ギャラリーで [このリソース グループの API Apps] を展開し、使用可能なすべての API Apps を表示します。 ギャラリーから Salesforce] を選択し、Salesforce コネクタがフローに追加されます。  
![][5]

8.  Salesforce アカウントにアクセスするロジック アプリを承認するには、Salesforce のログイン資格情報を提供するには、[承認] をクリックします。  
![][6]

9.  Salesforce のログイン ページにリダイレクトされ、Salesforce の資格情報を使って認証することができます。  
![][7]  
![][8]

10. 承認が完了する、すべてのアクションが表示されます。  
![][9]

11. アカウントの作成] アクションを選択、入力パラメーターが表示されます。  
![][10]

12. アカウント名を入力し、[✓] をクリックします。  
![][11]

13. ギャラリーの [最近使用した項目] セクションで [Salesforce コネクタ] を選択します。新しい Salesforce アクションが追加されます。

14. アクション アカウントの更新の操作が表示されますの入力パラメーターの一覧からアカウントの更新を選択します。  
![][12]

15. アカウントの作成] アクションの出力の id の値を取得する [レコード Id] の横にある ['+' をクリックします。  
![][13]

16. 課金番地請求先の市区町村、課金および郵便番号の値を指定し、[✓] をクリックします。  
![][14]

17. ロジック アプリ エディターの画面で [OK] をクリックし、[作成] をクリックします。 作成が完了するまで約 30 秒かかります。

18. 新しく作成されたロジック アプリを参照し、[実行] をクリックして実行します。

19. Salesforce アカウントに "Contoso" という名前の新しいアカウントが作成されたことを確認します。

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。 参照してください [Logic Apps とは?](app-service-logic-what-are-logic-apps.md)します。

>[AZURE.NOTE] Azure Logic Apps と Azure アカウントにサインアップする前に開始する場合に、 [ロジック アプリを実行してください](https://tryappservice.azure.com/?appservice=logic), 、App Service で有効期間の短いスターター ロジック アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

表示、Swagger の REST API リファレンス [コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)します。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。 参照してください [の管理し、監視、組み込みの API Apps とコネクタ](app-service-logic-monitor-your-connectors.md)します。

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png

