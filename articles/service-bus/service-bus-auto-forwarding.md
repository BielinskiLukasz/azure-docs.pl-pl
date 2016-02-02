<properties 
   pageTitle="Service Bus メッセージング エンティティの自動転送 |Microsoft Azure"
   description="キューまたはサブスクリプションを同じ名前空間に属する別のキューまたはトピックにチェーンする方法を説明します。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/18/2015"
   ms.author="sethm" />


# 自動転送を使用した Service Bus エンティティのチェーン

*自動転送*機能を使用すると、キューまたはサブスクリプションを同じサービスの名前空間に属する別のキューまたはトピックにチェーンできます。 自動転送が有効な場合は、Service Bus は、一方のキューまたはサブスクリプション (転送元) にあるメッセージを自動的に削除し、もう一方のキューまたはトピック (転送先) に追加します。 ここで、転送先エンティティにメッセージを直接送信できることに注意してください。 また、配信不能キューなどのサブキューは別のキューまたはトピックにチェーンできないことにも注意してください。

## 自動転送の使用

自動転送を有効に設定できます、 [QueueDescription.ForwardTo:operator[]][] または [SubscriptionDescription.ForwardTo][] プロパティを [QueueDescription][] または [SubscriptionDescription][] 次の例のように、ソースのオブジェクト。

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

転送先エンティティは、ソース エンティティの作成時に存在している必要があります。 転送先エンティティが存在しない場合、Service Bus は、ソース エンティティを作成するように要求されたときに例外を返します。

自動転送を使用すると、個々のトピックをスケールアウトできます。 Service Bus は、特定のトピックのサブスクリプションの数を制限します。 第 2 レベルのトピックを作成することで、追加のサブスクリプションに対応できます。 Service Bus のサブスクリプションの数に関する制限がない場合でも、トピックの 2 番目のレベルを追加することで、トピック全体のスループットを向上させることができます。

![自動転送のシナリオ][0]

また、自動転送を使用して、メッセージの送信者と受信者を分離することができます。 たとえば、注文処理、在庫管理、顧客関係管理の 3 つのモジュールで構成される ERP システムがあるとします。 これらのモジュールはそれぞれ、対応するトピックのキューに登録されるメッセージを生成します。 Alice と Bob は、顧客に関連するすべてのメッセージに関心のある営業担当者です。 これらのメッセージを受信するため、Alice と Bob は、ERP トピックごとに個人用のキューとサブスクリプションを作成して、自動的にすべてのメッセージが自分のキューに転送されるようにします。

![自動転送のシナリオ][1]

Alice が休暇中の場合、ERP トピックではなく、個人用のキューがいっぱいになります。 このシナリオでは、1 人の営業担当者がメッセージをまったく受信しないため、すべての ERP トピックがクォータに達しません。

## 自動転送に関する考慮事項

転送先エンティティに多数のメッセージが蓄積されクォータを超過した場合や、転送先エンティティが無効な場合、ソース エンティティは転送先に領域ができるまで (またはエンティティが再度有効になるまで)、配信不能キューにメッセージを追加します。 これらのメッセージは、配信不能キューに残り続けるので、配信不能キューで明示的に受信して処理する必要があります。

多くのサブスクリプションを含む複合トピックにするために個別のトピックをチェーンするときには、第 1 レベルのトピックに中程度のサブスクリプションと、2 番目のレベルのトピックに多くのサブスクリプションが含まれるようにすることをお勧めします。 たとえば、第 1 レベルのトピックが 20 のサブスクリプションを持ち、そのそれぞれが 200 のサブスクリプションを持つ第 2 レベルのトピックにチェーンされている場合、第 1 レベルのトピックが 200 のサブスクリプションを持ち、そのそれぞれが 20 のサブスクリプションを持つ第 2 レベルのトピックにチェーンされている場合よりも高いスループットが実現されます。

Service Bus では、メッセージの転送ごとに 1 操作を請求します。 たとえば、別のキューまたはトピックにメッセージを自動転送するようにそれぞれ構成されている 20 のサブスクリプションを持つトピックにメッセージを送信した場合、第 1 レベルのすべてのサブスクリプションがメッセージを受信すると、21 の操作として請求されます。

別のキューまたはトピックにチェーンされているサブスクリプションを作成するには、サブスクリプションの作成者は、転送元エンティティと転送先エンティティの両方に対する**管理**アクセス許可が必要です。 転送元トピックにメッセージを送信する場合は、転送元トピックに対する**送信**アクセス許可のみが必要です。

## 次のステップ

自動転送の詳細については、次のリファレンス トピックを参照してください。

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

サービス バスのパフォーマンスの改善点についての詳細についてを参照してください。 [パーティション分割のメッセージング エンティティ []][]します。


[queuedescription.forwardto]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx 
[subscriptiondescription.forwardto]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx 
[queuedescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx 
[subscriptiondescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx 
[0]: ./media/service-bus-auto-forwarding/IC628631.gif 
[1]: ./media/service-bus-auto-forwarding/IC628632.gif 
[partitioning messaging entities]: service-bus-partitioning.md 

