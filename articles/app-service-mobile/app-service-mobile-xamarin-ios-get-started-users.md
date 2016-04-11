<properties 
    pageTitle="Xamarin iOS での Mobile Apps の認証の使用" 
    description="Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin iOS アプリのユーザーを認証する方法について説明します。" 
    services="app-service\mobile" 
    documentationCenter="xamarin" 
    authors="mattchenderson" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="na" 
    ms.tgt_pltfrm="mobile-xamarin-ios" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="11/25/2015" 
    ms.author="mahender"/>

# Xamarin.iOS アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。 このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、Xamarin.iOS クイック スタート プロジェクトに認証を追加します。 モバイル アプリによって正常に認証され、承認されるとユーザー ID 値が表示され、制限付きのテーブル データにアクセスできます。

最初のチュートリアルを完了する必要があります [Create a Xamarin.iOS app]します。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、次を参照してください。 [Azure モバイル アプリの .NET バックエンド サーバー SDK と連携](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)します。 

##アプリケーションを認証に登録し、App Services を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)] 

##アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

&nbsp;&nbsp;4. Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターを使用してクライアント プロジェクトを実行します。 アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 デバッガーのコンソールに、エラーが記録されます。 Visual Studio では、[出力] ウィンドウでエラーを確認する必要があります。

&nbsp;&nbsp;この許可されていない問題は、アプリが認証されないユーザーとしてモバイル アプリのバックエンドにアクセスしようとするために発生します。  *TodoItem* テーブルには、今すぐ認証が必要です。

次に、認証されたユーザーで Mobile App のバックエンドからリソースを要求するように、クライアント アプリを更新します。

##アプリケーションに認証を追加する

ここでは、データを表示する前にログイン画面を表示するようにアプリケーションを変更します。 アプリケーションが起動したときには、App Service には接続されず、データも表示されません。 ユーザーが最初に更新操作を実行した後で、ログイン画面が表示されます。ログインに成功すると、Todo 項目の一覧が表示されます。

1. クライアント プロジェクトでファイルを開きます **QSTodoService.cs** し、以下の追加ステートメントを使用し、 `MobileServiceUser` QSTodoService クラスにアクセサーを持つ。

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2. という名前の新しいメソッドを追加 **認証** に **QSTodoService** を次の定義。


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] If you are using an identity provider other than a Facebook, change the value passed to **LoginAsync** above to one of the following: _MicrosoftAccount_, _Twitter_, _Google_, or _WindowsAzureActiveDirectory_.

3. 開いている **QSTodoListViewController.cs**します。 メソッド定義を変更 **ViewDidLoad** への呼び出しを削除する **RefreshAsync()** 最後に記載します。

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. メソッドを変更 **RefreshAsync** 場合認証のため、 **ユーザー** プロパティが null です。 メソッド定義の最初に次のコードを追加します。

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
    
5. Mac の Xamarin Build Host に接続しているVisual Studio または Xamarin Studio で、デバイスまたはエミュレーターをターゲットとしているクライアント プロジェクトを実行します。 アプリケーションにデータが表示されないことを確認します。 

    項目の一覧をプルダウンして更新操作を実行すると、ログイン画面が表示されます。 有効な資格情報を正しく入力すると、Todo 項目の一覧が表示され、データを更新できるようになります。

 
<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Create a Xamarin.iOS app]: app-service-mobile-xamarin-ios-get-started.md
 


