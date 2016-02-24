<properties
   pageTitle="SQL Data Warehouse でのテーブル デザイン | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse でのテーブルのデザインに関するヒント。"
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

# SQL Data Warehouse でのテーブル デザイン #
SQL Data Warehouse は、超並列処理 (MPP) 分散データベース システムです。 呼ばれるさまざまな場所にデータを保管する **ディストリビューション**します。 各 **配布** バケットに似ては、データ ウェアハウスのデータの一意のサブセットを格納します。 SQL Data Warehouse は、データと処理機能を複数のノードに分散させることで、単一のシステムをはるかに超えた大規模なスケーラビリティを実現します。

SQL Data Warehouse でテーブルを作成すると、そのテーブルは実際にはすべてのディストリビューションに分散されます。

この記事では、次のトピックについて説明します。

- サポートされているデータ型
- データ分散の原則
- ラウンド ロビン分散
- ハッシュ分散
- テーブル パーティション
- 統計
- サポートされていない機能

## サポートされているデータ型
SQL Data Warehouse では、次の一般的なビジネス データ型がサポートされています。

- **bigint**
- **binary**
- **bit**
- **char**
- **date**
- **datetime**
- **datetime2**
- **datetimeoffset**
- **decimal**
- **float**
- **int**
- **money**
- **nchar**
- **nvarchar**
- **real**
- **smalldatetime**
- **smallint**
- **smallmoney**
- **time**
- **tinyint**
- **varbinary**
- **varchar**

次のクエリを使用して、データ ウェアハウスで互換性のない型が含まれた列を特定できます。

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

このクエリにはユーザー定義のデータ型が含まれていますが、これらはサポートされていません。

以下に、サポートされていないデータ型の代わりに使用できるデータ型をいくつか示します。

代替のデータ型は次のとおりです。

- **ジオメトリ**, 、varbinary 型の使用
- **geography**, 、varbinary 型の使用
- **hierarchyid**, 、ネイティブではない CLR 型
- **イメージ**, 、**テキスト**, 、**ntext** テキスト ベースの場合、varchar/nvarchar を使用して (小さいほど良好)
- **nvarchar (max)**, 、nvarchar (4000) を使用するか、パフォーマンス向上のためより小さい
- **数値**, 、decimal を使用します
- **sql_variant**, 分割された、厳密に型指定の列を複数の列に
- **sysname**, 、nvarchar (128) を使用して
- **テーブル**, 、一時テーブルに変換します
- **タイムスタンプ**, 、datetime2 を使用するコードを再作業と `CURRENT_TIMESTAMP` 関数です。 current_timestamp を既定の制約として指定することはできません。また、値は自動的には更新されません。 timestamp で型指定された列から rowversion 値を移行する必要がある場合は、行バージョンの値 NOT NULL または NULL に BINARY(8) または VARBINARY(8) を使用します。
- **varchar (max)**, 、varchar (8000) を使用するか、パフォーマンス向上のためより小さい
- **uniqueidentifier**, 、varbinary (8) を使用して
- **ユーザー定義型**, 、可能なネイティブ型に変換します
- **xml**, 、varchar (8000) を使用して、または小さい場合に、複数の列分割のパフォーマンスを向上させるために必要な

部分的なサポート:

- 既定の制約では、リテラルと定数のみがサポートされます。 `GETDATE()` や `CURRENT_TIMESTAMP` など、不明確な式または関数はサポートされていません。

> [AZURE.NOTE] 可変長列の全長を含め、行の最大サイズが 32,767 バイトを超えないように、テーブルを定義します。 この数値を超える可能性のある可変長データを含む行を定義することはできますが、データをテーブルに挿入できなくなります。 また、クエリを実行する際のスループットをさらに向上させるために、可変長列のサイズを制限してください。

## データ分散の原則

SQL Data Warehouse でのデータ分散には、次の 2 つの選択肢があります。

1. 均等でありながらランダムにデータを分散させる 
2. 1 つの列の値のハッシュに基づいてデータを分散させる

