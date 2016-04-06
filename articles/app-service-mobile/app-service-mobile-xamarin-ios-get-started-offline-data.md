<properties
    pageTitle="Azure モバイル アプリ (Xamarin iOS) のオフライン同期を有効にする"
    description="App Service Mobile App を使用して、Xamarin iOS アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="wesmc"/>

# Xamarin iOS モバイル アプリのオフライン同期を有効にする

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概要

このチュートリアルでは、Xamarin iOS 向けの Azure モバイル アプリのオフライン同期機能について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。 変更は、ローカル データベースに格納されます。デバイスがオンラインに戻ると、これらの変更は、リモート サービスと同期されます。

このチュートリアルでは、チュートリアルでは、Xamarin.iOS アプリケーション プロジェクトを更新します [Create a Xamarin iOS app] Azure Mobile Apps のオフライン機能をサポートします。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、次を参照してください。 [Azure モバイル アプリの .NET バックエンド サーバー SDK と連携](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)します。 

オフライン同期機能の詳細については、トピックを参照してください。 [Offline Data Sync in Azure Mobile Apps]します。

## 必要条件

このチュートリアルには、次のものが必要です。

* Visual Studio 2013
* Visual Studio [Xamarin extension] **または** [Xamarin Studio] OS x
* このチュートリアルを完了 [Create a Xamarin iOS app]します。 このチュートリアルはそのチュートリアルでカバーされている完成したアプリを使用します。

## クライアント同期コードの確認

チュートリアルを完了したときにダウンロードした Xamarin クライアント プロジェクト [Create a Xamarin iOS app] ローカル SQLite データベースを使用してオフラインの同期をサポートするコードが既に含まれています。 チュートリアルのコードにすでに含まれているものの概要を示します。 この機能の概念的な概要を参照してください。 [Offline Data Sync in Azure Mobile Apps]します。

* テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。 `QSTodoListViewController.ViewDidLoad()` が `QSTodoService.InitializeStoreAsync()` を実行すると、ローカル ストアのデータベースが初期化されます。 これにより、Azure Mobile Apps クライアント SDK で提供される `MobileServiceSQLiteStore` クラスを使用して、新しいローカルの SQLite データベースが作成されます。 

    `DefineTable` メソッドを実行すると、提供された型のフィールドに一致するテーブルがローカル ストアに作成されます。この例では、`ToDoItem` になります。 この型に、リモート データベース内のすべての列を含める必要はありません。 列のサブセットのみ格納できます。  

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* `QSTodoService` の `todoTable` メンバーは、`IMobileServiceTable` ではなく、`IMobileServiceSyncTable` 型です。 これは、テーブルの作成、読み取り、更新、および削除 (CRUD) といったすべての操作がローカル ストア データベースに行われるようにします。 
 
    これらの変更がいつ Azure Mobile Apps バックエンドにプッシュされるかを決定するには、クライアント接続のための同期コンテキストを使用して `IMobileServiceSyncContext.PushAsync()` を呼び出すことで行います。 同期コンテキストは、`PushAsync` が呼び出されたときに、クライアント アプリが変更を行ったすべてのテーブルで、変更を追跡およびプッシュすることで、テーブルの関係を保持するのに役立ちます。 

    todoitem リストの更新、または todoitem の追加や完了があれば、提供されているコードは `QSTodoService.SyncAsync()` を呼び出して同期します。 同期コンテキストへのプッシュや同期テーブルへのプルを実行するようなローカルの変更があれば毎回同期が行われます。 ただし、コンテキストによって追跡された保留中のローカル更新のあるテーブルに対してプルが実行される場合、そのプルの処理は自動的にコンテキストのプッシュを最初にトリガーします。 これら (項目の更新、追加、完了) のケースでは、明示的な `PushAsync` の呼び出しを省略できます。 冗長となるからです。

    提供されたコードでは、リモートの `TodoItem` テーブルのすべてのレコードはクエリされますが、クエリ ID やクエリを `PushAsync` に渡すことでレコードをフィルター処理することも可能です。 詳細については、セクションを参照してください。 *増分同期* で [Offline Data Sync in Azure Mobile Apps]します。

    <!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
    -->


        // QSTodoService.cs

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## クライアント アプリの実行

ローカル ストアのデータベースの設定をするために、少なくとも 1 回はクライアント アプリを実行してください。 次のセクションでは、オフラインのシナリオをシミュレートし、アプリがオフラインの間にローカル ストアのデータを変更します。


## クライアント アプリの同期の動作を更新する

このセクションでは、バックエンドに無効なアプリケーション URL を使用することで、クライアント プロジェクトを変更して、オフラインのシナリオをシミュレートします。 データ項目を追加または変更すると、これらの変更はローカル ストアに保持されますが、接続が再確立されるまでは、バックエンドのデータ ストアには同期されません。

1. `QSTodoService.cs` の上部で、次のように、`applicationURL` の初期化を変更して無効な URL を設定します。

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; 


2. 追加の `catch` を `QSTodoService.SyncAsync` の `Exception` クラスに追加すると、例外メッセージがコンソールに記述されます。 

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
        }

3. クライアント アプリの構築と実行 新しい todo 項目を追加すると、モバイル アプリのバックエンドと同期を試みるごとに、コンソールに例外がログ記録されることに注意してください。 これらの新しい項目は、モバイル バックエンドにプッシュされるまでは、ローカル ストア内にのみ存在します。 クライアント アプリケーションは、バックエンドに接続されているかのように動作し、作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートします。

4. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

5. (省略可能) Visual Studio を使用して、Azure SQL Database テーブルを表示し、バックエンドのデータベースのデータが変更されていないことを確認します。 

    Visual Studio で開きます **サーバー エクスプ ローラー**します。 内のデータベースに移動 **Azure**]-> [**SQL データベース**します。 データベースを右クリックして **[SQL Server オブジェクト エクスプ ローラーで開く**します。 これで SQL データベースのテーブルとその内容を参照できます。

6. (省略可能) Fiddler や Postman などの REST ツールを使用して、モバイルのバックエンドをクエリします。その際、`https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem` の形式で、GET クエリを使用します。 

## クライアント アプリを更新し、モバイルのバックエンドを再接続します。

このセクションでは、アプリケーションをモバイル バックエンドに再接続して、アプリケーションがオンライン状態に戻ったときの動作をシミュレーションします。 更新ジェスチャを実行すると、データがモバイル バックエンドに同期されます。

1. `QSTodoService.cs` を開きます。 `applicationURL` と `gatewayURL` を修正し、正しい URL に設定します。

2. クライアント アプリの再構築と実行 起動後、アプリは Azure モバイル アプリのバックエンドとの同期を試みます。 デバッグ コンソールに例外がログ記録されていないことを確認してください。

3. (省略可能) SQL Server Object Explorer または Fiddler などの REST ツールを使用して、更新データを表示します。 データが同期されるのは、Azure モバイル アプリのバックエンドのデータベースとローカル ストアの間であることに注意してください。

    データがデータベースとローカル ストアの間で同期されており、アプリが接続されていない状況で追加した項目が含まれていることを確認してください。

## その他のリソース

* [Azure モバイル アプリでのオフライン データ同期]

* [Cloud Cover: Offline Sync in Azure Mobile Services] \ (注: ビデオは、モバイル サービスには、Azure モバイル アプリで同様の方法でのオフライン同期機能)    

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Create a Xamarin iOS app]: ../app-service-mobile-xamarin-ios-get-started.md
[Offline Data Sync in Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
 
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri


