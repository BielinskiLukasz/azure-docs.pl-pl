<properties
    pageTitle="認証の使用 (Windows Phone) | Microsoft Azure"
    description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows Phone アプリのユーザーを認証する方法について説明します。"
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/02/2015"
    ms.author="glenga"/>


# Mobile Services アプリへの認証の追加

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

## 概要

このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。 このチュートリアルでは、Mobile Services でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。 モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

次のビデオで、Nick Harris によるこのチュートリアルのデモをご覧いただけます。

> [AZURE.VIDEO mobile-authorize-users-in-scripts-windows-phone]

このチュートリアルは、Mobile Services のクイック スタートに基づいています。 まず Mobile Services の追加、既存のアプリケーションに」チュートリアルを行う必要があります。
>[AZURE.NOTE]このチュートリアルでは、さまざまな ID プロバイダーを使用した、モバイル サービスによって管理される認証フローについて説明します。 この方法は構成が容易で、複数のプロバイダーをサポートしています。 クライアントによって管理される認証を使用することにより、アプリは ID プロバイダーが保持する追加のユーザー データにアクセスできます。 サーバー スクリプトで **user.getIdentities()** 関数を呼び出せば、モバイル サービス内にある同じユーザー データを取得できます。 詳細については、次を参照してください。 [この投稿](http://go.microsoft.com/fwlink/p/?LinkId=506605)します。

## <a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成します。

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a>認証されたユーザーへのアクセス許可を制限します。

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


&nbsp;&nbsp;3. Visual Studio 2012 Express for Windows Phone、チュートリアルを完了したときに作成したプロジェクトを開きます [既存のアプリケーションへの Mobile Services の追加](mobile-services-windows-phone-get-started-data.md)します。

&nbsp;&nbsp;4. F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリに認証を追加します。

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="tokens"></a>クライアントに認証トークンを保存します。

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>次のステップ

次のチュートリアルでは、 [モバイル サービス ユーザーのサービス側承認](mobile-services-javascript-backend-service-side-authorization.md), 、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、モバイル サービスから返されたデータをフィルター処理に使用します。





[register your app for authentication and configure mobile services]: #register 
[restrict table permissions to authenticated users]: #permissions 
[add authentication to the app]: #add-authentication 
[next steps]: #next-steps 
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png 
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png 
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png 
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png 
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png 
[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[add mobile services to an existing app]: mobile-services-windows-phone-get-started-data.md 
[authorize users with scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md 

