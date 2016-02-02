<properties 
    pageTitle="Windows ユニバーサル アプリ Reach SDK 統合" 
    description="Windows ユニバーサル アプリと Azure Mobile Engagement Reach を統合する方法"
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="07/07/2015" 
    ms.author="piyushjo" />


# Windows ユニバーサル アプリ Reach SDK 統合

実行の統合手順を実行する必要があります、 [Windows ユニバーサル エンゲージメント SDK 統合](mobile-engagement-windows-store-integrate-engagement.md) このガイドの手順の前にします。

## エンゲージメント Reach SDK を Windows ユニバーサル プロジェクトに組み込む

追加するものが必要はありません。 `EngagementReach` リファレンスとリソースは、既にプロジェクト内です。
> [AZURE.TIP] 格納されているイメージをカスタマイズすることができます、 `リソース` ブランド アイコン (その既定のエンゲージメント アイコン) 特に、プロジェクトのフォルダーです。 ユニバーサル アプリに移動することも、 `リソース` フォルダー、共有プロジェクト、アプリ間でコンテンツを共有するには、維持する必要が、 `Resources\EngagementConfiguration.xml` の既定の場所にファイルはプラットフォームに依存します。

## Windows 通知サービスを有効にする

### Windows 8.x および Windows Phone 8.1 のみ

使用するために、 **Windows Notification Service** (WNS といいます) で、 `Package.appxmanifest` 上のファイル `アプリケーション UI` ] をクリックして `すべてのイメージ資産` 左下のボックスです。 ダイアログ ボックスの右 `通知`, 、変更 `トースト対応` から `(未設定)` に `(Yes)`します。

### すべてのプラットフォーム

アプリを Microsoft アカウントとエンゲージメント プラットフォームと同期させる必要があります。 これには、アカウントを作成するか、ログオンする必要があります [windows デベロッパー センター](https://dev.windows.com)します。 その後に、新しいアプリケーションを作成して SID と秘密キーを検索します。 エンゲージメントのフロント エンドのアプリ設定に移動 `ネイティブ プッシュ` し、資格情報を貼り付けます。 その後、プロジェクトを右クリックして `格納` と `アプリケーションを... ストアと関連付ける`します。 同期するには、前に作成したアプリケーションを選択する必要があります。

## Engagement Reach SDK を初期化する

変更、 `App.xaml.cs`:

-   挿入 `EngagementReach.Instance.Init` 直後 `EngagementAgent.Instance.Init` で、 `InitEngagement` メソッド。

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    `EngagementReach.Instance.Init` 専用のスレッドで実行します。 自分で実行する必要はありません。

> [AZURE.NOTE] アプリケーションでプッシュ通知を他の場所で使用しているかどうかは、する必要がある [プッシュ チャネルを共有](#push-channel-sharing) エンゲージメント リーチを使用します。

## 統合

Engagement では、2 通りの方法で Reach の通知とアナウンスを実装できます。オーバーレイ統合と Web ビュー統合です。

オーバーレイ統合の場合、アプリケーションに大量のコードを記述する必要がありません。 EngagementPageOverlay を使用して、ページ、xaml ファイル、cs ファイルにタグ付けをするだけです。 さらに、Engagement の既定のビューをカスタマイズした場合、そのカスタマイズはタグ付けされたすべてのページで共有されるので、一度定義するだけで済みます。 ページが EngagementPageOverlay 以外のオブジェクトから継承する必要がある場合は、処理が中断し、Web ビュー統合を使用するように強制されます。

Web ビュー統合の場合、実装方法がより複雑です。 ただし、アプリ ページが「Page」以外のオブジェクトから継承する必要がある場合は、Web ビューとその動作を統合する必要があります。
> [AZURE.TIP] ルート レベルを追加する必要がある `< グリッド ></グリッド >` 要素すべてのページのコンテンツを囲むようにします。 Web ビュー統合では、Web ビューをこのグリッドの子として追加します。 Engagement コンポーネントを他の場所に設定する必要がある場合は、表示サイズを自分で管理する必要があることに注意してください。

### オーバーレイ統合

Engagement は、通知とアナウンスを表示するためのオーバーレイを提供します。

オーバーレイ統合を使用する場合は、Web ビュー統合を使用しないでください。

.xaml ファイルで、EngagementPage リファレンスを EngagementPageOverlay に変更します。

-   次の内容を名前空間宣言に追加します。

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   置換 `エンゲージメント: EngagementPage` と `エンゲージメント: EngagementPageOverlay`:

**EngagementPage を使用する場合:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
    
            
        </engagement:EngagementPage>

**EngagementPageOverlay を使用する場合:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
    
            
        </engagement:EngagementPageOverlay>

> **EngagementPageOverlay for 8.1+ を使用する場合:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
            <Grid>
              
            </Grid>
        </engagement:EngagementPageOverlay>

次に .cs ファイルで、"EngagementPage" の代わりに "EngagementPageOverlay" でページをタグ付けし、"Microsoft.Azure.Engagement.Overlay" をインポートします。

            using Microsoft.Azure.Engagement.Overlay;

-   置換 `EngagementPage` と `EngagementPageOverlay`:

**EngagementPage を使用する場合:**

            using Microsoft.Azure.Engagement;
    
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**EngagementPageOverlay を使用する場合:**

            using Microsoft.Azure.Engagement.Overlay;
    
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }

このページがエンゲージメント オーバーレイ メカニズムを使用するようになったので、Web ビューを挿入する必要はありません。

エンゲージメント オーバーレイは、xaml ファイルで見つけた最初の「Grid」要素を使用し、ページに 2 つの Web ビューを追加します。 Web ビューが設定される位置を決めるために、「EngagementGrid」という名前のグリッドを次のように定義ができます。

            <Grid x:Name="EngagementGrid"></Grid>

通知とアナウンスのオーバーレイは、xaml ファイルと cs ファイルで直接カスタマイズできます。

-   `EngagementAnnouncement.html` : `アナウンス` web ビュー html デザイン。
-   `EngagementOverlayAnnouncement.xaml` : `アナウンス` xaml デザイン。
-   `EngagementOverlayAnnouncement.xaml.cs` : `EngagementOverlayAnnouncement.xaml` リンク コード。
-   `EngagementNotification.html` : `通知` web ビュー html デザイン。
-   `EngagementOverlayNotification.xaml` : `通知` xaml デザイン。
-   `EngagementOverlayNotification.xaml.cs` : `EngagementOverlayNotification.xaml` リンク コード。
-   `EngagementPageOverlay.cs` : `オーバーレイ` アナウンスと通知は、コードを表示します。

### Web ビュー統合

Web ビュー統合を使用する場合は、オーバーレイ統合を使用しないでください。

エンゲージメントの内容を表示するには、各ページで 2 つの xaml Web ビューを統合し、通知とアナウンスを表示する必要があります。 そのため、次のコードを xaml ファイルに追加します。

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **8.1+ 統合の場合:**

            <engagement:EngagementPage
                xmlns:engagement="using:Microsoft.Azure.Engagement">
                <Grid>
                  
                  <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
                  <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Right" VerticalAlignment="Top"/> 
                </Grid>
            </engagement:EngagementPage>

関連する .cs ファイルは、次の内容である必要があります。

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;
    
    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
    
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
    
                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }
    
              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
    
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
    
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
    
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
    
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    
    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
    
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

