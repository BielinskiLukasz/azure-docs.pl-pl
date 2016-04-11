<properties
   pageTitle="Service Fabric 高信頼アクターの概要 | Microsoft Azure"
   description="Service Fabric Reliable Actors のプログラミング モデルの概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Service Fabric Reliable Actors の概要
高信頼アクター API はによって提供される 2 つの高度なフレームワークの 1 つ [Service Fabric](service-fabric-technical-overview.md), 、と共に、 [Reliable Services API](service-fabric-reliable-services-introduction.md)します。

高信頼アクター API は、アクター パターンに基づいて、Service Fabric によって実現する拡張性と信頼性を利用しながら、コードを簡素化する非同期のシングル スレッド プログラミング モデルを提供します。

## アクター
アクターは、独立したシングル スレッド コンポーネントであり、状態と動作の両方をカプセル化します。 このアクターは、.NET オブジェクトに似ているため、自然なプログラミング モデルを提供します。 .NET オブジェクトが .NET 型のインスタンスであるように、アクターはすべてアクター型のインスタンスです。 たとえば、電卓の機能を実装するアクター型がある場合や、クラスター全体のさまざまなノードで分散されるその型のアクターが多数存在する場合があります。 このようなアクターはそれぞれ、アクター ID で一意に識別されます。

## アクター インターフェイスの定義と実装

アクターは、要求応答パターンを使用して非同期メッセージを渡すことによって、他のアクターを含む、システムの残りの部分と対話します。 これらの対話は、非同期メソッドとしてインターフェイスで定義されます。 たとえば、電卓の機能を実装するアクター型のインターフェイスは、次のように定義される場合があります。

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

アクター型では、このインターフェイスを次のように実装できます。

