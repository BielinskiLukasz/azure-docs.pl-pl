<properties 
    pageTitle="Mobile Services アプリケーション へのプッシュ通知の追加 (Windows Phone) | Microsoft Azure" 
    description="Azure Mobile Services と Notification Hubs を使用して Windows Phone アプリにプッシュ通知を送信する方法について説明します。" 
    services="mobile-services,notification-hubs" 
    documentationCenter="windows" 
    authors="ggailey777" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="glenga"/>


# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##概要

このトピックでは、Azure Mobile Services を使用して Windows Phone Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。 このチュートリアルでは、クイック スタート プロジェクトへの Azure Notification Hubs を使用したプッシュ通知を有効にします。 完了すると、モバイル サービスは、レコードが挿入されるたびに Notification Hubs を使用してプッシュ通知を送信します。 作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

このチュートリアルは、TodoList サンプル アプリケーションに基づいています。 トピックを完了してこのチュートリアルを開始する前におく必要があります [Add Mobile Services to an existing app] 、プロジェクトをモバイル サービスに接続します。 モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続を作成できます。 

>[AZURE.NOTE]Windows Phone 8.1 ストア アプリにプッシュ通知を送信するには、次の [Windows ストア アプリ](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) このチュートリアルのバージョン。

##<a id="update-app"></a>アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;

3. 次のコードを App.xaml.cs に追加します。
    
        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                    // Register for notifications using the new channel
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    このコードは Windows Phone 8.x "Silverlight" で使用される Microsoft Push Notification Service (MPNS) からアプリの ChannelURI を取得し、それをプッシュ通知用に登録します。

    >[AZURE.NOTE]これでこのチュートリアルでは、モバイル サービス トースト通知を送信、デバイスにします。 タイル通知を送信するときに呼び出す必要があります、 **BindToShellTile** 、チャネル上のメソッドです。

4. 上部にある、 **Application_Launching** App.xaml.cs のイベント ハンドラーが、新規作成] を次の呼び出しを追加 **AcquirePushChannel** メソッド。

        AcquirePushChannel();

    これにより、ページが読み込まれるたびに登録が要求されるようになります。 アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。 

5. キーを押して、 **f5 キーを押して** キーをアプリケーションを実行します。 登録キーを示すポップアップ ダイアログが表示されます。
  
6.  ソリューション エクスプ ローラーで、 **プロパティ**, 、WMAppManifest.xml ファイルを開き、をクリックして、 **機能** ] タブを確認して、 **_ _ _push_notification** 機能がオンです。

    ![VS で通知を有効にする](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)

    これにより、アプリケーションでトースト通知の使用が有効になります。 

##<a id="update-scripts"></a>サーバー スクリプトを更新してプッシュ通知を送信する

最後に、通知を送信するためには、TodoItem テーブルの挿入操作に登録されているスクリプトを更新する必要があります。

1. をクリックして **TodoItem**, 、] をクリックして **スクリプト** 選択 **挿入**します。 

2. クリックして insert 関数を次のコードに置き換えます **保存**:

        function insert(item, user, request) {
        // Define a payload for the Windows Phone toast notification.
        var payload = '<?xml version="1.0" encoding="utf-8"?>' +
            '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
            '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
            '</wp:Text2></wp:Toast></wp:Notification>';
        
        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.mpns.send(null, payload, 'toast', 22, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse);
                        request.respond();
                        },              
                        error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
                            request.respond(500, { error: pushResponse });
                            }
                        });
                    }
                });      
        }

    この insert スクリプトによって、挿入が成功した後、すべての Windows Phone アプリケーション登録にプッシュ通知が (挿入された項目のテキストと共に) 送信されます。

3. をクリックして、 **プッシュ** ] タブで、チェック **非認証プッシュ通知を有効にする**, 、] をクリックし、 **保存**します。

    これにより、非認証モードで MPNS に接続するモバイル サービスがプッシュ通知を送信できるようになります。

    >[AZURE.NOTE]このチュートリアルでは、非認証モードで MPNS を使用します。 このモードでは、MPNS はデバイス チャネルに送信できる通知の数を制限します。 この制限を削除するには、**[アップロード]** をクリックして証明書を生成してアップロードし、その証明書を選択する必要があります。 証明書を生成する方法の詳細については、次を参照してください。 [Setting up an authenticated web service to send push notifications for Windows Phone]します。

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

    >[AZURE.NOTE] Windows Phone エミュレーターでテストする場合は、401 許可されていないエラー「registrationauthorizationexception」が発生する可能性があります。 これは、Windows Phone エミュレーターのホスト PC の時計との同期方法によって `RegisterNativeAsync()` 呼び出し中に発生します。 セキュリティ トークンで拒否される場合があります。 これを解決するには、テストする前にエミュレーターの時計を手動で設定します。

5. アプリケーションで、テキスト「こんにちは push」と入力、テキスト ボックスに、クリックして **保存**, 、すぐに開始] ボタンまたは [戻る] ボタンをクリックしてアプリケーションを閉じます。

    ![アプリケーションにテキストを入力する](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png)

    これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。 デバイスがというトースト通知を受信 **こんにちは push**します。

    ![受信したトースト通知](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

    >[AZURE.NOTE]アプリケーションでまだしたら、通知を受信しませんされます。 アプリがアクティブな間は、トースト通知を受信するには、処理、 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx) イベントです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。 次に、次のチュートリアルのいずれかを完了します。

+ [登録者へのブロードキャスト通知の送信](../notification-hubs-windows-phone-send-breaking-news.md)
    <br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

+ [登録者へのプラットフォーム固有の通知を送信します。](../notification-hubs-aspnet-cross-platform-notify-users.md)
    <br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成することがなく、モバイル サービスからプッシュ通知を送信する方法について説明します。


Mobile Services と Notification Hubs については次のトピックを参照してください。

* [Azure Notification Hubs - 診断ガイドライン](../notification-hubs-diagnosing.md)
    <br/>プッシュ通知の問題のトラブルシューティングを行う方法について説明します。

* [Get started with authentication]
  <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [What are Notification Hubs?]
  <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Mobile Services .NET How-to Conceptual Reference]
  <br/>.NET で Mobile Services を使用する方法について説明します。

* [Mobile Services server script reference]
  <br/>Mobile Services にビジネス ロジックを実装する方法について説明します。

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Add Mobile Services to an existing app]: mobile-services-windows-phone-get-started-data.md
[Get started with authentication]: mobile-services-windows-phone-get-started-users.md

[Setting up an authenticated web service to send push notifications for Windows Phone]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

[What are Notification Hubs?]: ../notification-hubs-overview.md

 

