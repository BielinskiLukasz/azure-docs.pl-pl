<properties
    pageTitle="オプティミスティック同時実行制御でデータベース書き込み競合を処理 (Windows ストア) | Microsoft Azure"
    description="サーバーと Windows ストア アプリケーションの両方でデータベースへの書き込みの競合を処理する方法について説明します。"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="wesmc"/>


# データベースの書き込み競合の処理

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;




## 概要

このチュートリアルでは、Windows ストア アプリケーションの同じデータベース レコードに複数のクライアントが書き込みを行ったときに発生する競合を処理する方法について説明します。 シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。 競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。 Azure モバイル サービスには、このような競合を検出して解決する機能がサポートされています。 このトピックでは、サーバー上とアプリケーション内でデータベース書き込み競合を処理する手順を紹介します。

このチュートリアルでは、クイック スタート アプリケーションに機能を追加して、TodoItem データベースを更新するときに発生する可能性がある競合を処理します。


## 前提条件

このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2013 以降
+ このチュートリアルは、Mobile Services のクイック スタートに基づいています。 このチュートリアルを開始する前に完了しておく必要 [を使ってみるモバイル サービス] です。
+ [Azure アカウント]
+ Azure モバイル サービス NuGet パッケージ 1.1.0 以降。 最新バージョンを入手するには、次の手順に従います。
    1. Visual Studio のソリューション エクスプローラーで、プロジェクトを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。

        ![][19]

    2. **[オンライン]** を展開し、**[Microsoft and .NET]** をクリックします。 検索ボックスに「**Azure Mobile Services**」と入力します。 **[Azure Mobile Services]** NuGet パッケージで **[インストール]** をクリックします。

        ![][20]




## 更新を実行できるようにアプリケーションを更新する

このセクションでは、TodoList ユーザー インターフェイスを更新して、ListBox コントロールの各項目のテキストを更新できるようにします。 ListBox にはデータベース テーブルの各項目に対応する CheckBox コントロールと TextBox コントロールが含まれます。 TodoItem のテキスト フィールドを更新できるようになります。 アプリケーションで処理して、 `LostFocus` 、データベース内の項目を更新するには、その TextBox のイベントをします。


1. Visual Studio でダウンロードした TodoList プロジェクトを開き、[モバイル サービスの開始を取得] チュートリアルです。
2. Visual Studio のソリューション エクスプ ローラーで MainPage.xaml を開くし、置換、 `ListView` の定義を `ListView` 」に記載されて変更を保存します。

        <ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                        <TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

4. Visual Studio Solution Explorer で、共有プロジェクトにある MainPage.cs を開きます。 MainPage に TextBox イベント ハンドラーを追加 `LostFocus` イベントの次のようにします。

        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. 共有プロジェクトの MainPage.cs で、MainPage の定義を追加 `UpdateToDoItem()` メソッド参照されている、イベント ハンドラーの下に示すようにします。

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }


これで、TextBox からフォーカスが外れたときに各項目のテキストの変更がデータベースに書き戻されるようになります。

## アプリケーションでの競合検出を有効にする

シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。 競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。 [オプティミスティック同時実行制御] には、各トランザクションがコミットでき、したがってロックが使用されない任意リソースことが想定しています。 オプティミスティック同時実行制御ではトランザクションをコミットする前に、他のトランザクションがそのデータを変更していないことを確認します。 データが変更されている場合、トランザクションのコミットはロール バックされます。 Azure のモバイル サービスを使用して各項目の変更を追跡することによってオプティミスティック同時実行制御をサポートしている、 `_ _version` 各テーブルに追加されているシステム プロパティ列。 このセクションでは、アプリケーションをこれらの書き込み競合を検出できるように、 `_ _version` システム プロパティです。 アプリケーションでお知らせ、 `MobileServicePreconditionFailedException` と前回のクエリ以降にレコードが変更されている場合、更新中にします。 その際、データベースに変更をコミットするか、前回の変更をそのままデータベースに残すかというどちらかの処理を選択することができます。 Mobile Services のシステムのプロパティの詳細については、[システムのプロパティ] を参照してください。

1. 更新プログラム、共有プロジェクトで TodoItem.cs を開き、 `TodoItem` クラス定義に含める次のコードを `_ _version` システム プロパティの書き込み競合の検出サポートを有効にするとします。

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
            [JsonProperty(PropertyName = "__version")]
            public string Version { set; get; }
        }

    > [AZURE.NOTE] 型指定のないテーブルを使用する場合にオプティミスティック同時実行制御を有効にするには、テーブルの SystemProperties に Version フラグを追加します。
    >
    >`````
    //Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
`````

