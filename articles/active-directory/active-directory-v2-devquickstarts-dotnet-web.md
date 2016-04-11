<properties
    pageTitle="アプリ モデル v2.0 .NET Web アプリ | Microsoft Azure"
    description="サインインに個人の Microsoft アカウントと会社/学校アカウントの両方を使用する .NET MVC Web アプリを構築する方法を説明します。"
    services="active-directory"
    documentationCenter=".net"
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

# アプリ モデル v2.0 プレビュー: .NET MVC Web アプリへのサインインの追加

v2.0 アプリ モデルを使用すると、Microsoft の個人および職場/学校アカウントの両方に対応した Web アプリに認証をすばやく追加できます。  ASP.NET Web アプリでは、.NET Framework 4.5 に含まれる Microsoft の OWIN ミドルウェアを使用することにより、これを達成できます。

  > [AZURE.NOTE]
    この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。  一般的に使用できる Azure AD と統合する方法については、サービスを参照してください、 [Azure Active Directory 開発者ガイド 』](active-directory-developers-guide.md)します。

 ここでは、OWIN を使用して次のことを行います。
-   Azure AD と v2.0 アプリ モデルを使用するアプリにユーザーをサインインします。
-   ユーザーについての情報を表示します。
-   ユーザーをアプリからサインアウトします。

これを行うには、次の手順を実行する必要があります。

1. アプリを登録します。
2. OWIN 認証パイプラインを使用するようにアプリをセットアップする
3. OWIN を使用して、サインイン要求およびサインアウト要求を Azure AD に発行する
4. ユーザーに関するデータを印刷する

このチュートリアルのコードは保持 [github](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet)します。  理解するには [.zip としてアプリのスケルトンをダウンロードする](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) またはスケルトンを複製。

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

完成したアプリは、このチュートリアルの終わりにも示しています。

## 1.アプリを登録します
新しいアプリを作成する [apps.dev.microsoft.com](https://apps.dev.microsoft.com), 、次の [詳細な手順について](active-directory-v2-app-registration.md)します。  次のことを確認します。

- 下のコピー、 **アプリケーション Id** をアプリに割り当てる必要があります、すぐにします。
- 追加、 **Web** アプリのプラットフォームです。
- 正しい入力 **リダイレクト URI**します。 リダイレクト URI は、認証の応答が送られる Azure AD を示します。このチュートリアルの既定値は `https://localhost:44326/` です。

## 2.アプリで OWIN 認証パイプラインを使用するよう設定します。
ここでは、OpenID Connect 認証プロトコルを使用するように、OWIN ミドルウェアを構成します。  OWIN は、サインイン要求またはサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

-   最初に、プロジェクトのルートにある `web.config` ファイルを開いて、`<appSettings>` セクションでアプリの構成値を入力します。
    -    `ida:ClientId` は、 **アプリケーション Id** 登録ポータルでアプリに割り当てられます。
    -    `ida:RedirectUri` は、 **リダイレクト Uri** 、ポータルで入力します。

-   次に、パッケージ マネージャー コンソールを使用して、プロジェクトに OWIN ミドルウェア NuGet パッケージを追加します。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   クラスを追加、"OWIN スタートアップ"プロジェクトと呼ばれる `Startup.cs`  右プロジェクトをクリックして **追加** --> **[新しい項目の** "OWIN"を検索します。  アプリが起動すると、OWIN ミドルウェアは `Configuration(...)` メソッドを呼び出します。
-   クラス宣言を `public partial class Startup` に変更します。このクラスの一部は別ファイルで実装済みです。  `Configuration(...)` メソッドで、ConfigureAuth(...) を呼び出して、Web アプリ用の認証をセットアップします。  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}```

-   Open the file `App_Start\Startup.Auth.cs` and implement the `ConfigureAuth(...)` method.  The parameters you provide in `OpenIdConnectAuthenticationOptions` will serve as coordinates for your app to communicate with Azure AD.  You'll also need to set up Cookie Authentication - the OpenID Connect middleware uses cookies underneath the covers.

```C#
public void ConfigureAuth(IAppBuilder app)
             {
                     app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

                     app.UseCookieAuthentication(new CookieAuthenticationOptions());

                     app.UseOpenIdConnectAuthentication(
                             new OpenIdConnectAuthenticationOptions
                             {
                                     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                                     // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                     // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                                     ClientId = clientId,
                                     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                     RedirectUri = redirectUri,
                                     Scope = "openid",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## 3.OWIN を使用して、サインイン要求およびサインアウト要求を Azure AD に発行する
これまでに、アプリは、OpenID Connect 認証プロトコルを使用して v2.0 エンドポイントと適切に通信するように構成されています。  OWIN は、認証メッセージの構築、Azure AD からのトークンの検証、およびユーザー セッションの維持を行うためのすべての煩わしい処理を実行します。  OWIN により処理されないのは、ユーザーにサインインおよびサインアウトの方法を提供する処理のみです。

- コントローラーで承認タグを使用することにより、特定のページでは、サインインしてからでないとアクセスできないようにすることができます。  `Controllers\HomeController.cs` を開いて、`[Authorize]` タグを About コントローラーに追加します。

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   OWIN を使用することにより、コード内から認証要求を直接発行することもできます。  `Controllers\AccountController.cs` を開きます。  SignIn() アクションおよび SignOut() アクションで、それぞれ OpenID Connect チャレンジおよびサインアウト要求を発行します。

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   ここで、`Views\Shared\_LoginPartial.cshtml` を開きます。  ここは、ユーザーに、アプリのサインイン リンクおよびサインアウト リンクを表示し、ユーザーの名前をビュー内に表示する場所です。

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## 4.ユーザー情報を表示する
OpenID Connect を持つユーザーを認証するときに、バージョン 2.0 エンドポイントを返します。 id_token、アプリケーションが含まれていますが、 [クレーム](active-directory-v2-tokens.md#id_tokens), 、またはユーザーについてのアサーションです。  これらの要求を使用して、アプリを個人向けにカスタマイズすることができます。

- `Controllers\HomeController.cs` ファイルを開きます。  `ClaimsPrincipal.Current` セキュリティ プリンシパル オブジェクトを介して、コントローラー内のユーザー クレームにアクセスできます。

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

最後に、アプリを構築して実行します。   Microsoft の個人または職場/学校アカウントのいずれかでサインインすると、ユーザーの ID が上部のナビゲーション バーにどのように反映されるかがわかります。  これで、Web アプリが業界標準のプロトコルで保護され、個人および職場/学校アカウントの両方でユーザーを認証できるようになりました。

リファレンスについては、完全なサンプル (構成値) を除く [.zip をここでは現状](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), 、または GitHub から複製することができます。

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## 次のステップ

これ以降は、さらに高度なトピックに進むことができます。  次のチュートリアルを試してみてください。

[v2.0 アプリ モデルでの Web API の保護 >>](active-directory-devquickstarts-webapi-dotnet.md)

その他のリソースについては、以下を参照してください。
- [アプリ モデル v2.0 プレビュー >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow"azure active directory"タグ >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

