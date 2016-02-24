<properties 
    pageTitle="Windows Phone Silverlight Engagement SDK 統合" 
    description="Windows Phone Silverlight アプリと Azure Mobile Engagement を統合する方法"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="07/07/2015" 
    ms.author="piyushjo" />

#Windows Phone Silverlight Engagement SDK 統合

> [AZURE.SELECTOR] 
- [Windows ユニバーサル](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

ここでは、Windows Phone Silverlight アプリケーションで Azure  Mobile Engagement の分析やモニタリング機能を有効にする簡単な方法について説明します。

次の手順は、ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関するすべての統計情報を計算するのに必要なログのレポートを有効にするためのものです。 イベント、エラー、ジョブなどその他の統計の計算に必要なログのレポートは、エンゲージメント API を使用して手動で行う必要があります (を参照してください [、高度なモバイル エンゲージメント Windows Phone Silverlight アプリでタグ付け API を使用する方法](mobile-engagement-windows-phone-use-engagement-api.md) の下) これらの統計がアプリケーションに依存するためです。

##サポートされているバージョン

Mobile Engagement SDK for Windows Silverlight は次を対象としたアプリケーションにのみ統合できます。

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Windows Phone 8.1 (Silverlight 以外) を対象とする場合を参照してください、 [Windows Universal の統合手順](mobile-engagement-windows-store-integrate-engagement.md)します。

##Mobile Engagement Silverlight SDK をインストールする

Mobile Engagement SDK for Windows Silverlight と呼ばれる Nuget パッケージとして利用可能な *MicrosoftAzure.MobileEngagement*します。 これは Visual Studio Nuget Package Manager からインストールできます。 

##機能を追加する

Engagement SDK が正常に動作するには、Windows Phone Silverlight SDK の一部の機能が必要になります。

`WMAppManifest.xml` ファイルを開き、次の機能が `Capabilities` パネルで宣言されていることを必ずご確認ください。

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##Engagement SDK を初期化する

### Engagement の構成

Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで集中管理されます。

このファイルを編集して次を指定します:

-   `<connectionString>` タグと `<\connectionString>` タグの間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、 Engagement エージェントを初期化する前に、次のメソッドを呼び出すことができます。

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

アプリケーションの接続文字列が Azure クラシック ポータルに表示されます。

### Engagement の初期化

新しいプロジェクトを作成すると、`App.xaml.cs` ファイルが生成されます。 このクラスは、`Application` から継承し、多くの重要なメソッドが含まれています。 また、Engagement SDK を初期化するためにも使用されます。

`App.xaml.cs` を変更します。

-   次の内容を `using` ステートメントに追加します。

        using Microsoft.Azure.Engagement;

-   `EngagementAgent.Instance.Init` を `Application_Launching` メソッドに挿入します。

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
          EngagementAgent.Instance.Init();
        }

-   `EngagementAgent.Instance.OnActivated` を `Application_Activated` メソッドに挿入します。

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
        }

> [AZURE.WARNING] お勧めすると、アプリケーションの別の場所で Engagement の初期化を追加します。 ただし、`EngagementAgent.Instance.Init` メソッドは UI スレッドではなく専用スレッドで実行する点にご注意ください。

##基本的なレポート

### 推奨される方法: `PhoneApplicationPage` クラスをオーバーロードします

ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関する統計情報を計算するために、Engagement が必要とするすべてのログのレポートを有効にするには、単純にすべての `PhoneApplicationPage` サブクラスが `EngagementPage` クラスから継承されるようにします。

次に、この操作をアプリケーションのページに対して実行する例を示します。 同じことを、アプリケーションのすべてのページに対して実行できます。

#### C# ソース ファイル

ページの `.xaml.cs` ファイルを変更します。

-   次の内容を `using` ステートメントに追加します。

        using Microsoft.Azure.Engagement;

-   `PhoneApplicationPage` を `EngagementPage` に置き換えます。

**エンゲージメントを使用しない場合:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**エンゲージメントを使用する場合:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.WARNING] ページが継承する場合、 `OnNavigatedTo` メソッドに注意、 `base.OnNavigatedTo(e)` を呼び出します。 呼び出しが行われないと、アクティビティがレポートされません。 実際には、`EngagementPage` は `OnNavigatedTo` メソッド内の `StartActivity` を呼び出します。

