<properties
    pageTitle="Azure Notification Hubs の安全なプッシュ"
    description="Azure でセキュリティで保護されたプッシュ通知を送信する方法について説明します。コード サンプルは .NET API を使用して C# で記述されています。"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="wesmc"/>


# Azure Notification Hubs の安全なプッシュ

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-windows-dotnet-secure-push.md)
- [iOS](notification-hubs-aspnet-backend-ios-secure-push.md)
- [Android](notification-hubs-aspnet-backend-android-secure-push.md)



## 概要

Microsoft Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。

規制やセキュリティの制約により、アプリケーションでは、標準のプッシュ通知インフラストラクチャからは転送できないものを通知に含める必要がある場合があります。 このチュートリアルでは、クライアントのデバイスとアプリケーションのバックエンドとの間の安全で認証された接続を通して機密情報を送信することによって、同じエクスペリエンスを実現する方法について説明します。

大まかには、フローは次のようになります。

1. アプリケーションのバックエンド:
    - バックエンド データベースに安全なペイロードを格納します。
    - この通知の ID をデバイスに送信します (安全でない情報は送信しません)。
2. 通知を受け取ったときのデバイスのアプリケーション:
    - デバイスは、安全なペイロードを要求するバックエンドにアクセスします。
    - アプリケーションはデバイスに通知としてペイロードを表示できます。

重要なのは、前のフロー (およびこのチュートリアル) では、デバイスは、ユーザーがログインした後、認証トークンをローカル ストレージに保存すると想定していることです。 デバイスはこのトークンを使用して通知の安全なペイロードを取得できるため、これによって完全にシームレスなエクスペリエンスが保証されます。 アプリケーションがデバイスに認証トークンを格納しない、またはそれらのトークンが期限切れの場合、デバイスのアプリケーションは、通知を受け取ったときにアプリケーションの起動を促す一般的な通知を表示する必要があります。 その後、アプリケーションはユーザーを認証し、通知ペイロードを表示します。

この安全なプッシュのチュートリアルでは、プッシュ通知を安全に送信する方法を説明します。 チュートリアルに基づいて記述、 [ユーザーへの通知](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) チュートリアルでは、最初にそのチュートリアルの手順を完了する必要がありますので。
> [AZURE.NOTE] このチュートリアルを作成し、」の説明に従って通知ハブを構成 [Notification Hubs (Windows ストア) の概要](notification-hubs-windows-store-dotnet-get-started.md)します。
また、Windows Phone 8.1 には (Windows Phone ではなく) Windows の資格情報が必要で、Windows Phone 8.0 または Silverlight 8.1 ではバックグラウンド タスクが機能しないことに注意してください。 Windows ストア アプリケーションの場合は、アプリケーションでロック画面が有効 (Appmanifest でチェック ボックスをオンにする) な場合にだけバックグラウンド タスクから通知を受信できます。

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## Windows Phone プロジェクトを変更する

1. **NotifyUserWindowsPhone** プロジェクトで、次のコードを App.xaml.cs に追加して、プッシュ バックグラウンド タスクを登録します。 末尾に次のコード行を追加、 `OnLaunched()` メソッド。

        RegisterBackgroundTask();

2. 引き続き App.xaml.cs で次のコードを追加した直後にコード、 `OnLaunched()` メソッド。

     private async void RegisterBackgroundTask()
     {
         if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
         {
             var result = await BackgroundExecutionManager.RequestAccessAsync();
             var builder = new BackgroundTaskBuilder();
    
             builder.Name = "PushBackgroundTask";
             builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
             builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
             BackgroundTaskRegistration task = builder.Register();
         }
     }

3. 次の追加 `を使用して` App.xaml.cs ファイルの上部にあるステートメント。

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. Visual Studio の **[ファイル]** メニューで **[すべて保存]** をクリックします。

## プッシュ バックグラウンド コンポーネントを作成する

次の手順では、プッシュ バックグラウンド コンポーネントを作成します。

1. ソリューション エクスプローラーで、ソリューションの最上位ノード (この場合は、**Solution SecurePush**) を右クリックし、**[追加]**、**[新しいプロジェクト]** の順にクリックします。