```csharp
public class CalculatorActor : StatelessActor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

メソッド呼び出しとその応答が、最終的にはクラスター全体でネットワーク要求になるため、引数と返されるタスクの結果の型は、プラットフォームがシリアル化できる必要があります。 具体的には、必要がある [データ コントラクト シリアル化可能](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)します。

> [AZURE.TIP] Fabric アクター ランタイムは、いくつか出力 [アクター メソッドに関するイベントとパフォーマンス カウンター](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)します。 これらは、診断やパフォーマンスの監視に役立ちます。

アクター インターフェイス メソッドに関連する次の規則は特筆に価する機能です。
- アクター インターフェイス メソッドはオーバー ロードすることはできません。
- アクター インターフェイス メソッドを持つことはできません、ref、または省略可能なパラメーターです。

## アクターの通信
### アクター プロキシ
アクター クライアント API は、アクター インスタンスとアクター クライアント間の通信を提供します。 アクターと通信するために、クライアントは、アクター インターフェイスを実装するアクター プロキシ オブジェクトを作成します。 クライアントは、プロキシ オブジェクトでメソッドを呼び出すことによって、アクターと対話します。 アクター間の通信だけでなく、クライアントとアクター間の通信でもアクター プロキシを使用できます。 ここでも電卓を例とします。電卓アクターのクライアント コードは以下のように記述できます。

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

アクター プロキシ オブジェクトの作成には、アクター ID とアプリケーション名という 2 つの情報が使用されていることに注意してください。 アクター ID は、アプリケーション名を識別中に、アクターを一意に識別する識別子、 [Service Fabric アプリケーション](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) で、アクターが配置されています。

### アクターの有効期間

Service Fabric アクターは仮想アクターです。つまり、その有効期間は、メモリ内表現に関連付けられていません。 したがって、明示的に作成したり、破棄したりする必要はありません。 アクター ランタイムは、そのアクターの要求の初回受信時に、自動的にアクターをアクティブ化します。 アクターが一定の時間使用されていない場合、アクター ランタイムは、アクターが存在するという情報を維持しながら、メモリ内オブジェクトをガベージ コレクトします。このアクターは、必要に応じて後で再アクティブ化できます。 詳細については、次を参照してください。 [アクターのライフ サイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)します。

### 場所の透過性と自動フェールオーバー

Service Fabric アクターは、高可用性と信頼性を実現するために、クラスター全体にアクターを分散し、障害が発生したノードのアクターを、正常に稼働しているノードに必要に応じて自動的に移行します。  `ActorProxy` ID を使用してアクターを検索するために必要な解決を実行してクライアント側でクラス [パーティション](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) との通信チャネルを開きます。 `ActorProxy` も、通信エラーやフェールオーバーが発生した場合に再試行します。 これにより障害が発生してもメッセージは確実に配信されます。また、アクター実装によって、同じクライアントから重複するメッセージを取得できることも意味します。

## 同時実行
### ターンに基づくアクセス

アクター ランタイムは、アクター メソッドにアクセスするためのターンに基づくモデルを提供します。 これは、アクター コード内で随時アクティブ化できるスレッドが 1 つだけであることを意味します。

ターンは、その他のアクターまたはクライアントからの要求に応じたアクター メソッドの実行の完了またはの完全な実行、 [タイマーとアラーム](service-fabric-reliable-actors-timers-reminders.md) コールバックします。 これらのメソッドとコールバックが非同期でも、アクター ランタイムはこれらをインターリーブしません。ターンは、新しいターンが許可される前に完全に完了する必要があります。 つまり、現在実行しているアクター メソッドまたはタイマーとアラームのコールバックは、メソッドまたはコールバックの新しい呼び出しが許可される前に完全に完了する必要があります。 実行がメソッドまたはコールバックから返され、メソッドまたはコールバックによって返されたタスクが完了した場合は、そのメソッドまたはコールバックは完了したと見なされます。 ターンごとの同時実行は、メソッド、タイマーおよびコールバックが異なる場合でも優先されることに注意してください。

アクター ランタイムは、ターンの開始時にアクターごとのロックを取得し、ターンの終了時にロックを解除することで、ターンごとの同時実行を強制します。 そのため、ターンごとの同時実行は、アクター全体ではなく、アクターごとに強制されます。 アクターのメソッドおよびタイマーとアラームのコールバックは、別のアクターの代わりに同時に実行できます。

上記の概念を以下の例で示します。 2 つの非同期メソッドを実装するアクター型を考えてみましょう (と答えて *Method1* と *Method2*)、タイマーおよびアラームです。 次の図の 2 つのアクターの代わりのこれらのメソッドとコールバックの実行タイムラインの例を示します *ActorId1* と *ActorId2* -このアクター型に属しています。

![][1]

上記の図では次の規則に従っています。

- 各垂直線は、特定のアクターの代わりにメソッドまたはコールバックを実行する場合の論理フローを示しています。
- 各垂直線上にマークされているイベントは発生順になっており、古いイベントの下に新しいイベントが続きます。
- タイムラインでは、異なるアクターに対応する異なる色が使用されています。
- 強調表示は、アクターごとのロックがメソッドまたはコールバックの代わりに保持される期間を示すために使用されています。

上記の図では次の点に注意する必要があります。

-  *Method1* の代理として実行する *ActorId2* クライアント要求に対して *xyz789*, 、別のクライアント要求 *abc123* が到着するも必要です *Method1* によって実行される *ActorId2*します。 ただし、後者の実行の *Method1* 、前者の実行が完了するまでは開始されません。 同様に、によって登録されたアラーム *ActorId2* 中に発生 *Method1* クライアント要求に応答が実行されている *xyz789*します。 両方の実行後にのみ、アラームのコールバックが実行 *Method1* が完了します。 これはすべてターンごとの同時実行に対して強制されるため *ActorId2*します。
- 同様に、ターンごとの同時実行もに対しては適用 *ActorId1*, の実行で示すように、 *Method1*, 、*Method2* およびタイマーのコールバックの代理として *ActorId1* は逐次的に発生します。
- 実行 *Method1* の代理として *ActorId1* の代理としての実行と重複して *ActorId2*します。 これは、ターンごとの同時実行が、アクター全体ではなくアクター内でのみ強制されるためです。
- メソッドやコールバックのいくつかの実行では、メソッドやコールバックによって返される `Task` はメソッドが返してから完了します。 その他の実行では、メソッドやコールバックが返すまでに`Task` は既に完了しています。 いずれの場合も、アクターごとのロックが解除されるのは、両方が実行された後 (つまり、メソッドやコールバックが返してから、`Task` が完了した後) のみです。

### 再入

アクター ランタイムでは、既定で再入が許可されます。 つまり、アクター A のアクター メソッドが アクター B に対してメソッドを呼び出してから、アクター B がアクター A に対して別のメソッドを呼び出す場合、メソッドは同じ論理呼び出しチェーン コンテキストの一部であるため実行が許可されます。 すべてのタイマーとアラームの呼び出しは新しい論理呼び出しコンテキストで始まります。 参照してください [再入](service-fabric-reliable-actors-reentrancy.md) 詳細についてです。

### 同時実行の保証の範囲

アクター ランタイムは、これらのメソッドの呼び出しを制御する状況でこのような同時実行を保証します。 たとえば、クライアント要求の受信に対する応答として行われるメソッド呼び出しおよびタイマーとアラームのコールバックに対して、このような保証を提供します。 ただし、アクター コードがアクター ランタイムによって提供されるメカニズム以外でこれらのメソッドを直接呼び出す場合、ランタイムは同時実行を保証できません。 たとえば、メソッドが、アクター メソッドによって返されるタスクに関連付けられていない一部のタスクのコンテキストで呼び出される場合、またはアクターが独自に作成するスレッドから呼び出される場合、ランタイムは同時実行を保証することはできません。 そのため、バック グラウンド操作を実行するアクター使用する必要があります [アクターのタイマーまたはアクターのアラーム](service-fabric-reliable-actors-timers-reminders.md) 、ターンごとの同時実行を尊重します。

> [AZURE.TIP] Fabric アクター ランタイムは、いくつか出力 [同時実行制御に関連するイベントとパフォーマンス カウンター](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters)します。 これらは、診断やパフォーマンスの監視に役立ちます。

## アクターの状態管理
Fabric アクターでは、ステートレスまたはステートフルなアクターを作成することができます。

### ステートレス アクター
`StatelessActor` 基本クラスから派生したステートレス アクターには、アクター ランタイムによって管理される状態はありません。 アクターのメンバー変数は、他の .NET 型と同じように、そのメモリ内の有効期間にわたって保持されます。 ただし、アクターはアイドル状態がしばらく続くとガベージ コレクトされるため、その状態は失われます。 同様に、フェールオーバーによって状態が失われる場合もあります。フェールオーバーは、アップグレード、リソース バランシング操作、またはアクター プロセスやアクター プロセスをホストするノードの障害によって発生します。

ステートレス アクターの例を次に示します。

```csharp
class HelloActor : StatelessActor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### ステートフル アクター
ステートフル アクターは、ガベージ コレクションとフェールオーバー全体で保持する必要がある状態を持つことです。 このステートフル アクターは `StatefulActor<TState>` から派生します。`TState` は、保持する必要がある状態の種類です。 状態には、基本クラスの `State` プロパティを使用して、アクター メソッドでアクセスできます。  