データ分散はテーブル レベルで決定されます。 テーブルはすべて分散されます。 SQL Data Warehouse データベースのテーブルごとに、分散を割り当てます。

最初のオプションと呼ばれる **ラウンド ロビン** 配布 - ランダム ハッシュとも呼ばれます。 これは、既定のオプションまたはフェール セーフ オプションと考えることができます。

2 番目のオプションと呼ばれる、 **ハッシュ** 配布します。 これは、データ分散の最適化された形と考えることができます。 テーブルのクラスターで共通の結合条件や集計条件を共有する場合は、このオプションが推奨されます。

## ラウンド ロビン分散

ラウンド ロビン分散は、すべてのディストリビューションにデータをできるだけ均等に分散させる方法です。 データ行を格納するバッファーが、各ディストリビューションに順に割り当てられます (このことから "ラウンド ロビン" と呼ばれます)。 すべてのデータ バッファーが割り当てられるまで、このプロセスが繰り返されます。 ラウンド ロビン分散テーブルでは、どの段階においてもデータの並べ替えや順序付けは行われません。 そのため、ラウンド ロビン分散は、ランダム ハッシュと呼ばれる場合もあります。 データは、ディストリビューション間でできるだけ均等に分散されます。

ラウンド ロビン分散テーブルの例を次に示します。

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

これもラウンド ロビン分散テーブルの例です。

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
)
;
```

> [AZURE.NOTE] いる上記の 2 番目の例言及されていません、ディストリビューション キーのことを確認します。 Round Robin が既定値であるため、この分散キーの記述は必須というわけではありません。 ただし、テーブル デザインを見直すときに、他の開発者に意図が伝わるように、明示的にすることが推奨されます。

ラウンド ロビン分散テーブルは、データのハッシュに使用する明確なキー列がない場合に使用するのが一般的です。 また、移動コストがそれほど大きくないと考えられる小さなテーブルまたは重要度の低いテーブルで使用することもできます。

ラウンド ロビン分散テーブルへのデータの読み込みは、ハッシュ分散テーブルへの読み込みよりも高速である傾向があります。 ラウンド ロビン分散テーブルでは、読み込み前にデータを理解したり、ハッシュを実行したりする必要はありません。 そのため、多くの場合、ラウンド ロビン テーブルは優れた読み込みターゲットとなっています。

> [AZURE.NOTE] ディストリビューションにデータをラウンド ロビン分散させると、データが割り当てられている、 *バッファー* レベルです。

### 推奨事項

次のシナリオでは、テーブルにラウンド ロビン分散を使用することを検討してください。

- 明確な結合キーが存在しない場合
- ハッシュ分散の候補キーが不明な場合
- テーブルが共通の結合キーを他のテーブルと共有していない場合
- 結合がクエリの他の結合ほど重要ではない場合
- テーブルが初期読み込みテーブルの場合

## ハッシュ分散

ハッシュン分散では、内部関数を使用して 1 つの列をハッシュすることで、各ディストリビューションにデータセットを分散させます。 データをハッシュしたときに、ディストリビューションに割り当てられるデータに明示的な順序はありません。 ただし、ハッシュ自体は確定的なプロセスです。 そのため、ハッシュの結果が予測可能になります。 たとえば、値 10 を含む整数型の列をハッシュすると、常に同じハッシュ値が生成されます。 つまり、この ***、*** に同じディストリビューションに割り当てられる値 10 を含むハッシュされた整数型の列になります。 これはテーブルにも当てはまります。

ハッシュの予測可能性は非常に重要です。 これは、ハッシュでデータを分散させると、データを読み取り、テーブルを結合するときに、パフォーマンスが向上する可能性があることを意味します。

後述するように、ハッシュ分散はクエリの最適化で大きな効果を発揮する可能性があります。 ハッシュ分散がデータ分散の最適化された形と考えられているのはそのためです。

> [AZURE.NOTE] ご注意ください。 データの値ではなく、ハッシュするデータの型に基づいていることに注意してください。

ProductKey でハッシュ分散されたテーブルを次に示します。

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
)
;
```