> Web ビューが組み込まれている実装では、デバイス画面の向きが変わると、サイズが変わります。

## データのプッシュを操作する (オプション)

アプリケーションが Reach データのプッシュを受信できるようにするには、EngagementReach クラスの 2 つのイベントを実装する必要があります。

"Public App(){}" の App.xaml.cs で次のように追加します。

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

各メソッドのコールバックがブール値を返すことを確認できます。 データのプッシュをディスパッチした後、Engagement はバックエンドにフィードバックを送信します。 コールバックが false を返した場合、 `終了` フィードバックが送信されます。 それ以外の場合、なります `アクション`します。 イベントにコールバックが設定されていない場合、 `ドロップ` フィードバックがエンゲージメントに返されます。
> [AZURE.WARNING] Engagement は、データのプッシュのフィードバックを複数受信することができません。 複数のハンドラーをイベントに設定する計画がある場合は、最後に送信されたものがフィードバックに相当することに注意してください。 この場合、フロントエンドでフィードバックが混同されるのを避けるために、常に同じ値を返すことをお勧めします。

## UI をカスタマイズする (オプション)

### 最初の手順

Reach UI をカスタマイズできるようにします。

これを行うには、サブクラスを作成する必要がある、 `EngagementReachHandler` クラスです。

**サンプル コード:**

            using Microsoft.Azure.Engagement;
    
            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

内容を次に、設定、 `EngagementReach.Instance.Handler` オブジェクトを使用してカスタム フィールド、 `App.xaml.cs` クラス内で、 `App()` メソッドです。

**サンプル コード:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE] 既定では、エンゲージメントは独自の実装を使用して `EngagementReachHandler`します。
> 自分用の実装を作成する必要はなく、そうする場合でも、すべてのメソッドをオーバーライドする必要はありません。 既定の動作では、Engagement の基本オブジェクトを選択します。

### Web ビュー

既定では、Reach は DLL に組み込まれたリソースを使用して、通知とページを表示します。

