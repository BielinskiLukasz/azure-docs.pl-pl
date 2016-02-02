<properties
    pageTitle="ユニバーサル Windows アプリでのオフライン データの使用 | Microsoft Azure"
    description="Azure Mobile Services を使用して、ユニバーサル Windows アプリでオフライン データをキャッシュおよび同期する方法について説明します。"
    documentationCenter="mobile-services"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/06/2015"
    ms.author="donnam"/>


# Mobile Services でのオフライン データの同期の使用

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

このチュートリアルでは、Azure Mobile Services を使用して、Windows ユニバーサル ストアのアプリケーションにオフライン サポートを追加する方法について説明します。 オフライン サポートを使用すると、アプリがオフラインの状況でもローカル データベースと対話できます。 アプリがバックエンドのデータベースとオンラインになったときに、オフライン機能を使用したローカルの変更を同期します。

右側のクリップを見ると、このチュートリアルと同じ手順をビデオで確認できます。

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

このチュートリアルでは、Universal アプリケーション プロジェクトを更新、[モバイル サービスの開始を取得] Azure Mobile Services のオフライン機能をサポートするチュートリアルです。 切断されたオフラインの状況でデータを追加、それらの項目をオンライン データベースに同期し、ログインして、[Azure クラシック ポータル] データに、アプリケーションの実行中に加えられた変更を表示します。
>[AZURE.NOTE] このチュートリアルの目的は、Azure を使用して Windows ストア アプリケーションのデータを格納および取得できるようにするためのモバイル サービスのしくみを説明することにあります。 初めてのモバイル サービスを使用する場合は、チュートリアルを完了する必要があります [を使ってみるモバイル サービス] 最初です。

## 前提条件

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013。
* 完了、[モバイル サービスの開始を取得] です。
* [Azure Mobile Services SDK バージョン 1.3.0 (またはそれ以降) ][mobile services sdk nuget]
* [Azure Mobile Services SQLite Store バージョン 1.0.0 (またはそれ以降) ][sqlite store nuget]
* [SQLite for Windows 8.1](http://www.sqlite.org/download.html)
* Azure アカウント。 アカウントがない場合は、Azure 試用版にサインアップして最大 10 の無料モバイル サービスを取得し、試用期間が終わった後でも使用し続けることができます。 詳細については、「 [Azure 無料試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28)します。

## <a name="enable-offline-app"></a>オフライン機能をサポートするためにアプリケーションを更新します。

Azure Mobile Services のオフライン機能を使用すると、モバイル サービスに対してオフラインになっている状況でも、ローカル データベースとやり取りすることができます。 アプリケーションでこれらの機能を使用する初期化、 `MobileServiceClient.SyncContext` をローカル ストアにします。 使用してテーブルを参照、 `IMobileServiceSyncTable` インターフェイスです。 このチュートリアルでは、ローカル ストアに SQLite を使用します。
>[AZURE.NOTE] このセクションをスキップして、既にオフラインをサポートしているプロジェクト例を、GitHub の Mobile Services のサンプル リポジトリから取得することもできます。 オフライン サポートを有効になっているサンプル プロジェクトが存在するここでは、[TodoList オフライン サンプル] です。

1. Windows 8.1 および Windows Phone 8.1 の SQLite ランタイムをインストールします。

    * **Windows 8.1 Runtime:** [Windows 8.1 用 SQLite] をインストールします。
    * **Windows Phone 8.1:** [SQLite for Windows Phone 8.1] をインストールします。
    >[AZURE.NOTE] Internet Explorer を使用している場合は、SQLite をインストールするためにこのリンクをクリックすると、.vsix を .zip ファイルとしてダウンロードするためのプロンプトが表示されることがあります。 ファイルに .zip ではなく .vsix 拡張子を付けて、ハード ドライブ上の場所に保存します。 エクスプローラーで .vsix ファイルをダブルクリックすると、インストールが実行されます。

2. Visual Studio で完成させたプロジェクトを開き、[モバイル サービスの開始を取得] チュートリアルです。 Windows 8.1 ランタイムおよび Windows Phone 8.1 プロジェクト向けの **WindowsAzure.MobileServices.SQLiteStore** NuGet パッケージをインストールします。

    * **Windows 8.1:** ソリューション エクスプローラーで、Windows 8.1 プロジェクトを右クリックし、[**Nuget パッケージの管理**] をクリックして NuGet パッケージ マネージャーを実行します。 **SQLiteStore** を見つけ、`WindowsAzure.MobileServices.SQLiteStore` パッケージをインストールします。
    * **Windows Phone 8.1:** Windows Phone 8.1 プロジェクトを右クリックし、[**Nuget パッケージの管理**] をクリックして NuGet パッケージ マネージャーを実行します。 **SQLiteStore** を見つけ、`WindowsAzure.MobileServices.SQLiteStore` パッケージをインストールします。
    >[AZURE.NOTE] インストールで古いバージョンの SQLite への参照が作成される場合は、重複した参照だけを削除できます。

    ![][2]

2. ソリューション エクスプローラーで Windows 8.1 ランタイムおよび Windows Phone 8.1 プラットフォーム プロジェクトの [**参照設定**] を右クリックし、[**拡張**] セクションにある SQLite への参照を追加します。

    ![][1]
    </br>

    **Windows 8.1 Runtime**

    ![][11]
    </br>

    **Windows Phone 8.1**

3. SQLite ランタイムでは、ビルド対象のプロジェクトのプロセッサ アーキテクチャを **[x86]**、**[x64]**、または **[ARM]** に変更する必要があります。 **[任意の CPU]** はサポートされません。 ソリューション エクスプ ローラーで、上部にある [ソリューション] をクリックし、プロセッサ アーキテクチャのドロップ ダウン ボックスをテストしたいサポート済みの設定の 1 つに変更します。

    ![][13]

5. ソリューション エクスプローラーで共有プロジェクトを使用し、MainPage.cs ファイルを開きます。 ファイルの先頭にある次の using ステートメントをコメント解除します。

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. MainPage.cs の定義をコメント `todoTable` を呼び出す次の行をコメント解除します `MobileServicesClient.GetSyncTable()`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. MainPage.cs で、マークされているリージョンに `オフライン同期`, 、メソッドのコメントを解除 `InitLocalStoreAsync` と `SyncAsync`します。 メソッド `InitLocalStoreAsync` SQLite ストアを使用してクライアントの同期コンテキストを初期化します。

     private async Task InitLocalStoreAsync()
     {
         if (!App.MobileService.SyncContext.IsInitialized)
         {
             var store = new MobileServiceSQLiteStore("localstore.db");
             store.DefineTable<TodoItem>();
             await App.MobileService.SyncContext.InitializeAsync(store);
         }
    
         await SyncAsync();
     }
    
     private async Task SyncAsync()
     {
         await App.MobileService.SyncContext.PushAsync();
         await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
     }

8. `OnNavigatedTo` イベント ハンドラーへの呼び出しをコメント解除 `InitLocalStoreAsync`:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. 呼び出しをコメント解除します、3 `SyncAsync` メソッドで `InsertTodoItem`, 、`UpdateCheckedTodoItem`, 、および `ButtonRefresh_Click`:

     private async Task InsertTodoItem(TodoItem todoItem)
     {
         await todoTable.InsertAsync(todoItem);
         items.Add(todoItem);
    
         await SyncAsync(); // offline sync
     }
    
     private async Task UpdateCheckedTodoItem(TodoItem item)
     {
         await todoTable.UpdateAsync(item);
         items.Remove(item);
         ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);
    
         await SyncAsync(); // offline sync
     }
    
     private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
     {
         ButtonRefresh.IsEnabled = false;
    
         await SyncAsync(); // offline sync
         await RefreshTodoItems();
    
         ButtonRefresh.IsEnabled = true;
     }

