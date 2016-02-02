<properties
   pageTitle="SQL Data Warehouse | Microsoft Azure での名前変更"
   description="ソリューション開発のための Azure SQL Data Warehouse でのテーブルの名前変更に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/05/2015"
   ms.author="twounder;JRJ@BigBangData.co.uk;barbkess"/>


# SDL Data Warehouse での名前変更

SQL Server は、ストアド プロシージャを使用して名前を変更するデータベースをサポートしている `sp_renamedb`, 、SQL Data Warehouse では、DDL 構文を使用して、同じ目標を達成します。 DDL コマンドは `オブジェクトの名前変更`します。

## テーブルの名前変更

現時点で、名前を変更できるのはテーブルのみです。 テーブルの名前を変更するための構文は次のとおりです。

```
RENAME OBJECT Customer TO NewCustomer;
```

テーブルの名前を変更すると、テーブルに関連付けられたすべてのオブジェクトおよびプロパティが、新しいテーブル名を参照するように更新されます。 たとえば、テーブルの定義、インデックス、制約、およびアクセス許可が更新されます。 ビューは更新されません。

## 外部テーブルの名前変更

外部テーブルの名前を変更すると、SQL Server PDW 内の該当するテーブル名が変更されます。 これによって、テーブルの外部データの場所が影響されることはありません。

## テーブル スキーマの変更

オブジェクトが属するスキーマを変更する場合は、ALTER SCHEMA を次のように使用して目的を達成します。

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## テーブル名の変更には排他的なテーブル ロックが必要

使用中のテーブルの名前は変更できないことに注意してください。 テーブルの名前の変更には、テーブル上での排他的なロックが必要です。 テーブルを使用中である場合は、テーブルを使用しているセッションを終了することが必要な場合があります。 使用する必要があります。 セッションを終了する、 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) コマンドです。 使用するときは注意 `KILL` とき、セッションが終了し、コミットされていない作業はロールバックされます。 SQL Data Warehouse 内のセッションにはプレフィックスとして 'SID' が付けられます。 KILL コマンドを呼び出す際には、このプレフィックスとセッション番号を指定する必要があります。 たとえば `KILL 'SID1234'`


## 次のステップ

他の開発のヒントを参照してください。 [開発の概要 []][]します。





[development overview]: sql-data-warehouse-overview-develop.md 

