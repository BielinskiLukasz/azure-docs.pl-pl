<properties
    pageTitle="Azure SQL Database のリソース制限"
    description="このページでは、Azure SQL Database に対するいくつかの一般的なリソース制限について説明します。"
    services="sql-database"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="11/03/2015"
    ms.author="jroth" />


# Azure SQL Database のリソース制限

## 概要

Azure SQL Database では 2 つの異なるメカニズムを使用してデータベースに利用可能なリソース管理: **リソース ガバナンス** と **の制限の強制**します。 このトピックでは、リソース管理のこれら 2 つの主な領域について説明します。

## リソース ガバナンス
Basic、Standard、および Premium サービス プランの設計目標の 1 つは、Azure SQL Database の動作を、データベースが他のデータベースから完全に分離された専用のコンピューターで稼働しているかのようにすることです。 リソース ガバナンスは、この動作をエミュレートします。 集計されたリソース使用率が、データベースに割り当てられた利用可能な CPU、メモリ、ログ I/O、データ I/O のリソースの最大値に達すると、リソース ガバナンスは、実行中のクエリをキューに配置し、リソースが解放されると、キューに配置されたクエリにそのリソースを割り当てます。

専用のコンピューターの場合と同様、利用可能なリソースをすべて利用すると、現在実行中のクエリの実行時間が長くなり、コマンドがクライアントでタイムアウトする可能性があります。 積極的な再試行ロジックを使用するアプリケーションやデータベースに対して高い頻度でクエリを実行するアプリケーションでは、同時要求の制限に達したときに新しいクエリを実行しようとするとエラー メッセージが表示される場合があります。

### 推奨事項:
データベースの最大使用率に近づいてきたら、リソース使用率に加えて、クエリの平均応答時間も監視します。 クエリの待機時間が長くなる場合、一般的には 3 つのオプションがあります。

1.  データベースへの着信要求を少なくして、タイムアウトと大量の要求を防ぎます。

2.  データベースにより高いパフォーマンス レベルを割り当てます。

3.  クエリを最適化して、各クエリのリソース使用率を引き下げます。 詳細については、Azure SQL Database パフォーマンス ガイダンスのクエリのチューニングとヒント設定に関するセクションを参照してください。

## 制限の適用
制限に達すると、新しい要求を拒否することによって CPU、メモリ、ログ I/O、およびデータ I/O 以外のリソースが適用されます。 クライアントは受信、 [エラー メッセージ](sql-database-develop-error-messages.md) に達して制限によって異なります。

たとえば、SQL Database への接続数と処理可能な同時要求の数が制限されます。 SQL Database では、接続プールをサポートするために、データベースへの接続数が同時要求の数を上回ることを許可します。 利用可能な接続数はアプリケーションで簡単に制御できるのに対し、並列要求の数の見積もりや制御は困難であることがよくあります。 特に、負荷のピーク時に、アプリケーションから送信される要求が多すぎる場合や、データベースがそのリソースの制限に達し、クエリの実行時間が長くなることが原因でワーカー スレッドが蓄積され始める場合に、エラーが発生することがあります。 

## サービス プランとパフォーマンス レベル

単一のデータベースの場合、データベースの制限はデータベース サービス階層とパフォーマンス レベルによって決まります。 次の表では、パフォーマンス レベルごとの Basic、Standard、Premium の各データベースの特性について説明します。

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

[エラスティック データベース プール](sql-database-elastic-pool.md) 、プール内のデータベース間でリソースを共有します。 次の表では、Basic、Standard、および Premium のエラスティック データベース プールの特性について説明します。

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

前の表に示されている各リソースの拡張の定義の説明を参照してください。 [サービス層の機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)します。 サービス階層の概要については、次を参照してください。 [Azure SQL Database サービス階層とパフォーマンス レベル](sql-database-service-tiers.md)します。

## サーバーごとの DTU クォータ

Azure SQL Database では、論理サーバーごとの DTU クォータが現在 15000 DTU となっています。 このクォータは、論理サーバーでホストできる DTU を表し、サーバー上の各データベースのパフォーマンス レベルにおける DTU の合計に基づいています。 たとえば、5 つの Basic データベース (最大 5 x 5 DTU)、2 つの Standard S1 データベース (最大 2 x 20 DTU)、3 つの Premium P1 データベース (最大 3 x 100 DTU) があるサーバーは、15000 DTU クォータのうち 365 DTU を使用します。

>[AZURE.NOTE] によってこのクォータの増加を要求する [サポートに連絡して](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)します。

## その他の SQL Database の制限

| 領域 | 制限 | 説明 |
|---|---|---|
| サブスクリプションあたりの自動エクスポートを使用するデータベース | 10 | 自動エクスポートを使用すると、カスタム スケジュールを作成して、SQL Database をバックアップできます。 詳細については、次を参照してください。 [SQL データベース: SQL データベースの自動エクスポートのサポート](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines)します。|

## リソース

[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[Azure SQL データベースのサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)

[SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md)

