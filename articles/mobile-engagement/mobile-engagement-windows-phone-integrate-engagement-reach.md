<properties 
    pageTitle="Windows Phone Silverlight Reach SDK 統合" 
    description="Windows Phone Silverlight アプリと Azure モバイル エンゲージメント Reach を統合する方法"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="07/07/2015" 
    ms.author="piyushjo" />

#Windows Phone Silverlight Reach SDK 統合

実行の統合手順を実行する必要があります、 [Windows Phone Silverlight Engagement SDK 統合](mobile-engagement-windows-phone-integrate-engagement.md) このガイドの手順の前にします。

##エンゲージメント リーチ SDK を Windows Phone Silverlight プロジェクトに埋め込む

追加するものが必要はありません。 `EngagementReach` リファレンスとリソースは既にプロジェクトにします。

> [AZURE.TIP]  格納されているイメージをカスタマイズすることができます、 `Resources` ブランド アイコン (その既定のエンゲージメント アイコン) 特に、プロジェクトのフォルダーです。

##機能を追加する

エンゲージメント Reach SDK には、いくつかの追加機能が必要になります。

`WMAppManifest.xml` ファイルを開き、次の機能が宣言されていることを確認します。

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

1 つ目はトースト通知の表示を許可するために MPNS サービスによって使用されます。 2 つ目は SDK にブラウザーのタスクを埋め込むために使用されます。

`WMAppManifest.xml` ファイルを編集し、`<Capabilities />` タグ内に追加します。

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##Microsoft プッシュ通知サービスを有効にする

使用するために、 **Microsoft プッシュ通知サービス** (MPNS と呼ばれます)、 `WMAppManifest.xml` ファイルが必要な `<App />` タグを `Publisher` 属性は、プロジェクトの名前に設定します。

##Engagement Reach SDK を初期化する

### エンゲージメントの構成

Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで集中管理されます。

このファイルを編集して、リーチの構成を指定します:

-   *省略可能な*, 、間ではなくまたはネイティブのプッシュ (MPNS) がアクティブかどうかを示す `<enableNativePush>` と `</enableNativePush>` タグ (`true` 既定)。
-   *省略可能な*, 、間のプッシュ チャネルの名前を示す `<channelName>` と `</channelName>` タグ、そのために、アプリケーションの現在が使用または空白のままにします。

代わりに指定を実行時に行う場合は、エンゲージメント エージェントを初期化する前に、次のメソッドを呼び出すことができます。

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] アプリケーションの MPNS プッシュ チャネルの名前を指定できます。 既定では、エンゲージメントはアプリ ID に基づいて、名前を作成します。 エンゲージメントの外部でプッシュ チャネルを使用する計画がある場合を除いて、自分で名前を指定する必要はありません。

### エンゲージメントの初期化

`App.xaml.cs` を変更します。

-   次の内容を `using` ステートメントに追加します。

        using Microsoft.Azure.Engagement;

-   `Application_Launching` の `EngagementAgent.Instance.Init` の直後に `EngagementReach.Instance.Init` を挿入します。

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   `EngagementReach.Instance.OnActivated` を `Application_Activated` メソッドに挿入します。

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT]  `EngagementReach.Instance.Init` 専用のスレッドで実行します。 自分で実行する必要はありません。

##アプリ ストアの送信に関する考慮事項

Microsoft はプッシュ通知を使用する場合のルールを設定しています。

Microsoft から [Application Policies] ドキュメント、セクション 2.9。

1) ユーザーにプッシュ通知を受信することに同意するように依頼する必要があります。 次に、設定で、プッシュ通知を無効にする方法を追加します。

EngagementReach オブジェクトは、オプトイン / オプトアウト、`EnableNativePush()` と `DisableNativePush()` を管理する 2 つの方法を提供します。 たとえば、設定でMPNS を有効または無効に切り替えるオプションを作成ができます。

エンゲージメントの構成] \ < windows-電話の sdk-reach-\ > で MPNS を非アクティブ化することもできます。

> 2.9.1) アプリケーションが提供される通知について説明する必要があります最初および **ユーザーの明確な許可 (オプトイン) を入手**, 、および **プッシュ通知を受信しないよう、ユーザー、メカニズムを提供する必要があります**します。 Microsoft プッシュ通知サービスを使用して提供されるすべての通知がユーザーに提供される説明と一致する必要があり、すべてに準拠する必要があります該当 [アプリケーション ポリシー] [Content Policies] と [Additional Requirements for Specific Application Types]します。

2) 多くのプッシュ通知を使用しないでください。 エンゲージメントが代わりに通知を処理します。

> 2.9.2) アプリケーションと Microsoft プッシュ通知サービスのアプリケーションの使用量は、Microsoft が合理的な裁量によって決定した、Microsoft プッシュ通知サービスのネットワーク容量または帯域幅を超えてはいけません。さもないと、過剰なプッシュ通知により、Windows Phone または、その他の Microsoft デバイスまたはサービスに過剰に負担がかかります。また Microsoft のネットワークまたはサーバーまたは Microsoft プッシュ通知サービスに接続されるサード パーティ製のサーバーまたはネットワークに損害を与えたり、干渉したりしてはいけません。

