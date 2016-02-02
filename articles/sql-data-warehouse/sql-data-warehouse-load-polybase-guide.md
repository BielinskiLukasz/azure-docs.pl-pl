<properties
   pageTitle="SQL Data Warehouse で PolyBase を使用するためのガイド | Microsoft Azure"
   description="SQL Data Warehouse のシナリオで PolyBase を使用するためのガイドラインおよび推奨事項を説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/19/2015"
   ms.author="sahaj08;barbkess"/>



# SQL Data Warehouse で PolyBase を使用するためのガイド

このガイドでは、SQL Data Warehouse で PolyBase を使用するための実用的な情報を提供します。

最初を参照してください、 [PolyBase でデータを読み込む][] チュートリアルです。


## ストレージ キーの交換

セキュリティ上の理由で、BLOB ストレージへのアクセス キーの交換を検討する必要がある場合があります。

このタスクを実行する最もスマートな方法として、"キーの交換" と呼ばれるプロセスがあります。 BLOB ストレージ アカウントには 2 つのストレージ キーがあります。 これらを切り替えることができます。

Azure ストレージ アカウント キーの交換は、次のシンプルな 3 つの手順から成るプロセスです

1. セカンダリ ストレージ アクセス キーに基づいたセカンダリ データベース スコープの資格情報を作成します
2. この新しい資格情報に基づいて 2 つ目の外部データ ソースを作成します
3. 外部データ ソースを参照する外部テーブルを削除して、新しい外部テーブルを作成します

すべての外部テーブルを新しい外部データ ソースに移行したら、クリーンアップ作業を実行できます。

1. 1 つ目の外部データ ソースを削除する
2. プライマリ ストレージ アクセス キーに基づいた 1 つ目のデータベース スコープ資格情報を削除します
3. Azure にログインし、次回に備えてプライマリ アクセス キーを再生成します

## Azure BLOB ストレージ データのクエリ

外部テーブルに対するクエリでは、リレーショナル テーブルであるかのように、単にテーブル名が使用されます。

```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] 外部テーブルに対するクエリが、*"クエリは中止されました。外部ソースの読み取り中に最大拒否しきい値に達しました"* というエラーで失敗する場合があります。 これは、外部データに*ダーティ*なレコードが含まれていることを示します。 データ レコードは、列の実際のデータの種類/数値が外部テーブルの列定義と一致しない場合、またはデータが指定された外部ファイルの形式に従っていない場合に「ダーティ」であるとみなされます。 これを修正するには、外部テーブルと外部ファイルの形式の定義が正しいこと、および外部データがこれらの定義に従っていることを確認します。 外部データ レコードのサブセットがダーティである場合は、CREATE EXTERNAL TABLE DDL の中で拒否オプションを使用することで、クエリでこれらのレコードを拒否することを選択できます。


## Azure BLOB ストレージからのデータのロード

この例では、Azure BLOB ストレージから SQL Data Warehouse データベースにデータをロードします。

データを直接格納すると、クエリのデータ転送に要する時間が削減されます。 columnstore インデックスを使用してデータを格納すると、分析クエリに対するクエリのパフォーマンスが最大で 10 倍改善されます。

この例では、CREATE TABLE AS SELECT ステートメントを使用してデータをロードします。 新しいテーブルは、クエリで指定された列を継承します。 これは、外部テーブル定義からそれらの列のデータ型を継承します。

CREATE TABLE AS SELECT は、SQL Data Warehouse のすべてのコンピューティング ノードにデータを同時に読み込む高パフォーマンス Transact-SQL ステートメントです。 もともと Analytics Platform System で超並列処理 (MPP) エンジンの開発しは SQL Data Warehouse に移行します。

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

参照してください [CREATE TABLE AS SELECT (TRANSACT-SQL)][]します。

## 新しくロードしたデータの統計を作成する

Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。 クエリから最高のパフォーマンスを取得するには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。 統計情報の詳細については、次を参照してください。、 [統計情報 []][] 開発一連のトピックの「します。 この例でロードしたテーブルの統計を作成する方法の簡単な例を次に示します。

```
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## Azure BLOB ストレージにデータをエクスポートする

