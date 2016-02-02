<properties 
 pageTitle="Scheduler の高可用性と信頼性" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>



# Scheduler の高可用性と信頼性

## Azure Scheduler の高可用性

Azure プラットフォーム サービスのコアである Azure Scheduler は、高可用性に加え、geo 冗長サービスのデプロイと geo リージョン ジョブ レプリケーションの両方の機能を提供します。

### geo 冗長サービスのデプロイ

Azure Scheduler は、現在 Azure が対応しているほとんどすべての geo リージョンで UI を介して利用できます。 Azure Scheduler を使用するリージョンの一覧は、 [ここ](http://azure.microsoft.com/regions/#services)します。 ホストされているリージョン内のデータ センターが使用不可と表示されている場合、Azure Scheduler のフェールオーバー機能により、他のデータ センターからそのサービスを利用できます。

### geo リージョンのジョブ レプリケーション

Azure Scheduler のフロント エンドを管理要求のために利用できるほか、ジョブが geo レプリケートされます。 1 つのリージョンに障害が発生すると、Azure Scheduler によってフェールオーバーが行われ、ペアの地理的リージョン内の別のデータ センターでジョブが確実に実行されます。

たとえば、米国中南部でジョブを作成した場合は、Azure Scheduler は、このジョブを自動的に米国中北部にレプリケートします。 Azure Scheduler では、米国中南部で障害が発生した場合、米国中北部でジョブが確実に実行されます。 [Azure リージョンのペアの一覧はここで使用可能な](https://msdn.microsoft.com/library/azure/dn758204.aspx)します。

![][1]

このように、Azure に障害が発生した場合でも、Azure Scheduler により、データは同じより広域の地理的リージョン内に保持されます。 そのため、高可用性を追加するためだけにジョブを複製する必要がなくなります。Azure Scheduler は、ジョブの高可用性機能を自動的に提供します。

## Azure Scheduler の信頼性

Azure Scheduler では、独自の高可用性を保証し、ユーザーが作成したジョブに対して別のアプローチを採用しています。 たとえば、使用できない HTTP エンドポイントをジョブが呼び出したとします。 この場合、Azure Scheduler は、エラーを処理する別のオプションを提供して、ジョブを正常に実行しようと試みます。 Azure Scheduler は、これを次の 2 つの方法で行います。

### "retryPolicy" を使用する構成可能な再試行ポリシー

Azure Scheduler では、再試行ポリシーを構成することができます。 ジョブが失敗した場合、Scheduler は、既定で 30 秒間隔でさらに 4 回ジョブの実行を試みます。 この再試行ポリシーの構成を変更して、より積極的な設定 (たとえば、30 秒間隔で 10 回) にすることも、より緩い設定 (たとえば、1 日に 1 回ずつ 2 回) にすることもできます。

この構成が役に立つ状況の例として、週に 1 回実行されて HTTP エンドポイントを呼び出すジョブを作成したとします。 HTTP エンドポイントが数時間にわたってダウンしているときにこのジョブを実行したとします。既定の再試行ポリシーも失敗するこの状況で、ジョブをもう一度実行するためにさらに 1 週間待つことは避けたいでしょう。 このような場合は、30 秒ごとではなく (たとえば) 3 時間ごとに再試行するように、標準の再試行ポリシーを再構成します。

再試行ポリシーを構成する方法についてを参照してください [retryPolicy](scheduler-concepts-terms.md#retrypolicy)します。

### "errorAction" を使用する別のエンドポイントの構成可能性

Azure Scheduler ジョブの対象のエンドポイントにアクセスできない状態が続く場合、Azure Scheduler は、再試行ポリシーに従った処理を行った後、代替エラー処理エンドポイントにフォールバックします。 代替エラー処理エンドポイントが構成されている場合、Azure Scheduler はその代替エンドポイントを呼び出します。 代替エンドポイントを用意することで、エラーが発生した場合でもジョブの高可用性が確保されます。

たとえば、次の図において、Azure Scheduler は、再試行ポリシーに従ってニューヨークの Web サービスにアクセスします。 再試行が失敗した後、Azure Scheduler は、代替アクションがあるかどうかを確認します。 代替アクションがあることを確認した Azure Scheduler は、同じ再試行ポリシーに基づいて代替サービスに対して要求の送信を開始します。

![][2]

ここで、元のアクションと代替エラー アクションの両方に同じ再試行ポリシーが適用されることに注意してください。 代替エラー アクションをメイン アクションとは異なる種類に設定することもできます。 たとえば、メイン アクションで HTTP エンドポイントを呼び出す一方、エラー アクションでエラーのログ記録を行うストレージ キュー アクションを実行できます。

代替エンドポイントを構成する方法についてを参照してください [errorAction](scheduler-concepts-terms.md#action-and-erroraction)します。

## 関連項目

 [Scheduler とは何ですか。](scheduler-intro.md)

 [Azure Scheduler の概念、用語、およびエンティティ階層](scheduler-concepts-terms.md)

 [Azure ポータルでのスケジューラの使用の概要します。](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [複雑なスケジュールと Azure Scheduler で高度な繰り返しを構築する方法](scheduler-advanced-complexity.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)

 [Azure のスケジューラの PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)


 [Azure Scheduler の制限、規定値、およびエラー コード](scheduler-limits-defaults-errors.md)

 [Azure のスケジューラ送信認証](scheduler-outbound-authentication.md)



[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png 
[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png 