3) 重要な情報の送信では MPNS に依存しないでください。 エンゲージメントは MPNS を使用するため, このルールはエンゲージメントのフロントエンド内で作成されるキャンペーンにも適用されます。

> 2.9.3) Microsoft プッシュ通知サービスは、ミッション クリティカルな通知、それ以外の生死にかかわる問題に影響を与える通知、医療機器または条件に関連するクリティカルな通知を含みますが、これらに限定することなく、これらの送信には使用できません。 MICROSOFT は、Microsoft プッシュ通知サービスの使用またはMicrosoft プッシュ通知サービスの通知の配信が、中断やエラーがなく実行されること、また、リアルタイムで配信されることについての保証を一切いたしません。

**これらの推奨事項を順守しない場合は、アプリケーションが検証プロセスに合格することを保証できません。**

##データのプッシュを操作する (オプション)

アプリケーションが Reach データのプッシュを受信できるようにするには、EngagementReach クラスの 2 つのイベントを実装する必要があります。

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };
    
    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

各メソッドのコールバックがブール値を返すことを確認できます。 データのプッシュをディスパッチした後、Engagement はバックエンドにフィードバックを送信します。 コールバックが false を返した場合、`exit` フィードバックが送信されます。 それ以外の場合は、`action` になります。 イベントにコールバックが設定されていない場合は、`drop` フィードバックがエンゲージメントに返されます。

> [AZURE.WARNING] Engagement では、データ プッシュのフィードバックを複数の受信できません。 複数のハンドラーをイベントに設定する計画がある場合は、最後に送信されたものがフィードバックに相当することに注意してください。 この場合、フロントエンドでフィードバックが混同されるのを避けるために、常に同じ値を返すことをお勧めします。

##UI をカスタマイズする (オプション)

### 最初の手順

Reach UI をカスタマイズできるようにします。

そのためには、`EngagementReachHandler` クラスのサブクラスを作成する必要があります。

**サンプル コード :**

    using Microsoft.Azure.Engagement;
    
    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

次に、`Application_Launching` メソッドに含まれる `App.xaml.cs` クラスのカスタム オブジェクトを使用して、`EngagementReach.Instance.Handler` フィールドの内容を設定します。

**サンプル コード :**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] 既定では、エンゲージメントは独自の実装を使用して `EngagementReachHandler`します。 自分用の実装を作成する必要はなく、そうする場合でも、すべてのメソッドをオーバーライドする必要はありません。 既定の動作では、エンゲージメントの基本オブジェクトを選択します。

### レイアウト

既定では、Reach は DLL に組み込まれたリソースを使用して、通知とページを表示します。

ただし、これらのコンポーネントにブランドを反映するように独自のリソースを使用ができます。

サブクラスに `EngagementReachHandler` メソッドをオーバーライドして、エンゲージメントにレイアウトを使用させることができます。

**サンプル コード :**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP]  `CreateNotification` メソッドは null を返します。 通知は表示されず、リーチ キャンペーンは削除されます。

レイアウトの実装を簡素化するために、コードの基礎として機能する独自の xaml を提供します。 それらはエンゲージメント SDK アーカイブに配置されています (/src/reach/)。

> [AZURE.WARNING] 提供するソースでは、使用されている正確な同じものを示します。 それゆえ、直接変更する場合は、名前空間と名前を忘れずに変更します。

### 通知の位置

既定では、アプリ内通知はアプリケーションの下部左側に表示されます。 `GetNotificationPosition` オブジェクトの `EngagementReachHandler` メソッドをオーバーライドして、この動作を変更できます。

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

現在、`BOTTOM` (既定) と `TOP` の間で選択できます。

### メッセージを起動する

ユーザーがシステム通知 (トースト) をクリックすると、エンゲージメントはアプリを起動し、プッシュ メッセージの内容を読み込んで、対応するキャンペーンのページを表示します。

アプリケーションが起動されてからページが表示されるまでの間に、(ネットワークの速度に応じて) 遅延が発生します。

読み込み中であることをユーザーに示すには、進行状況バーや進行状況インジケーターなどの視覚的な情報を提供する必要があります。 エンゲージメント自身では、それに対応することはできませんが、いくつかのハンドラーを提供します。

コールバックを実装するには次のことを実行します:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
    
    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
    
    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

`Application_Launching` ファイルの `App.xaml.cs` メソッドにコールバックを設定できます。`EngagementReach.Instance.Init()` コールの前が最適です。

> [AZURE.TIP] 各ハンドラーは、UI スレッドにより呼び出されます。 メッセージボックスや UI 関連のものは、安心して使用できます。

[Application Policies]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Additional Requirements for Specific Application Types]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 

