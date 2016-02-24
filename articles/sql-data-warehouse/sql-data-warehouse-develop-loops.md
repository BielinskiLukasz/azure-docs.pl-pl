<properties
   pageTitle="SQL Data Warehouse | Microsoft Azure でのループ"
   description="ソリューションを開発するための  Azure SQL Data Warehouse  でのTRANSACT-SQL のループと、カーソルの置換に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
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

# SDL Data Warehouse でのループ
SQL Data Warehouse には、ステートメント ブロックを繰り返し実行するための [WHILE] ループがサポートしています。 これは、指定された条件が true の場合に限り、またはコードが `BREAK` キーワードを使用してループを終了するまで実行されます。 ループは、SQL コードで定義されているカーソルを置き換えるために特に便利です。 また、SQL コードで記述されているほとんどすべてのカーソルは、高速順方向、読み取り専用など豊富です。 そのため [中] は、ループは優れた代替 if する表示を置き換える必要があります。

## SQL Data Warehouse でのループの活用とカーソルの置換
ただし、始める前に、まず、自分で「このカーソルは、セット ベースの操作を使用して再作成できるか」と問いかけてください。 多くの場合、答えは「はい」であり、この方法が最適です。 セット ベースの操作は、1 行ずつの反復的なアプローチをとるよりも非常に速く実行されます。

高速順方向の読み取り専用カーソルは、ループ構造で簡単に置き換えることができます。 単純な例を次に示します。 このコード例は、データベース内のすべてのテーブルの統計を更新します。 ループ内のテーブルを反復処理することで、シーケンス内の各コマンドを実行できます。

最初に、個々のステートメントを識別するための一意の行番号が含まれている、一時テーブルを作成します。
  
```
CREATE TABLE #tbl 
WITH 
(   LOCATION     = USER_DB
,   DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) ) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

次に、ループの実行に必要な変数を初期化します。

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

1 つずつ実行するステートメントをループします。

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最後に、最初の手順で作成した一時テーブルを削除します。

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 次のステップ
他の開発のヒントについては、[開発の概要に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->



