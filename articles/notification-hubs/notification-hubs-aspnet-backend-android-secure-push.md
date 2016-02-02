<properties
    pageTitle="Azure Notification Hubs の安全なプッシュ"
    description="セキュリティで保護されたプッシュ通知を Azure から Android アプリに送信する方法について説明します。コード サンプルは Java と C# で記述されています。"
    documentationCenter="android"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/05/2015" 
    ms.author="wesmc"/>


# Azure Notification Hubs の安全なプッシュ

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-secure-push.md)
- [iOS](notification-hubs-aspnet-backend-ios-secure-push.md)
- [Android](notification-hubs-aspnet-backend-android-secure-push.md)


## 概要

Microsoft Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。

規制やセキュリティの制約により、アプリケーションでは、標準のプッシュ通知インフラストラクチャからは転送できないものを通知に含める必要がある場合があります。 このチュートリアルでは、クライアントのデバイスとアプリケーションのバックエンドとの間の安全で認証された接続を通して機密情報を送信することによって、同じエクスペリエンスを実現する方法について説明します。

大まかには、フローは次のようになります。

1. アプリケーションのバックエンド:
    - バックエンド データベースに安全なペイロードを格納します。
    - この通知の ID をデバイスに送信します (安全でない情報は送信しません)。
2. 通知を受け取ったときのデバイスのアプリケーション:
    - デバイスは、安全なペイロードを要求するバックエンドにアクセスします。
    - アプリケーションはデバイスに通知としてペイロードを表示できます。

重要なのは、前のフロー (およびこのチュートリアル) では、デバイスは、ユーザーがログインした後、認証トークンをローカル ストレージに保存すると想定していることです。 デバイスはこのトークンを使用して通知の安全なペイロードを取得できるため、これによって完全にシームレスなエクスペリエンスが保証されます。 アプリケーションがデバイスに認証トークンを格納しない、またはそれらのトークンが期限切れの場合、デバイスのアプリケーションは、通知を受け取ったときにアプリケーションの起動を促す一般的な通知を表示する必要があります。 その後、アプリケーションはユーザーを認証し、通知ペイロードを表示します。

この安全なプッシュのチュートリアルでは、プッシュ通知を安全に送信する方法を説明します。 チュートリアルに基づいて記述、 [ユーザーへの通知](notification-hubs-aspnet-backend-android-notify-users.md) チュートリアルでは、最初にそのチュートリアルの手順を完了する必要がありますので。
> [AZURE.NOTE] このチュートリアルを作成し、」の説明に従って通知ハブを構成 [Notification Hubs (Android) の概要](notification-hubs-android-get-started.md)します。

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## Android プロジェクトを変更する

通知の **ID だけを送信するようにアプリケーション バックエンドを変更したため、Android アプリケーションがその通知を処理し、バックエンドをコールバックしてから安全なメッセージを取得して表示するように変更する必要があります。
そのためには、Android アプリケーションが、プッシュ通知を受け取ったときにバックエンドでそれ自体を認証する方法を知っている必要があります。

アプリケーションの共有設定に認証ヘッダー値を保存するために、ここでは *login* フローを変更します。 類似したメカニズムを使用して、ユーザー資格情報を要求せずに、アプリケーションが使用する必要がある任意の認証トークン (OAuth トークンなど) を保存できます。

1. Android アプリケーション プロジェクトで、**MainActivity** クラスの先頭に次の定数を追加します。

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. 引き続き、 **MainActivity** クラス、更新、 `getAuthorizationHeader()` メソッドを次のコードが含まれます。

     private String getAuthorizationHeader() throws UnsupportedEncodingException {
         EditText username = (EditText) findViewById(R.id.usernameText);
         EditText password = (EditText) findViewById(R.id.passwordText);
         String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
         basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
    
         SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
         sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
    
         return basicAuthHeader;
     }

3. 次の追加 `インポート` ステートメントの先頭に、 **MainActivity** ファイル。

        import android.content.SharedPreferences;


ここで、通知を受け取ったときに呼び出されるヘッダーを変更します。

4. **MyHandler** クラスの変更、 `OnReceive()` を格納するメソッド。

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. 追加し、 `retrieveNotification()` プレース ホルダーを置き換える方法 `{バック エンド endpoint}` をバックエンドのデプロイ時に取得したバックエンド エンドポイント。

     private void retrieveNotification(final String secureMessageId) {
         SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
         final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);
    
         new AsyncTask<Object, Object, Object>() {
             @Override
             protected Object doInBackground(Object... params) {
                 try {
                     HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                     request.addHeader("Authorization", "Basic "+authorizationHeader);
                     HttpResponse response = new DefaultHttpClient().execute(request);
                     if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                         Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                         throw new RuntimeException("Error retrieving secure notification");
                     }
                     String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                     JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                     sendNotification(secureNotification.getString("Payload"));
                 } catch (Exception e) {
                     Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                     return e;
                 }
                 return null;
             }
         }.execute(null, null, null);
     }



このメソッドは、共有設定に格納された資格情報によってアプリケーション バックエンドを呼び出して通知コンテンツを取得し、正常な通知として表示します。 この通知は、アプリケーションのユーザーにとっては、他のプッシュ通知とまったく同じように見えます。

認証ヘッダー プロパティが不明な場合やバックエンドによって拒否された場合などに対応できるようにすることをお勧めします。 こうしたケースに対する特定の処理は、ターゲット ユーザーのエクスペリエンスによって異なります。 1 つのオプションとしては、ユーザーに通知と共に認証を求める一般的なプロンプトを表示して、実際の通知を取得する方法があります。

## アプリケーションの実行

アプリケーションを実行するには、以下の手順に従います。

1. **AppBackend** が Azure にデプロイされていることを確認します。 Visual Studio を使用している場合は、**AppBackend** Web API アプリケーションを実行します。 ASP.NET Web ページが表示されます。

2. Eclipse で、物理的な Android デバイスまたはエミュレーターでアプリケーションを実行します。

3. Android アプリケーションの UI で、ユーザー名とパスワードを入力します。 文字列は任意ですが、値は同じである必要があります。

4. Android アプリケーションの UI で、**[ログイン]** をクリックします。 次に、**[プッシュを送信する]** をクリックします。





