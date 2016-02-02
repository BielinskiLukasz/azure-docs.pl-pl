<properties
    pageTitle="Facebook 認証用に登録する | Azure Mobile Services"
    description="Azure Mobile Services アプリケーションで Facebook 認証を使用する方法について説明します。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="11/15/2015"
    ms.author="glenga"/>


# Mobile Services での Facebook 認証用のアプリケーションの登録

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

このトピックでは、Facebook を使用して Azure Mobile Services で認証できるようにアプリケーションを登録する方法について説明します。
>[AZURE.NOTE] このチュートリアルでは、[Azure Mobile Services], はあらゆるプラットフォームの拡張性の高いモバイル アプリケーションを構築するためのソリューションです。 Mobile Services によって簡単にデータの同期化を行い、ユーザーを認証して、プッシュ通知を送信できます。 このページをサポートしています、 [認証を使ってみる](mobile-services-ios-get-started-users.md) チュートリアル アプリにユーザーをログインさせる方法を説明します。 初めてのモバイル サービスを使用する場合は、このチュートリアルを完了してください [モバイル サービスを使ってみる](mobile-services-ios-get-started.md)します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。 新しい Facebook アカウントを作成するには [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285)します。

1. 移動し、 [Facebook Developers](http://go.microsoft.com/fwlink/p/?LinkId=268285) web サイトと、Facebook にログイン アカウントの資格情報。

2. (省略可能) まだ登録していない場合は、[**My Apps**]、[**Register as a Developer**] の順にクリックし、ポリシーに同意して、登録手順に従います。

3. [**My Apps**] 、[**Add a New App**] 、[**Advanced setup**] の順にクリックします。

4. [**Display name**] にアプリケーションの一意の名前を入力し、[**Category**] の [**Apps for Pages**] を選択してから、[**Create App ID**] をクリックしてセキュリティの手順を完了します。

    これにより、新しい Facebook アプリ ID が作成されます。

5. [**Settings**] をクリックして、[**App Domains**] にモバイル サービスのドメインを入力し、[**Contact Email**] を入力してから (省略可能)、[**Add Platform**] をクリックして [**Website**] を選択します。

    ![][3]

6. **[Site URL]** にモバイル サービスの URL を入力し、**[Save Changes]** をクリックします。

7. **[Show]** をクリックし、入力を求められたらパスワードを入力し、**[App ID]** と **[App Secret]** の値を書き留めておきます。

    ![][5]
    &nbsp;
    >[AZURE.IMPORTANT] アプリケーション シークレットは、重要なセキュリティ資格情報です。 このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。
    &nbsp;

8. **[Advanced]** タブをクリックし、次のいずれかの URL 形式を **[Valid OAuth redirect URIs]** に入力して、**[Save Changes]** をクリックします。

    + **.NET バックエンド**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
    + **JavaScript バックエンド**: `https://<mobile_service>.azure-mobile.net/login/facebook`
     >[AZURE.NOTE]Mobile Services バックエンドの種類として、正しいリダイレクト URL パスの形式を使用してください。 これが正しくない場合、認証は失敗します。

9. [**Status & Review**]、[**Yes**] の順にクリックし、アプリへの汎用パブリック アクセスを有効にします。

    新しいアプリを登録するために使用した Facebook アカウントは、アプリの管理者であり、アプリへの管理者としてのアクセス権があります。 この手順で汎用パブリック アクセスが与えられるため、アプリはその他の Facebook アカウントを使用して認証できるようになります。


これで、App ID と App Secret の値を Mobile Services に渡すことにより、Facebook ログインを使用してアプリケーションで認証を使用する準備ができました。






[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png 
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png 
[facebook developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286 
[get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/ 
[azure mobile services]: http://azure.microsoft.com/services/mobile-services/ 

