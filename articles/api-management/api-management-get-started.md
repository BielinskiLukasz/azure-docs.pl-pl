<properties
    pageTitle="Azure API Management での最初の API の管理 | Microsoft Azure"
    description="API の作成方法、操作の追加方法、API Management の基本操作について説明します。"
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/07/2015"
    ms.author="sdanie"/>


# Azure API Management での最初の API の管理

## <a name="overview"> </a>の概要

このガイドでは、Azure API Management の基本的な使用方法を簡単に説明し、実際に API を呼び出します。

## <a name="concepts"> </a>Azure API Management は何ですか。

Azure API Management を任意のバックエンドで実行し、それに基づいて本格的な API プログラムを起動できます。

一般的なシナリオは、次のとおりです。

* **モバイル インフラストラクチャのセキュリティを強化する**。API キーによるアクセス制御、スロットル処理による DOS 攻撃の防止、JWT トークン検証などの高度なセキュリティ ポリシーの使用によって、これを行います。
* **ISV パートナー エコシステムを可能にする**。開発者ポータルを通したパートナーとの迅速な協力の提供と、パートナーによる使用の準備が整っていない内部実装から切り離された API ファサードの構築によって、これを行います。
* **内部 API プログラムを実行する**。組織が可用性と API に対する最新の変更を伝達するための一元的な場所の提供と、組織アカウントに基づいたアクセスの制限によって、これを行います。そのすべてが、API ゲートウェイとバックエンドの間のセキュリティ保護されたチャネルに基づいて実行されます。


システムは、次のコンポーネントで構成されます。

* **API ゲートウェイ**。これは次の機能を持つエンドポイントです。
  * API 呼び出しを受け入れ、バックエンドにルーティングします。
  * API キー、JWT トークン、証明書、その他の資格情報を検証します。
  * 使用量クォータとレート制限を適用します
  * コードを変更せずにその場で API を変換します。
  * セットアップ時にバックエンドの応答をキャッシュします。
  * 分析目的で呼び出しメタデータを記録します。

* **パブリッシャー ポータル**は、API プログラムをセットアップする管理インターフェイスです。 このインターフェイスを使用して、次の操作を行います。
    * API スキーマを定義またはインポートします。
    * API を製品にパッケージします。
    * API のクォータや変換などのポリシーを設定します。
    * 分析から洞察を得ます。
    * ユーザーを管理します。

* **開発者ポータル**は、開発者用のメイン Web として機能し、次の操作を実行できます。
    * API のドキュメントを読みます。
    * 対話型コンソールを使用して API を試します。
    * API キーを取得するために、アカウントを作成してサブスクライブします。
    * 自分自身の使用に関する分析にアクセスします。


## <a name="create-service-instance"> </a>API Management インスタンスの作成

>[AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。 アカウントがない場合は、無料試用版アカウントを数分で作成することができます。 詳細については、「 [Azure 無料評価版の []][]します。

API Management を使用するにあたって最初に行うことは、サービス インスタンスの作成です。 サインイン、 [Azure 旧ポータルの][] ] をクリック **新規**, 、**App Services**, 、**API Management**, 、**作成**します。

![API Management の新しいインスタンス][api-management-create-instance-menu]

**[URL]** に、サービス URL に使用する一意のサブドメイン名を指定します。

サービス インスタンスの **[サブスクリプション]** と **[リージョン]** を選択します。 それらを選択したら、**[次へ]** ボタンをクリックします。

![新しい API Management サービス][api-management-create-instance-step1]

**[組織名]** フィールドに「**Contoso Ltd.**」と入力し、**[管理者の電子メール]** フィールドに電子メール アドレスを入力します。
>[AZURE.NOTE] API Management システムからの通知には、この電子メール アドレスが使用されます。 詳細については、次を参照してください。 [と ][]します。

![新しい API Management サービス][api-management-create-instance-step2]