10. 例外ハンドラーを追加、 `SyncAsync` メソッド。

    private async Task SyncAsync()
    {
        String errorString = null;
    
        try
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
        }
    
        catch (MobileServicePushFailedException ex)
        {
            errorString = "Push failed because of sync errors: " +
              ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
        }
        catch (Exception ex)
        {
            errorString = "Pull failed: " + ex.Message +
              "\n\nIf you are still in an offline scenario, " +
              "you can try your Pull again when connected with your Mobile Serice.";
        }
    
        if (errorString != null)
        {
            MessageDialog d = new MessageDialog(errorString);
            await d.ShowAsync();
        }
    }

この例では、リモートのすべてのレコードを取得 `todoTable`, がクエリを渡すことでレコードをフィルター処理することもできます。 最初のパラメーター `PullAsync` を使用する増分同期に使用されるクエリ id、 `UpdatedAt` タイムスタンプ、前回の同期以降に変更されたレコードのみを取得します。 クエリ ID は、アプリ内の各論理クエリに対して一意の、わかりやすい文字列にする必要があります。 オプトアウトの増分同期に渡す `null` クエリ ID として これによってプル操作ごとにすべてのレコードを取得することになり、効率が悪くなる可能性があります。
>[AZURE.NOTE] * をモバイル サービス データベースで削除されたときに、デバイスのローカル ストアからレコードを削除して、[論理的な削除] を有効にする必要があります。 それ以外の場合、アプリを呼び出す必要があります定期的に `IMobileServiceSyncTable.PurgeAsync()` ローカル ストアを消去します。

なお、 `MobileServicePushFailedException` プッシュ操作とプル操作の両方で発生することができます。 リレーションシップを持つすべてのテーブルに一貫性があることを確認するために、プル操作で内部的にプッシュが実行されることから、この例外はプルで発生する可能性があります。 次のチュートリアルの [モバイル サービスのオフライン サポートでの競合の処理]、ですこれらの同期関連の例外を処理する方法です。

11. Visual Studio で、**F5** キーを押してアプリケーションをリビルドして実行します。 アプリケーションは、前にオフライン同期の変更を実施したときと同様に動作しますが、これは挿入や更新の操作時にアプリケーションが同期の動作をするためです。

