<properties
   pageTitle="Service Fabric プログラミング モデル | Microsoft Azure"
   description="Service Fabric では、アクター フレームワークとサービス フレームワークという、サービスを構築するための 2 つのフレームワークが提供されています。それぞれ、シンプル性とコントロールという、両極端の特性を持っています。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/15/2015"
   ms.author="seanmck"/>


# サービスのフレームワークを選択する

Service Fabric は、Reliable Actors API と Reliable Services API という、サービスを構築するための 2 つの高度なフレームワークを提供します。 いずれも同一の Service Fabric コア上に構築されていますが、同時実行、パーティション分割、および通信の点で、簡潔性と柔軟性の釣り合いの取り方に違いがあります。 アプリケーション内の特定のサービスに適切なフレームワークを選択できるように、両方のモデルを理解しておくと便利です。

## Reliable Actors API と Reliable Services API の比較

| **Reliable Actors API**| **Reliable Services API**|
|-----------------------|--------------------------|
| 問題空間に、小規模な独立したユニットの状態およびロジックが多数含まれている| 複数のコンポーネント間でロジックを維持する必要がある|
| シングル スレッドのオブジェクトを操作しつつ、拡張および一貫性の維持を可能にする| 信頼性の高いコレクション (.NET ディクショナリやキューなど) を使用して状態を格納し管理する|
| フレームワークで状態の同時実行性と粒度を管理する| 状態の粒度と同時実行性を自分で制御する|
| プラットフォームで通信を管理する| 通信の管理と、サービスのパーティション分割構成の制御を自分で実行する|

アプリ内のサービスごとに異なるフレームワークを使用することは、非常に合理的であることに留意してください。 たとえば、多くのアクターによって生成されたデータを集計するステートフル サービスを使用する場合があります。

## 次のステップ

- [高信頼アクター Api の詳細についてください。](service-fabric-reliable-actors-introduction.md)
- [高信頼サービス Api の詳細についてください。](../Service-Fabric/service-fabric-reliable-services-introduction.md)





