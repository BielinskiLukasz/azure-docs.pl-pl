<properties
    pageTitle="Xamarin Android アプリケーション用 Mobile Services の認証の使用 | Microsoft Azure"
    description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin Android アプリのユーザーを認証する方法について説明します。"
    services="mobile-services"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor="mollybos"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/01/2015" 
    ms.author="donnam"/>


# Mobile Services での認証の使用

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、アプリケーションから Azure Mobile Services のユーザーを認証する方法について説明します。 このチュートリアルでは、Mobile Services でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。 Mobile Services によって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [認証用アプリの登録し、Mobile Services を構成する]
2. [認証されたユーザーへのテーブルのアクセス許可を制限する]
3. [アプリに認証の追加]

このチュートリアルは、Mobile Services のクイック スタートに基づいています。 先にチュートリアル「Mobile Services の使用」を完了している必要があります。

## <a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成します。

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>認証されたユーザーへのアクセス許可を制限します。

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>Visual Studio または Xamarin Studio で、デバイスまたはシミュレーターを使用してクライアント プロジェクトを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
    <p>この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、 <em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリに認証を追加します。

1. **TodoActivity** クラスに次のプロパティを追加します。

            private MobileServiceUser user;

2. **TodoActivity** クラスに次のメソッドを追加します。

            private async Task Authenticate()
            {
                try
                {
                    user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
            }

    これで、認証プロセスを処理する新しいメソッドが作成されます。 ユーザーは、Facebook ログインを使用して認証されます。 認証されたユーザーの ID を示すダイアログが表示されます。
    > [AZURE.NOTE] Facebook 以外の ID プロバイダーを使用している場合は、上の例の **LoginAsync** に渡される値を _MicrosoftAccount_、_Twitter_、_Google_、_WindowsAzureActiveDirectory_ のいずれかに変更します。

3. **OnCreate** メソッドをインスタンス化するコードの後に次のコード行を追加、 `MobileServiceClient` オブジェクトです。

        await Authenticate(); // add this line

    この呼び出しは、認証プロセスを開始し、認証プロセスを非同期に待機します。

4. **[実行]** メニューの **[デバッグの開始]** をクリックしてアプリケーションを開始し、選択した ID プロバイダーでサインインします。

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、Mobile Services を照会してデータを更新できるようになります。







[register your app for authentication and configure mobile services]: #register 
[restrict table permissions to authenticated users]: #permissions 
[add authentication to the app]: #add-authentication 
[next steps]: #next-steps 
[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[my applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[live sdk for windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253 
[get started with mobile services]: mobile-services-dotnet-backend-xamarin-android-get-started.md 
[get started with authentication]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md 
[get started with push notifications]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md 
[authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md 
[javascript and html]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md 

