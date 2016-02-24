<properties
    pageTitle="ユニバーサル Windows アプリでのオフライン データの競合の処理 | Microsoft Azure"
    description="Azure Mobile Services を使用して、ユニバーサル Windows アプリケーションでのオフライン データの同期時に発生する競合を処理する方法について説明します。"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/12/2015"
    ms.author="glenga"/>


# モバイル サービスでのオフライン データの同期との競合の処理

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

##概要

このトピックでは、Azure Mobile Services のオフライン機能を使用しているときに、データを同期し、競合を処理する方法について説明します。

このチュートリアルのビデオを見る場合は、下のクリップでこのチュートリアルと同じ手順が表示されます。

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

このチュートリアルでは、オフラインの同期で発生する競合の処理をサポートするアプリケーション用に、ユニバーサル Windows C# ソリューションをダウンロードします。 モバイル サービスとアプリケーションを統合します。次に、Windows ストア 8.1 と Windows Phone 8.1 のクライアントを実行して同期の競合を生成して、これを解決します。

このチュートリアルでは、手順およびサンプル アプリケーションを前のチュートリアルに基づいて [オフライン データの開始を取得] です。 このチュートリアルを開始する前に完了してください [オフライン データの開始を取得] です。


##前提条件

このチュートリアルでは、Windows 8.1 で実行されている Visual Studio 2013 が必要です。


##サンプル プロジェクトのダウンロード

![][0]

このチュートリアルは、[Todo オフラインのモバイル サービスのサンプル] がローカルのオフライン ストアと Azure のモバイル サービス データベースの間の同期の競合を処理する方法のチュートリアルです。

1. [Mobile Services サンプル GitHub リポジトリ] の zip ファイルをダウンロードして、作業ディレクトリに抽出します。

2. 場合は、まだインストールしていない SQLite for Windows 8.1 と Windows Phone 8.1 で説明したように、[オフライン データの開始を取得] チュートリアルでは、両方をランタイムをインストールします。

3. Visual Studio 2013 で開き、 *mobile-services-samples\TodoOffline\WindowsUniversal\TodoOffline-Universal.sln* ソリューション ファイルです。 キーを押して、 **f5 キーを押して** キーを再構築し、プロジェクトを実行します。 NuGet パッケージが復元され、参照が正しく設定されていることを確認します。

    >[AZURE.NOTE] SQLite ランタイムへの以前の参照を削除してで説明したように更新された参照で置き換える必要があります、[の使用」のオフライン データのチュートリアルです。

4. アプリケーションにテキストを入力 **Insert a TodoItem**, 、] をクリックし、 **保存** 、ローカル ストアをいくつかの todo 項目を追加します。 次に、アプリケーションを閉じます。

アプリケーションはまだ接続して、モバイル サービスにのでボタン **プッシュ** と **プル** は例外をスローします。




##モバイル サービスに対するアプリケーションをテストします。

次に、Mobile Services に対してアプリケーションをテストします。

1. [Azure クラシック ポータルで] をクリックして、モバイル サービスのアプリケーション キーを検索 **キーの管理** のコマンド バーで、 **ダッシュ ボード** ] タブをクリックします。 コピー、 **アプリケーション キー**します。

2. Visual Studio のソリューション エクスプローラーで、クライアント サンプル プロジェクトの App.xaml.cs ファイルを開きます。 初期化を変更、 **MobileServiceClient** モバイル サービス URL を使用してアプリケーション キー。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. Visual Studio でキーを押して、 **f5 キーを押して** キーをビルドし、アプリケーションを再度実行します。

    ![][0]


##競合を作成するためのバックエンドでのデータの更新

現実のシナリオでは、1 つのアプリケーションがデータベースのレコードに更新をプッシュし、次に別のアプリケーションがそのレコードの使用していないバージョンを使用して同じレコードへプッシュしようとする場合に同期の競合が発生します。 説明した場合、[オフライン データ] を開始、オフラインの同期の機能をサポートするバージョンのシステム プロパティが必要です。 このバージョン情報は、データベースの更新ごとに検証されます。 アプリケーションのインスタンスが以前のバージョンを使用してレコードを更新しようとすると、競合が発生し、アプリケーションで `MobileServicePreconditionFailedException` として捕捉されます。 アプリケーションが `MobileServicePreconditionFailedException` を捕捉しなかった場合は、発生した同期エラーの数を示す `MobileServicePushFailedException` が最終的にスローされます。

>[AZURE.NOTE] オフライン データの同期で削除されたレコードの同期をサポートするために有効にしてください [論理的な削除](mobile-services-using-soft-delete.md)します。 それ以外の場合は、ローカル ストアのレコードを手動で削除するか、`IMobileServiceSyncTable::PurgeAsync()` を呼び出してローカル ストアを消去する必要があります。