完全なカスタマイズを可能にするには、Web ビューのみを使用します。 レイアウトをカスタマイズする場合、リソース ファイルでは直接上書き `EngagementAnnouncement.html` と `EngagementNotification.html`します。 必要なすべてのコードでの engagement `< 本文 ></body >` 正常に実行します。 外側のタグを追加することができますが、 `engagement_webview_area`します。

また、独自のリソースを使用することも決定できます。

オーバーライドできます `EngagementReachHandler` サブクラスに使用する、独自のレイアウトを考慮して、エンゲージメントにメソッドが組み込まれたエンゲージメントのメカニズム。

**サンプル コード:**

            // In your subclass of EngagementReachHandler
    
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }

既定では、AnnouncementHTML は、 `ms-appx-web:///Resources/EngagementAnnouncement.html`します。 これは、プッシュ メッセージの内容を設計する html ファイルを示します (Text アナウンス、Web アナウンス、Poll アナウンス)。 AnnouncementName は `engagement_announcement_content`します。 これは、xaml ページの Web ビュー設計の名前です。

NotfificationHTML は `ms-appx-web:///Resources/EngagementNotification.html`します。 これは、プッシュ メッセージの通知を設計する html ファイルを示します。 NotfificationName は `engagement_notification_content`します。 これは、xaml ページの Web ビュー設計の名前です。

### カスタマイズ

Engagement オブジェクトを保持する場合、希望する通知とアナウンスの Web ビューをカスタマイズできます。 Web ビュー オブジェクトは 3 回記述されていることに注意してください。1 回目は xaml ファイルで、2 回目は .cs ファイルの "setwebview()" メソッドで、3 回目は html ファイルで記述されます。

-   xaml ファイルでは、現在のグラフィカル レイアウトの Web ビュー コンポーネントを記述します。
-   .cs ファイルでは、"setwebview()" を定義して、2 つの Web ビュー (通知とアナウンス) のディメンションを設定できます。 この機能は、アプリケーションがサイズ変更する場合に非常に効果的です。
-   Engagement の html ファイルで、Web ビューのコンテンツ、デザイン、互いの要素間の位置を記述します。

### メッセージを起動する

ユーザーがシステム通知 (トースト) をクリックすると、Engagement がアプリケーションを起動して、プッシュ メッセージの内容をロードし、対応するキャンペーンのページを表示します。

アプリケーションが起動されてからページが表示されるまでの間に、(ネットワークの速度に応じて) 遅延が発生します。

読み込み中であることをユーザーに示すには、進行状況バーや進行状況インジケーターなどの視覚的な情報を提供する必要があります。 Engagement 自身では、それに対応することはできませんが、いくつかのハンドラーを提供します。

コールバックを実装するには、App.xaml.cs の "Public App(){}" に次の内容を追加します。

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

「Public app {}」メソッドにコールバックを設定することができます、 `App.xaml.cs` ファイル前が最適に、、 `EngagementReach.Instance.Init()` 呼び出します。
> [AZURE.TIP] 各ハンドラーは、UI スレッドにより呼び出されます。 メッセージボックスや UI 関連のものは、安心して使用できます。

## <a id="push-channel-sharing"></a> プッシュ チャネルの共有

アプリケーションで、他の目的でプッシュ通知を使用している場合は、Engagement SDK のプッシュ チャネル共有機能を使用する必要があります。 これはプッシュが実行されないことを防ぐためです。

- Engagement Reach の初期化に独自のプッシュ チャネルを提供できます。 SDK は新しいものを要求する代わりにこれを使用します。

プッシュ チャネルでエンゲージメント リーチの初期化を更新、 `InitEngagement` メソッドから、 `App.xaml.cs` ファイル。

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- または、単に Reach の初期化後にプッシュ チャネルを使用する場合は、プッシュ チャネルが SDK によって作成された後に取得されるように、Engagement Reach にコールバックを設定することができます。

コールバックは Reach の初期化の**後に**、任意の場所に設定します。

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## カスタム スキーマのヒント

カスタム スキーマを使用すると、 エンゲージメントのフロントエンドから、エンゲージメント アプリケーションで使用する別の種類の URI を送信できます。 などの既定のスキーマ `http、ftp、` Windows により管理されます、ウィンドウが求められる既定のアプリケーションがデバイスにインストールされていない場合。 アプリケーションに対してカスタム スキーマを作成することもできます。

アプリケーションでカスタム スキーマを設定する簡単な方法を開いて、 `Package.appxmanifest` に移動 `宣言` パネルです。 選択 `プロトコル` で使用可能な宣言] スクロール ボックスとそれに追加します。 編集、 `名` フィールド新しいプロトコルと希望する名前。

これでこのプロトコルを使用する次のように編集します。、 `App.xaml.cs` で、 `OnActivated` メソッドでは、ここで、エンゲージメントの初期化もお忘れなく。

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
    
              //TODO design action to do when app is launch
    
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
    
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion



