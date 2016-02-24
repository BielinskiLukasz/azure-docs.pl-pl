<properties
    pageTitle="1 つのモバイル サービス バックエンドで複数のクライアントを使用する方法 | Azure Mobile Services"
    description="さまざまなモバイル プラットフォームを対象とする複数のクライアント アプリから単一の Mobile Services バックエンドを使用する方法について説明します。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor="mollybos"/>
<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="glenga"/>

# シングル モバイル サービスから複数のデバイス プラットフォームをサポートする

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


モバイル アプリ開発で Azure モバイル サービスを使用する主なメリットの 1 つに、複数のクライアント プラットフォームでアプリをサポートするシングル バックエンド サービスの使用があります。 モバイル サービスでは、すべての主要デバイス プラットフォームに対してネイティブ クライアント ライブラリを提供するため、シングル バックエンド サービスとクロスプラットフォーム開発者ツールを使用して簡単にアプリが開発できます。 このトピックではシングル モバイル サービス バックエンドを使用しながら複数のクライアント プラットフォームでアプリを実行させるための考慮事項について説明します。

##<a id="push"></a>クロスプラットフォーム プッシュ通知

モバイル サービスでは、すべての主要デバイス プラットフォームのクライアント アプリへのプッシュ通知の送信に Azure 通信ハブを使用しています。 Notification Hubs によって提供される一貫性のある統一されたインフラストラクチャで、デバイスの登録を作成、管理したり、クロスプラットフォーム プッシュ通知を送信したりできます。 Notification Hubs では、次のプラットフォーム固有の通知サービスを使用してプッシュ通知の送信をサポートしています。

+ iOS アプリ向け Apple Push Notification Service (APNS)
+ Android アプリ向け Google Cloud Messaging (GCM) サービス
+ Windows ストア、Windows Phone 8.1 ストア、ユニバーサル Windows アプリ向け Windows Notification Service (WNS)
+ Windows Phone Silverlight アプリ向け Microsoft Push Notification Service (MPNS)

詳細については、[Azure Notification Hubs] を参照してください。

クライアントの登録は、プラットフォーム固有のモバイル サービス クライアント ライブラリで register 関数か、モバイル サービス REST API を使用して作成されます。 Notification Hubs では次の 2 種類のデバイス登録をサポートします。

+ **ネイティブ登録**<br/>ネイティブ登録はプラットフォーム固有のプッシュ通知サービスに合わせて調整します。 ネイティブ登録を使用して登録したデバイスに通知を送信する場合、モバイル サービスでプラットフォーム固有の API を呼び出す必要があります。 複数のプラットフォーム上のデバイスに通知を送信するには、複数のプラットフォーム固有の呼び出しが必要になります。

+ **テンプレート登録**<br/>通知ハブには、プラットフォーム固有のテンプレート登録もサポートしています。 テンプレート登録を使用すると、1 回の API 呼び出しを使用して登録したデバイス上で稼働するアプリに通知を送信できます。 詳細については、[ユーザーにクロスプラット フォーム通知の送信] を参照してください。

次のセクションのテーブルはクライアント固有のチュートリアルにリンクしており、.NET と JavaScript バックエンド両方のモバイル サービスからプッシュ通知を実装する方法を示しています。

###.NET バックエンド

[SendAsync] メソッドを呼び出して通知を送信する .NET バックエンド モバイル サービスで、 [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) から取得したオブジェクト、 [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx) プロパティです。 送信したプッシュ通知 (ネイティブまたはテンプレート) は、特定の依存 [IPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx)-派生オブジェクトは、次の表に示すように、[SendAsync] メソッドに渡されます。

|Platform |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) | MPNS
|-----|-----|----|----|-----|
|ネイティブ|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx)   |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx)     |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

次のコードは .NET バックエンド サービスからすべての iOS および Windows ストア デバイス登録にプッシュ通知を送信します。

    // Define a push notification for APNS.
    ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

    // Define a push notification for WNS.
    WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";

    // Send push notifications to all registered iOS and Windows Store devices.
    await Services.Push.SendAsync(apnsMessage);
    await Services.Push.SendAsync(wnsMessage);

その他のネイティブ クライアント プラットフォームにプッシュ通知を送信する方法の例は、上記のテーブルのヘッダーにあるプラットフォームのリンクをクリックしてください。

ネイティブ クライアント登録ではなく、テンプレート クライアント登録を使用するときに次のように、[TemplatePushMessage] オブジェクトを指定して、[SendAsync] に 1 回の呼び出しで同じ通知を送信できます。

    // Create a new template message and add the 'message' parameter.
    var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

    // Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload);

###JavaScript バックエンド

JavaScript バックエンド モバイル サービスで呼び出すことで通知を送信する、 **送信** プラットフォームに固有のオブジェクトのメソッドは、グローバルから取得した [push オブジェクト]、次の表に示すように。

