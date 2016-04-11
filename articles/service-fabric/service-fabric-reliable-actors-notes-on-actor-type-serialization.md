<properties
   pageTitle="高信頼アクターのアクター型のシリアル化に関する留意事項"
   description="Service Fabric 高信頼アクターの状態とインターフェイスを定義する場合に使用できるシリアル化可能なクラスを定義するための基本的な要件について説明します。"
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Service Fabric Reliable Actors 型のシリアル化に関する留意事項

アクターのインターフェイスと状態を定義する際に留意する必要がある重要な事項がいくつかあり、型はデータ コントラクト シリアル化可能にする必要があります。 データ コントラクトの詳細についてで参照できる [MSDN](https://msdn.microsoft.com/library/ms731923.aspx)します。

## アクター インターフェイスで使用される型

すべてのメソッドで定義されている各メソッドによって返されるタスクの結果の型の引数、 [アクター インターフェイス](service-fabric-reliable-actors-introduction.md#actors) データ コントラクト シリアル化可能にする必要があります。 これで定義されたメソッドの引数にも当てはまります [アクター イベント インターフェイス](service-fabric-reliable-actors-events.md#actor-events)します。 (アクター イベント インターフェイス メソッドは常に void を返します)。
たとえば、`IVoiceMail` インターフェイスで以下のようにメソッドを定義するとします。

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

この場合、`List<T>` は既にデータ コントラクト シリアル化可能な標準の .NET 型です。 `Voicemail` 型をデータ コントラクト シリアル化可能にする必要があります。

```csharp

[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}

```

## アクター状態のクラス

アクター状態をデータ コントラクト シリアル化可能にする必要があります。 たとえば、次のようなアクター クラスの定義があるとします。

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

この場合、状態クラスはクラスとそのメンバーで定義され、注釈として DataContract および DataMember という属性がそれぞれ付けられます。

```csharp

[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}

```