> [AZURE.NOTE] データは、ハッシュ分散ときは、行レベルでディストリビューションにデータが割り当てられます。

## テーブルのパーティション
テーブル パーティションがサポートされており、テーブル パーティションを簡単に定義できます。

SQL Data Warehouse のパーティション分割された `CREATE TABLE` コマンドの例を次に示します。

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

この定義には、パーティション関数もパーティション構成も含まれていないことに注意してください。 これらはすべてテーブルの作成時に準備されます。 必要なのは、パーティション キーになる列の境界点を指定することだけです。

## 統計

SQL Data Warehouse では、分散クエリ オプティマイザーを使用して、ユーザーがテーブルを照会するときに適切なクエリ プランを作成します。 作成されたクエリ プランにより、データにアクセスし、ユーザー要求を処理するためにデータベースで使用される戦略と方法が提供されます。 SQL Data Warehouse のクエリ オプティマイザーはコストに基づいています。 つまり、相対的なコストに基づいてさまざまなオプション (プラン) を比較し、使用できる最も効率的なプランが選択されます。 そのため、SQL Data Warehouse は、十分な情報に基づくコスト ベースの決定を下すために多くの情報を必要とします。 テーブル (テーブル サイズ) に関する統計情報は、`STATISTICS` と呼ばれるデータベース オブジェクトに保持されます。

統計は、インデックスまたはテーブルの 1 つまたは複数の列に対して保持されます。 統計により、コスト ベースのオプティマイザーに、基数と値の選択度に関する重要な情報が提供されます。 これは、オプティマイザーがクエリの JOIN、GROUP BY、HAVING、WHERE の各句 を評価する必要がある場合に特に重要となります。 これらの統計情報に含まれる情報が、オブジェクトを使用することは非常に重要なしたがって *正確に* テーブルの現在の状態を反映します。 これが重要なコストの精度であることを理解しておく必要があります。 統計がテーブルの状態を正確に表している場合、プランを比較してコストを最小限に抑えることができます。 統計が正確でない場合、SQL Data Warehouse が間違ったプランを選択する可能性があります。

SQL Data Warehouse の列レベルの統計は、ユーザーが定義します。

つまり、これらの統計は、開発者が自分で作成する必要があります。 この点を見過ごさないようにしてください。 これは、SQL Server と SQL Data Warehouse の重要な違いの 1 つです。 SQL Server では、列を照会したときに統計が自動的に作成されます。 既定では、これらの統計の更新も自動的に実行されます。 しかし、SQL Data Warehouse では、統計を手動で作成し、手動で管理する必要があります。

### 推奨事項

統計の生成について、次の推奨事項が適用されます。

1. `WHERE`、`JOIN`、`GROUP BY`、`ORDER BY`、`DISTINCT` の各句で使用される列では、単一列統計を作成します。
2. 複合句では複数列統計を生成します。
3. 統計を定期的に更新します。 更新は自動的には実行されないことに注意してください。

>[AZURE.NOTE] SQL Server データ ウェアハウスだけを利用するが一般的 `AUTOSTATS` 列の統計情報を常に最新の状態に保つことにします。 これは、SQL Server データ ウェアハウスもベスト プラクティスではありません。 `AUTOSTATS` 20% 変化率数百万行あるいは数十億行の大規模なファクト テーブルには不十分である可能性がありますがトリガされます。 そのため、統計にテーブルの基数が正確に反映されるように、統計を常に最新の状態に維持することをお勧めします。

## サポートされていない機能
SQL Data Warehouse では、次の機能は使用またはサポートされません。

- 主キー
- 外部キー
- CHECK 制約
- UNIQUE 制約
- 一意のインデックス
- 計算列
- スパース列
- ユーザー定義型
- インデックス付きビュー
- ID
- シーケンス
- トリガー
- シノニム


## 次のステップ
他の開発のヒントについては、[開発の概要に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

