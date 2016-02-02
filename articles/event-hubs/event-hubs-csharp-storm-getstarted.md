<properties
    pageTitle="C# および Apache Storm での Event Hubs の使用 | Microsoft Azure"
    description="このチュートリアルでは、C# でイベントを送信し、Apache Storm クラスターでそれを受信するするための Azure Event Hubs の使用方法について説明します。"
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="11/05/2015"
    ms.author="sethm"/>


# Event Hubs の使用

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## はじめに

Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、[Event Hubs の概要] を参照してください。

このチュートリアルでは、C# のコンソール アプリケーションを使用して、イベント ハブにメッセージをインジェストし、Apache Storm を使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、以下が必要です。

+ Microsoft Visual Studio Express 2013 for Windows

+ Java 開発環境を実行するように構成 [Maven](http://maven.apache.org/)します。 このチュートリアルではものと [Eclipse](https://www.eclipse.org/)

+ アクティブな Azure アカウント <br/>アカウントを持っていない場合は、ほんの数分で無料の試用アカウントを作成できます。 詳細については、「 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 無料試用版</a>します。

## Event Hub を作成する

1. ログオン、 [Azure クラシック ポータルの [][], 、] をクリック **新規** 、画面の下部にあります。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[Event Hub]**、**[簡易作成]** の順にクリックします。

    ![][1]

3. Event Hub の名前を入力して、目的のリージョンを選択し、**[Create a new Event Hub (新しい Event Hub を作成する)]** をクリックします。

    ![][2]

4. 作成した名前空間をクリックして (通常 *** イベント ハブ名 *-ns**)。

    ![][3]

5. ページ上部にある、**[Event Hubs]]** タブをクリックし、先ほど作成したイベント ハブをクリックします。

    ![][4]

6. 上部にある **[構成]** タブをクリックし **SendRule** という名前のルールを *Send* 権限で、**ReceiveRule** という別のルールを *Listen* 権限で追加して、**[保存]** をクリックします。

    ![][5]

7. 同じページで、**ReceiveRule**に対して生成されたキーをメモしておきます。

    ![][6c]

8. 上部にある **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。 2 つの接続文字列をメモします。

    ![][6]

イベント ハブが作成され、イベントの送受信に必要な接続文字列が手元にあります。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.  Eclipse で **LogTopology** クラスを実行し、すべてのパーティションの受信側が起動するまで待機します。

2.  **Sender** プロジェクトを実行し、コンソール ウィンドウで **Enter** キーを押して、受信側ウィンドウに表示されるイベントを確認します。

    ![][22]

## 次のステップ

Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

- 完全な [Event Hubs を使用するサンプル アプリケーション][]します。
- [Event Hubs でイベント処理のスケール][] サンプルです。
- A [メッセージング ソリューションのキューに置かれた][] Service Bus キューを使用します。




[1]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub1.png 
[2]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub2.png 
[3]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub3.png 
[4]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub4.png 
[5]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub5.png 
[6]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub6.png 
[6c]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub6c.png 
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png 
[azure classic portal]: https://manage.windowsazure.com/ 
[event processor host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost 
[event hubs overview]: event-hubs-overview.md 
[sample application that uses event hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097 
[scale out event processing with event hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3 
[queued messaging solution]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md 