## <a name="update-sync"></a>更新プログラム、アプリケーションの同期の動作

このセクションでは、挿入や更新の操作時ではなく、[**更新**] ボタンが押された場合にのみ同期を実行するようアプリケーションを変更します。 その後、オフラインの状況をシミュレートするために、モバイル サービスに対するアプリケーションの接続を解除します。 データ項目を追加すると、それらはローカル ストア内に保持されますが、モバイル サービスには同期されません。

1. 共有プロジェクトで MainPage.cs を開きます。 メソッドを編集 `InsertTodoItem` と `UpdateCheckedTodoItem` 呼び出しをコメント アウトする `SyncAsync`します。

2. 共有プロジェクトで App.xaml.cs を編集します。 **MobileServiceClient** の初期化をコメント アウトし、無効なモバイル サービス URL を使用する次の行を追加します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3. `InitLocalStoreAsync()`, 、呼び出しをコメント アウト `SyncAsync()`, 、アプリケーションが起動時に同期を実行しないようにします。

4. **F5** キーを押し、アプリケーションをビルドして実行します。 新しい todo 項目をいくつか入力し、それぞれについて [**保存**] をクリックします。 モバイル サービスにプッシュされるまで、新しい todo 項目はローカル ストア内にのみ存在します。 クライアント アプリケーションは、まるで作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートするモバイル サービスに接続されているかのように動作します。

5. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

## <a name="update-online-app"></a>モバイル サービスに再接続するアプリケーションを更新します。

ここでは、アプリケーションをモバイル サービスに再接続します。 これは、アプリケーションがオフライン状態から、モバイル サービスとのオンライン状態に移行したことをシミュレートします。 [Refresh] をクリックすると、データがモバイル サービスに同期されます。

1. 共有プロジェクトで App.xaml.cs を開きます。 先の初期化をコメント解除 `MobileServiceClient` を正しいモバイル サービス URL とアプリケーション キーを追加し直します。

2. **F5** キーを押して、アプリケーションをリビルドして実行します。 アプリケーションは現在モバイル サービスに接続されていますが、オフラインの状況と同じ表示になっていることに注意してください。 これは、このアプリケーションで常に動作しているため、 `IMobileServiceSyncTable` 、ローカル ストアを指しています。

3. [Azure 旧ポータル] にログインし、モバイル サービスのデータベースを参照します。 開発中のサービスが、モバイル サービスとして JavaScript バックエンドを使用している場合は、モバイル サービスの **[データ]** タブからデータを参照できます。

    モバイル サービスの .NET バックエンドを使用している場合は、Visual Studio で、[**サーバー エクスプローラー**]、[**Azure**]、[**SQL データベース**] の順に移動します。 データベースを右クリックし、[**SQL Server オブジェクト エクスプローラーで開く**] を選択します。

    データベースとローカル ストアの間でデータがまだ同期されていないことを確認します。

    ![][6]

4. アプリケーションで、[**更新**] ボタンを押します。 これにより、アプリケーションを呼び出す `PushAsync` と `PullAsync`します。 このプッシュ操作により、ローカル ストアの項目が Mobile Services に送信され、Mobile Services から新しいデータが取得されます。

    プッシュ操作を実行、 `MobileServiceClient.SyncContext` の代わりに、 `IMobileServicesSyncTable` し、その同期コンテキストに関連付けられているすべてのテーブルに対して変更をプッシュします。 これは、テーブル間にリレーションシップがある状況に対応することを目的としています。

    ![][7]

5. アプリケーションで、ローカル ストアで完了させる項目の横にあるチェック ボックスをクリックします。

    ![][8]

6. プッシュ、 **更新** 再度] クリックすると `SyncAsync` に呼び出されます。 `SyncAsync` はプッシュとプルの両方を呼び出しますが、ここで私たちでした削除呼び出し `PushAsync`します。 これは、**プルは必ず、最初にプッシュを実行する**ことが原因です。 これは、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。

    ![][10]


## 概要

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## 次のステップ

* [モバイル サービスのオフライン サポートでの競合を処理する]

* [Mobile Services ][soft delete]





[update the app to support offline features]: #enable-offline-app 
[update the sync behavior of the app]: #update-sync 
[update the app to reconnect your mobile service]: #update-online-app 
[next steps]: #next-steps 
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png 
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png 
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png 
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png 
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png 
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png 
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png 
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png 
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png 
[handling conflicts with offline support for mobile services]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md 
[todolist offline sample]: http://go.microsoft.com/fwlink/?LinkId=394777 
[get started with mobile services]: /develop/mobile/tutorials/get-started/#create-new-service 
[getting started]: ../mobile-services-dotnet-backend-windows-phone-get-started.md 
[get started with mobile services]: ../mobile-services-windows-store-get-started.md 
[sqlite for windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776 
[sqlite for windows phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953 
[soft delete]: mobile-services-using-soft-delete.md 
[mobile services sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0 
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0 
[azure classic portal]: https://manage.windowsazure.com 

