<properties
   pageTitle="VETR を使用した EAI ロジック アプリの作成 (Azure App Service の Logic Apps) | Microsoft Azure"
   description="BizTalk XML サービスの検証機能、エンコード機能、および変換機能"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="rajram"/>



# VETR を使用した EAI ロジック アプリの作成

ほとんどの Enterprise Application Integration (EAI) シナリオでは、ソースと出力先との間でデータの仲介をします。 このようなシナリオには、多くの場合、次のような共通の要件があります。

- 異なるシステムから受け取るデータが正しい形式であることを確認する。
- 受信データに対して "ルックアップ" を実行して意思決定を行う。
- データの形式を変換する。 ある形式から別の形式 (たとえば、CRM システムのデータ形式から ERP システムのデータ形式) にデータを変換する。
- 目的のアプリケーションまたはシステムにデータを伝送するルートを作成する。

この記事では、一般的な統合パターンの "一方向メッセージ仲介" または VETR (検証、強化、変換、ルート) と呼ばれるパターンについて説明します。 VETR パターンは、ソース エンティティと、出力先エンティティとの間でデータを仲介します。 通常は、ソースも出力先もデータ ソースです。

注文を受け付ける Web サイトの場合を考えます。 ユーザーは、HTTP を使用してシステムに注文をポストします。 その間、システムは受信データが正確かどうかを検証したうえで正規化し、次の処理のために Service Bus キューに保持します。 システムは、キューから注文を取り出し、特定の形式で待ち受けます。 このため、エンド ツー エンド フローは、次のようになります。

**HTTP** → **検証** → **変換** → **Service Bus**

![基本的な VETR フロー][1]

次の BizTalk API は、このパターンを作成する際に役立ちます。

* **HTTP トリガー** - メッセージ イベントをトリガーするソース。
* **検証** - 受信データが正確であるかどうかを検証する。
* **変換** - データを受信形式から下流システムで必要な形式に変換する。
* **Service Bus コネクタ** - データを送信する出力先エンティティ。


## 基本的な VETR パターンの構築

### 基本

Azure ポータルで、**[+新規]**、**[Web + モバイル]**、**[ロジック アプリ]** の順に選択します。 名前、場所、サブスクリプション、リソース グループ、および動作する場所を選択します。 リソース グループは、アプリのコンテナーとして機能します。また、アプリのリソースはすべて同じリソース グループに移動します。

次に、トリガーとアクションを追加します。


## HTTP トリガーの追加

1. **[ロジック アプリのテンプレート]** の **[ゼロから作成]** を選択します。
1. ギャラリーから **[HTTP リスナー]** を選択して新しいリスナーを作成します。 このリスナーを **HTTP1** と呼びます。
2. **[応答を自動的に送信しますか?]** を false に設定します。 _Http メソッドで、トリガーのアクションを構成する _ を _POST_ URL_ [/onewaypipeline] を設定する _/OneWayPipeline_:  
    ![HTTP トリガー][2]
3. 緑色のチェックマークをオンにして、トリガーを完了します。

## 検証アクションの追加

ここでは、トリガーが起動したとき (HTTP エンドポイントで呼び出しが受信されたとき) に常に実行されるアクションを入力します。

1. ギャラリーから **[BizTalk XML Validator]** を追加し、_(Validate1)_ という名前を付けてインスタンスを作成します。
2. 受信 XML メッセージを検証する XSD スキーマを構成します。 _[検証]_ アクションを選択し、_inputXml_ パラメーターの値として _[triggers(‘httplistener’).outputs.Content]_ を選択します。

これで、検証アクションは、HTTP リスナー後の最初のアクションになります。

![BizTalk XML Validator][3]

同様にして、他のアクションも追加します。

## 変換アクションの追加

受信データを正規化するために変換を構成します。

1. ギャラリーから **BizTalk 変換サービス**を追加します。
2. 変換を構成して受信 XML メッセージを変換するには、この API が呼び出されたときに実行するアクションとして **[変換]** アクションを選択します。 選択 `triggers('httplistener').outputs します。コンテンツ` の値として _inputXml_します。 受信データは、構成済みのすべての変換に適合しますが、スキーマに一致するものだけが適用されるため、*Map* はオプションのパラメーターです。
3. 最後に、検証が成功した場合にのみ、変換が実行されるようにします。 この条件を構成するのには、右、上にある歯車アイコンを選択し、met_ する条件の追加 (_a) を選択します。 条件を設定します `equals(actions('xmlvalidator').status,'Succeeded')`:

![BizTalk 変換][4]


## Service Bus コネクタの追加

次に、データを書き込む場所である出力先 (Service Bus キュー) を追加します。

1. ギャラリーから **Service Bus コネクタ**を追加します。 **[名前]** を _Servicebus1_ に、**[接続文字列]** をユーザーのサービス バス インスタンスへの接続文字列に、**[エンティティ名]** を _[キュー]_ に設定します。**[サブスクリプション名]** はスキップします。
2. **メッセージの送信**アクションを選択し、このアクションの **[コンテンツ]** フィールドを _actions('transformservice').outputs.OutputXml_ に設定します。
3. **[コンテンツの種類]** フィールドを *application/xml* に設定します。

![Service Bus][5]


## HTTP 応答の送信

パイプライン処理の完了後、成功した場合と失敗した場合のそれぞれについて HTTP 応答を返すように構成します。手順は次のとおりです。

1. ギャラリーから **HTTP リスナー**を追加し、**HTTP 応答の送信**アクションを選択します。
2. **[応答 ID]** を "*メッセージの送信*" に設定します。
2. **[応答コンテンツ]** を "*パイプライン処理が完了しました*" に設定します。
3. HTTP 200 OK を指定するために **[応答状態コード]** を "*200*" に設定します。
4. 右上のドロップダウン メニューをクリックし、**[満たされる条件を追加する]** を選択します。 次のように、条件を設定します。  
    `@equals(actions('azureservicebusconnector').status,'Succeeded')`  <br/>
5. 失敗した場合も HTTP 応答を送信するために、同じ手順を繰り返します。 変更 **条件** 次のようにします。  
`@not(equals(actions('azureservicebusconnector').status,'Succeeded'))` <br/>
6. **[OK]** を選択し、**[作成]** を選択します。



## 完了

メッセージが HTTP エンドポイントに送信されるたびに、エンドポイントがアプリをトリガーし、上記で作成したアクションが実行されます。 このようにして作成したロジック アプリを管理するには、Azure ポータルで **[参照]** を選択し、**[Logic Apps]** を選択します。 確認するアプリを選択すると、詳細が表示されます。

いくつかの役に立つトピック:

[管理し、API Apps とコネクタの監視](app-service-logic-monitor-your-connectors.md)  <br/>
[ロジック アプリを監視します。](app-service-logic-monitor-your-logic-apps.md)



[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG 
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG 
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG 
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG 
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG 

