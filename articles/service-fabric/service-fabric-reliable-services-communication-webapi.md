<properties
   pageTitle="ASP.NET Web API によるサービス通信 | Microsoft Azure"
   description="Reliable Services API で OWIN 自己ホストと ASP.NET Web API を使用して、サービスの通信を実装する方法を説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# OWIN 自己ホストによる Microsoft Azure Service Fabric Web API の概要

Service Fabric は、サービスがユーザーや相互に通信する方法を決定する際に役立ちます。 このチュートリアルは、Service Fabric で OWIN 自己ホストと ASP.NET Web API を使用してサービスの通信を実装することに重点を置いています *信頼性の高いサービス* API です。 詳しく状態になる、 *信頼性の高いサービス* プラグ可能な通信 API と例として使用する Web API でのカスタム通信リスナー サービスをセットアップする方法について手順を追って表示されます。 Web API 通信リスナーの完全な例を参照してください、 [GitHub の Service Fabric WebApplication のサンプル](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication)します。


## Service Fabric の Web API の概要

ASP.NET Web API は、.NET Framework に基づいて HTTP API を構築するための人気のある強力なフレームワークです。  [Www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) を使い慣れていないに既に場合 Web API の詳細について参照してください。

Service Fabric の Web API は、馴染みのある同じ ASP.NET Web API です。 方法に違いがする *ホスト* Web API アプリケーション (ヒント: IIS を使用することはありません)。 違いを深く理解するために、2 つの部分に分けてみましょう。

 1. Web API アプリケーション (コント ローラー、モデルなど)
 2. ホスト (Web サーバー、通常 IIS)

Web API アプリケーション自体はここでは変わりありません。これまでに作成したことがある Web API アプリケーションと違いはなく、アプリケーション コードのほとんどを単純にそっくり移動できるはずです。 アプリケーションのホストは、IIS でホストしていた場合はこれまでと多少異なる場合があります。 ただし、ホスト部分に入る前に、馴染みのある部分である Web API アプリケーションから始めましょう。


## アプリケーションを作成する

Visual Studio 2015 で、1 つのステートレス サービスと新しい Service Fabric アプリケーションを作成することから始めます。