|Platform |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|ネイティブ|[apns オブジェクト](http://msdn.microsoft.com/library/azure/jj839711.aspx)   |[gcm オブジェクト](http://msdn.microsoft.com/library/azure/dn126137.aspx)     |[wns オブジェクトに関するページ](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [mpns オブジェクト](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

次のコードはすべての Android および Windows Phone 登録にプッシュ通知を送信します。

    // Define a push notification for GCM.
    var gcmPayload =
    '{"data":{"message" : item.text }}';

    // Define the payload for a Windows Phone toast notification.
    var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
    '</wp:Text2></wp:Toast></wp:Notification>';

    // Send push notifications to all registered Android and Windows Phone 8.0 devices.
    push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

その他のネイティブ クライアント プラットフォームにプッシュ通知を送信する方法の例は、上記のテーブルのヘッダーにあるプラットフォームのリンクをクリックしてください。

ネイティブ クライアント登録ではなく、テンプレート クライアント登録を使用する場合は、1 回の呼び出しで同じ通知を送信することができます、 **送信** の関数にグローバル [push オブジェクト] 次のように、テンプレート メッセージ ペイロードを指定します。

    // Create a new template message with the 'message' parameter.
    var templatePayload = { "message": item.text };

    // Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

##<a id="xplat-app-dev"></a>クロスプラットフォーム アプリ開発
すべての主要モバイル デバイス プラットフォームでネイティブ モバイル デバイス アプリを開発するには、少なくとも Objective-C、Java、C#、または JavaScript プログラミング言語の専門知識が必要になります。 こうした異種プラットフォームにおける開発費用の問題から、中にはアプリに完全な Web ブラウザ ベースの操作環境を選ぶ開発者もいます。 しかし、こうした Web ベースの操作環境では、ユーザーがモバイル デバイスで期待する高度な操作環境を提供するネイティブ リソースにはほとんどアクセスできません。

クロスプラットフォーム ツールでは、シングル コード ベース (通常は JavaScript) を共有しながら、モバイル デバイスでより高度なネイティブ操作環境を提供します。 モバイル サービスでは、次の開発プラットフォームにおいてクイックスタート チュートリアルを提供し、クロスプラットフォームのアプリ開発プラットフォームのバックエンド サービスを簡単に作成し、管理できます。

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Appcelerator では、JavaScript を使用して、すべてのモバイル デバイス プラットフォームでネイティブと実行にコンパイルされたシングル アプリを開発することができます。 これにより、UI における高度な操作環境、すべてのネイティブ デバイス リソースへのアクセス、ネイティブ アプリ パフォーマンスが実現します。 詳細については、[Appcelerator チュートリアル][Appcelerator] を参照してください。

+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (Apache Cordova プロジェクトのディストリビューション) は無料かつオープン ソース フレームワークで標準化された web Api、Android、iOS および Windows デバイスで実行される 1 つのアプリを開発するには、HTML および JavaScript を使用しています。 PhoneGap では Web 表示ベースの UI を提供しますが、プッシュ通知や加速度計、カメラ、ストレージ、位置情報、アプリ内ブラウザなどデバイスのネイティブ リソースへのアクセスによって操作環境が強化されます。 詳細については、[PhoneGap クイック スタート チュートリアル] を参照してください。 [PhoneGap] です。

    Visual Studio では Visual Studio の Multi-Device Hybrid アプリ拡張機能 (プレリリース ソフトウェア) を使用してクロスプラットフォームの Cordova アプリを構築できます。 詳細については、次を参照してください。 [マルチ デバイス ハイブリッド アプリを使用して HTML と JavaScript の概要](http://msdn.microsoft.com/library/dn771545.aspx)します。

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch は、単一 HTML および JavaScript コード ベースからのモバイル デバイスのさまざまなネイティブのようなエクスペリエンスを提供する、タッチ画面用に最適化されたコントロールのセットを提供します。 Sencha Touch は PhoneGap や Cordova ライブラリと併用し、ネイティブ デバイス リソースへのアクセスをユーザーに提供できます。 詳細については、[Sencha Touch クイック スタート チュートリアル] を参照してください。 [Sencha] です。

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Xamarin に完全なネイティブ UI とすべてのデバイス リソースへのアクセスで iOS および Android デバイス向けに完全なネイティブ アプリを作成できます。 Xamarin アプリは Objective-C と Java ではなく、 C# でコード化されています。 これにより、.NET 開発者は iOS と Android にアプリを発行し、Windows プロジェクトからのコードを共有できます。 Xamarin では iOS と Android デバイスの両方に C# コードから完全にネイティブの操作環境を提供します。 これにより、Windows アプリからのモバイル サービス コードを iOS や Android デバイスで再利用できるようになります。 詳細については、次を参照してください。 [Xamarin 開発](#xamarin) 以下です。


<!-- URLs -->
[Azure Notification Hubs]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Send cross-platform notifications to users]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[How to use a .NET client with Mobile Services]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[push object]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]:http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[What's next for Windows Phone 8 developers]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Building universal Windows apps for all Windows devices]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Universal Windows app project for Azure Mobile Services using MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

