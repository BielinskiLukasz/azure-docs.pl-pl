<properties 
    pageTitle="Windows ユニバーサル アプリ Engagement SDK 統合" 
    description="Windows ユニバーサル アプリと Azure Mobile Engagement を統合する方法"                  
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

# Windows ユニバーサル アプリ Engagement SDK 統合

> [AZURE.SELECTOR] 
- [ユニバーサル Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

この手順では、Windows ユニバーサル アプリケーションで Engagement の分析機能と監視機能をアクティブ化する最も簡単な方法を説明します。

次の手順は、ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関するすべての統計情報を計算するのに必要なログのレポートを有効にするためのものです。 イベント、エラー、ジョブなどその他の統計の計算に必要なログのレポートは、エンゲージメント API を使用して手動で行う必要があります (を参照してください [、高度なモバイル エンゲージメント Windows ユニバーサル アプリでタグ付け API を使用する方法](mobile-engagement-windows-store-use-engagement-api.md) これらの統計がアプリケーションに依存するためです。

## サポートされているバージョン

Mobile Engagement SDK for Windows ユニバーサル アプリは次を対象とした Windows ランタイム アプリケーションおよびユニバーサル Windows プラットフォーム アプリケーションにのみ統合できます。

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (デスクトップとモバイル ファミリ)

> [AZURE.NOTE] Windows Phone Silverlight を対象とするかどうかを参照してください、 [Windows Phone Silverlight の統合手順](mobile-engagement-windows-phone-integrate-engagement.md)します。

## Mobile Engagement ユニバーサル アプリ SDK をインストールします。

### すべてのプラットフォーム

Mobile Engagement SDK for Windows Universal App と呼ばれる Nuget パッケージとして利用可能な *MicrosoftAzure.MobileEngagement*します。 これは Visual Studio Nuget Package Manager からインストールできます。

### Windows 8.x および Windows Phone 8.1

NuGet はアプリケーション プロジェクトのルートにある `Resources` フォルダーに SDK のリソースを自動的にデプロイします。

### Windows 10 ユニバーサル Windows プラットフォーム アプリケーション

NuGet は、まだ UWP アプリケーションで SDK のリソースを自動的にデプロイしません。 NuGet でリソースのデプロイメントが再導入されるまで手動で行う必要があります。

1.  ファイル エクスプ ローラーを開きます。
2.  次の場所に移動 (**x.x.x** をインストールするサービスのバージョン)。
*%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  ドラッグ アンド ドロップ、 **リソース** ファイル エクスプ ローラーから Visual Studio でプロジェクトのルート フォルダーです。
4.  Visual Studio でプロジェクトを選択し、アクティブ化、 **ファイルをすべて表示** アイコンの上に、 **ソリューション エクスプ ローラー**します。
5.  一部のファイルは、プロジェクトには含まれません。 インポートすることを一度にを右クリックして、 **リソース** フォルダー、 **プロジェクトから除外する** 別右クリックして、 **リソース** フォルダー、 **プロジェクトに含める** 再びフォルダー全体を含める。 すべてのファイル、 **リソース** フォルダーがプロジェクトに追加されています。

エンゲージメント パッケージの展開は上でも見られます *$(Solutiondir) \Packages* するで定義されているまたは *NuGet.config* ファイルです。

## 機能を追加する

Engagement SDK が適切に機能するには、Windows SDK の機能が一部必要になります。

`Package.appxmanifest` ファイルを開き、次の機能が宣言されていることを確認します。

-   `Internet (Client)`

## Engagement SDK を初期化する

### Engagement の構成

Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで集中管理されます。

このファイルを編集して、次の内容を指定します。

-   `<connectionString>` タグと `<\connectionString>` タグの間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、 Engagement エージェントを初期化する前に、次のメソッドを呼び出すことができます。
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Connection string for my Windows Store App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

アプリケーションの接続文字列が Azure クラシック ポータルに表示されます。

### Engagement の初期化

新しいプロジェクトを作成すると、`App.xaml.cs` ファイルが生成されます。 このクラスは、`Application` から継承し、多くの重要なメソッドが含まれています。 また、Engagement SDK を初期化するためにも使用されます。

`App.xaml.cs` を変更します。

-   次の内容を `using` ステートメントに追加します。

        using Microsoft.Azure.Engagement;

-   すべての呼び出しに対して 1 回で Engagement の初期化を共有するメソッドを定義します。

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   `OnLaunched` メソッドで `InitEngagement` を呼び出します。

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   カスタム スキーマ、別のアプリケーション、コマンドラインを使用してアプリケーションが起動されると、`OnActivated` メソッドが呼び出されます。 また、アプリをアクティブ化する際は、 Engagement SDK を初期化する必要があります。 そのために、`OnActivated` メソッドをオーバーライドします。

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] お勧めすると、アプリケーションの別の場所で Engagement の初期化を追加します。

## 基本的なレポート

### 推奨される方法: `Page` クラスをオーバーロードします

ユーザー、セッション、アクティビティ、クラッシュ、テクニカルに関する統計情報を計算するために、エンゲージメントが必要とするすべてのログのレポートを有効にするには、単純にすべての `Page` サブクラスが `EngagementPage` クラスから継承されるようにします。

次に、この操作をアプリケーションのページに対して実行する例を示します。 同じことを、アプリケーションのすべてのページに対して実行できます。

#### C# ソース ファイル

ページの `.xaml.cs` ファイルを変更します。

-   次の内容を `using` ステートメントに追加します。

        using Microsoft.Azure.Engagement;

-   `Page` を `EngagementPage` に置き換えます。

**エンゲージメントを使用しない場合:**
    
        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**エンゲージメントを使用する場合:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] ページをオーバーライドする場合、 `OnNavigatedTo` メソッドを呼び出してください `base.OnNavigatedTo(e)`します。 それ以外の場合、アクティビティは報告されません (、 `EngagementPage` 呼び出し `StartActivity` 内の `OnNavigatedTo` メソッド)。

