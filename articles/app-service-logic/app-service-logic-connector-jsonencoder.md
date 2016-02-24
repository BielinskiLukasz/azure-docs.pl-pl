<properties
   pageTitle="Logic Apps での BizTalk JSON エンコーダー コネクタの使用 | Microsoft Azure App Service "
   description="BizTalk JSON エンコーダー コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
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
   ms.date="11/25/2015"
   ms.author="rajram"/>

# BizTalk JSON エンコーダーの使用開始とロジック アプリへの追加 
BizTalk JSON エンコード/デコード コネクタは、JSON データと XML データの間でアプリを相互運用するのに役立ちます。 JSON インスタンスを指定して XML に変換したり、その逆方向に変換したりできます。

この BizTalk JSON エンコーダーをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。 

## BizTalk JSON エンコーダーを使用する
BizTalk JSON エンコーダー を使用するには、まず、BizTalk JSON Encoder API アプリのインスタンスを作成する必要があります。 これは、ロジック アプリの作成時にインラインで作成するか、または Azure Marketplace から BizTalk JSON エンコーダー API アプリを選択することによって作成できます。

## Logic Apps のデザイナー画面で BizTalk JSON エンコーダーを使用する
[ロジック アプリの作成] の手順に従います。 BizTalk JSON エンコーダーは、アクションとして使用できます。 トリガーはありません。

### アクション
- 右側のウィンドウの [BizTalk JSON エンコーダー] をクリックします。

    ![アクションの設定][3]
- [->] (右矢印) をクリックします。

    ![アクションの一覧][4]
- BizTalk JSON エンコーダーは、2 つの操作をサポートします。 選択 *Xml から JSON へ*

    ![JSON 入力に Xml][5]
- アクションの入力を設定して構成します。

    ![エンコードおよび送信の構成][6]

パラメーター|型|パラメーターの説明
---|---|---
Input Xml|オブジェクト|入力 XML のコンテンツ
Remove Outer Envelope|文字列|XML コンテンツからルート ノードを削除するために設定されるフラグ

アクションは、入力コンテンツを JSON 表記で返します。

## コネクタでできること
これでコネクタが作成されたため、ロジック アプリを使用してこのコネクタをビジネス フローに追加することができます。 参照してください [Logic Apps とは?](app-service-logic-what-are-logic-apps.md)します。

>[AZURE.NOTE] Azure Logic Apps と Azure アカウントにサインアップする前に開始する場合に、 [ロジック アプリを実行してください](https://tryappservice.azure.com/?appservice=logic), 、App Service で有効期間の短いスターター ロジック アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

表示、Swagger の REST API リファレンス [コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)します。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。 参照してください [管理と監視 API アプリとコネクタの](../app-service-api/app-service-api-manage-in-portal.md)です。

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

