<properties
   pageTitle="Reliable Service 通信の概要 | Microsoft Azure"
   description="Reliable Service 通信モデルの概要。サービスのリッスン開始、エンドポイントの解決、サービス間の通信などが含まれます。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# Reliable Service 通信モデル

プラットフォームとしての Service Fabric は、サービス間の通信にまったく依存しません。 UDP から HTTP まで、あらゆるプロトコルとスタックに対応します。 サービスの通信方法の選択は、サービス開発者に委ねられています。 Reliable Services アプリケーション フレームワークは、構築済みの通信スタックと、カスタム通信スタックを展開するためのツール (クライアントがサービス エンドポイントを検出して通信するために使用できる抽象化など) を提供します。

## サービス通信のセットアップ

Reliable Services API では、サービス通信に単純なインターフェイスを使用します。 サービスのエンドポイントを開くには、このインターフェイスを実装します。

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

次に、サービス基本クラスのメソッド オーバーライドで通信リスナー実装を返すことで、これを追加します。 

ステートレスの場合、次のようになります。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

ステートフルの場合、次のようになります。

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

どちらの場合も、サービスが複数のリスナーを簡単に使用できるように、リスナーのコレクションを返します。たとえば、HTTP リスナーと別の WebSocket リスナーがあるとします。 各リスナーが名前を取得し、その結果生じる名前とアドレスの組み合わせのコレクションが、クライアントがサービス インスタンスまたはサービス パーティションのリッスン アドレスを要求したときに JSON オブジェクトとして表されます。

ステートレス サービスでは、オーバーライドによって ServiceInstanceListeners のコレクションが返されます。 ServiceInstanceListener には、ICommunicationListener を作成する関数が含まれ、これに名前を付けます。 ステートフル サービスでは、オーバーライドによって ServiceReplicaListeners のコレクションが返されます。 ServiceReplicaListener は、セカンダリ レプリカで ICommunicationListener を開くこともできるため、ステートレスの対応する要素とは若干異なります。 これにより、サービスで複数の通信リスナーを使用できるだけでなく、セカンダリ レプリカで要求を受け入れるリスナーと、プライマリ レプリカでリッスンするだけのリスナーを指定することもできます。 

たとえば、プライマリで RPC 呼び出しのみを受け取る ServiceRemotingListener と、セカンダリ レプリカで読み取り要求を受け取る 2 つ目のカスタム リスナーを使用できます。

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

最後に、サービスの必要なエンドポイントの説明、 [サービス マニフェスト](service-fabric-application-model.md) 、Endpoints セクションです。

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

通信リスナーは、`ServiceInitializationParameters` の `CodePackageActivationContext` から、割り当てられているエンドポイント リソースにアクセスでき、開かれたときに、要求のリッスンを開始できます。

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] エンドポイント リソースは、サービス パッケージ全体に共通して、サービス パッケージがアクティブ化されたときに、Service Fabric によって割り当てられます。 そのため、同じ ServiceHost 共有でホストされているすべてのレプリカは、同じポートを共有します。 これは、通信リスナーがポート共有をサポートする必要があることを意味します。 これを実行する推奨される方法は、通信リスナーが、リッスン アドレスを生成する際に、パーティション ID とレプリカ/インスタンス ID を使用することです。

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

詳細についてはこの記事の執筆、 `ICommunicationListener`, を参照してください [owin 自己ホスト サービスの Microsoft Azure Service Fabric Web API の概要](service-fabric-reliable-services-communication-webapi.md)

## クライアントとサービス間の通信
Reliable Services API は、サービスと通信するクライアントの作成を簡単にする次の抽象化を提供します。

### ServicePartitionResolver
このユーティリティ クラスは、クライアントが実行時に Service Fabric サービスのエンドポイントを特定するのに役立ちます。 サービスのエンドポイントを特定するプロセスは、Service Fabric の用語で、サービス エンドポイント解決と呼ばれます。

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE] FabricClient は、Service Fabric クラスターでさまざまな管理操作の Service Fabric クラスターとの通信に使用されるオブジェクトです。

一般に、クライアント コードでは `ServicePartitionResolver` を直接操作する必要はありません。 それが作成されると、Reliable Service の API の、内部でリゾルバーを使用し、サービスとのクライアント通信に役立つ他のヘルパークラスに渡されます。

### CommunicationClientFactory
`ICommunicationClientFactory` は、ServiceFabric サービスと通信できるクライアントを生成する通信クライアント ファクトリによって実装される基本インターフェイスを定義します。 CommunicationClientFactory の実装は、クライアントが通信しようとする Service Fabric で使用される通信スタックによって異なります。 Reliable Service の API は、この `ICommunicationClientFactory` インターフェイスの基本実装を提供し、すべての通信スタックに共通するタスク (`ServicePartitionResolver` を使用して、サービス エンドポイントを特定するなど) を実行する CommunicationClientFactoryBase<TCommunicationClient> を提供します。 クライアントは、通常 CommunicationClientFactoryBase 抽象クラスを実装して、通信スタック固有ロジックを処理します。

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

### ServicePartitionClient
`ServicePartitionClient` は CommunicationClientFactory (および内部で、ServicePartitionResolver) を使用し、一般的な通信および Service Fabric の一時的な例外の再試行を処理することによって、サービスとの通信に役立ちます。

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

一般的な使用パターンは、次のようになります。

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## 次のステップ
* [Reliable Services のリモート処理によるリモート プロシージャ コール](service-fabric-reliable-services-communication-remoting.md)

* [Reliable Services の OWIN 対応 Web API](service-fabric-reliable-services-communication-webapi.md)

* [Reliable Services との WCF 通信](service-fabric-reliable-services-communication-wcf.md)

 

