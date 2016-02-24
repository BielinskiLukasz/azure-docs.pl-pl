<properties
    pageTitle="既存のユニバーサル Windows ストア アプリへの Mobile Services の追加 | Microsoft Azure"
    description="Mobile Services を使用して Windows ストア アプリでデータを活用する方法について説明します。"
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="glenga"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##概要

このトピックでは、Azure のモバイル サービスを Windows ストア アプリのバックエンド データソースとして使用する方法について説明します。 このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合し、アプリケーションの実行時にデータに加えられた変更を表示します。

このチュートリアルで作成するモバイル サービスは .NET バックエンド モバイル サービスです。 .NET バックエンドによって、モバイル サービスにおけるサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できるようになります。さらに、ローカル コンピューターでモバイル サービスを実行し、デバックすることができます。 JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「JavaScript バックエンド バージョン」を参照してください。

>[AZURE.NOTE]このトピックでは、Visual Studio Professional 2013 with Update 3 でツーリングを使用して、新しいモバイル サービスをユニバーサル Windows アプリに接続する方法を説明します。 同じ手順を使用して、モバイル サービスを Windows ストアまたは Windows Phone ストア 8.1 アプリに接続することができます。 Windows Phone 8.0 または Windows Phone Silverlight 8.1 アプリにモバイル サービスを接続するを参照してください。 [Windows Phone 向けデータの概要](mobile-services-dotnet-backend-windows-phone-get-started-data.md)します。

> Visual Studio Professional 2013 Update 3 にアップグレードできないか、手動で Windows ストア アプリ ソリューションにモバイル サービス プロジェクトを追加、表示 [このバージョン](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md) トピックです。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)をご覧ください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (更新プログラム 3 またはそれ以降のバージョン)。

##GetStartedWithData プロジェクトをダウンロードする

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##Visual Studio からの新しいモバイル サービスの作成

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

&nbsp;&nbsp;7. ソリューション エクスプ ローラーで、GetStartedWithData.Shared プロジェクト フォルダーの App.xaml.cs コード ファイルを開くしに追加された新しい静的フィールド、 **アプリ** 次のような Windows ストア アプリの条件付きコンパイル ブロック内のクラス。

    public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient
        todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


& nbsp; & nbsp;このコードのインスタンスを使用して、アプリケーションの場合は、新しいモバイル サービスへのアクセスを提供、 [MobileServiceClient](http://go.microsoft.com/fwlink/p/?LinkId=302030) クラスです。 クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。 この静的フィールドは、アプリケーションのすべてのページで使用できます。

&nbsp;&nbsp;8. Windows Phone アプリ プロジェクトを右クリックし、をクリックして **追加**, 、] をクリックして **接続済みサービス]**, を作成したモバイル サービスを選択してクリックして **OK**します。 同じコードは共有 App.xaml.cs ファイルにも追加されますが、今回は Windows Phone アプリの条件付きコンパイル ブロック内に追加されています。

この時点で、Windows ストア アプリと Windows Phone ストア アプリのどちらも新しいモバイル サービスに接続されます。 次の手順は、新しいモバイル サービス プロジェクトのテストです。


##モバイル サービス プロジェクトをローカルにテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##モバイル サービスを使用するためのアプリケーションの更新

このセクションでは、ユニバーサル Windows アプリを更新してモバイル サービスをアプリケーションのバックエンド サービスとして使用します。 変更する必要があるのは、GetStartedWithData.Shared プロジェクト フォルダーの MainPage.cs プロジェクト ファイルだけです。

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]


##モバイル サービスを Azure に発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##Azure でホストされているモバイル サービスをテストする

これで、Azure でホストされているモバイル サービスに対して両方のバージョンのユニバーサル Windows アプリをテストできます。

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

##SQL Database に格納されているデータの表示

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../../includes/mobile-services-dotnet-backend-view-sql-data.md)]

これでチュートリアルは終了します。

##次のステップ

このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトでモバイル サービスのデータを操作できるようにするための基本について説明しました。 次に、以下のその他のトピックのいずれかを読むことを検討してください。

* [認証の使用の開始を取得]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

* [開始プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services C# の場合操作方法に関する概念リファレンス](mobile-services-windows-dotnet-how-to-use-client-library.md)
  <br/>.NET で Mobile Services を使用する方法について説明します。


<!-- Images. -->



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Get started with offline data sync]: mobile-services-windows-store-dotnet-get-started-offline-data.md

[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030

