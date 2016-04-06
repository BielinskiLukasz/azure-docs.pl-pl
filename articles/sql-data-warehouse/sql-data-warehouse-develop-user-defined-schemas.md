<properties
   pageTitle="SQL Data Warehouse のユーザー定義のスキーマ | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse での Transact-SQL スキーマの使用に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse のユーザー定義のスキーマ

従来のデータ ウェアハウスでは、多くの場合、個別のデータベースを使用して、ワークロード、ドメイン、またはセキュリティに基づくアプリケーション境界を作成しています。 たとえば、従来の SQL Server データ ウェアハウスは、ステージング データベース、データ ウェアハウス データベース、複数のデータ マート データベースで構成されていることがあります。 このトポロジでは、各データベースがアーキテクチャでワークロードとセキュリティの境界として機能します。

これに対して、SQL Data Warehouse では、データ ウェアハウスの全ワークロードを 1 つのデータベース内で実行します。 データベース間の結合は許可されていません。 そのため、SQL Data Warehouse では、ウェアハウスで使用されるすべてのテーブルが 1 つのデータベースに格納されていることが求められます。

> [AZURE.NOTE] SQL Data Warehouse は、任意の種類のデータベースにまたがるクエリをサポートしていません。 したがって、このパターンを利用するデータ ウェアハウスの実装を変更する必要があります。

## 推奨事項 

ユーザー定義のスキーマを使用して、ワークロード、セキュリティ、ドメイン、機能の境界を統合する際の推奨事項を以下に示します。

1. 1 つの SQL Data Warehouse データベースを使用して、データ ウェアハウスの全ワークロードを実行します。
2. 既存のデータ ウェアハウス環境を統合して、1 つの SQL Data Warehouse データベースを使用します。
3. 活用 **ユーザー定義のスキーマ** データベースを使用して実装されていた境界を提供します。

ユーザー定義のスキーマをこれまで使用していなかった場合は白紙の状態です。 SQL Data Warehouse データベースのユーザー定義のスキーマの基礎として、古いデータベース名を使用します。

スキーマを既に使用していた場合、次のオプションがあります。

1. 従来のスキーマ名を削除し、最初からやり直す
2. テーブル名の先頭に従来のスキーマ名を追加することで、従来のスキーマ名を保持する
3. 追加のスキーマにテーブルのビューを実装して、古いスキーマ構造を再作成することで、従来のスキーマ名を保持する

> [AZURE.NOTE] 一見オプション 3 を最も魅力的なオプションのように思えます。 細部に落とし穴があります。 SQL Data Warehouse では、ビューは読み取り専用です。 データまたはテーブルの変更は、すべてベース テーブルに対して実行する必要があります。 また、オプション 3 では、ビューのレイヤーがシステムに導入されます。 アーキテクチャでビューを既に使用している場合は、この点をさらに検討することをお勧めします。


### 次に例を示します。

1. データベース名に基づくユーザー定義のスキーマを実装します。

```
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

2. テーブル名の先頭に従来のスキーマ名を追加することで、従来のスキーマ名を保持します。 ワークロードの境界にスキーマを使用します。

```
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

3. ビューを使用して従来のスキーマ名を保持します。

```
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] スキーマ戦略を変更しても、データベースのセキュリティ モデルのレビューが必要です。 多くの場合、スキーマ レベルでアクセス許可を割り当てることで、セキュリティ モデルを簡素化できます。 きめ細かなアクセス許可が必要な場合は、データベース ロールを使用します。

## 次のステップ
他の開発のヒントを参照してください。 [開発の概要][]します。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

