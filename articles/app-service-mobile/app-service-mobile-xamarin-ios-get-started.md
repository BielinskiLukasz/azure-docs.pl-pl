<properties
    pageTitle="Xamarin.iOS アプリに対して Azure App Service Mobile Apps を使用する | Microsoft Azure"
    description="このチュートリアルに従って、Xamarin.iOS 開発用の Azure Mobile Apps を使用します。"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="11/23/2015"
    ms.author="normesta"/>


#Xamarin.iOS アプリを作成する

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概要

このチュートリアルでは、Azure Mobile Apps バックエンドを使用して Xamarin.iOS モバイル アプリにクラウドベースのバックエンド サービスを追加する方法を示します。  新しいモバイル アプリ バックエンドと簡単なの両方を作成する _Todo リスト_ Xamarin.iOS アプリを Azure にアプリケーション データを保存します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Xamarin.iOS チュートリアルを実行する前に完了しておく必要があります。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント アカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。このアプリは評価終了後も使用できます。 詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)をご覧ください。

* [Visual Studio Community 2013] 以降。  Visual Studio Community 2013 をインストールする場合は、インストール [Xamarin] とは別にします。  Visual Studio 2015 をインストールするときに、Xamarin ツールをインストールできます。

* Mac を [Xcode] v7.0 以降と [Xamarin Studio] をインストールします。
 
* Visual Studio を実行する Windows ベースのコンピューターでアプリの構築を計画する場合でも、実際にビルドしてデプロイするには、Xamarin.iOS Build Host を実行するネットワークで接続された Mac にアクセスする必要があります。 詳細については、「 [Xamarin.iOS Windows 上にインストールします。](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/)

>[AZURE.NOTE] 場合は、Azure アカウントにサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](https://tryappservice.azure.com/?appServiceName=mobile)します。 有効期間が短いスターター モバイル アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 新しい Azure Mobile App バックエンドの作成

新しいモバイル アプリ バックエンドを作成するには、次の手順に従います。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## サーバー プロジェクトの構成

これで、モバイル クライアント アプリケーションで使用できる Azure モバイル アプリ バックエンドのプロビジョニングが完了しました。 次は、簡単な "todo list" バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

以下の手順に従って、Node.js または .NET バックエンドを使用するようにサーバー プロジェクトを構成します。

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## (省略可能) バックエンド プロジェクトのローカルなテスト

上で .NET バックエンド構成を選択した場合は、必要に応じて、バックエンドをローカルにテストできます。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]



## Xamarin.iOS アプリのダウンロードと実行

1. Mac で、開く、 [Azure portal] ブラウザー ウィンドウにします。

    >[AZURE.NOTE] Mac で Xamarin.iOS アプリを実行する方が簡単 必要であれば Visual Studio を使用して Windows コンピューター上で Xamarin.iOS を実行できますが、ネットワーク化された Mac に接続する必要があるため、少し複雑になります。 これを行うに知りたい場合は、次を参照してください。 [Installing Xamarin.iOS on Windows]します。

2. モバイル アプリの [設定] ブレードをクリックして **開始** > **Xamarin.iOS**します。 手順 3 では、をクリックして  **新しいアプリを作成する** が選択されていない場合。  次へ] をクリックして、 **ダウンロード** ] ボタンをクリックします。

    これにより、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。 圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

3. ダウンロードしたプロジェクトを抽出し、Xamarin Studio または Visual Studio で開きます。

    ![][9]

    ![][8]

4. F5 キーを押してプロジェクトをビルドし、iPhone エミュレーターでアプリを起動します。

5. アプリケーションになど意味のあるテキストを入力 _学習 Xamarin_, 、順にクリックし、 **+** ] ボタンをクリックします。

    ![][10]

    これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

>[AZURE.NOTE]照会および QSTodoService.cs c# ファイルにデータを挿入するようにモバイル アプリ バックエンドにアクセスするコードを確認できます。

##次のステップ

* [アプリへの認証の追加 ](app-service-mobile-xamarin-ios-get-started-users.md)
  <br/>Id プロバイダーを使用してアプリのユーザーを認証する方法について説明します。

* [アプリケーションにプッシュ通知を追加する](app-service-mobile-xamarin-ios-get-started-push.md)
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure portal]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/


