<properties
   pageTitle="一般的な問題のトラブルシューティング | Microsoft Azure"
   description="Microsoft Azure Service Fabric でサービスをデプロイするときに発生するお問い合わせの多い問題について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/10/2015"
   ms.author="mattrow"/>



# 一般的な問題のトラブルシューティング

開発コンピューター上のサービスを実行しているときは、使いやすい [Visual Studio のデバッグ ツール](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)します。 リモート クラスター [正常性レポート](service-fabric-view-entities-aggregated-health.md) は常をお勧めします。 これらのレポートにアクセスする最も簡単な方法は、PowerShell を使って、または [SFX](service-fabric-visualizing-your-cluster.md)します。 この記事では、ユーザーがリモート クラスターのデバッグを実行しており、これらのツールのいずれかの使用方法について基本的な知識があることを前提としています。

## アプリケーションのクラッシュ

「パーティションはターゲット レプリカまたはインスタンス カウントより下です」は、サービスがクラッシュしていることを示します。 サービスのクラッシュが発生している場所を特定するには、もう少し調査する必要があります。 大きな規模で実行している場合、一番の味方はよく練られたトレースのセットです。 試してみることをお勧め [Azure 診断](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) 収集し、それらのトレースを表示します。

![SFX パーティションの正常性](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

### サービスまたはアクターの初期化中

サービス タイプの初期化前の例外はすべて、プロセスのクラッシュの原因となります。 この種のクラッシュでは、アプリケーション イベント ログにサービスからのエラーが表示されます。
これらはサービスが初期化される前に最もよく見られる例外です。

| エラー| 説明|
| --- | --- |
| System.IO.FileNotFoundException| このエラーは、多くの場合、アセンブリの依存関係がないために発生します。Visual Studio の CopyLocal プロパティまたは ノードのグローバル アセンブリ キャッシュを確認します。
| System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory) での System.Runtime.InteropServices.COMException| これは、登録されているサービス タイプの名前が、サービス マニフェストと一致していないことを示します。|

[Azure 診断](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) 、すべてのノードのアプリケーション イベント ログを自動的にアップロードするように構成できます。

### RunAsync() または OnActivateAsync()

登録済みのサービス タイプやアクターの初期化中または実行中にクラッシュが発生する場合は、Azure Service Fabric で例外がキャッチされます。 これらは「次のステップ」で説明する EventSource プロバイダーで確認できます。

## 次のステップ

Service Fabric によって提供される次の既存の診断について説明します。

* [信頼性の高いアクターの診断](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services の診断](service-fabric-reliable-services-diagnostics.md)