2. **[ストア アプリ]** を展開し、**[Windows Phone アプリ]**、**[Windows ランタイム コンポーネント (Windows Phone)]** の順にクリックします。 プロジェクトの名前として「**PushBackgroundComponent**」と入力し、**[OK]** をクリックしてプロジェクトを作成します。

    ![][12]

3. ソリューション エクスプローラーで、**PushBackgroundComponent (Windows Phone 8.1)** プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 新しいクラスに「**PushBackgroundTask.cs**」という名前を付けます。 **[追加]** をクリックしてクラスを生成します。

4. 内容全体を置き換える、 **PushBackgroundComponent** 名前空間定義を次のコードで、プレース ホルダー `{バック エンド endpoint}` をバックエンドのデプロイ時に取得したバックエンド エンドポイント。

     public sealed class Notification
         {
             public int Id { get; set; }
             public string Payload { get; set; }
             public bool Read { get; set; }
         }
    
         public sealed class PushBackgroundTask : IBackgroundTask
         {
             private string GET_URL = "{back-end endpoint}/api/notifications/";
    
             async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
             {
                 // Store the content received from the notification so it can be retrieved from the UI.
                 RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                 var notificationId = raw.Content;
    
                 // retrieve content
                 BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                 var httpClient = new HttpClient();
                 var settings = ApplicationData.Current.LocalSettings.Values;
                 httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
    
                 var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);
    
                 var notification = JsonConvert.DeserializeObject<Notification>(notificationString);
    
                 ShowToast(notification);
    
                 deferral.Complete();
             }
    
             private void ShowToast(Notification notification)
             {
                 ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                 XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                 XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                 toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                 ToastNotification toast = new ToastNotification(toastXml);
                 ToastNotificationManager.CreateToastNotifier().Show(toast);
             }
         }

5. ソリューション エクスプローラーで **PushBackgroundComponent (Windows Phone 8.1)** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

6. 左側で、**[オンライン]** をクリックします。

7. **[検索]** ボックスに、「**Http Client**」と入力します。

8. 結果の一覧で、**[Microsoft HTTP Client Libraries]**、**[インストール]** の順にクリックします。 インストールを完了します。

9. NuGet **[検索]** ボックスに戻り、「**Json.net**」と入力します。 **Json.NET** パッケージをインストールし、NuGet パッケージ マネージャーのウィンドウを閉じます。

10. 次の追加 `を使用して` ステートメントの先頭に、 **PushBackgroundTask.cs** ファイル。

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. ソリューション エクスプローラーで、**NotifyUserWindowsPhone (Windows Phone 8.1)** プロジェクトの **[参照]** を右クリックし、**[参照の追加]** をクリックします。 参照マネージャー ダイアログで、**PushBackgroundComponent** のチェック ボックスをオンにして、**[OK]** をクリックします。

12. ソリューション エクスプローラーで、**NotifyUserWindowsPhone (Windows Phone 8.1)** プロジェクトの **[Package.appxmanifest]** をダブルクリックします。 **[通知]** で、**[トースト対応]** を **[はい]** に設定します。

    ![][3]

13. 引き続き **Package.appxmanifest** で、上部の **[宣言]** メニューをクリックします。 **[使用可能な宣言]** ボックスで、**[バックグラウンド タスク]**、**[追加]** の順にクリックします。

14. **Package.appxmanifest** で、**[プロパティ]** の **[プッシュ通知]** チェック ボックスをオンにします。

15. **Package.appxmanifest** で、**[アプリ設定]** の **[エントリ ポイント]** フィールドに「**PushBackgroundComponent.PushBackgroundTask**」と入力します。

    ![][13]

16. **[ファイル]** メニューの **[すべて保存]** をクリックします。

## アプリケーションの実行

アプリケーションを実行するには、以下の手順に従います。

1. Visual Studio で、**AppBackend** Web API アプリケーションを実行します。 ASP.NET Web ページが表示されます。

2. Visual Studio で、**NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone アプリケーションを実行します。 Windows Phone エミュレーターが自動的に起動し、アプリケーションを読み込みます。

3. **NotifyUserWindowsPhone** アプリケーションの UI で、ユーザー名とパスワードを入力します。 文字列は任意ですが、値は同じである必要があります。

4. **NotifyUserWindowsPhone** アプリケーションの UI で、**[ログインして登録]** をクリックします。 次に、**[プッシュを送信する]** をクリックします。


[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png 
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png 
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png 

