<properties
   pageTitle="Logic Apps での Twitter コネクタの使用 | Microsoft Azure App Service"
   description="Twitter コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
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



# Twitter コネクタの使用開始とロジック アプリへの追加

Twitter フィードに接続して、ツイートを投稿し、自分のタイムライン、友人のタイムライン、Twitter アカウントのフォロワーからツイートを取得します。 コネクタを Logic Apps で使用して、"ワークフロー" の一部としてデータを取得、処理、またはプッシュできます。 Twitter コネクタをワークフローで使用すると、さまざまなシナリオを実現できます。 たとえば、次のようなことができます。

- 指定したキーワードまたはテキストに関連付けられている新しいツイートを取得します。 新しいツイートを取得すると、ワークフローの新しいインスタンスがトリガーされ、ワークフローの次のコネクタにデータが渡されます。 たとえば、Twitter コネクタを作成し、#peanutbutterandjelly を監視する New Tweet From Search トリガーを使用します。 #peanutbutterandjelly で新しいツイートがある場合は、必ずワークフロー (ロジック アプリともいいます) が自動的に開始されます。
- "Search Tweets" など、さまざまなアクションを使用して、応答を取得し、ワークフロー内で使用します。 たとえば、会社名でツイートを検索できます。 見つかった場合は、ロジック アプリを使用して、SQL Server データベースにこのデータを書き込むことができます。 次に、SQL Server のデータを使用して、会社のどのような話題がツイートされているのかを特定します。
- あるすべての演算子を使用して [Twitter 検索](https://twitter.com/search)します。 **演算子**リンクを選択します。 Twitter コネクタは、表示されているすべての演算子をサポートします。


## トリガーとアクション

*トリガー*とは、発生するイベントを指します。 たとえば、新しいツイートは、ワークフローまたはプロセスをトリガー、つまり開始できます。 *アクション*とは、何かの操作の結果です。 たとえば、特定のツイートを検索して見つかったときに、電子メールを送信したり、データベースを更新したりします。

Twitter コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。

Twitter コネクタでは、次のトリガーとアクションを使用できます。

 トリガー| アクション
--- | ---
 New Tweet From Search| <ul><li>ユーザーのタイムラインの取得</li><li>ツイートの検索</li><li>ツイート</li><li>Get メンション タイムライン</li><li>帰宅タイムライン</li><li>フォロワーの取得</li><li>友人を取得</li><li>ユーザー詳細の取得</li><li>ユーザーにツイート</li><li>直接メッセージを送信</li></ul>

**New Tweet** トリガーはアーカイブされました。 現時点では、高度な操作として引き続き使用できます。 **Retweet** アクションが削除され、サポートされなくなりました。 Retweet アクションを使用すると、実行時に失敗します。 よって、ロジック アプリから、Retweet アクションを削除してください。


## Twitter コネクタの作成

> [AZURE.IMPORTANT] Twitter コネクタの作成時に自分のアプリを Twitter に登録し、Twitter コネクタでアプリ キーを使用することもできます。 アプリケーションを無料で登録 [http://apps.twitter.com](http://apps.twitter.com)します。 登録するときに、必ずコールバック URL を指定してください。 Twitter コネクタの作成後に、コールバック URL を変更することもできます。 コネクタを作成するには、Twitter API キーとシークレットが必要です。

コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。 Marketplace からコネクタを作成するには、次の操作を実行します。

1. [省略可能]Twitter での無料アプリケーションを作成 [http://apps.twitter.com](http://apps.twitter.com)します。
    * アプリを登録するときに、Web サイトの任意の URL を指定できます。 任意のコールバック URL を指定します (空白のままにしないでください)。後で更新できます。
2. Azure のスタート画面で、**[Marketplace]** を選択します。
3. "Twitter コネクタ" を検索して選択し、**[作成]** を選択します。
4. [オプション] [パッケージの設定] をクリックし、Twitter アプリから [clientId] フィールドに "コンシューマー キー" を貼り付けます。 コンシューマー シークレットを Twitter アプリから 'clientSecret' フィールドに貼り付けます。  
![][10]
5. コネクタ名、App Service、およびリソース グループなど、その他の必要な設定を入力します。
6.  **[作成]** をクリックします。

> [AZURE.NOTE] さらに、リダイレクト URL を使用して、Twitter API をセキュリティで保護する場合は、そのを実行する方法の詳細を表示できます [ここ](app-service-logic-oauth-security.md)


## ロジック アプリで Twitter Connector を使用する

API アプリを作成した後は、Logic Apps のトリガーまたはアクションとして Twitter コネクタを使用できます。 これを行うには、次の手順を実行します。

1.  新しいロジック アプリを作成するか、既存のロジック アプリを開きます。  
![][2]
2.  **[トリガーとアクション]** を開いて、Logic Apps デザイナーを開きます。  
![][3]
3.  Twitter コネクタは、右側に一覧表示されます。 コネクタを選択すると、自動的にロジック アプリに追加されます。  
![][4]
4.  **[承認]** を選択し、Twitter の資格情報を入力します。**[アプリの承認]** を選択します。  
![][5]


これで、Twitter コネクタを構成して、ワークフローを構築できます。 Twitter トリガーを使って取得したツイートを、フローの他のアクションで使用できます。  
![][6]

ワークフローで Twitter アクションを使用する方法もほぼ同じです。 Twitter アクションを選択して、対象のアクションの入力を構成してください。  
![][7]  
![][8]

## コネクタでできること

コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。 を参照してください [Logic Apps とは?](app-service-logic-what-are-logic-apps.md).
>[AZURE.NOTE] Azure Logic Apps と Azure アカウントにサインアップする前に開始する場合に、 [ロジック アプリを実行してください](https://tryappservice.azure.com/?appservice=logic), 、App Service で有効期間の短いスターター ロジック アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

表示、Swagger の REST API リファレンス [コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)します。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。 参照してください [の管理し、監視、組み込みの API Apps とコネクタ](app-service-logic-monitor-your-connectors.md)します。



[1]: ./media/app-service-logic-connector-twitter/img1.png 
[2]: ./media/app-service-logic-connector-twitter/img2.png 
[3]: ./media/app-service-logic-connector-twitter/img3.png 
[4]: ./media/app-service-logic-connector-twitter/img4.png 
[5]: ./media/app-service-logic-connector-twitter/img5.png 
[6]: ./media/app-service-logic-connector-twitter/triggers.png 
[7]: ./media/app-service-logic-connector-twitter/img7.png 
[8]: ./media/app-service-logic-connector-twitter/actions.png 
[9]: ./media/app-service-logic-connector-twitter/settings.PNG 
[10]: ./media/app-service-logic-connector-twitter/TwitterAPISettings.png 