#### XAML ファイル

ページの `.xaml` ファイルを変更します。

-   次の内容を名前空間宣言に追加します。

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   `Page` を `engagement:EngagementPage` に置き換えます。

**エンゲージメントを使用しない場合:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**エンゲージメントを使用する場合:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### 既定の動作をオーバーライドする

既定では、ページのクラス名は、何も付加しないアクティビティ名として報告されます。 クラスが「Page」サフィックスを使用している場合、Engagement はそれも削除します。

名前の既定の動作をオーバーライドする場合は、単純に次の内容をコードに追加します。

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

`Page` クラスをオーバーロードできない、またはしない場合は、代わりに `EngagementAgent` メソッドを直接呼び出して、アクティビティを開始できます。

ページの `OnNavigatedTo` メソッドの中から `StartActivity` を呼び出すことをお勧めします。

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  セッションは正常に終了したことを確認します。
> 
> Windows ユニバーサル SDK は、アプリケーションを終了する際に、自動的に `EndActivity` メソッドを呼び出します。 そのため、 **高** を呼び出すことをお勧め、 `StartActivity` 、ユーザーのアクティビティが変化するごとにされ、 **NEVER** を呼び出す、 `EndActivity` メソッドでは、このメソッドは送信 Engagement サーバーに現在のユーザーがアプリケーションを終了したことは、このはすべてのアプリケーション ログに影響を与えます。

## 詳細な報告

オプションとして、アプリケーション特有のイベント、エラー、ジョブについて報告できます。そのためには、`EngagementAgent` クラスの別のメソッドを使用します。 Engagement API により、Engagement のすべての高度な機能を使用できます。

詳細については、次を参照してください。 [、高度なモバイル エンゲージメント Windows ユニバーサル アプリでタグ付け API を使用する方法](../mobile-engagement-windows-store-use-engagement-api/)します。

##詳細な構成

### 自動クラッシュ レポートを無効にする

Engagement の自動クラッシュ レポート機能を無効にできます。 その場合、未処理の例外が発生しても、Engagement は何も行いません。

> [AZURE.WARNING] この機能を無効にする場合は、アプリで未処理のクラッシュが発生すると、エンゲージメントはした送信せず、クラッシュ留意 **AND** セッションとジョブも閉じられません。

自動クラッシュ レポートを無効にするには、宣言した方法に応じて、構成をカスタマイズするだけです。

#### `EngagementConfiguration.xml` ファイルを使用する場合

`<reportCrash>` タグと `</reportCrash>` タグの間で、report crash を `false` に設定します。

#### 実行時に `EngagementConfiguration` オブジェクトを使用する場合

EngagementConfiguration オブジェクトを使用して、report crash を false に設定します。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### バースト モード

既定では、Engagement サービスはログをリアルタイムで報告します。 アプリケーションがログを非常に頻繁に報告する場合、ログをバッファーに格納して、それらすべてを定期的に一度に報告する方が適切です (これを「バースト モード」と呼びます)。

そのためには、次のメソッドを呼び出します。

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引数の値は、 **ミリ秒**します。 リアルタイムのログ報告を再度有効にする場合は常に、パラメーターを指定しないか、値 0 を指定して、このメソッドを呼び出します。

バースト モードではわずかにバッテリーの寿命が延びますが、Engagement の監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。 バーストのしきい値は、30000 (30 秒) よりも長くしないことをお勧めします。 保存できるログは 300 項目に制限されていることに気を付けてください。 送信時間が長すぎる場合は、ログがいくつか失われる可能性があります。

> [AZURE.WARNING] 1 秒よりも短い期間には、バーストのしきい値を構成できません。 それを試みた場合、SDK はエラーのトレースを表示し、自動的に既定値 (0 秒) に再設定します。 これにより、SDK はログをリアルタイムで報告するようになります。

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 

