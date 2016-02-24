<properties 
   pageTitle="Logic Apps での待機コネクタの使用 | Microsoft Azure App Service" 
   description="待機コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法" 
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
   ms.date="11/30/2015"
   ms.author="rajram"/>

# 待機コネクタの使用開始とロジック アプリへの追加
待機コネクタを使用すると、指定した期間が経過するまで、または指定した時間になるまで、アプリの実行を遅らせることができます。 この待機コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。 ロジック アプリの中で使用して、実行を遅らせることができます。

## 待機コネクタを使用する
待機コネクタを使用するには、まず待機コネクタ API アプリのインスタンスを作成する必要があります。 これは、ロジック アプリの作成中にインラインで作成するか、Azure Marketplace から待機コネクタ API アプリを選択することで実行できます。

## Logic Apps デザイナー画面で待機コネクタを使用する
待機コネクタは、アクションとして使用できます。 トリガーはありません。

### アクション
- 右側のペインから、[待機コネクタをクリックします。  
![アクションの一覧][1]
- 待機コネクタは、2 つのアクションをサポートします。 
    - 遅延
    - この時間まで遅延
     
- 選択 *遅延*:  
![遅延の入力][2]
- アクションの入力を指定し、それを構成します。  
![アクションの構成][3]

パラメーター|型|パラメーターの説明
---|---|---
期間 (分)|整数|分単位の遅延期間


## コネクタでできること
これでコネクタが作成されたため、ロジック アプリを使用してこのコネクタをビジネス フローに追加することができます。 参照してください [Logic Apps とは?](app-service-logic-what-are-logic-apps.md)します。

>[AZURE.NOTE] Azure Logic Apps と Azure アカウントにサインアップする前に開始する場合に、 [ロジック アプリを実行してください](https://tryappservice.azure.com/?appservice=logic), 、App Service で有効期間の短いスターター ロジック アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

表示、Swagger の REST API リファレンス [コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)します。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。 参照してください [管理と監視 API アプリとコネクタの](../app-service-api/app-service-api-manage-in-portal.md)です。

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

