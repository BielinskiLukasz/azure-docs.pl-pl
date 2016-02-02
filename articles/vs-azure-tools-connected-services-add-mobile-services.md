<properties 
   pageTitle="Visual Studio の接続済みサービスを使用した Mobile Services の追加 | Microsoft Azure"
   description="Visual Studio の [接続済みサービスの追加] ダイアログ ボックスを使用して Mobile Services を追加する"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="08/12/2015"
   ms.author="tarcher" />


# Visual Studio 接続済みサービスを使用した Mobile Services の追加

Visual Studio 2015 では、**[接続済みサービスの追加]** ダイアログを利用して Azure Mobile Services に接続できます。 あらゆる C# クライアント アプリ、JavaScript アプリ、プラットフォーム非依存 Cordova アプリから接続できます。 接続すると、データを作成してアクセスしたり、カスタム API や定期ジョブを作成したり、プッシュ通信のサポートを追加したりできます。 接続済みサービスの操作は適切なあらゆる参照と接続コードを追加します。 また、Azure AD、Facebook、Twitter、Microsoft アカウントなど、さまざまな人気 ID スキームによる認証に対応しており、それを最大限に活用できます。

## サポートされているプロジェクトの種類

>[AZURE.NOTE] Visual Studio 2015 では、[接続済みサービスの追加] ダイアログで Azure Mobile Services を Windows Universal (Windows 10) プロジェクトに追加することはできません。 プロジェクトの NuGet パッケージ マネージャーを利用して適切なパッケージをインストールし、Azure Mobile Services を追加できます。

[接続済みサービス] ダイアログを使用して、次の種類のプロジェクトで Azure Mobile Services に接続することができます。

- .NET Windows 8.1 ストア、Phone、ユニバーサル アプリのプロジェクト

- JavaScript Windows 8.1 ストア、Phone、ユニバーサル アプリのプロジェクト

- Apache Cordova の Visual Studio ツールを使用して作成されたプロジェクト


## [接続済みサービスの追加] ダイアログを利用して Azure Mobile Services に接続する

1. Azure アカウントがあることを確認します。 サインアップすることができます、Azure アカウントを取得していない場合、 [無料評価版](http://go.microsoft.com/fwlink/?LinkId=518146)します。

1. **[接続済みのサービスの追加]** ダイアログ ボックスを開きます。
 - .NET アプリの場合、Visual Studio でプロジェクトを開き、ソリューション エクスプローラーで **[参照]** ノードのコンテキスト メニューを開いて **[接続済みサービスの追加]** を選択します。

        ![Azure のモバイル サービスに接続します。](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - Apache Cordova アプリ プロジェクトの場合、Visual Studio でプロジェクトを開き、ソリューション エクスプローラーでプロジェクト ノードのコンテキスト メニューを開いて **[接続済みサービスの追加]** を選択します。

1. **[接続済みサービスの追加]** ダイアログ ボックスで、**[Azure Mobile Services]** を選択し、**[構成]** ボタンをクリックします。 Azure にまだログインしていない場合は、ログインを促すメッセージが表示される場合があります。

    ![Azure Mobile Service の追加](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. **[Azure Mobile Services]** ダイアログ ボックスで、既存のモバイル サービスがある場合はそのモバイル サービスを選択します。 新しい Azure モバイル サービスを作成する必要がある場合は、次の手順に従って作成します。 そうでない場合は、次の手順に進みます。

    新しいモバイル サービスのアカウントを作成します。
    1. 選択、 ** サービスの作成 **] ダイアログ ボックスの下部にあるリンクです。
        ![新しいモバイル接続サービスを追加する](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)

    2. **[モバイル サービスの作成]** ダイアログ ボックスで、**[ランタイム]** ドロップダウン リストから JavaScript バックエンド モバイル サービスまたは .NET バックエンド モバイル サービスを選択できます。

        ![モバイル サービスの作成](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        JavaScript バックエンド サービスは、シンプルで強力です。 JavaScript バックエンド モバイル サービスを作成した場合、サーバー側の JavaScript コードがクラウドに保存されますが、サーバー エクスプローラーまたは Azure 管理ポータルを利用してサーバー スクリプトを編集できます。

        .NET バックエンド モバイル サービスでは、Web API とエンティティ フレームワークのすべてのパワーと柔軟性が与えられます。 .NET バックエンド モバイル サービスを作成すると、プロジェクトが自動的に作成され、ソリューションに追加されます。

    1. モバイル サービスを配置する **[リージョン]** を選択し、サーバーのユーザー名とパスワードを入力します。

    1. 必要なすべての情報を入力したら、**[作成]** ボタンをクリックし、モバイル サービスを作成します。
    2. 新しいモバイル サービスは、**[Azure Mobile Services]** ダイアログ ボックスのサービスの一覧に表示されます。 一覧で新しいモバイル サービスを選択します。**[追加]** ボタンをクリックし、プロジェクトにサービスを追加します。

1. 表示された作業の開始ページを確認し、プロジェクトがどのように変更されたかを確認します。 作業の開始ページは、接続済みサービスを追加したかどうかに関係なく、ブラウザーに表示されます。 推奨される次の手順とコード例を確認したり、[変更内容] ページに切り替えて、プロジェクトに追加された参照と、コードと構成ファイルがどのように変更されたかを確認したりすることができます。

1. コード サンプルをガイドとして利用し、モバイル サービスにアクセスするためのコードを記述します。

## プロジェクトを変更する方法

Visual Studio でプロジェクトを変更する方法はプロジェクトの種類によって異なります。 C# クライアント アプリで、次を参照してください。 [何が起こったか – c# プロジェクト](http://go.microsoft.com/fwlink/p/?LinkId=513119)します。 JavaScript クライアント アプリケーションの場合、次を参照してください。 [何が起こったか – JavaScript プロジェクト](http://go.microsoft.com/fwlink/p/?LinkId=513120)します。 Cordova アプリを参照してください。 [何が起こったか – Cordova プロジェクト](http://go.microsoft.com/fwlink/p/?LinkId=513116)します。


## 次のステップ

質問してヘルプを表示します。

 - [Azure Mobile Services の MSDN フォーラム:](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [Microsoft Azure チームのブログでの azure のモバイル サービス](http://azure.microsoft.com/blog/topics/mobile/)

 - [Azure の Mobile Services (azure.microsoft.com)](http://azure.microsoft.com/services/mobile-services/)

 - [Azure Mobile Services のドキュメント (azure.microsoft.com)](http://azure.microsoft.com/documentation/services/mobile-services/)








