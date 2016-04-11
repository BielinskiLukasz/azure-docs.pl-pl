<properties 
    pageTitle="Engagement API を Android で使用する方法" 
    description="最新の Android SDK - Engagement API を Android で使用する方法"
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/10/2015" 
    ms.author="piyushjo" />

#Engagement API を Android で使用する方法

このドキュメントは、ドキュメントへのアドオン [Engagement を Android に統合する方法](mobile-engagement-android-integrate-engagement.md)します。 エンゲージメント API を使用してアプリケーションの統計情報を報告する方法についての詳細を提供しています。

Engagement を使用してアプリケーションのセッション、アクティビティ、クラッシュ、および技術情報に関するレポートのみを作成する場合、すべての `Activity` サブクラスを対応する `EngagementActivity` クラスから継承するように設定する方法が簡単です。

他の操作を実行する場合、たとえば、アプリケーションの特定のイベント、エラー、ジョブを報告する場合や、`EngagementActivity` クラスに実装されているのとは別の方法でアプリケーションのアクティビティを報告する必要がある場合は、エンゲージメント API を使用する必要があります。

エンゲージメント API は `EngagementAgent` クラスによって提供されます。 このクラスのインスタンスは、`EngagementAgent.getInstance(Context)` 静的メソッドを呼び出すことで取得できます (返される `EngagementAgent` オブジェクトはシングルトンです)。

##エンゲージメントの概念

次の部分は、一般的なを絞り込む [モバイル エンゲージメントの概念](mobile-engagement-concepts.md), 、Android プラットフォーム用です。

### `Session` と `Activity`

複数の数秒間アイドル状態を状態が続く場合 *アクティビティ*, のシーケンス *アクティビティ* 異なる 2 つに分割される *セッション*します。 この数秒間のことを「セッション タイムアウト」と呼びます。

 *アクティビティ* つまりは通常、アプリケーションの 1 つの画面に関連付けられて、 *アクティビティ* 画面が表示され、画面を閉じるときに停止したときに開始: を使用して、エンゲージメント SDK を統合した場合、そうでは、 `EngagementActivity` クラスです。

 *アクティビティ* 制御することも手動でエンゲージメント API を使用しています。 これにより、特定の画面をいくつかのサブ パートに分割して、この画面の使用状況の詳細情報を取得できます (たとえば、ダイアログがこの画面内で使用される頻度や時間を知ることができます)。

##アクティビティを報告する

> [AZURE.IMPORTANT] レポートなどのアクティビティを使用している場合、このセクションで説明する必要はありません、 `EngagementActivity` クラスとそのバリアント Android ドキュメントのエンゲージメントを統合する方法」に記載されているとします。

### ユーザーが新しいアクティビティを開始する

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

ユーザー アクティビティが変更されるたびに `startActivity()` を呼び出す必要があります。 この関数の最初の呼び出しで、新しいユーザー セッションが開始します。

この関数は、各アクティビティの `onResume` コールバックで呼び出すのが最適です。

### ユーザーが現在のアクティビティを終了する

            EngagementAgent.getInstance(this).endActivity();

ユーザーが最後のアクティビティを終了する際には、`endActivity()` を少なくとも 1 回呼び出す必要があります。 これにより、ユーザーが現在休止状態にあり、セッション タイムアウトの期限が終了したタイミングでユーザー セッションを閉じる必要があることを Engagement SDK に通知します (セッション タイムアウトの期限が終了する前に `startActivity()` を呼び出すと、そのセッションが再開されます)。

この関数は、各アクティビティの `onPause` コールバックで呼び出すのが最適です。

##イベントを報告する

### セッション イベント

通常、セッション イベントは、セッション中にユーザーによって実行されるアクションの報告に使用されます。

**余分なデータがない例:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**余分なデータがある例:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### スタンドアロン イベント

セッション イベントとは逆に、スタンドアロン イベントはセッション外でも発生する場合があります。

