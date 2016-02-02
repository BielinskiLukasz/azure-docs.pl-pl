<properties
   pageTitle="SQL Data Warehouse | Microsoft Azure での動的 SQL"
   description="ソリューション開発のための Azure SQL Data Warehouse での動的 SQL  の使用に関するヒント。"
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


# SQL Data Warehouse での動的 SQL

SQL Data Warehouse のアプリケーション コードを開発する際に、柔軟性が高く汎用的なモジュール ソリューションを提供するための動的 SQL を使用する必要のある場合があります。 現時点では、SQL Data Warehouse は BLOB データ型をサポートしていません。 これにより、blob 型には varchar (max) と nvarchar (max) の両方の型が含まれるので、文字列のサイズが制限される場合があります。 大量の文字列を構築するときにアプリケーション コードでこれらの型を使用した場合、コードをチャンクに分割し、代わりに EXEC ステートメントを使用する必要があります。

単純な例を次に示します。

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

文字列が短い場合は、使う [sp_executesql:operator[]][] 通常どおりです。


## 次のステップ

他の開発のヒントを参照してください。 [開発の概要 []][]します。










[development overview]: sql-data-warehouse-overview-develop.md 
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx 