2. 追加することで、 `バージョン` プロパティを `TodoItem` クラスでは、アプリケーションが通知されます、 `MobileServicePreconditionFailedException` 、前回のクエリ以降にレコードが変更された場合、更新中に例外です。 この例外には、サーバーから取得された最新バージョンの項目が含まれます。 共有プロジェクトの MainPage.cs で、追加の例外を処理する次のコード、 `UpdateToDoItem()` メソッドです。

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }

3. MainPage.cs で、追加の定義、 `ResolveConflict()` で参照されたメソッド `UpdateToDoItem()`します。 競合を解決する順序に注意してください。ローカル項目のバージョンをサーバーから取得された更新後のバージョンに設定した後で、ユーザーの決定をコミットします。 この順序に従わない場合、競合が次々と発生します。

        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
                                                        serverItem.Text, localItem.Text),
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;
                // Updating recursively here just in case another
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };
            ServerBtn.Invoked = async (IUICommand command) =>
            {
                RefreshTodoItems();
            };
            await msgDialog.ShowAsync();
        }




## アプリケーションでデータベース書き込み競合をテストする

このセクションでは、Windows ストア アプリケーション パッケージをビルドして、そのアプリケーションを 2 台目のコンピューターまたは仮想マシンにインストールします。 その後、2 台のコンピューターでアプリケーションを実行し、書き込み競合を発生させて、コードをテストします。 アプリケーションの両方のインスタンスが同じ項目を更新しよう `テキスト` プロパティの競合を解決するのには、ユーザーが必要です。


1. Windows ストア アプリケーション パッケージを作成し、2 台目のコンピューターまたは仮想マシンにインストールします。 それには、Visual Studio で **[プロジェクト]**、**[ストア]**、**[アプリケーション パッケージの作成]** の順にクリックします。

    ![][0]

2. [パッケージの作成] 画面で、Windows ストアにこのパッケージをアップロードするかどうかを確認する質問に対して **[いいえ]** をクリックします。 その後、**[次へ]** をクリックします。

    ![][1]

3. [パッケージの選択と構成] 画面で、既定の設定をそのまま適用し、**[作成]** をクリックします。

    ![][10]

4. [パッケージの作成が完了しました] 画面で、**[出力場所]** リンクをクリックして、パッケージの出力場所を開きます。

    ![][11]

5. パッケージ フォルダー "todolist_1.0.0.0_AnyCPU_Debug_Test" を 2 台目のコンピューターにコピーします。 このコンピューター上でこのパッケージ フォルダーを開き、次に示すように、**Add-AppDevPackage.ps1** PowerShell スクリプトを右クリックして **[PowerShell で実行]** をクリックします。 表示される手順に従ってアプリケーションをインストールします。

    ![][12]

5. Visual Studio で **[デバッグ]**、**[デバッグの開始]** の順にクリックして、アプリケーション インスタンス 1 を実行します。 2 台目のコンピューターの [スタート] 画面で、下向きの矢印ボタンをクリックし、[アプリ 名前順] を表示します。 **todolist** アプリケーションをクリックして、アプリケーション インスタンス 2 を実行します。

    アプリケーション インスタンス 1
    ![][2]

    アプリケーション インスタンス 2
    ![][2]

6. アプリのインスタンス名、最後のアイテムのテキストを更新 **Test Write 1**, 、別のテキスト ボックスをクリックしてできるように、 `LostFocus` イベント ハンドラーによってデータベースが更新されます。 次のスクリーンショットのようになります。

    アプリケーション インスタンス 1
    ![][3]

    アプリケーション インスタンス 2
    ![][2]

7. この時点で、アプリケーション インスタンス 2 内の対応する項目には古いバージョンが含まれています。 アプリの場合、次のように入力します。 **Test Write 2** の、 `テキスト` プロパティです。 別のテキスト ボックスをクリックし、ため、 `LostFocus` イベント ハンドラーが、古いでデータベースを更新しようとしています。 `_version` プロパティです。

    アプリケーション インスタンス 1
    ![][4]

    アプリケーション インスタンス 2
    ![][5]

8. `_ _Version` の更新の試行で使用される値は、サーバーと一致していない `_ _version` 値、Mobile Services SDK のスロー、 `MobileServicePreconditionFailedException` 、アプリケーションでこの競合を解決できるようにします。 競合を解決するには、[**Commit Local Text**] をクリックし、インスタンス 2 から値をコミットします。 または [**Leave Server Text**] をクリックしてインスタンス 2 の値を削除し、コミットしたアプリケーションのインスタンス 1 からの値をそのまま残します。

    アプリケーション インスタンス 1
    ![][4]

    アプリケーション インスタンス 2
    ![][6]