状態にアクセスするステートフル アクターの例を次に示します。

```csharp
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

アクターの状態はディスクに保持され、クラスター内の複数のノードにレプリケートされます。これにより、ガベージ コレクションおよびフェールオーバー全体で保持されます。 つまりなどのメソッドの引数と戻り値をアクター状態の型でなければなりません  [データ コントラクト シリアル化可能](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)します。

> [AZURE.NOTE] 参照してください、 [シリアル化に関する信頼性の高いアクター メモ](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) インターフェイスおよびアクター状態の型の定義方法のモードの詳細については、資料です。  

#### アクター状態プロバイダー
状態の格納と取得はアクター状態プロバイダーによって提供されます。 状態プロバイダーは、状態プロバイダーの特定の属性を使用して、アクターごとまたはアセンブリ内のすべてのアクターに構成できます。 アクターがアクティブ化されている場合、その状態はメモリに読み込まれます。 アクター メソッドが完了すると、変更済みの状態は、状態プロバイダーに対してメソッドを呼び出すことで、アクター ランタイムによって自動的に保存されます。 保存中にエラーが発生した場合、アクター ランタイムは、新しいアクター インスタンスを作成し、最後の一貫性のある状態を状態プロバイダーから読み込みます。

既定では、ステートフル アクターはキー値ストアのアクター状態プロバイダーを使用します。この状態プロバイダーは、Service Fabric プラットフォームで提供される分散キー値ストアに構築されます。 詳細についてを参照してください、トピック「 [状態プロバイダーの選択](service-fabric-reliable-actors-platform.md#actor-state-provider-choices)します。

> [AZURE.TIP] アクター ランタイムは、いくつか出力 [アクター状態管理に関連するイベントとパフォーマンス カウンター](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters)します。 これらは、診断やパフォーマンスの監視に役立ちます。

#### 読み取り専用メソッド
既定では、アクター ランタイムは、アクター メソッド呼び出し、タイマーのコールバック、またはアラームのコールバックの完了時にアクター状態を保存します。 状態の保存が完了するまでは、他のアクターの呼び出しは許可されません。  

アクター メソッドで状態が変更されない場合があります。その場合、状態の保存にさらに時間がかかり、システムの全体的なスループットに影響する可能性があります。 これを回避するために、読み取り専用として、状態を変更しないメソッドとタイマーのコールバックをマークすることができます。

次の例は、`Readonly` 属性を使用して、アクター メソッドを読み取り専用としてマークする方法を示しています。

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

タイマーのコールバックも同じように `Readonly` 属性を使用してマークできます。 アラームの場合は、読み取り専用フラグが引数として、アラームを登録するために呼び出される `RegisterReminder` メソッドに渡されます。

## 次のステップ
### 概念
[アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)

[アクターのタイマーとアラーム](service-fabric-reliable-actors-timers-reminders.md)

[アクター イベント](service-fabric-reliable-actors-events.md)

[アクターの再入](service-fabric-reliable-actors-reentrancy.md)

[Fabric アクターの Service Fabric プラットフォームの使用方法](service-fabric-reliable-actors-platform.md)

[KVSActorStateProvider アクターの構成](service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

