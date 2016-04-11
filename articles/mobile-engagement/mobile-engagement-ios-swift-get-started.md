<properties
    pageTitle="Swift で IOS の Azure Mobile Engagement を開始する"
    description="iOS アプリ の分析やプッシュ通知で Azure Mobile Engagement を使用する方法を説明します。"
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="get-started-article"
    ms.date="09/22/2015"
    ms.author="piyushjo" />

# Swift で IOS アプリ の Azure  Mobile Engagement を開始する

> [AZURE.SELECTOR]
- [ユニバーサル Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、iOS アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Apple プッシュ通知システム (APNS) を使用して基本データを収集し、プッシュ通知を受信する空の iOS アプリケーションを作成します。

このチュートリアルには、次のものが必要です。

+ XCode 6 または XCode 7。Mac アプリ ストアからインストールできます。
+ 、 [Mobile Engagement iOS SDK]
+ プッシュ通知証明書 (.p12)。Apple Dev Center で入手できます

> [AZURE.NOTE] このチュートリアルでは、Swift バージョン 2.0 を使用します。 

このチュートリアルを完了することは、iOS アプリケーションの他のすべての Mobile Engagement チュートリアルの前提条件です。

> [AZURE.IMPORTANT] IOS アプリの他のすべてのモバイル エンゲージメント チュートリアルの前提条件このチュートリアルを完了することは、これを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>」を参照してください。

##<a id="setup-azme"></a>iOS アプリ用に Mobile Engagement を設定する

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。 完全な統合のドキュメントについては記載されて、 [モバイル エンゲージメント iOS SDK の統合](../mobile-engagement-ios-sdk-overview/)

統合のデモンストレーションを行うために、XCode で基本的なアプリを作成します。

###新しい iOS プロジェクトを作成する

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###アプリを Mobile Engagement のバックエンドに接続する

1. ダウンロード、 [Mobile Engagement iOS SDK]
2. コンピューター上のフォルダーに .tar.gz ファイルを抽出します
3. プロジェクトを右クリックし、[Add files to ...] を選択します

    ![][1]

4. SDK を抽出したフォルダーに移動し、 `EngagementSDK` フォルダーを選択して、[OK] を押します。

    ![][2]

5. `Build Phases` タブを開き、`Link Binary With Libraries` メニューで次に示すように、フレームワークを追加します。

    ![][3]

6.  **XCode 7** -追加 `libxml2.tbd` の代わりに `libxml2.dylib`します。

7. ブリッジ ヘッダーを作成して、[作成] > [新規] > [ファイル] > [iOS] > [ソース] > [ヘッダー ファイル] を選択して SDK's Objective C API を使用できるようにします。

    ![][4]

8. ブリッジ ヘッダー ファイルを編集して、Mobile Engagement Objective-C コードを Swift コードに公開し、次の imports を追加します。

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEIdfaProvider.h"

9. [ビルドの設定] で、Swift コンパイラの Objective-C Bridging Header のビルド設定で Swift コンパイラ - コード生成にこのヘッダーへのパスがあることを確認します。 パスの例を次に示します: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (パスによる)**

    ![][6]

10. アプリの Azure ポータルに戻り *接続情報]* ページし、接続文字列をコピー

    ![][5]

11. `didFinishLaunchingWithOptions` 認証で、接続文字列を貼り付けます。

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>リアルタイム監視の有効化

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

1. 開いている、 **ViewController.swift** ファイルし、の基本クラスを置換 **ViewController** する **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>リアルタイム監視を使用してアプリを接続する

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

 Mobile Engagement により、ユーザーと通信を行い、キャンペーンのコンテキストに関するプッシュ通知とアプリ内メッセージングを届けることができます。 このモジュールは、 Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するためにアプリをセットアップします。

### アプリがサイレント プッシュ通知を受信できるようにする

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### リーチ ライブラリをプロジェクトに追加する

1. プロジェクトを右クリックします
2. `Add file to ...` を選択します
3. SDK を抽出したフォルダーに移動します
4. `EngagementReach` フォルダーを選択します
5. [追加] をクリックします。
6. ブリッジ ヘッダー ファイルを編集して、Mobile Engagement Objective-C Reach ヘッダーを Swift コードに公開し、次の imports を追加します。

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### アプリケーション デリゲートを変更する

1. 内部、 `didFinishLaunchingWithOptions` - reach モジュールを作成し、それを既存の Engagement 初期化行に渡します。

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
            let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###アプリで APNS プッシュ通知を受信できるようにする
1. 次の行を `didFinishLaunchingWithOptions` メソッドに追加します。

        /* Ask user to receive push notifications */
        if #available(iOS 8.0, *)
        {
           let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
           application.registerUserNotificationSettings(settings)
           application.registerForRemoteNotifications()
        }
        else
        {
           application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
        }

2. 次のように、`didRegisterForRemoteNotificationsWithDeviceToken` メソッドを追加します。

        func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
        {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. 次のように、`didReceiveRemoteNotification:fetchCompletionHandler:` メソッドを追加します。

        func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
        {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png