## サーバー スクリプトで競合の解決を自動的に処理する

サーバー スクリプトで書き込み競合を検出し、解決することができます。 ユーザーの操作ではなくスクリプト ロジックによって競合を解決できる場合、この方法を使用することをお勧めします。 このセクションでは、アプリケーションで使用する TodoItem テーブルにサーバー側スクリプトを追加します。 このスクリプトで使用されるロジックでは、次の方法で競合を解決します。

+  場合、TodoItem の ` 完了` は完了したと見なされます] フィールドが true に設定し、 `テキスト` 変更できなくします。
+  場合は、TodoItem の ` 完了` フィールドが false のまま、更新の試行 `テキスト` がコミットされます。

次の手順で、サーバー更新スクリプトの追加とテストの方法を説明します。

1. [Azure 旧ポータルにログイン] をクリックして **Mobile Services**, 、アプリケーションをクリックします。

    ![][7]

2. **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][8]

3. **[スクリプト]** をクリックし、**[更新]** 操作を選択します。

    ![][9]

4. 既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

        function update(item, user, request) {
            request.execute({
                conflict: function (serverRecord) {
                    // Only committing changes if the item is not completed.
                    if (serverRecord.complete === false) {
                        //write the updated item to the table
                        request.execute();
                    }
                    else
                    {
                        request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                    }
                }
            });
        }

5. 両方のコンピューターで **todolist** アプリケーションを実行します。 TodoItem を変更する `テキスト` インスタンス 2 で最後の項目にします。 別のテキスト ボックスをクリックし、ため、 `LostFocus` イベント ハンドラーによってデータベースが更新されます。

    アプリケーション インスタンス 1
    ![][4]

    アプリケーション インスタンス 2
    ![][5]

6. アプリケーション インスタンス 1 で、最後の text プロパティに別の値を入力します。 別のテキスト ボックスをクリックし、そのため、 `LostFocus` イベント ハンドラーは不適切なでデータベースを更新しようとしました。 `_ _version` プロパティです。

    アプリケーション インスタンス 1
    ![][13]

    アプリケーション インスタンス 2
    ![][14]

7. この項目は完了としてマークされていないため、サーバー側スクリプトによって更新が許可され、競合が解決されることから、アプリケーション内では例外が発生しません。 更新が本当に成功したかどうか確認するには、インスタンス 2 の **[Refresh]** をクリックして、データベースに再びクエリを実行してください。

    アプリケーション インスタンス 1
    ![][15]

    アプリケーション インスタンス 2
    ![][15]

8. インスタンス 1 で、最後の Todo 項目のチェック ボックスをオンにして、完了としてマークします。

    アプリケーション インスタンス 1
    ![][16]

    アプリケーション インスタンス 2
    ![][15]

9. インスタンス 2 で最後の TodoItem のテキストとトリガーを更新してください、 `LostFocus` イベントです。 この項目は既に完了しているため、スクリプトが更新を拒否することで競合が解決されます。

    アプリケーション インスタンス 1
    ![][17]

    アプリケーション インスタンス 2
    ![][18]

## 次のステップ

このチュートリアルでは、Windows ストア アプリケーションでモバイル サービスのデータを操作する際の書き込み競合を処理できるようにする方法について説明しました。 次に、次の Windows ストアに関するチュートリアルのいずれかを行うことをお勧めします。

* [アプリに認証の追加]
  <br/>アプリのユーザーを認証する方法について説明します。

* [アプリにプッシュ通知の追加]
  <br/>モバイル サービスを使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。






[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png 
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png 
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png 
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png 
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png 
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png 
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png 
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png 
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png 
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png 
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png 
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png 
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png 
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png 
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png 
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png 
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png 
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png 
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png 
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png 
[optimistic concurrency control]: http://go.microsoft.com/fwlink/?LinkId=330935 
[get started with mobile services]: /develop/mobile/tutorials/get-started/#create-new-service 
[azure account]: http://www.windowsazure.com/pricing/free-trial/ 
[validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet 
[refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet 
[get started with mobile services]: /develop/mobile/tutorials/get-started 
[get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet 
[add authentication to your app]: /develop/mobile/tutorials/get-started-with-users-dotnet 
[add push notifications to your app]: /develop/mobile/tutorials/get-started-with-push-dotnet 
[azure classic portal]: https://manage.windowsazure.com/ 
[windows phone 8 sdk]: http://go.microsoft.com/fwlink/p/?LinkID=268374 
[mobile services sdk]: http://go.microsoft.com/fwlink/p/?LinkID=268375 
[developer code samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146 
[system properties]: http://go.microsoft.com/fwlink/?LinkId=331143 

