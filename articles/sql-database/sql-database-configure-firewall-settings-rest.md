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



# 方法: REST API を使用してファイアウォール設定を構成する (SQL Database)

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)



Microsoft Azure SQL Database では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。 データベースへのアクセスを選択的に許可するには、Azure SQL Database サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。
> [AZURE.IMPORTANT] Azure のアプリケーションからデータベース サーバーに接続を許可するには、Azure の接続を有効にする必要があります。 詳細については、ファイアウォール規則と Azure からの接続を有効にすると、次を参照してください。 [Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)します。 Azure クラウド境界内で接続を行う場合は、追加の TCP ポートをいくつか開かなければならない場合があります。 詳細については、次を参照してください、 **SQL Database の V12: 外部と内部** の [1433 for ADO.NET 4.5 と SQL Database V12 における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)。


## REST API を使用してサーバー レベルのファイアウォール規則を管理する

1. REST API を介してファイアウォール規則を管理する場合、認証される必要があります。 詳細については、「サービス管理要求の認証」を参照してください。
2. サーバーレベルの規則は、REST API を使用して、作成、更新、または削除できます。

    サーバーレベルのファイアウォール規則を作成または更新するには、次を使用して、POST メソッドを実行します。

        https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules

    要求本文

        <ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
          <Name>ContosoFirewallRule</Name>
          <StartIPAddress>192.168.1.4</StartIPAddress>
          <EndIPAddress>192.168.1.10</EndIPAddress>
        </ServiceResource>

    既存のサーバーレベルのファイアウォール規則を削除するには、次を使用して、DELETE メソッドを実行します。

        https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule



## サービス管理 REST API を使用したファイアウォール規則の管理

* [ファイアウォール ルールを作成します。](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [ファイアウォール ルールを削除します。](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [ファイアウォール規則を取得します。](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [ファイアウォール規則の一覧表示](https://msdn.microsoft.com/library/azure/dn505715.aspx)

## 次のステップ

Database の作成に関するチュートリアルについては、次を参照してください。 [最初の Azure SQL Database を作成する](sql-database-get-started.md)します。
オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL database に接続する方法の詳細については、次を参照してください。 [プログラムで SQL データベースを Azure に接続するためのガイドライン](https://msdn.microsoft.com/library/azure/ee336282.aspx)します。
データベースを参照してください] に移動する方法を理解する [Azure SQL Database におけるデータベースの管理とログイン](https://msdn.microsoft.com/library/azure/ee336235.aspx)します。









[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png 
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png 

