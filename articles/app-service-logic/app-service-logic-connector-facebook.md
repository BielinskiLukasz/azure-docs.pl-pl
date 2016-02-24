<properties
   pageTitle="Logic Apps での Facebook コネクタの使用 | Microsoft Azure App Service"
   description="Facebook コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="rajram"/>


# Facebook コネクタの使用開始とロジック アプリへの追加
Facebook アカウントに接続して、メッセージを投稿したり、写真を公開したりできます。 ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

Facebook コネクタでは、次のことを行えます。

- トリガーを使用して、"ユーザー タイムラインの新しい投稿" または "ページの新しい投稿" を取得します。 新しい投稿が取得されると、フローの新しいインスタンスをトリガーし、要求で受信したデータを処理のためにフローに渡します。
- "投稿の発行"、"写真の発行" などを実行できるアクションを使用します。 これらのアクションによって応答を取得すると、フローの後続のアクションで使用できます。

この Facebook コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。 

## トリガーとアクション

トリガー | アクション
--- | ---
<ul><li>ユーザーのタイムラインに新しい投稿</li><li>ページに新しい投稿</li></ul> | <ul><li>投稿を発行します。</li><li>写真を公開します。</li></ul>



## ロジック アプリ用の Facebook コネクタを作成する
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。 Marketplace からコネクタを作成するには、次の操作を実行します。  

1. Azure のスタート画面では、次のように選択します。 **Marketplace**します。
2. 「Facebook コネクタ」を検索して選択し、選択 **作成**します。
3. 名前、App Service プラン、その他のプロパティを入力します。  
    ![][1]
4.  選択 **作成**します。


## ロジック アプリでの Facebook コネクタの使用
API アプリを作成すると、ロジック アプリのトリガーやアクションとして Facebook コネクタを使用できます。 そのためには、次の手順を実行する必要があります。

1.  ロジック アプリで開く **トリガーとアクション** Logic Apps デザイナーを開き、フローを構成します。  
    ![][3]
2.  Facebook コネクタは、ギャラリーにリストされます。  
    ![][4]
3. Facebook コネクタを選択すると、自動的にデザイナーに追加されます。 選択 **Authorize**, 、資格情報を入力し、選択 **許可**:  
    ![][5]
    ![][6]
    ![][7]
    ![][8]
4.  トリガーを選択します。  
    ![][9]

これで、Facebook トリガーから取得した投稿を他のアクションで使用できるようになりました。 次のフロー、新しいメッセージがユーザーの Facebook のタイムラインに投稿されるたびに同じ投稿が tweeted ツイート ユーザーの Twitter のタイムライン。  
    ![][10]

同様に、Facebook コネクタのアクションを使用してフローを作成できます。 次のフロー、Yammer グループに投稿された新しいメッセージを取得し、ユーザーによって管理される Facebook ページに同じ投稿を発行します。  
    ![][11]

> [AZURE.TIP] Facebook ページの ID や Yammer グループ ID を取得するには、URL 内の数値コードを探します。

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。 参照してください [Logic Apps とは?](app-service-logic-what-are-logic-apps.md)します。

>[AZURE.NOTE] Azure Logic Apps と Azure アカウントにサインアップする前に開始する場合に、 [ロジック アプリを実行してください](https://tryappservice.azure.com/?appservice=logic), 、App Service で有効期間の短いスターター ロジック アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

表示、Swagger の REST API リファレンス [コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)します。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。 参照してください [の管理し、監視、組み込みの API Apps とコネクタ](app-service-logic-monitor-your-connectors.md)します。

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