API Management サービス インスタンスは、Developer、Standard、Premium の 3 つのレベルで利用できます。 既定では、新しい API Management サービス インスタンスは、Developer レベルで作成されます。 Standard レベルまたは Premium レベルを選択するには、**[詳細設定]** チェック ボックスをオンにし、次の画面で希望のレベルを選択します。
>[AZURE.NOTE] Developer レベルは、高可用性が重要ではない、開発、テスト、パイロット API プログラムのためのレベルです。 Standard レベルと Premium レベルでは、より多くのトラフィックを処理するために予約ユニット数を拡張できます。 Standard レベルと Premium レベルでは、最も高い処理能力とパフォーマンスを備えた、API Management サービスが提供されます。 このチュートリアルは、どのレベルを使用しても完了できます。 API Management レベルの詳細については、次を参照してください。 [API Management の料金の []][]します。

チェック ボックスをクリックすると、サービス インスタンスが作成されます。

![新しい API Management サービス][api-management-instance-created]

サービス インスタンスが作成されたら、次は、API の作成またはインポートを行います。

## <a name="create-api"> </a>API のインポート

API は、クライアント アプリケーションから呼び出すことのできる一連の操作で構成されます。 API の操作は、既存の Web サービスに引き渡されます。

API は、手動で作成して操作を追加することも、インポートすることもできます。 このチュートリアルでは、Microsoft によって提供され、Azure でホストされる、サンプル電卓 Web サービスの API をインポートします。
>[AZURE.NOTE] API を作成して操作を手動で追加する方法については、次を参照してください。 [Api を作成する方法](api-management-howto-create-apis.md) と [API に操作を追加する方法](api-management-howto-add-operations.md)します。

API の構成は、Azure クラシック ポータルから発行者ポータルにアクセスして行います。 発行者ポータルにアクセスするには、API Management サービスの Azure クラシック ポータルで **[管理]** をクリックします。

![パブリッシャー ポータル][api-management-management-console]

電卓 API をインポートするには、左側の **[API Management]** メニューの **[API]** をクリックし、**[API のインポート]** をクリックします。

![API ボタンのインポート][api-management-import-api]

電卓 API を構成するには、次の手順を実行します。

1. をクリックして **URL から**, 、入力 **http://calcapi.cloudapp.net/calcapi.json** に、 **仕様ドキュメント URL** テキスト ボックス、およびクリック、 **Swagger** オプション ボタンをクリックします。
2. **[Web API URL サフィックス]** テキスト ボックスに「**calc**」と入力します。
3. **[製品 (オプション)]** ボックスをクリックし、**[スターター]** を選択します。
4. **[保存]** をクリックして、API をインポートします。

![新しい API を追加する][api-management-import-new-api]

API がインポートされると、API の概要ページがパブリッシャー ポータルに表示されます。

![API の概要][api-management-imported-api-summary]

