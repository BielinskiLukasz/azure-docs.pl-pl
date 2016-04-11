<properties
   pageTitle="SQL Data Warehouse のストアド プロシージャ | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse でのストアド プロシージャの実装に関するヒント。"
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

# SQL Data Warehouse のストアド プロシージャ 

SQL Data Warehouse では、SQL Server の Transact-SQL 機能の多くをサポートしています。 さらに重要なのは、ソリューションのパフォーマンスを最大限にするために活用できる、スケールアウト専用の機能が用意されていることです。

ただし、SQL Data Warehouse のスケールとパフォーマンスを維持するために、動作が異なる機能もあれば、サポートされていない機能もあります。

この記事では、SQL Data Warehouse でストアド プロシージャを実装する方法について説明します。

## ストアド プロシージャの概要
ストアド プロシージャは、SQL コードをカプセル化し、データ ウェアハウスのデータの近くに格納するための優れた方法です。 コードを管理しやすい単位にカプセル化することで、開発者はソリューションをモジュール化することができ、コードの再利用性が大幅に促進されます。 ストアド プロシージャの柔軟性をさらに高めるために、各ストアド プロシージャはパラメーターを受け入れることもできます。

SQL Data Warehouse は、簡素化され、合理化されたストアド プロシージャの実装を提供します。 SQL Server との最大の違いは、ストアド プロシージャがプリコンパイル済みコードではないことです。 データ ウェアハウスでは、一般に開発者はコンパイル時間をあまり気にしません。 大量のデータに対して操作を実行するときに、ストアド プロシージャ コードが適切に最適化されていることの方が重要です。 目標は、数時間、数分、(数ミリ秒ではなく) 数秒節約することです。 したがって、ストアド プロシージャは、SQL ロジックのコンテナーと考えるとよいでしょう。     
 
SQL Data Warehouse がストアド プロシージャを実行すると、実行時に SQL ステートメントが解析、変換され、最適化されます。 このプロセスの中で、各ステートメントが分散クエリに変換されます。 データに対して実際に実行される SQL コードは、送信されるクエリとは異なります。

## ストアド プロシージャの入れ子
ストアド プロシージャが他のストアド プロシージャを呼び出すか、動的 SQL を実行する場合、内側のストアド プロシージャまたはコードの呼び出しは "入れ子になっている" といわれます。

SQL Data Warehouse では、最大 8 レベルの入れ子をサポートします。 これは SQL Server と少し異なります。 SQL Server の入れ子レベルは 32 です。

最上位のストアド プロシージャの呼び出しは、入れ子レベル 1 になります。

```
EXEC prc_nesting
``` 
ストアド プロシージャから別の EXEC の呼び出しも行う場合、入れ子レベルは 2 に増えます。
```
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
次に 2 つ目の手順で何らかの動的 SQL を実行すると、入れ子レベルは 3 に増えます。
```
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

SQL Data Warehouse では、@@NESTLEVEL を現在サポートしていません。 入れ子レベルは、開発者が自分で追跡する必要があります。 入れ子レベルが上限の 8 に達することはほとんどありませんが、上限に達した場合は、コードを再作成し、この制限の範囲内に収まるようにフラット化する必要があります。 

## INSERT..EXECUTE
SQL Data Warehouse では、INSERT ステートメントでストアド プロシージャの結果セットを使用することはできません。 ただし、別の方法を使用できます。

次の記事を参照してください [temporary tables] これを行う方法の例です。

## 制限事項

SQL Data Warehouse で実装されていない Transact-SQL ストアド プロシージャの側面がいくつかあります。

次に例を示します。

- 一時ストアド プロシージャ
- 番号付きストアド プロシージャ
- 拡張ストアド プロシージャ
- CLR ストアド プロシージャ
- 暗号化オプション
- レプリケーション オプション
- テーブル値パラメーター
- 読み取り専用パラメーター
- 既定のパラメーター
- 実行コンテキスト
- return ステートメント

## 次のステップ
他の開発のヒントを参照してください。 [開発の概要][]します。

<!--Image references-->

<!--Article references-->
[temporary tables]: sql-data-warehouse-develop-temporary-tables.md
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->