このセクションでは、SQL Data Warehouse から Azure BLOB ストレージにデータをエクスポートする方法を示します。 この例では、すべてのコンピューティング ノードからデータを同時にエクスポートする高パフォーマンス Transact-SQL ステートメントである CREATE EXTERNAL TABLE AS SELECT を使用します。

次の例では、dbo.Weblogs テーブルの列の定義とデータを使用して、外部テーブル Weblogs2014 を作成します。 外部テーブルの定義は、SQL Data Warehouse に格納されます。また、SELECT ステートメントの結果は、データ ソースで指定された BLOB コンテナーにある "/archive/log2014/" ディレクトリにエクスポートされます。 データは、指定されたテキスト ファイル形式でエクスポートされます。

```
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## PolyBase UTF-8 要件に対処する

現在、PolyBase では、UTF-8 でエンコードされたデータ ファイルの読み込みをサポートしています。 UTF-8 では ASCII と同じ文字エンコードを使用するため、PolyBase でも ASCII でエンコードされたデータの読み込みがサポートされています。 ただし、PolyBase では、UTF-16 / Unicode または拡張 ASCII 文字などの文字エンコードはサポートされていません。 拡張 ASCII には、ドイツ語によく見られるウムラウトなどのアクセント付きの文字が含まれています。

この要件に対処するための最善の方法は、UTF-8 エンコードを書き換えることです。

これを行うには、いくつかの方法があります。 Powershell を使用した 2 つの方法を以下に紹介します。

### 小さいファイル向けの単純な例

以下は、ファイルを作成する単純な 1 行の Powershell スクリプトです。

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

これは、データを再エンコードする簡単な方法ですが、最も効率的であるとは言えません。 以下の IO ストリーミングの例では、これよりも格段に速く同じ結果を得られます。

### 大きいファイル向けの IO ストリーミングの例

以下のコード サンプルはやや複雑ですが、ソースからターゲットにデータ行をストリームするため、はるかに効率的です。 この方法は、大規模なファイル向けです。

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## 次のステップ

SQL Data Warehouse にデータを移動する方法の詳細についてを参照してください、 [データ移行の概要][]します。







[load data with bcp]: sql-data-warehouse-load-with-bcp.md 
[load data with polybase]: sql-data-warehouse-load-with-polybase.md 
[solution partners]: sql-data-warehouse-solution-partners.md 
[development overview]: sql-data-warehouse-overview-develop.md 
[statistics]: sql-data-warehouse-develop-statistics.md 
[data migration overview]: sql-data-warehouse-overview-migrate.md 
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx 
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx 
[sql server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx 
[ssis]: https://msdn.microsoft.com/library/ms141026.aspx 
[create external data source (transact-sql)]: https://msdn.microsoft.com/library/dn935022.aspx 
[create external file format (transact-sql)]: https://msdn.microsoft.com/library/dn935026).aspx 
[create external table (transact-sql)]: https://msdn.microsoft.com/library/dn935021.aspx 
[drop external data source (transact-sql)]: https://msdn.microsoft.com/library/mt146367.aspx 
[drop external file format (transact-sql)]: https://msdn.microsoft.com/library/mt146379.aspx 
[drop external table (transact-sql)]: https://msdn.microsoft.com/library/mt130698.aspx 
[create table as select (transact-sql)]: https://msdn.microsoft.com/library/mt204041.aspx 
[insert...select (transact-sql)]: https://msdn.microsoft.com/library/ms174335.aspx 
[create master key (transact-sql)]: https://msdn.microsoft.com/library/ms174382.aspx 
[create credential (transact-sql)]: https://msdn.microsoft.com/library/ms189522.aspx 
[create database scoped credential (transact-sql)]: https://msdn.microsoft.com/library/mt270260.aspx 
[drop credential (transact-sql)]: https://msdn.microsoft.com/library/ms189450.aspx 

