<properties 
    pageTitle="Azure App Service を使用して Xamarin.Forms アプリにプッシュ通知を追加する" 
    description="Azure App Service を使用して Xamarin.Forms アプリにプッシュ通知を送信する方法について説明します" 
    services="app-service\mobile" 
    documentationCenter="xamarin" 
    authors="wesmc7777"
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-xamarin" 
    ms.devlang="dotnet" 
    ms.topic="article"
    ms.date="11/25/2015" 
    ms.author="wesmc"/>


# Xamarin.Forms アプリにプッシュ通知を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概要

このチュートリアルに基づいて、 [Xamarin.Forms のクイック スタート チュートリアル](app-service-mobile-xamarin-forms-get-started.md), 、最初に完了する必要があります。 ここでは、Xamarin.Forms クイック スタート プロジェクトでサポートする各プロジェクトに、プッシュ通知のサポートを追加します。 レコードが挿入されるたびに、プッシュ通知が送信されます。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、次を参照してください。 [Azure モバイル アプリの .NET バックエンド サーバー SDK と連携](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)します。

[IOS シミュレーターはプッシュ通知をサポートしない](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), ため、物理 iOS デバイスを使用する必要があります。 サインアップする必要も、 [Apple Developer Program メンバーシップ](https://developer.apple.com/programs/ios/)します。

## 前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント  
アカウントがない場合は、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手してください。 このアプリは、評価終了後も使用できます。 参照してください [Azure 無料評価版](http://azure.microsoft.com/pricing/free-trial/)します。

* Mac を [Xamarin Studio] と [Xcode] v4.4 以降がインストールされています。 必要であれば Windows コンピューター上で Visual Studio を使用して Xamarin.Forms を実行できますが、Xamarin.iOS Build Host を実行するネットワーク接続された Mac にアクセスする必要があるため、少し複雑になります。 これを行うに知りたい場合は、[windows Xamarin.iOS をインストールする] を参照してください。

* 物理的な iOS デバイス iOS シミュレーターでは、プッシュ通知はサポートされていません。

* 完了、 [Xamarin.Forms のクイック スタート チュートリアル](app-service-mobile-xamarin-forms-get-started.md)します。

## モバイル アプリ用の通知ハブを作成する

通知を送信するようにアプリを構成するには、新しい通知ハブを作成し、使用するプラットフォームの通知サービス向けに構成します。

次の手順で、通知ハブを順を追って新規作成します。 既に作成された通知ハブがある場合は、その通知ハブを選択するだけで済みます。

1. ログイン、 [Azure ポータル](https://portal.azure.com/)します。 **[参照]**、**[Mobile Apps]**、既存のバックエンド、**[設定]**、**[モバイル]**、**[プッシュ]**、**[通知ハブ]**、**[+ 通知ハブ]** の順にクリックし、新しい通知ハブの名前と名前空間を指定してから、**[OK]** をクリックします。

    ![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. [通知ハブの作成] ブレードで、**[作成]** をクリックします。


## プッシュ通知を送信するようにサーバー プロジェクトを更新する

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## 更新したサーバー プロジェクトを Azure にデプロイする

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


## (省略可能) Android プロジェクトを構成して実行する

このセクションでは、Android 用の Xamarin Android プロジェクトを実行します。 Android デバイスを使用していない場合は、このセクションを省略できます。


#### Google Cloud Messaging (GCM) を有効にする

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


#### GCM 用に通知ハブを構成する

1. ログイン、 [Azure ポータル](https://portal.azure.com/)します。 **[参照]**、**[Mobile Apps]**、作成したモバイル アプリ、**[設定]**、**[プッシュ]**、**[Google (GCM)]** の順にクリックします。 前に作成したサーバーの API キーを貼り付けて、**[保存]** をクリックします。 これで、Android 用のプッシュ通知と連携するようにサービスが構成されました。

    ![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


#### Android プロジェクトにプッシュ通知を追加する

1. Components フォルダーを右クリックし、[その他のコンポーネントを取得...] をクリックし、**[Google Cloud Messaging Client]** コンポーネントを検索し、それをプロジェクトに追加します。 このコンポーネントにより、Xamarrin Android プロジェクトでプッシュ通知を簡単に操作できます。

2. MainActivity.cs プロジェクト ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

        using Gcm.Client;

3. 次のコードを追加、 `OnCreate` メソッド呼び出しの後に `LoadApplication`します。

     try
     {
         // Check to ensure everything's setup right
         GcmClient.CheckDevice(this);
         GcmClient.CheckManifest(this);
    
         // Register for push notifications
         System.Diagnostics.Debug.WriteLine("Registering...");
         GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
     }
     catch (Java.Net.MalformedURLException)
     {
         CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
     }
     catch (Exception e)
     {
         CreateAndShowDialog(e.Message, "Error");
     }

4. 次のコードを追加、 `CreateAndShowDialog` ヘルパー メソッドです。

     private void CreateAndShowDialog(String message, String title) 
     {
         AlertDialog.Builder builder = new AlertDialog.Builder(this);
    
         builder.SetMessage (message);
         builder.SetTitle (title);
         builder.Create().Show ();
     }

5. `MainActivity` クラスでを現在を公開する次のコードを追加 `MainActivity` メイン UI スレッド上の UI を実行します。

     // Create a new instance field for this activity.
     static MainActivity instance = null;
    
     // Return the current activity instance.
     public static MainActivity CurrentActivity
     {
         get
         {
             return instance;
         }
     }

6. 初期化、 `インスタンス`, 、変数の先頭に、 `MainActivity.OnCreate` メソッドです。

        // Set the current instance of MainActivity.
        instance = this;

7. 新しいクラス ファイルを **Droid** プロジェクトに追加します。 新しいクラス ファイルに **GcmService** という名前を付けます。

8. 次の確認 `を使用して` ファイルの上部にあるステートメントを追加します。

        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Android.App;
        using Android.Content;
        using System.Collections.Generic;
        using System.Diagnostics;
        using Android.Util;
        using Newtonsoft.Json.Linq;
        using System.Text;
        using System.Linq;

9. 後に、ファイルの上部にある次のアクセス許可要求を追加、 `を使用して` ステートメントとする前に、 `名前空間` 宣言します。

     [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
     [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
     [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
    
     //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
     [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
     [assembly: UsesPermission(Name = "android.permission.INTERNET")]
     [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. 名前空間に次のクラス定義を追加します。置換 * *<PROJECT_NUMBER>* * 前にメモしたプロジェクト番号です。

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]     
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {       
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };        
        }

11. 更新プログラム、 `GcmService` 新しいブロードキャスト レシーバーを使用するクラス。

     [Service]
     public class GcmService : GcmServiceBase
     {
         public static string RegistrationID { get; private set; }
    
         public GcmService()
             : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
     }

12. OnRegistered イベント ハンドラーと実装をオーバーライドする GcmService クラスに次のコードを追加、 `登録` メソッドです。

このコードを使用してテンプレート通知を受信するテンプレートの本文を登録、 `messageParam` パラメーター。 テンプレート通知を使用すると、クロスプラットフォームの通知を送信できます。 詳細については、「 [テンプレート](https://msdn.microsoft.com/library/azure/dn530748.aspx)します。

    protected override void OnRegistered(Context context, string registrationId)
    {
        Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
        RegistrationID = registrationId;
    
        createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");
    
        var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    
        MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
    
    }
    
    public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
    {
        try
        {
            const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
              {"body", templateBodyGCM}
            };
    
            await push.RegisterAsync(RegistrationID, templates);
            Log.Info("Push Installation Id", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
            Debugger.Break();
        }
    }

13. 実装する必要があります `OnMessage` 受信のプッシュ通知を処理します。 このコードでは、通知を処理して、通知マネージャーに送信します。

    protected override void OnMessage(Context context, Intent intent)
    {
        Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");
    
        var msg = new StringBuilder();
    
        if (intent != null && intent.Extras != null)
        {
            foreach (var key in intent.Extras.KeySet())
                msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
        }
    
        //Store the message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();
    
        string message = intent.Extras.GetString("message");
        if (!string.IsNullOrEmpty(message))
        {
            createNotification("New todo item!", "Todo item: " + message);
            return;
        }
    
        string msg2 = intent.Extras.GetString("msg");
        if (!string.IsNullOrEmpty(msg2))
        {
            createNotification("New hub message!", msg2);
            return;
        }
    
        createNotification("Unknown message details", msg.ToString());
    }
    
    void createNotification(string title, string desc)
    {
        //Create notification
        var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
    
        //Create an intent to show ui
        var uiIntent = new Intent(this, typeof(MainActivity));
    
        //Create the notification
        var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
    
        //Auto cancel will remove the notification once the user touches it
        notification.Flags = NotificationFlags.AutoCancel;
    
        //Set the notification info
        //we use the pending intent, passing our ui intent over which will get called
        //when the notification is tapped.
        notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
    
        //Show the notification
        notificationManager.Notify(1, notification);
    }

14. 実装することもあります `OnUnRegistered` と `OnError` 、受信側のハンドラーです。

    protected override void OnUnRegistered(Context context, string registrationId)
    {
        Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
    }
    
    protected override void OnError(Context context, string errorId)
    {
        Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
    }




#### Android アプリでプッシュ通知をテストする

1. Visual Studio または Xamarin Studio で、**droid** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. **[Run]** ボタンを押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。
    > [AZURE.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。 これが必要であるのは、初めてアプリケーションを実行するときだけです。

2. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。





## (省略可能) iOS プロジェクトを構成して実行する

このセクションでは、iOS デバイス用の Xamarin iOS プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

[AZURE.INCLUDE [Notification Hubs Xamarin Enable Apple Push Notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


#### APNS 用に通知ハブを構成する

1. ログイン、 [Azure ポータル](https://portal.azure.com/)します。 **[参照]**、**[Mobile Apps]**、作成したモバイル アプリ、**[設定]**、**[プッシュ]**、**[Apple (APNS)]**、**[証明書のアップロード]** の順にクリックします。 以前にエクスポートした .p12 プッシュ証明書ファイルをアップロードします。 開発とテスト用に開発プッシュ証明書を作成した場合は、**[サンドボックス]** を選択します。 それ以外の場合は、**[運用]** を選択します。 これで、iOS のプッシュ通知と連携するようにサービスが構成されました。

    ![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

    次に、Xamarin Studio または Visual Studio で iOS のプロジェクト設定を構成します。

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


#### iOS アプリへのプッシュ通知の追加

1. 次の追加 `を使用して` ステートメントの先頭に、 **AppDelegate.cs** ファイルです。

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. IOS プロジェクトを開き AppDelegate.cs 更新`FinishedLaunching` 次のようにリモート通知をサポートします。

     public override bool FinishedLaunching (UIApplication app, NSDictionary options)
     {
         global::Xamarin.Forms.Forms.Init ();
    
         Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
    
         // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
         // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
         //SQLitePCL.CurrentPlatform.Init();
    
         // registers for push for iOS8
         var settings = UIUserNotificationSettings.GetSettingsForTypes(
             UIUserNotificationType.Alert
             | UIUserNotificationType.Badge
             | UIUserNotificationType.Sound,
             new NSSet());
    
         UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
         UIApplication.SharedApplication.RegisterForRemoteNotifications();
    
         LoadApplication (new App ());
    
         return base.FinishedLaunching (app, options);
     }

4. AppDelegate.cs で、通知に登録するために、**RegisteredForRemoteNotifications** イベントのオーバーライドを追加します。

     public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
     {
         const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
    
         JObject templates = new JObject();
         templates["genericMessage"] = new JObject
             {
               {"body", templateBodyAPNS}
             };
    
         // Register for push with your mobile app
         Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
         push.RegisterAsync(deviceToken, templates);
     }

5. AppDelegate.cs で、アプリの実行中に受信した通知を処理するために、**DidReceivedRemoteNotification** イベントのオーバーライドを追加します。

     public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
     {
         NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;
    
         string alert = string.Empty;
         if (aps.ContainsKey(new NSString("alert")))
             alert = (aps[new NSString("alert")] as NSString).ToString();
    
         //show alert
         if (!string.IsNullOrEmpty(alert))
         {
             UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
             avAlert.Show();
         }
     }


これで、アプリケーションがプッシュ通知をサポートするように更新されました。

#### iOS アプリでプッシュ通知をテストする

1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. Visual Studio で **[実行]** または **F5** キーを押して、プロジェクトをビルドし、iOS 対応デバイスでアプリケーションを起動します。**[OK]** をクリックして、プッシュ通知を受け入れます。
    > [AZURE.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。 これが必要であるのは、初めてアプリケーションを実行するときだけです。

3. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。

4. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。




## (省略可能) Windows プロジェクトを構成して実行する

このセクションでは、Windows デバイス用の Xamarin WinApp プロジェクトを実行します。 Windows デバイスを使用していない場合は、このセクションを省略できます。


#### WNS を使用して Windows アプリをプッシュ通知に登録する

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


#### WNS 用に通知ハブを構成する

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


#### Windows アプリにプッシュ通知を追加する

1. Visual Studio で、**WinApp** プロジェクトにある **App.xaml.cs** を開きます 次の追加 `を使用して` ステートメントです。

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using WesmcMobileAppGaTest;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. App.xaml.cs で次のコードを追加 `InitNotificationsAsync` メソッドです。 このメソッドによって、プッシュ通知チャネルが取得され、通知ハブからテンプレート通知を受け取るためのテンプレートが登録されます。 テンプレート通知をサポートする `messageParam` このクライアントに配信されます。

     private async Task InitNotificationsAsync()
     {
         var channel = await PushNotificationChannelManager
             .CreatePushNotificationChannelForApplicationAsync();
    
         const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";
    
         JObject headers = new JObject();
         headers["X-WNS-Type"] = "wns/toast";
    
         JObject templates = new JObject();
         templates["genericMessage"] = new JObject
             {
               {"body", templateBodyWNS},
               {"headers", headers} // Only needed for WNS & MPNS
             };
    
         await TodoItemManager.DefaultManager.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
     }

3. App.xaml.cs の更新プログラムで、 `OnLaunched` イベント ハンドラーで、 `async` 属性と呼び出し `InitNotificationsAsync`

     protected async override void OnLaunched(LaunchActivatedEventArgs e)
     {
         Frame rootFrame = Window.Current.Content as Frame;
    
         // Do not repeat app initialization when the Window already has content,
         // just ensure that the window is active
         if (rootFrame == null)
         {
             // Create a Frame to act as the navigation context and navigate to the first page
             rootFrame = new Frame();
             // Set the default language
             rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
             rootFrame.NavigationFailed += OnNavigationFailed;
             Xamarin.Forms.Forms.Init(e); 
             if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
             {
                 //TODO: Load state from previously suspended application
             }
             // Place the frame in the current Window
             Window.Current.Content = rootFrame;
         }
    
         if (rootFrame.Content == null)
         {
             // When the navigation stack isn't restored navigate to the first page,
             // configuring the new page by passing required information as a navigation
             // parameter
             rootFrame.Navigate(typeof(MainPage), e.Arguments);
         }
         // Ensure the current window is active
         Window.Current.Activate();
    
         await InitNotificationsAsync();
     }

4. Visual Studio のソリューション エクスプローラーで、**Package.appxmanifest** ファイルを開き、**[通知]** で **[トースト対応]** を **[はい]** に設定します。

5. アプリをビルドし、エラーがないことを確認します。 これで、クライアント アプリケーションが、モバイル アプリ バックエンドから送信されるテンプレート通知に登録されました。


#### Windows アプリでプッシュ通知をテストする

1. Visual Studio で、**WinApp** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. **[Run]** ボタンを押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。
    > [AZURE.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。 これが必要であるのは、初めてアプリケーションを実行するときだけです。

3. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。

4. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。







[xamarin studio]: http://xamarin.com/platform 
[install xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532 
[xcode]: https://go.microsoft.com/fwLink/?LinkID=266532 
[installing xamarin.ios on windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/ 
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333 

