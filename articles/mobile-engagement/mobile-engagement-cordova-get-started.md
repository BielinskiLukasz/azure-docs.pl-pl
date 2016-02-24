<properties
    pageTitle="Cordova/Phonegap 用 Azure Mobile Engagement の使用"
    description="Cordova/Phonegap アプリで Azure Mobile Engagement の分析機能やプッシュ通知を使用する方法について説明します。"
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="article" 
    ms.date="09/22/2015"
    ms.author="piyushjo" />

# Cordova/Phonegap 用 Azure Mobile Engagement の使用

> [AZURE.SELECTOR]
- [Windows ユニバーサル](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS |Obj C](mobile-engagement-ios-get-started.md)
- [iOS |Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、Azure Mobile Engagement を使用して、アプリの使用状況を把握する方法、および Cordova を使用して開発されたモバイル アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。

このチュートリアルでは、Mac を使用して空の Cordova アプリを作成してから、Mobile Engagement SDK と統合します。 このアプリは、基本的な分析データを収集し、iOS では Apple Push Notification System (APNS)、Android では Google Cloud Messaging (GCM) を使用してプッシュ通知を受信します。 このアプリを、テスト用に iOS デバイスまたは Android デバイスにデプロイします。 

> [AZURE.IMPORTANT] このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>をご覧ください。

このチュートリアルには、次のものが必要です。

+ XCode (iOS へのデプロイ用)。Mac App Store からインストールできます。
+ [Android SDK およびエミュレーター](http://developer.android.com/sdk/installing/index.html) (デプロイ用に Android へ)
+ APNS のプッシュ通知証明書 (.p12)。Apple Dev Center から入手できます。
+ GCM 用の GCM プロジェクト番号。Google デベロッパー コンソールから入手できます。
+ [Mobile Engagement 用の Cordoca プラグイン](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] ソース コードおよび readme ファイルのあります Cordova プラグインの [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>モバイル エンゲージメント Cordova アプリを設定します。

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>アプリをモバイル エンゲージメントのバックエンドに接続する

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。 

統合のデモンストレーションを行うために、Cordova で基本的なアプリを作成します。

###新しい Cordova プロジェクトを作成します。

1. 起動 *ターミナル* ウィンドウで、既定のテンプレートから新しい Cordova を作成し、次のプロジェクト、Mac コンピューターと型。 iOS アプリのデプロイに最終的に使用する発行プロファイルが、App ID として「com.mycompany.myapp」を使用していることを確認します。 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. プロジェクトを構成するのには、次を実行 **iOS** し、iOS シミュレーターで実行します。

        $ cordova platform add ios 
        $ cordova run ios

3. プロジェクトを構成するのには、次を実行 **Android** し、Android エミュレーターで実行します。

        $ cordova platform add android
        $ cordova run android

4.  Cordova Console プラグインを追加します。 

        $ cordova plugin add cordova-plugin-console 

###アプリを Mobile Engagement のバックエンドに接続する

1. Azure Mobile Engagement Cordova プラグインに変数値を設定しながら、このプラグインをインストールします。

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_LOG=true|false

*Android 到達アイコン* : 任意の拡張機能でも描画可能なプレフィックスなしのリソースの名前を指定する必要があります (例: mynotificationicon)、アイコン ファイルは、android のプロジェクト (プラットフォーム/android/res/描画可能な) にコピーする必要があります

*iOS 到達アイコン*  : 拡張子が付いたリソースの名前を指定する必要があります (ex: mynotificationicon.png)、および、(ファイルの追加] メニューを使用)、XCode で、アイコン ファイルを iOS プロジェクトに追加する必要があります

##<a id="monitor"></a>リアルタイム監視を有効にします。

1. Cordova プロジェクトでの編集 **www/js/index.js** 新しいアクティビティを宣言する Mobile Engagement への呼び出しを追加する、 *deviceReady* イベントを受信します。

         onDeviceReady: function() {
                app.receivedEvent('deviceready');
                AzureEngagement.startActivity("myPage",{});
            }

2. アプリケーションを実行します。
        
    - **iOS の場合**
    
        [`Terminal`] ウィンドウで次を実行して、新しいシミュレーター インスタンスでアプリを起動します。

            cordova run ios

    - **Android の場合**
        
        [`Terminal`] ウィンドウで次を実行して、新しいエミュレーター インスタンスでアプリを起動します。

            cordova run android

3. コンソールのログでは、次のことを確認できます。

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>リアルタイム監視を使用してアプリを接続する

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にします。

Mobile Engagement を導入すると、キャンペーンとの関連でプッシュ通知とアプリ内メッセージングを利用し、ユーザーとのやり取りを行うことができます。 このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するためにアプリをセットアップします。

###Mobile Engagement のプッシュの資格情報を設定する

Mobile Engagement がユーザーに代わりプッシュ通知を送信できるようにするには、Mobile Engagement に Apple iOS 証明書または GCM サーバーの API キーへのアクセスを許可する必要があります。 
    
1. Mobile Engagement ポータルに移動します。 このプロジェクトで使用しているアプリ] をクリックしてでいることを確認、 **の関心を引く** 下部にあるボタンをクリックします。
    
    ![][1]
    
2. Engagement ポータルの設定ページが表示されます。 クリックし、 **ネイティブ プッシュ通知** セクション。
    
    ![][2]

3. iOS の証明書/GCM サーバーの API キーの設定

    **(iOS)**

    a. .p12 を選択してアップロードし、パスワードを入力します。
    
    ![][3]

    **(Android)**

    a. 前に編集アイコンをクリックして **API キー** GCM 設定] セクションと表示され、ポップアップで GCM サーバー キーを貼り付けて] をクリックして **OK**します。 
        
    ![][4]

