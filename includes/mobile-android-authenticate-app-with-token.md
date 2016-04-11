
前の例では、標準のサインインを示しました。標準のサインインでは、アプリケーションが開始されるたびに、クライアントは ID プロバイダーとバックエンド Azure サービスの両方にアクセスする必要があります。 この方法は非効率であるだけなく、多くの顧客が同時にアプリケーションを開始すると、使用率に関連した問題が発生する場合があります。 よって、Azure サービスから返される承認トークンをキャッシュし、最初にその承認トークンの使用を試してから、プロバイダー ベースのサインインを使用するほうが効果的です。 

>[AZURE.NOTE]Azure サービス クライアントによって管理される、またはサービスによって管理される認証を使用しているかどうかに関係なく、バックエンドによって発行されたトークンをキャッシュすることができます。 このチュートリアルでは、サービスによって管理される認証を使用します。


1. ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. 次のメンバーを `ToDoActivity` クラスに追加します。

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. ToDoActivity.java ファイル内で、次の `cacheUserToken` メソッドの定義を追加します。
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    このメソッドでは、プライベートとマークされた設定ファイルに、ユーザー ID とトークンを格納します。 これにより、設定がアプリケーション用にサンドボックス化されるため、デバイスの他のアプリケーションはトークンにアクセスできなくなり、キャッシュへのアクセスが保護されます。 ただし、他のユーザーは、デバイスにアクセスできるようになると、他の手段によってトークン キャッシュにアクセスできる可能性があります。 

    >[AZURE.NOTE]データへのアクセスが機密性の高い、ユーザーがデバイスへのアクセスをする可能性があるにさらに、暗号化トークンを保護できます。 ただし、完全に安全なソリューションは、このチュートリアルの範囲を超えており、またセキュリティの要件によって異なります。


4. ToDoActivity.java ファイル内で、次の `loadUserTokenCache` メソッドの定義を追加します。

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, "undefined"); 
            if (userId == "undefined")
                return false;
            String token = prefs.getString(TOKENPREF, "undefined"); 
            if (token == "undefined")
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5.  *ToDoActivity.java* ファイルで置き換えます、 `authenticate` トークン キャッシュを使用する次のメソッドを持つメソッドです。 Google 以外のアカウントを使用する場合は、ログイン プロバイダーを変更します。

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. アプリケーションをビルドし、有効なアカウントを使用して認証をテストします。 最低 2 回アプリケーションを実行します。 最初の実行で、ログインとトークン キャッシュの作成を求めるプロンプトが表示されます。 その後は実行するたびに認証用のトークン キャッシュが読み込まれ、ログインする必要はなくなります。




