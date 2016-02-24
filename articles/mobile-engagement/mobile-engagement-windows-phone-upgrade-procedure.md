<properties 
    pageTitle="Windows Phone Silverlight SDK アップグレード手順" 
    description="Azure モバイル エンゲージメント向け Windows Phone Silverlight SDK のアップグレード手順"                  
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
    ms.date="08/10/2015" 
    ms.author="piyushjo" />

#Windows Phone Silverlight SDK アップグレード手順

既に古いバージョンの SDK をアプリケーションに統合している場合は、SDK をアップグレードする際に次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。 たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

##1.1.1 から 2.0.0 に移行

Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。 

> [Azure.IMPORTANT] Capptain とモバイル エンゲージメントは、同じサービスではないと、次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。 アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。

以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 1.1.1 に移行し、次の手順を適用してください。

### NuGet パッケージ

置換 **Capptain.WindowsPhone** によって **MicrosoftAzure.MobileEngagement** Nuget パッケージ。

### Mobile Engagement の適用

SDK は `Engagement` という用語を使用します。 この変更を一致させるためにプロジェクトをアップグレードする必要があります。

現在の Capptain NuGet パッケージをアンインストールする必要があります。 [Capptain Resources] フォルダー内のすべての変更が削除されることを検討します。 これらのファイルを保持する場合は、コピーを作成します。

そのあと、新しい Microsoft Azure Engagement NuGet パッケージをプロジェクトにインストールします。 直接検索できます [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement)します。 この操作は、Engagement によって使用されるすべてのリソース ファイルを置換し、プロジェクトの参照に新しい Engagement の DLL を追加します。

Capptain DLL の参照を削除して、プロジェクトの参照をクリーンアップする必要があります。 これを実行しない場合は、Capptain のバージョンが競合してエラーが発生します。

Capptain リソースをカスタマイズした場合、古いファイルの内容をコピーし、新しい Engagement ファイルに貼り付けます。 xaml と cs ファイルの両方を更新する必要があることにご注意ください。

これらの手順が完了したら、新しい Engagement の参照で古い Capptain の参照を置き換える必要があります。

1. すべての Capptain 名前空間の更新が必要です。

    移行前:
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    移行後:
    
        using Microsoft.Azure.Engagement;

2. "Capptain" が含まれているすべての Capptain クラスには、"Engagement" が含まれている必要があります。

    移行前:
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    移行後:
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. xaml ファイルの Capptain 名前空間と属性も変更します。

    移行前:
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    移行後:
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Capptain 画像のようなその他のリソースも名前が変更されて、「Engagement」を使用しますので注意してください。

### アプリケーション ID / SDK キー

Engagement は、接続文字列を使用します。 Mobile Engagement でアプリケーション ID と SDK キーを指定する必要はありません。指定する必要があるのは接続文字列のみです。 接続文字列は、EngagementConfiguration ファイルで設定できます。

Engagement の構成は、プロジェクトの `Resources\EngagementConfiguration.xml` ファイルで設定できます。

このファイルを編集して、次の内容を指定します。

-   `<connectionString>` タグと `<\connectionString>` タグの間のアプリケーション接続文字列。

代わりに指定を実行時に行う場合は、 Engagement エージェントを初期化する前に、次のメソッドを呼び出すことができます。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

アプリケーションの接続文字列が Azure クラシック ポータルに表示されます。

### 項目名の変更

という名前のすべての項目 *capptain* 名前が付いている *エンゲージメント*します。 同様に、 *Capptain* に *エンゲージメント*します。

一般的に使用される Capptain 項目の例:

-   CapptainConfiguration は EngagementConfiguration という名前になりました
-   CapptainAgent は EngagementAgent という名前になりました
-   CapptainReach は EngagementReach という名前になりました
-   CapptainHttpConfig は EngagementHttpConfig という名前になりました
-   GetCapptainPageName は GetEngagementPageName という名前になりました

名前の変更はオーバーライドされたメソッドにも影響することにご注意ください。



 