###Cordova アプリでのプッシュ通知の有効化

編集 **www/js/index.js** Mobile engagement プッシュ通知の要求し、ハンドラーの宣言への呼び出しを追加します。

     onDeviceReady: function() {
            app.receivedEvent('deviceready');
            AzureEngagement.registerForPushNotification();
            AzureEngagement.onOpenURL(function(_url) { alert(_url); });
            AzureEngagement.startActivity("myPage",{});
        }

###アプリの実行

**(iOS)**

1. iOS では実在するデバイスへのプッシュ通知だけが許可されているため、XCode を使用してアプリをビルドし、デバイスにデプロイして、プッシュ通知をテストします。 Cordova プロジェクトを作成する場所に移動して **...\platforms\ios** 場所です。 XCode でネイティブ .xcodeproj ファイルを開きます。 
    
2. 先ほど Mobile Engagement ポータルにアップロードした証明書と、Cordova アプリの作成中に指定した App Id を含むプロビジョニング プロファイルが設定されたアカウントを使用して、Cordova アプリをビルドして iOS デバイスにデプロイします。 チェック アウトすることができます、 *Bundle identifier* で、 **\ *-info.plist** 照合 XCode 内のファイルです。 

3. デバイスに、アプリが通知を送信する許可を要求したことを示す標準の iOS ポップアップが表示されます。 送信を許可します。 

**(Android)**

GCM 通知は Android エミュレーターでサポートされているため、エミュレーターを使用するだけで Android アプリを実行することができます。 

    cordova run android

##<a id="send"></a>アプリへ通知を送信します。

デバイスで実行されているアプリにプッシュ通知を送る簡単なプッシュ通知キャンペーンを作成してみましょう。

1. 移動し、 **到達** モバイル エンゲージメント ポータルでタブをクリックします。

2. クリックして **新しいお知らせ** プッシュ キャンペーンを作成するには

    ![][6]

3. 入力にキャンペーンを作成する **(Android)**
    
    - 提供、 **名前** キャンペーン。 
    - 選択、 **配信タイプ** として *システム通知* *単純な*
    - 配信時刻を選択して *"いつでも]*
    - 提供、 **タイトル** プッシュの最初の行となる、通知します。
    - 提供、 **メッセージ** 、通知メッセージの本文として動作するのです。 

    ![][11]

4. 入力にキャンペーンを作成する **[iOS]**

    - 提供、 **名前** キャンペーン。 
    - 配信時刻を選択して *"アプリ外のみ]*
    - 提供、 **タイトル** プッシュの最初の行となる、通知します。
    - 提供、 **メッセージ** 、通知メッセージの本文として動作するのです。 
 
    ![][12]

5. スクロール ダウンし、コンテンツ セクションの選択で **通知のみ**

    ![][8]

6. (オプション) アクション URL を指定することもできます。 プラグインの提供された URL スキームが使用されていることを確認 **AZME REDIRECT URL** 変数など *myapp://test*します。  

7. 最も基本的なキャンペーンの設定が完了しました。 ここでもう一度下へスクロールし、クリックして、 **作成** ] ボタンをクリックしてキャンペーンを保存します。

8. 最後に **Activate** キャンペーン
    
    ![][10]

9. このキャンペーンでは、デバイスまたはエミュレーターにプッシュ通知が表示されるはずです。 

##<a id="next-steps"></a>次のステップ
[Cordova Mobile Engagement SDK で使用できるすべてのメソッドの概要](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png

[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png


