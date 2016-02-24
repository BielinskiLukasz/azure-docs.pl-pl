<properties
   pageTitle="SQL Data Warehouse の変数の代入 | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse での Transact-SQL 変数の代入に関するヒント。"
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

# SQL Data Warehouse の変数の代入
SQL Data Warehouse の変数は、`DECLARE` ステートメントまたは `SET` ステートメントを使用して設定します。 

以下に示す方法は、いずれも変数値を設定する有効な方法です。

## DECLARE を使用した変数の設定

DECLARE を使用した変数の初期化は、SQL Data Warehouse で変数値を設定する最も柔軟性の高い方法の 1 つです。

```
DECLARE @v  int = 0
;
```

DECLARE を使用して、一度に複数の変数を設定することもできます。 `SELECT` または `UPDATE` を使用して、これを行うことはできません。

```
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

同じ DECLARE ステートメントで変数を初期化し、使用することはできません。 次の例は、ポイントを説明するために **いない** @p1 両方を初期化し、同じ DECLARE ステートメントで使用されるように許可します。 これはエラーになります。

```
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## SET を使用した値の設定
SET は、1 つの変数を設定する際によく使用されるメソッドです。

次の例はすべて、SET を使用して変数を設定する有効な方法です。

```
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

SET を使用して設定できる変数は、一度に 1 つに限られます。 ただし、上記のように複合演算子を使用できます。

## 制限事項
SELECT および UPDATE は変数代入には使用できません。


## 次のステップ
他の開発のヒントについては、[開発の概要に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