次の手順では、同時に実行する Windows Phone 8.1 クライアントと Windows ストア 8.1 のクライアントに競合が発生し、サンプルを使用してこれを解決する方法を示します。

1. Visual Studio で、Windows Phone 8.1 プロジェクトを右クリックし、クリックして **スタートアップ プロジェクトとして設定**します。 キーを押します **Ctrl + F5** キーをデバッグなしの Windows Phone 8.1 クライアントを実行します。 Windows Phone 8.1 のクライアントのセットアップし、エミュレーターで実行する] をクリックした後、 **プル** 、ローカル ストアとデータベースの現在の状態を同期する] ボタンをクリックします。

    ![][3]


2. Visual Studio で、Windows 8.1 ランタイム プロジェクトを右クリックし、クリックして **スタートアップ プロジェクトとして設定** には、スタートアップ プロジェクトに設定します。 キーを押します **f5 キーを押して** を実行します。 Windows ストア 8.1 クライアントが、実行を作成したら、クリックして、 **プル** 、ローカル ストアとデータベースの現在の状態を同期する] ボタンをクリックします。

    ![][4]

3. この時点では、両方のクライアントはデータベースと同期しています。 両クライアントのコードは増分同期も使用しているため、完了していない Todo 項目のみ同期します。 完了した Todo 項目は無視されます。 項目のいずれかを選択し、両クライアントで同じ項目のテキストを別の値に編集します。 クリックして、 **プッシュ** 両方の変更サーバー上のデータベースとの同期] ボタンをクリックします。

    ![][5]

    ![][6]


4. 最後にプッシュを実行したクライアントには競合が発生しますが、ユーザーはデータベースにコミットする値をどちらにするか決定できます。 例外により、競合を解決するために使用する正しいバージョンの値が提供されます。

    ![][7]



##同期の競合を処理するためのコードの確認

Mobile Services でオフライン機能を使用するためには、ローカル データベースとデータ転送オブジェクトの両方に [バージョン] 列を含める必要があります。 これは `TodoItem` クラスを次のメンバーで更新することによって実行されます。

        [Version]
        public string Version { get; set; }

 `__version` では、ローカルのデータベースの列が含まれている、  `OnNavigatedTo()` メソッドと、 `TodoItem` 、ローカル ストアを定義するクラスを使用します。

コードでオフラインの同期の競合を処理するには、`IMobileServiceSyncHandler` を実装するクラスを作成します。 呼び出しのこの型のオブジェクトを `MobileServiceClient.SyncContext.InitializeAsync()` に渡します。 これにも発生、  `OnNavigatedTo()` 、サンプルのメソッドです。

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

クラス `SyncHandler` で **SyncHandler.cs** 実装 `IMobileServiceSyncHandler`します。 メソッド `ExecuteTableOperationAsync` は、各プッシュ操作がサーバーに送信されるときに呼び出されます。 型 `MobileServicePreconditionFailedException` の例外がスローされると、項目のローカルとリモートのバージョン間で競合が発生していることを意味します。

ローカル項目を優先して競合を解決するには、操作を再試行します。 競合が発生すると、ローカル項目のバージョンはサーバーのバージョンと一致するように更新されるため、操作をもう一度実行するとサーバーの変更はローカルの変更で上書きされます。

    await operation.ExecuteAsync();

サーバー項目を優先して競合を解決するには、`ExecuteTableOperationAsync` から戻ります。 オブジェクトのローカル バージョンは破棄され、サーバーの値に置き換えられます。

プッシュ操作を停止する (ただし、キューに設定された変更は保持) には、`AbortPush()` メソッドを使用します。

    operation.AbortPush();

これによって現在のプッシュ操作は停止しますが、`AbortPush` が `ExecuteTableOperationAsync` から呼び出される場合は、現在の操作を含めて、すべての保留中の変更は保存されます。 次回 `PushAsync()` が呼び出されると、これらの変更はサーバーに送信されます。

プッシュがキャンセルされると、`PushAsync` は `MobileServicePushFailedException` をスローし、例外プロパティ `PushResult.Status` の値は `MobileServicePushStatus.CancelledByOperation` となります。



<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: ../mobile-services-windows-store-get-started.md
[Get started with offline data]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure classic portal]: https://manage.windowsazure.com/
[Handling Database Conflicts]: mobile-services-windows-store-dotnet-handle-database-conflicts.md#test-app
[Mobile Services Samples GitHub Repository]: http://go.microsoft.com/fwlink/?LinkId=512865
[Todo Offline Mobile Services sample]: http://go.microsoft.com/fwlink/?LinkId=512866