#### XAML ファイル

ページの `.xaml` ファイルを変更します。

-   名前空間宣言に追加します :

        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   `phone:PhoneApplicationPage` を `engagement:EngagementPage` に置き換えます。

**エンゲージメントを使用しない場合:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**エンゲージメントを使用する場合:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
        
            <!-- layout -->
        </engagement:EngagementPage >

#### 既定の動作をオーバーライドします。

既定では、ページのクラス名は、何も付加しないアクティビティ名として報告されます。 クラスが「Page」サフィックスを使用している場合、Engagement はそれも削除します。

既定の動作の名前をオーバーライドする場合は、次をコードに追加します。

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

アクティビティに関する追加情報を報告する場合は、次の内容をコードに追加します。

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

これらのメソッドは、ページの `OnNavigatedTo` メソッドから呼び出されます。

### 代替の方法: 手動で `StartActivity()` を呼び出す

`PhoneApplicationPage` クラスをオーバーロードできない、またはしない場合は、代わりに `EngagementAgent` メソッドを直接呼び出して、アクティビティを開始できます。

`StartActivity` は PhoneApplicationPage の `OnNavigatedTo` メソッド内で呼び出すことをお勧めします。

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [AZURE.IMPORTANT] セッションは正常に終了したことを確認します。
>
> この SDK は、アプリケーションが閉じられると、`EndActivity` メソッドを自動的に呼び出します。 そのため、 **高** を呼び出すことをお勧め、 `StartActivity` 、ユーザーのアクティビティが変化するごとにされ、 **NEVER** を呼び出す、 `EndActivity` メソッドです。 このメソッドは、現在のユーザーがアプリケーションを終了したと Engagement サーバーに通知するため、すべてのアプリケーション ログが影響を受けるからです。

##詳細な報告

オプションとして、アプリケーション特有のイベント、エラー、ジョブについて報告できます。そのためには、`EngagementAgent` クラスの別のメソッドを使用します。 Engagement API により、Engagement のすべての高度な機能を使用できます。

詳細については、次を参照してください。 [、高度なモバイル エンゲージメント Windows Phone Silverlight アプリでタグ付け API を使用する方法](../mobile-engagement-windows-phone-use-engagement-api/)します。

##詳細な構成

### 自動クラッシュ レポートを無効にする

Engagement の自動クラッシュ レポート機能を無効にできます。 その場合、未処理の例外が発生しても、Engagement は何も行いません。

> [AZURE.WARNING] この機能を無効にする場合は、アプリで未処理のクラッシュが発生すると、エンゲージメントはした送信せず、クラッシュ留意 **AND** セッションとジョブも閉じられません。

自動クラッシュ レポートを無効にするには、宣言されている方法に基づいて構成をカスタマイズします:

#### `EngagementConfiguration.xml` ファイルを使用する場合

`<reportCrash>` タグと `</reportCrash>` タグの間で、report crash を `false` に設定します。

#### 実行時に `EngagementConfiguration` オブジェクトを使用する場合

EngagementConfiguration オブジェクトを使用して、report crash を false に設定します。

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### バースト モード

既定では、Engagement サービスはログをリアルタイムで報告します。 アプリケーションがログを非常に頻繁に報告する場合、ログをバッファーに格納して、それらすべてを定期的に一度に報告する方が適切です (これを「バースト モード」と呼びます)。

そのためには、次のメソッドを呼び出します。

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引数の値は、 **ミリ秒**します。 リアルタイムのログ報告を再度有効にする場合は常に、パラメーターを指定しないか、値 0 を指定して、このメソッドを呼び出します。

バースト モードではわずかにバッテリーの寿命が延びますが、Engagement の監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。 バーストのしきい値は、30000 (30 秒) よりも長くしないことをお勧めします。 保存できるログは 300 項目に制限されていることに気を付けてください。 送信時間が長すぎる場合は、ログがいくつか失われる可能性があります。

> [AZURE.WARNING] 短い期間に、バーストのしきい値を構成することはできません 1 秒よりもします。 1 秒未満にしようとすると、SDK でエラーのトレースが表示され、自動的に既定値の 0 秒にリセットされます。 これにより、SDK はログをリアルタイムで報告するようになります。
 