![Create a new Service Fabric application](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Create a single stateless service](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

これにより、Web API アプリケーションをホストする空のステートレス サービスが得られます。 アプリケーションを最初からセットアップして、どのようにそれをまとめるかを見ていきます。

最初の手順は、Web API のいくつかの NuGet パッケージを取得することです。 使用するパッケージが **Microsoft.AspNet.WebApi.OwinSelfHost**します。 このパッケージには、必要なすべての Web API パッケージが含まれています。 および *ホスト* 重要になる後でパッケージです。

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

パッケージがインストールされたら、基本的な Web API プロジェクト構造の構築を開始できます。 Web API を使用している場合、プロジェクトの構造は非常によくなります。 基本的な Web API ディレクトリの作成から始めます。

 + App_Start
 + コントローラー
 + モデル

App_Start ディレクトリに基本的な Web API 構成クラスを追加します。 ここでは、単に空のメディア タイプ フォーマッタ構成を追加します。

 + FormatterConfig.cs

```csharp

namespace WebApiService
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

Controllers ディレクトリに既定のコントローラーを追加します。

 + DefaultController.cs

```csharp

namespace WebApiService.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    [RoutePrefix("api")]
    public class DefaultController : ApiController
    {
        // GET api/values
        [Route("values")]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        [Route("values/{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        [Route("values")]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        [Route("values/{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        [Route("values/{id}")]
        public void Delete(int id)
        {
        }
    }
}

```

最後に、プロジェクト ルートにルーティング、フォーマッタ、およびその他の構成セットアップを登録するスタートアップ クラスを追加します。 これは、ここで Web API にプラグインもとは、 *ホスト*, 、後で再度参照されます。 スタートアップ クラスを設定中と呼ばれるインターフェイスを作成 *IOwinAppBuilder* 構成メソッドを定義するスタートアップ クラスにします。 Web API が機能するために技術的に必須ではありませんが、後でスタートアップ クラスを柔軟に使用できるようになります。

 + Startup.cs

```csharp

namespace WebApiService
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            config.MapHttpAttributeRoutes();
            FormatterConfig.ConfigureFormatters(config.Formatters);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApiService
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

以上が、アプリケーション部分です。 この時点では、ごく基本的な Web API プロジェクト レイアウトをセットアップしただけです。 過去に作成した Web API プロジェクトや基本的な Web API テンプレートと比較して、今までのところそれほど大きく違うように見えないはずです。 ビジネス ロジックは、通常どおりに、コント ローラーとモデルに進みます。

実際にそれを実行できるように、ホストをどのように処理すればよいでしょうか。


## サービスのホスト

Service fabric でサービスの実行、 *サービス ホスト プロセス* - する実行可能ファイルは、サービス コードを実行します。 Reliable Services API を使用してサービスを作成する場合、および実際に .NET の Service Fabric 上のサービスを作成するほとんどの場合に、サービス プロジェクトは、サービスの種類を登録して、コードを実行する .EXE にコンパイルするだけです。 実際には、開く場合 **Program.cs** ステートレス サービス プロジェクトに表示されます。

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType("WebApiServiceType", typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

コンソール アプリケーションへのエントリ ポイントのように見える場合は、それが次のようになっているためです。

サービス ホスト プロセスとサービスの登録に関する詳細は扱いませんが、この記事を理解しておく必要できた **独自のプロセスで、サービスのコードが実行されている**します。

## OWIN ホストによる自己ホスト型 Web API

Web API アプリケーション コードが、その独自のプロセスでホストされている場合、それをどのようにして Web サーバーに接続するのでしょうか。 入力 [OWIN](http://owin.org/)します。 OWIN は .NET Web アプリケーションと Web サーバー間の単なるコントラクトです。 従来、ASP.NET (MVC 5 まで) では、Web アプリケーションは System.Web 経由で IIS に厳密に結合されていました。 しかし、Web API は OWIN を実装しているため、ホストされる Web サーバーから分離した Web アプリケーションを書くことができます。 これにより、使用して、 *自己ホスト* OWIN web サーバーを独自のプロセスでは、先ほど説明した Service Fabric ホスティング モデルにぴったり収まるにすることができます。

この記事では、Web API アプリケーションの OWIN ホストとして Katana を使用します。 Katana は、オープン ソース OWIN ホスト実装です。

> [AZURE.NOTE] Katana の詳細についてに進んで、 [Katana サイト](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana), 、Katana を使用して Web API を自己ホストする方法の簡単な概要については、この記事を確認および [OWIN 移動 ASP.NET Web API 2 を使用して](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)します。


## Web サーバーのセットアップ

Reliable Services API が提供する通信エントリ ポイントに通信スタックを接続し、ユーザーおよびクライアントがサービスに接続できるようにすることができます。

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Web サーバーおよび Websocket などの今後使用する可能性のある他の任意の通信スタックは、システムに正しく統合されるように ICommunicationListener インターフェイスを使用する必要があります。 この理由は、次の手順で明らかになります。

まず ICommunicationListener を実装する OwinCommunicationListener というクラスを作成します。

 + OwinCommunicationListener.cs:

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

ICommunicationListener インターフェイスは、サービスの通信リスナーを管理する 3 つのメソッドを提供します。

 + **OpenAsync**: 要求のリッスンを開始します。
 + **CloseAsync**: 要求のリッスンを停止し、実行中の要求を完了し、正常にシャット ダウンします。
 + **中止**: すべてのものをキャンセルし、すぐに停止します。

最初に、URL パス プレフィックスのプライベート クラス メンバーを追加し、 **スタートアップ** 先ほど作成したクラスです。 これらは、コンストラクターによって初期化され、後でリッスンする URL を設定するときに使用されます。 さらに、初期化時と、後でサーバーが起動したときに、それぞれ作成されるリッスンしているアドレスとサーバー ハンドルを保存するためのプライベート クラス メンバーを追加します。

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;
    private readonly ServiceInitializationParameters serviceInitializationParameters;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
    {
        this.startup = startup;
        this.appRoot = appRoot;
        this.serviceInitializationParameters = serviceInitializationParameters;
    }        

    ...

```

### 実装

Web サーバーをセットアップするには、2 つの情報が必要です。

 + **URL パス プレフィックス**します。 オプションですが、アプリケーションで複数の Web サービスを安全にホストできるように、ここでこれを設定することをお勧めします。
 + **ポート**します。

Web サーバーのポートを取得する前に、Service Fabric が、アプリケーションとそれが実行される基盤のオペレーティング システム間のバッファーとして機能するアプリケーション層を提供することを理解しておくことが重要です。 Service Fabric を構成する方法を提供するよう、 *エンドポイント* サービスです。 Service Fabric は、サービスでエンドポイントを確実に使用できるように準備するため、ユーザーは基盤の OS 環境で、自分でそれを構成する必要はありません。 これにより、さまざまな環境で、Service Fabric アプリケーションを変更する必要なく簡単にホストできます (たとえば、Azure や独自のデータ センターで、同じアプリケーションをホストできます)。

PackageRoot\ServiceManifest.xml で HTTP エンドポイントを構成します。

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

サービス ホスト プロセスは制限付き資格情報 (Windows 上のネットワーク サービス) で実行されますが、これはサービスが独自の HTTP エンドポイントを設定するためにアクセスできないことを意味するため、この手順が重要になります。 エンドポイント構成を使用することによって、Service Fabric は、エンドポイントを構成する標準の場所を提供しながら、サービスがリッスンする URL の正しい ACL を設定することを認識します。


OwinCommunicationListener.cs に戻り、OpenAsync の実装を始めることができます。 ここで Web サーバーを開始します。 最初に、エンドポイントの情報を取得し、サービスがリッスンする URL を作成します。

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
    ...

```

ここで "http://+" が使われていることに注意してください。 これは、Web サーバーが、localhost、FQDN、マシン IP など、使用可能なすべてのアドレスで確実にリッスンするようにするためです。

OpenAsync を実装することは、Web サーバー (または任意の通信スタック) が、サービスで RunAsync() から直接開かれるのではなく、ICommunicationListener として実装されている最も重要な理由の 1 つです。 OpenAsync からの戻り値は、Web サーバーがリッスンしているアドレスです。 このアドレスがシステムに返されると、システムはアドレスをサービスに登録します。 Service Fabric は、クライアントやその他のサービスがサービス名でこのアドレスを要求できるようにする API を提供します。 これは、機能は、サービス アドレスが静的でないサービスがリソースの負荷分散と可用性を高めるのためのクラスター内で移動されたため重要です。 これは、クライアントがサービスのリッスンしているアドレスを解決できるようにするメカニズムです。

それを考慮して、OpenAsync は Web サーバーを起動し、リッスンしているアドレスを返します。 それは "http://+" でリッスンしていますが、アドレスを返す前に、それが現在存在しているノードの IP または FQDN で "+" を置き換えます。 この理由は、メソッドによって返されるこのアドレスが、システムに登録されたものであり、クライアントやその他のサービスがサービスのアドレスを要求したときに表示されるものであるためです。 クライアントがそれに正しく接続するためには、アドレスの実際の IP または FQDN が必要です。

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

この参照、 **スタートアップ** コンス トラクターで OwinCommunicationListener に渡されたクラスです。 この Startup インスタンスは、Web サーバーが Web API アプリケーションを起動するために使用します。

後でアプリケーションを実行したときに、Web サーバーが正常に起動していることを知らせるため、診断イベント ウィンドウに ServiceEventSource.Current.Message() 行が表示されます。

### CloseAsync と Abort

最後に、Web サーバーを停止する　CloseAsync と Abort の両方を実装します。 Web サーバーを停止するには、OpenAsync 時に作成されたサーバー ハンドルを破棄します。

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

この例の実装では、CloseAsync と Abort のどちらも単に Web サーバーを停止するだけです。 CloseAsync で、実行中の要求の完了を待ってから戻るなど、Web サーバーのより適切に調整されたシャットダウンを実行することもできます。

## Web サーバーの起動

これで、OwinCommunicationListener のインスタンスを作成して、返し、Web サーバーを起動する準備が整いました。 オーバーライドをサービス クラス (Service.cs) に戻り、 **CreateServiceInstanceListeners()** メソッド。

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

これは、ような場合、Web API *アプリケーション* と OWIN *ホスト* 最終的に接触: *ホスト* (**OwinCommunicationListener**) のインスタンスが与え、 *アプリケーション* (経由の Web API **スタートアップ**)、および Service Fabric がそのライフ サイクルを管理します。 この同じパターンは、一般に通信スタックでも従うことができます。

## まとめ

この例では、RunAsync() メソッドで何も実行する必要はないため、オーバーライドを単純に削除できます。

最後のサービス実装は、通信リスナーを作成する必要があるだけであるため、きわめて単純なはずです。

```csharp

namespace WebApiService
{
    using System.Collections.Generic;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;
    using Microsoft.ServiceFabric.Services.Runtime;

    public class WebApiService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
            };
        }
    }
}

```

さらに、`OwinCommunicationListener` クラスを完成させます。

```csharp

namespace WebApiService
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private readonly ServiceInitializationParameters serviceInitializationParameters;
        private IDisposable serverHandle;
        private string listeningAddress;
        
        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
        {
            this.startup = startup;
            this.appRoot = appRoot;
            this.serviceInitializationParameters = serviceInitializationParameters;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');

            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
            string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

            return Task.FromResult(publishAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            ServiceEventSource.Current.Message("Close");

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            ServiceEventSource.Current.Message("Abort");

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

すべての部分を適切に配置したら、プロジェクトは、Reliable Services API エントリ ポイントと OWIN ホストのある一般的な Web API アプリケーションのようになったはずです。


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Web ブラウザーによる実行と接続

実行していない場合、 [開発環境を設定する](service-fabric-get-started.md)です。


これでサービスを構築し、デプロイできます。 キーを押して **f5 キーを押して** Visual studio をビルドして、アプリケーションをデプロイします。 診断イベント] ウィンドウで、web サーバーで開かれたことを示すメッセージが表示する必要があります **http://localhost:80/webapp/api**


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] ポートが既にある場合、コンピューター上の別のプロセスによって開かれて、リスナーを開くことができませんでしたを示すエラーが表示されます。 その場合は、ServiceManifest.xml のエンドポイント構成で、別のポートを使用してみてください。


ブラウザーを開き、サービスが実行されるに移動 [http://localhost/webapp/api/values](http://localhost/webapp/api/values) それをテストします。

## スケール アウト

ステートレス Web アプリをスケール アウトすることは、コンピューターを追加して、それらで Web アプリを実行することを意味します。 Service Fabric のオーケストレーション エンジンは、新しいノードがクラスターに追加されるたびに、自動的にこれを実行します。 ステートレス サービスのインスタンスを作成する場合、作成するインスタンスの数を指定できます。 Service Fabric はそれに従って、クラスターのノードにその数のインスタンスを配置し、1 つのノードに複数のインスタンスを作成しないようにします。 インスタンス数に「-1」を指定して、常にすべてのノードにインスタンスを作成するように、Service Fabric に指示することもできます。 これにより、クラスターにノードを追加して、クラスターをスケールアウトするたびに、新しいノードにステートレス サービスのインスタンスが作成されることが保証されます。 この値はサービス インスタンスのプロパティであるため、PowerShell によってサービス インスタンスを作成するときに設定します。

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

または Visual Studio ステートレス サービス プロジェクトで既定のサービスを定義するときに設定します。

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

アプリケーションとサービス インスタンスの作成の詳細については、次を参照してください。 [のデプロイとアプリケーションを削除する方法](service-fabric-deploy-remove-applications.md)します。

## ASP.NET 5

ASP.NET 5 では、概念および分離することのあるプログラミング モデルで、 *アプリケーション* から、 *ホスト* web アプリケーションでは同じです。 それは他の形式の通信にも適用できます。 さらに、ですが、 *ホスト* ASP.NET 5、Web API で異なる場合があります *アプリケーション* レイヤーは同じままである、アプリケーション ロジックの大半が実際に存在します。

## 次のステップ

[Visual Studio での Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)

