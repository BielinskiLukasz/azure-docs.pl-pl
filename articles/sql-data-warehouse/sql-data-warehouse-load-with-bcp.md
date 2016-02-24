<properties
   pageTitle="bcp を使用した SQL Data Warehouse へのデータの読み込み | Microsoft Azure"
   description="データ ウェアハウジングのシナリオに沿って、bcp の概要と、その使用方法を学習します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/19/2015"
   ms.author="mausher;barbkess"/>


# bcp を使用したデータの読み込み

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp]** コマンドライン形式の一括読み込みユーティリティで、SQL Server、データ ファイル、および SQL Data Warehouse の間でデータをコピーすることができます。 bcp を使用して SQL Data Warehouse のテーブルに多数の行をインポートしたり、SQL Server のテーブルからデータ ファイルにデータをエクスポートしたりします。 queryout オプションと併用する場合を除き、bcp を使用するときに Transact-SQL の知識は必要ありません。

bcp を使用すれば、すばやく、簡単に SQL Data Warehouse データベースとの間で小規模なデータ セットを読み込み/抽出できます。 bcp を使用して読み込み/抽出する場合に推奨される正確なデータ量は、Azure データ センターへのネットワーク接続によって異なります。  一般に、ディメンション テーブルは読み込んで抽出できますが、非常に大容量のファクト テーブルは読み込みや抽出に非常に時間がかかる場合があります。

bcp では次のことができます。

- 簡単なコマンド ライン ユーティリティを使用して、SQL Data Warehouse にデータを読み込みます。
- 簡単なコマンド ライン ユーティリティを使用して、SQL Data Warehouse からデータを抽出します。

ここでは、次の操作方法について説明します。

- bcp in コマンドを使用してテーブルにデータをインポートする
- bcp out コマンドを使用してテーブルからデータをエクスポートする

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## 前提条件

このチュートリアルを進めるには、次が必要です。

- SQL Data Warehouse データベース
- インストールされた bcp コマンド ライン ユーティリティ
- インストールされた SQLCMD コマンド ライン ユーティリティ

>[AZURE.NOTE] Bcp および sqlcmd ユーティリティは、[Microsoft ダウンロード センター] からダウンロードできます。

## SQL Data Warehouse へのデータのインポート

このチュートリアルでは、Azure SQL Data Warehouse でテーブルを作成し、そのデータをテーブルにインポートします。

### 手順 1: Azure SQL Data Warehouse でテーブルを作成する

コマンド プロンプトから次のコマンドを使用してインスタンスに接続します。値は適宜置き換えて使用してください。

```
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I
```
接続したら、sqlcmd プロンプトで次のテーブル スクリプトをコピーし、Enter キーを押します。

```
CREATE TABLE DimDate2 
(
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH 
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
);
GO
```
>[AZURE.NOTE] 参照してください [テーブル デザイン][] WITH 句で使用できるオプションの詳細については、トピックの開発グループでのトピックです。

### 手順 2: ソース データ ファイルを作成する

メモ帳を開き、データの次の行を新しいファイルにコピーします。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

これをローカル一時ディレクトリ C:\Temp\DimDate2.txt に保存します。

> [AZURE.NOTE] ことが重要覚えて、bcp.exe が utf-8 のファイルのエンコードをサポートしていません。 bcp.exe を使用する場合は、ASCII でエンコードされたファイルを使用するか、ファイルに UTF-16 エンコードを使用してください。

### 手順 3: データに接続し、インポートする
bcp を使用して、次のコマンドでデータに接続し、インポートできます。値は適宜置き換えて使用してください。

```
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

前述のように sqlcmd を使用して接続し、次の TSQL コマンドを実行して、データが読み込まれたことを確認できます。

```
SELECT * FROM DimDate2 ORDER BY 1;
GO
```

これにより、次の結果が得られます。

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### 手順 4: 新しくロードしたデータの統計を作成する 

Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。 クエリから最高のパフォーマンスを取得するには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。 統計情報の詳細については、次を参照してください。、 [統計][] 開発一連のトピックの「します。 この例でロードしたテーブルの統計を作成する方法の簡単な例を次に示します

sqlcmd プロンプトから次の CREATE STATISTICS ステートメントを実行します。

```
create statistics [DateId] on [DimDate2] ([DateId]);
create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
GO
```

## SQL Data Warehouse からのデータのエクスポート
このチュートリアルでは SQL Data Warehouse 内のテーブルからデータ ファイルを作成します。 上記で作成したデータを DimDate2_export.txt という名前の新しいデータ ファイルにエクスポートします。

### 手順 1: データをエクスポートする

bcp ユーティリティを使用して、次のコマンドでデータに接続し、エクスポートできます。値は適宜置き換えて使用してください。

```
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
新しいファイルを開き、データが正しくエクスポートされたことを確認できます。 ファイル内のデータは、次のテキストと一致する必要があります。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE] 分散システムの性質上、データの順序場合がない同じ SQL Data Warehouse データベースです。 queryout パラメーターを使用して、実行する Transact-SQL クエリを指定することもできます。

## 次のステップ
読み込みの概要については、次を参照してください。 [SQL Data Warehouse にデータを読み込む][]です。
他の開発のヒントを参照してください。 [SQL Data Warehouse の開発の概要][]します。

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Design]: ./sql-data-warehouse-develop-table-design.md
[Statistics]: ./sql-data-warehouse-develop-statistics.md


<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx


<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433

