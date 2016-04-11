<properties
    pageTitle="方法: ファイアウォール設定を構成する | Microsoft Azure"
    description="Azure SQL データベースにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。"
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="11/13/2015"
    ms.author="rickbyh"/>


# 方法: PowerShell を使用してファイアウォール設定を構成する (SQL Database)


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。 データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。

> [AZURE.IMPORTANT] Azure からのアプリケーションは、データベース サーバーに接続を許可するのには、Azure の接続を有効にする必要があります。 詳細については、ファイアウォール規則と Azure からの接続を有効にすると、次を参照してください。 [Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)します。 Azure クラウド境界内で接続を行う場合は、追加の TCP ポートをいくつか開かなければならない場合があります。 詳細については、次を参照してください、 **SQL Database の V12: 外部と内部** の [1433 for ADO.NET 4.5 と SQL Database V12 における 1433 以外のポート。](sql-database-develop-direct-route-ports-adonet-v12.md)


## Azure PowerShell を使用してサーバー レベルのファイアウォール規則を管理する
1. Azure PowerShell を起動します。
2. Azure PowerShell を使用すると、サーバーレベルのファイアウォール規則を作成、更新、および削除できます。 

    新しいサーバーレベルのファイアウォール規則を作成するには、New-AzureSqlDatabaseServerFirewallRule コマンドレットを実行します。 次の例では、サーバー Contoso の IP アドレス範囲を有効にします。
 
        New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
    既存のサーバーレベルのファイアウォール規則を変更するには、Set-azuresqldatabaseserverfirewallrule コマンドレットを実行します。 次の例では、ContosoFirewallRule という名前の規則で許容される IP アドレスの範囲を変更します。
 
        Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

    既存のサーバーレベルのファイアウォール規則を削除するには、Remove-AzureSqlDatabaseServerFirewallRule コマンドレットを実行します。 次の例では、ContosoFirewallRule という名前の規則を削除します。

        Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso


## PowerShell を使用したファイアウォール規則の管理

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## 次のステップ

Database の作成に関するチュートリアルについては、次を参照してください。 [最初の Azure SQL Database を作成する](sql-database-get-started.md)です。
オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL database に接続する方法の詳細については、次を参照してください。 [プログラムで SQL データベースを Azure に接続するためのガイドライン](https://msdn.microsoft.com/library/azure/ee336282.aspx)します。
データベースを参照してください] に移動する方法を理解する [Azure SQL Database におけるデータベースの管理とログイン](https://msdn.microsoft.com/library/azure/ee336235.aspx)します。

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

