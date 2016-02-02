<properties
    pageTitle="Azure Automation を使用した Azure SQL データベースの管理"
    description="Azure Automation サービスを使用して、規模に応じて Azure SQL データベースを管理する方法について説明します。"
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jeffreyg"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2015"
    ms.author="jolevy"/>




# Azure Automation を使用した Azure SQL データベースの管理

このガイドでは、Azure Automation サービスと、このサービスを使用して Azure SQL データベースの管理を簡略化する方法について紹介します。


## Azure Automation とは

[Azure Automation](http://azure.microsoft.com/services/automation/) プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して実行時間の長いタスク、手動タスク、エラーが発生しやすいタスク、頻繁に繰り返されるタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮することができます。

Azure Automation は、組織の拡大に伴って変化するニーズに対応可能な、信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動実行すれば、運用上のオーバーヘッドが削減され、IT/DevOps スタッフの負担が軽減されるため、ビジネス価値の向上にフォーカスすることができます。


## Azure Automation を Azure SQL データベースの管理に役立てる方法

使用して Azure Automation で azure SQL データベースを管理できる、 [Azure SQL Database PowerShell コマンドレット](https://msdn.microsoft.com/library/azure/dn546726.aspx) で提供される、 [Azure PowerShell ツール](https://msdn.microsoft.com/library/azure/jj156055.aspx)します。 Azure Automation には、このような Azure SQL Database PowerShell コマンドレットがあらかじめ用意されており、サービス内ですべての SQL DB 管理タスクを実行することができます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

さらに、Azure Automation には、PowerShell を使用して SQL コマンドを発行することにより、SQL サーバーと直接通信する機能もあります。

[Azure Automation runbook ギャラリー](http://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) 製品チームおよびコミュニティの runbook を Azure SQL データベース、その他の Azure サービスおよびサード パーティ製システムの管理を自動化するためのさまざまなが含まれています。 ギャラリーに含まれている Runbook の例を示します。

 * [SQL Server データベースに対して SQL クエリを実行します。](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [垂直方向にスケール (アップまたはダウン)、スケジュールに従って、Azure SQL データベース](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [データベースが最大サイズに近づいた場合の SQL テーブルを切り捨てください。](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Azure SQL データベースのテーブルの断片化率が高い場合のインデックスします。](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## 次のステップ

ここまで、Azure Automation の基本と Azure Automation を使用して Azure SQL データベースを管理する方法について説明しました。Azure Automation の詳細については、これらのリンクを参照してください。

 * Azure automation の [チュートリアル入門](../automation-create-runbook-from-samples.md)
 * 読み取り、 [Azure Automation: クラウド内の SQL エージェント](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) ブログの投稿






