<properties
    pageTitle="アプリ モデル v2.0 .NET ネイティブ アプリ | Microsoft Azure"
    description="サインインに個人の Microsoft アカウントと職場/学校アカウントの両方を使用する .NET ネイティブ アプリを構築する方法を説明します。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: Windows デスクトップ アプリへのサインインの追加

v2.0 アプリ モデルを使用すると、Microsoft の個人および職場/学校アカウントの両方に対応したデスクトップ アプリに認証をすばやく追加できます。  アプリのバックエンドで安全に通信することもできます web api、両方のいくつか、 [Office 365 統合 Api](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)します。

> [AZURE.NOTE]
    この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。  一般的に使用できる Azure AD と統合する方法については、サービスを参照してください、 [Azure Active Directory 開発者ガイド 』](active-directory-developers-guide.md)します。

 [デバイス上で実行される .NET ネイティブ アプリ](active-directory-v2-flows.md#mobile-and-native-apps), 、Azure AD は、Active Directory 認証ライブラリ、または ADAL を提供します。  ADAL の唯一の目的は、アプリが Web サービスを呼び出すためのトークンを容易に取得できるようにすることです。  どれほど簡単かを示すために、ここで、次のような、.NET WPF To-Do List アプリを構築します。

-   ユーザーのサインインし、アクセスを使用してトークンを取得、 [OAuth 2.0 認証プロトコル](active-directory-v2-protocols.md#oauth2-authorization-code-flow)します。
-   バックエンド To-Do List Web サービスを安全に呼び出します。これは、OAuth 2.0 でも保護されます。
-   ユーザーのサインアウト処理を行います。

完全に動作するアプリを構築するには、次の手順を行う必要があります。

2. アプリを登録します。
3. ADAL をインストールおよび構成する
5. ADAL を使用して、Azure AD からトークンを取得する

このチュートリアルのコードは保持 [github](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet)します。  理解するには [.zip としてアプリのスケルトンをダウンロードする](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) またはスケルトンを複製。

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

完成したアプリは、このチュートリアルの終わりにも示しています。

## 1.アプリを登録します
新しいアプリを作成する [apps.dev.microsoft.com](https://apps.dev.microsoft.com), 、次の [詳細な手順について](active-directory-v2-app-registration.md)します。  次のことを確認します。

- 下のコピー、 **アプリケーション Id** をアプリに割り当てる必要があります、すぐにします。
- 追加、 **Mobile** アプリのプラットフォームです。
- 下のコピー、 **リダイレクト URI** ポータルからです。 既定値の `urn:ietf:wg:oauth:2.0:oob`を使用する必要があります。

## 2.ADAL のインストールと構成
マイクロソフトへのアプリの登録が完了したら、ADAL をインストールし、自分の ID 関連コードを記述できます。  ADAL が v2.0 エンドポイントと通信できるようにするには、アプリの登録に関するいくつかの情報を ADAL に提供する必要があります。

-   最初に、パッケージ マネージャー コンソールを使用して、ADAL を TodoListClient プロジェクトに追加します。

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease
```

-   TodoListClient プロジェクトで `app.config` を開きます。  アプリ登録ポータルで入力した値が反映されるように、`<appSettings>` セクションの要素の値を置き換えます。  これらの値は、コードで ADAL を使用する際に常に参照されます。
    -    `ida:ClientId` は、 **アプリケーション Id** 、ポータルからコピーしたアプリのです。
    -    `ida:RedirectUri` は、 **リダイレクト Uri** ポータルからです。
- TodoList-Service プロジェクトで、プロジェクトのルートにある `web.config` を開きます。  
    - 置き換える、 `ida:Audience` 値では、同じ **アプリケーション Id** ポータルからです。

## 3.ADAL を使用してトークンを取得します
ADAL を使用することの基本的なメリットは、アプリがアクセス トークンを必要とする場合、必要な操作は `authContext.AcquireToken(...)` を呼び出すことだけで、残りの処理は ADAL が実行することです。  

-   `TodoListClient` プロジェクトで、`MainWindow.xaml.cs` を開き、`OnInitialized(...)` メソッドを見つけます。  最初の手順は、アプリの `AuthenticationContext` (ADAL のプライマリ クラス) を初期化することです。  ここでは、ADAL が Azure AD と通信し、トークンをキャッシュする方法を通知するために必要な調整項目を ADAL に渡します。

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        authContext = new AuthenticationContext(authority, new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- アプリの起動時に、ユーザーが既にアプリにサインインしているかどうかを確認する必要があります。  ただし、まだサインイン UI は呼び出しません。これを行うには、ユーザーに [サインイン] をクリックさせます。  また、`OnInitialized(...)` メソッドで次のコードを実行します。

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
        result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

        // If we got here, a valid token is in the cache.  Proceed to
        // fetch the user's tasks from the TodoListService via the
        // GetTodoList() method.

        SignInButton.Content = "Clear Cache";
        GetTodoList();
}
catch (AdalException ex)
{
        if (ex.ErrorCode == "user_interaction_required")
        {
                // If user interaction is required, the app should take no action,
                // and simply show the user the sign in button.
        }
        else
        {
                // Here, we catch all other AdalExceptions

                string message = ex.Message;
                if (ex.InnerException != null)
                {
                        message += "Inner Exception : " + ex.InnerException.Message;
                }
                MessageBox.Show(message);
        }
}
```

- ユーザーがサインインしておらず、[サインイン] ボタンをクリックした場合、ログイン UI を呼び出し、ユーザーに資格情報を入力させます。  次のように、[サインイン] ボタン ハンドラーを実装します:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

        // If the user clicked the 'Sign-In' button, force
        // ADAL to prompt the user for credentials by specifying
        // PromptBehavior.Always.  ADAL will get a token for the
        // TodoListService and cache it for you.

        AuthenticationResult result = null;
        try
        {
                result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
                SignInButton.Content = "Clear Cache";
                GetTodoList();
        }
        catch (AdalException ex)
        {
                // If ADAL cannot get a token, it will throw an exception.
                // If the user canceled the login, it will result in the
                // error code 'authentication_canceled'.

                if (ex.ErrorCode == "authentication_canceled")
                {
                        MessageBox.Show("Sign in was canceled by the user");
                }
                else
                {
                        // An unexpected error occurred.
                        string message = ex.Message;
                        if (ex.InnerException != null)
                        {
                                message += "Inner Exception : " + ex.InnerException.Message;
                        }

                        MessageBox.Show(message);
                }

                return;
        }

}
```

- ユーザーが正常にサインインすると、ADAL によりトークンが自動的に受信してキャッシュされ、`GetTodoList()` メソッドの呼び出しを実行できます。  ユーザーのタスクを取得するために必要な操作は、`GetTodoList()` メソッドの実装だけです。

```C#
private async void GetTodoList()
{
        AuthenticationResult result = null;
        try
        {
                // Here, we try to get an access token to call the TodoListService
                // without invoking any UI prompt.  PromptBehavior.Never forces
                // ADAL to throw an exception if it cannot get a token silently.

                result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
        }
        catch (AdalException ex)
        {
                // ADAL couldn't get a token silently, so show the user a message
                // and let them click the Sign-In button.

                if (ex.ErrorCode == "user_interaction_required")
                {
                        MessageBox.Show("Please sign in first");
                        SignInButton.Content = "Sign In";
                }
                else
                {
                        // In any other case, an unexpected error occurred.

                        string message = ex.Message;
                        if (ex.InnerException != null)
                        {
                                message += "Inner Exception : " + ex.InnerException.Message;
                        }
                        MessageBox.Show(message);
                }

                return;
        }

        // Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the ADAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                authContext.TokenCache.Clear();
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

ご利用ありがとうございます。 ユーザーを認証し、OAuth 2.0 を使用して安全に Web API を呼び出すことができる、.NET WPF アプリが動作するようになりました。  両方のプロジェクトを実行し、個人用の Microsoft アカウントか職場または学校アカウントでサインインしてください。  ユーザーの To Do リストにタスクを追加します。  いったんサインアウトしてから、別のユーザーとして再度サインインし、ユーザーの To Do リストを表示します。  アプリを閉じて、再び実行します。  ユーザーのセッションに影響がないことを確認してください。これは、アプリがトークンをローカル ファイルにキャッシュしているためです。

ADAL では、個人用アカウントと職場アカウントの両方を使用して、一般的な ID 機能をアプリに簡単に組み込むことができます。  キャッシュ管理、OAuth プロトコル サポート、ログイン UI を使用してのユーザーの提示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。  習得する必要があるのは、単一の API 呼び出し、`authContext.AcquireTokenAsync(...)` のみです。

リファレンスについては、完全なサンプル (構成値) を除く [.zip をここでは現状](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), 、または GitHub から複製することができます。

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## 次のステップ

これ以降は、さらに高度なトピックに進むことができます。  次のチュートリアルを試してみてください。

- [v2.0 アプリ モデルでの TodoListService Web API の保護 >>](active-directory-v2-devquickstarts-dotnet-api.md)

その他のリソースについては、以下を参照してください。
- [アプリ モデル v2.0 プレビュー >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow"adal"タグ >>](http://stackoverflow.com/questions/tagged/adal)