**例:**

ブロードキャスト受信者がトリガーされたときに発生するイベントについてレポートを作成する場合は、次のとおりです。

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##エラーの報告

### セッション エラー

通常、セッション エラーは、セッション中にユーザーに影響するエラーの報告に使用されます。

**例:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### スタンドアロン エラー

セッション エラーとは反対に、スタンドアロン エラーはセッションのコンテキストの外で発生します。

**例:**

次の例は、アプリケーション プロセスの実行中に携帯電話のメモリーが少なくなったタイミングで出力されるエラーについてレポートを作成する方法を示します。

            public MyApplication extends EngagementApplication {
            
              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##ジョブを報告する

### 例

ログイン プロセスの実行時間を報告する場合を想定します。
            
            [...]
            public void signIn(Context context, ...) {
            
              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
            
              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);
            
              [... sign in ...]
            
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### ジョブ中のエラーを報告する

エラーは、現在のユーザー セッションに関連付ける代わりに、実行中のジョブに関連付けることができます。

**例:**

ログイン プロセス中に発生したエラーについてレポートを作成する場合は、次のとおりです。

[...]
public void signIn (コンテキストのコンテキスト、...){

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
            
              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);
            
              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);
            
                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### ジョブ中のイベントに関するレポートを作成する

イベントは、現在のユーザー セッションではなく、実行中のジョブに関連付けることができます。

**例:**

ソーシャル ネットワークを持っていて、ジョブを使用して、ユーザーがサーバーに接続している合計時間を報告する場合を想定します。 ユーザーは別のアプリケーションを使用している場合や携帯電話がスリープ状態にある場合でもバックグラウンドで接続されていることがあるため、セッションはありません。

ユーザーは友達からメッセージを受信できます。これがジョブ イベントです。
            
            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##追加のパラメーター

任意のデータをイベント、エラー、アクティビティ、ジョブに添付できます。

このデータは構造化可能で、Android の Bundle クラスを使用します (実際は Android Intent の追加パラメーターとして機能します)。 Bundle にはアレイや他の Bundle インスタンスが含まれる場合があります。

> [AZURE.IMPORTANT] Parcelable またはシリアル化可能なパラメーターを配置した場合に必ずその `toString()` 人間が判読できる文字列を返すメソッドを実装します。 Serializable クラスにシリアライズできない永続的なフィールドが含まれている場合、`bundle.putSerializable("key",value);` を呼び出すと Android がクラッシュします。

> [AZURE.WARNING] 余分なパラメーターのスパース アレイはサポートされませんが、配列としては、シリアル化されません。 追加パラメーターで使用する前に標準アレイに変換してください。

### 例

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### 制限

#### 構成する

`Bundle` の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

キーは、文字、数字、アンダー スコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### サイズ

Extras は **1024年** (Engagement サービスによってエンコードされた JSON で 1 回) 呼び出しにつき文字です。

前の例では、サーバーに送信される JSON は 58 文字です。

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##アプリケーションの情報を報告する

`sendAppInfo()` 関数を使用して、追跡情報 (または他のアプリケーション固有の情報) を手動で報告できます。

これらの情報は段階的に送信される可能性があることにご注意ください。特定のキーの最新の値のみが特定のデバイスに保持されます。

イベントの追加分などでは、アプリケーション情報の抽出に Bundle クラスが使用され、アレイやサブバンドルが (JSON シリアル化を使用して) フラットな文字列として扱われるのでご注意ください。

### 例

ユーザーの性別や誕生日を送信するサンプル コードはこちらです。

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### 制限

#### 構成する

`Bundle` の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

キーは、文字、数字、アンダー スコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### サイズ

アプリケーションの情報に限定 **1024年** (Engagement サービスによってエンコードされた JSON で 1 回) 呼び出しにつき文字です。

前の例では、サーバーに送信される JSON は 44 文字です。

            {"expiration":"2016-12-07","status":"premium"}
 