API セクションにはいくつかのタブがあります。 **[概要]** タブには、API に関する基本的なメトリックと情報が表示されます。  [設定](api-management-howto-create-apis.md#configure-api-settings) API の構成を表示および編集] タブを使用します。  [操作](api-management-howto-add-operations.md) タブは、API の操作を管理するために使用します。  **セキュリティ** 基本認証を使用してバックエンド サーバー用のゲートウェイ認証を構成する] タブを使用できますか [相互証明書認証](api-management-howto-mutual-certificates.md), 、し構成する [OAuth 2.0 を使用してユーザーの承認](api-management-howto-oauth2.md)します。 **[問題]** タブは、API を使用している開発者によって報告された問題を表示するために使用します。 **[成果物]** タブは、この API が含まれている成果物を構成するために使用します。

すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

-   **スターター**
-   **無制限**

このチュートリアルでは、API がインポートされたときに Basic Calculator API がスターター製品に追加されました。

API を呼び出すためには、その API へのアクセスを提供する成果物を開発者が事前にサブスクライブする必要があります。 開発者は、開発者ポータルで成果物にサブスクライブすることができます。また管理者がパブリッシャー ポータルで、開発者を成果物にサブスクライブすることもできます。 このチュートリアルで先ほど API Management インスタンスを作成したので、管理者になっています。既定で、既にすべての製品にサブスクライブしていることになります。

## <a name="call-operation"> </a>開発者ポータルから操作を呼び出す

開発者ポータルには、API の操作を見てテストするための便利な環境が用意されており、操作を直接呼び出すことができます。 このチュートリアルの手順では、Basic Calculator API の **2 つの整数を追加する**操作を呼び出します。 パブリッシャー ポータルの右上にあるメニューから **[開発者ポータル]** をクリックします。

![開発者ポータル][api-management-developer-portal-menu]

上部のメニューの **[API]** をクリックし、**[Basic Calculator]** をクリックして、利用できる操作を表示します。

![開発者ポータル][api-management-developer-portal-calc-api]

API および操作とともにサンプルの説明とパラメーターがインポートされることに注意してください。これは、この操作を使用する開発者のためのドキュメントとして提供されます。 操作を手動で追加するときに、これらの説明の追加もできます。

**2 つの整数を追加する**操作を呼び出すには、**[試してみる]** をクリックします。

![試してみる][api-management-developer-portal-calc-api-console]

パラメーターに値を入力するか、既定値を変更せずに **[送信]** をクリックします。

![HTTP Get][api-management-invoke-get]

操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

![応答][api-management-invoke-get-response]

## <a name="view-analytics"> </a>分析結果表示

Basic Calculator の分析結果を表示するには、開発者ポータルの右上にあるメニューから **[管理]** を選択してパブリッシャー ポータルに戻ります。

![管理][api-management-manage-menu]

パブリッシャー ポータルの既定のビューは**ダッシュボード**で、API Management インスタンスの概要が表示されます。

![ダッシュボード][api-management-dashboard]

**Basic Calculator** のグラフの上にマウス ポインターを合わせると、指定された期間での API の使用量について特定のメトリックが表示されます。
>[AZURE.NOTE] グラフに線が表示されない場合は、開発者ポータルに戻って API を数回呼び出し、少し経ってから、再度ダッシュボードに切り替えてください。

**[詳細の表示]** をクリックすると、表示されたメトリックの拡大版を含む、API の概要ページが表示されます。

![分析][api-management-mouse-over]

![まとめ][api-management-api-summary-metrics]

詳細なメトリックとレポートについては、左側の **[API Management]** メニューの **[分析]** をクリックします。

![概要][api-management-analytics-overview]

**[分析]** セクションには、次の 4 つのタブがあります。

-   **[概略]** には、全体的な使用量と正常性のメトリックのほか、開発者、成果物、API、操作それぞれのトップが表示されます。
-   **[使用量]** には、API 呼び出しと帯域幅の詳しい状況 (地理的情報を含む) が表示されます。
-   **[正常性]** には、ステータス コード、キャッシュの成功率、応答時間、API とサービスの応答時間が表示されます。
-   **[アクティビティ]** には、開発者、成果物、API、操作ごとの特定のアクティビティを詳しく分析したレポートが表示されます。

## <a name="next-steps"> </a>次のステップ

-   [その他のトピックもチェックして、 [高度な API の構成を使ってみる][] チュートリアルです。


[azure free trial]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a 
[create an api management instance]: #create-service-instance 
[create an api]: #create-api 
[add an operation]: #add-operation 
[add the new api to a product]: #add-api-to-product 
[subscribe to the product that contains the api]: #subscribe 
[call an operation from the developer portal]: #call-operation 
[view analytics]: #view-analytics 
[next steps]: #next-steps 
[how to manage developer accounts in azure api management]: api-management-howto-create-or-invite-developers.md 
[configure api settings]: api-management-howto-create-apis.md#configure-api-settings 
[how to configure notifications and email templates in azure api management]: api-management-howto-configure-notifications.md 
[responses]: api-management-howto-add-operations.md#responses 
[how create and publish a product]: api-management-howto-add-products.md 
[get started with advanced api configuration]: api-management-get-started-advanced.md 
[api management pricing]: http://azure.microsoft.com/pricing/details/api-management/ 
[azure classic portal]: https://manage.windowsazure.com/ 
[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png 
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png 
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png 
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png 
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png 
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png 
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png 
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png 
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png 
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png 
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png 
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png 
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png 
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png 
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png 
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png 
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png 
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png 
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png 
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png 
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png 
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png 
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png 
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png 
[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png 
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png 
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png 
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png 
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png 
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png 
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png 
[api-management-]: ./media/api-management-get-started/api-management-.png 
[api-management-]: ./media/api-management-get-started/api-management-.png 

