<properties
    pageTitle="データベース間クエリの概要 (垂直的パーティション分割) | Microsoft Azure"   
    description="垂直にパーティション分割されたデータベースでエラスティック データベース クエリを使用する方法"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2015"
    ms.author="torsteng" />


# データベース間クエリの概要 (垂直的パーティション分割)

Azure SQL Database 用の エラスティック データベース クエリ (プレビュー) を使用すると、1 つの接続ポイントで複数のデータベースにまたがる T-SQL クエリを実行することができます。 このトピックの対象 [データベースを垂直方向にパーティション分割](sql-database-elastic-query-vertical-partitioning.md)します。

終了すると、複数の関連するデータベースにまたがるクエリを実行するために Azure SQL Database を構成および使用する方法を習得できます。

エラスティック データベース クエリ機能の詳細については、次を参照してください  [Azure SQL Database エラスティック データベース クエリの概要](sql-database-elastic-query-overview.md)します。

## サンプル データベースの作成

最初に、同じ論理サーバーか異なる論理サーバーで「**Customers**」と「**Orders**」という 2 つのデータベースを作成する必要があります。

「**Orders**」データベースで次のクエリを実行し、「**OrderInformation**」テーブルを作成し、サンプル データを入力します。

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

次に、「Customers」データベースで次のクエリを実行し、「CustomerInformation」テーブルを作成し、サンプル データを入力します。

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## データベース オブジェクトを作成する

### データベース スコープのマスター キーと資格情報

これらを使用し、次の手順に従ってシャード マップ マネージャーとシャードに接続します。

1. SQL Server Management Studio または Visual Studio の SQL Server Data Tools を開きます。
2. Orders データベースに接続し、次の T-SQL コマンドを実行します。

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  

    「username」と「password」は Customers データベースのログインに使用するユーザー名とパスワードになります。

### 外部データ ソース

外部データ ソースを作成するには、Orders データベースで、次のコマンドを実行します。

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### 外部テーブル

CustomerInformation テーブルの定義に一致する外部テーブルを Orders データベースで作成します。

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## サンプルのエラスティック データベース T-SQL クエリを実行する

外部データ ソースと外部テーブルを定義すると、T-SQL を使用して外部テーブルにクエリを実行できるようになります。 Orders データベースでこのクエリを実行します。

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## コスト

現在のところ、エラスティック データベース クエリ機能は Azure SQL Database のコストに含まれています。

料金情報については、次を参照してください。 [SQL Database の料金](/pricing/details/sql-database)します。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]









