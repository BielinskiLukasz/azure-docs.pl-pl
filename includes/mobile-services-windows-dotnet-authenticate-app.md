
1. MainPage.xaml.cs プロジェクト ファイルを開き、次の using ステートメントを追加します。

        using Windows.UI.Popups;

2. MainPage クラスに、次のコード スニペットを追加します。
    
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    これにより、現在のユーザーを格納するためのメンバー変数と認証プロセスを処理するためのメソッドが作成されます。 ユーザーは、Facebook ログインを使用して認証されます。 Facebook 以外の id プロバイダーを使用している場合の値を変更 **MobileServiceAuthenticationProvider** 上、プロバイダーの値にします。

3. 既存 **OnNavigatedTo** メソッドのオーバーライドを次のメソッドを呼び出す新しい **認証** メソッド。

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
        
4. F5 キーを押してアプリケーションを実行し、選択した ID プロバイダーでアプリケーションにサインインします。 

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。
