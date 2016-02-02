<properties 
   pageTitle="Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する | Azure" 
   description="U SQL ウィンドウ関数を使用する方法について説明します。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/11/2015"
   ms.author="jgao"/>



# Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する

ウィンドウ関数は、2003 年に ISO/ANSI SQL 標準に導入されました。 U-SQL では、ANSI SQL 標準で定義されたウィンドウ関数のサブセットが採用されています。

ウィンドウ関数は、*ウィンドウ*と呼ばれる行セット内で計算処理を実行するために使用されます。 Windows は、OVER 句によって定義されます。 ウィンドウ関数は、いくつかの重要なシナリオを非常に効率的な方法で解決します。

このチュートリアルと学習ガイドでは、2 つのサンプル データセットを使用して、ウィンドウ関数を適用できるサンプル シナリオについて説明します。 詳細については、次を参照してください。 [U SQL リファレンス](http://go.microsoft.com/fwlink/p/?LinkId=691348)します。

ウィンドウ関数は、次のカテゴリに分類されます。

- [レポートなどの集計機能](#reporting-aggregation-functions), 、SUM や AVG など
- [順位付け関数](#ranking-functions), ROW_NUMBER、DENSE_RANK、NTILE、および順位など、
- [分析関数](#analytic-functions),  累積分布、百分位は、同じ結果セットの自己結合を使用せずに前の行からデータにアクセスなど、

**前提条件:**

- 次の 2 つのチュートリアルを完了します。

    - [Visual Studio の Azure Data Lake ツールを使い始める](data-lake-analytics-use-data-lake-tools.md)します。
    - [U SQL を使用して Azure Data Lake 分析ジョブの開始](data-lake-analytics-u-sql-get-started.md)します。
- Data Lake 分析アカウントの作成手順については「 [Visual Studio の Azure Data Lake ツールを使い始める](data-lake-analytics-use-data-lake-tools.md)します。
- Visual Studio U SQL プロジェクトの作成手順については「 [U SQL を使用して Azure Data Lake 分析ジョブの開始](data-lake-analytics-u-sql-get-started.md)します。

## サンプル データセット

このチュートリアルでは、2 つのデータセットを使用します。

- QueryLog

    QueryLog は、検索エンジンで検索された内容の一覧を表します。 各クエリ ログの内容は次のとおりです。

        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).

    QueryLog 行セットを作成するには、次のスクリプトをコピーして U-SQL プロジェクトに貼り付けます。

        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);

    実際には、データはデータ ファイルに格納される可能性が最も高くなります。 タブ区切りファイル内のデータにアクセスするには、次のコードを使用します。

        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();

- Employees

    Employee データセットには、次のフィールドが含まれます。

        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.

    Employees 行セットを作成するには、次のスクリプトをコピーして U-SQL プロジェクトに貼り付けます。

        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);

    次のステートメントでは、データ ファイルからデータを抽出して行セットを作成します。

        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();


チュートリアル内のサンプルをテストする場合は、行セットの定義を含める必要があります。 U-SQL では、使用する行セットのみを定義する必要があります。 サンプルによっては、必要な行セットは 1 つだけです。

また、結果行セットをデータ ファイルに出力するには、次のステートメントも追加する必要があります。

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();

 ほとんどのサンプルでは、結果に **@result** という変数を使用します。

## ウィンドウ関数とグループ化の比較

ウィンドウ化とグループ化は概念的に関連していますが、異なる部分もあります。 この関係を理解することは役に立ちます。

### 集計とグループ化の使用

次のクエリでは、集計を使用して、全従業員の給与の総額を計算します。

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;

>[AZURE.NOTE] テストし、出力を確認する手順については、次を参照してください。 [U SQL を使用して Azure Data Lake 分析ジョブの開始](data-lake-analytics-u-sql-get-started.md)します。

結果は 1 行 1 列で示されます。 $165000 は、テーブル全体の Salary 値の合計です。

|TotalSalary
|-----------
|165000
>[AZURE.NOTE] ウィンドウ関数に慣れていない場合は、出力内の数値を覚えておくと役立ちます。  

次のステートメントでは、GROUP BY 句を使用して、部門ごとに給与の総額を計算します。

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

結果は次のようになります。

| DeptName| SalaryByDept
|--------|------------
| Engineering| 60000
| HR| 30000
| Executive| 50000
| Marketing| 25000

SalaryByDept 列の合計は $165000 です。これは、前のスクリプトの金額と一致します。

次のいずれの場合も、出力行数は入力行数より少なくなります。

- GROUP BY を使用しない場合は、集計により、すべての行が 1 行にまとめられます。
- GROUP BY では、ここで N は個別の値に表示されるデータは、この場合、出力の 4 行が表示されます数 N 出力行があります。

### ウィンドウ関数の使用

次のサンプルの OVER 句は空です。 これにより、"ウィンドウ" にはすべての行を含めることが定義されます。 この例の SUM は、その直後にある OVER 句に適用されます。

このクエリは、"すべての行から成るウィンドウでの給与の総額" として捉えることができます。

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

GROUP BY とは異なり、出力行数は入力行数と同じです。

| EmpName| TotalAllDepts
|-------|--------------------
| Noah| 165000
| Sophia| 165000
| Liam| 165000
| Emma| 165000
| Jacob| 165000
| Olivia| 165000
| Mason| 165000
| Ava| 165000
| Ethan| 165000


165000 という値 (すべての給与の総額) が各出力行に示されています。 この総額は、すべての行から成る "ウィンドウ" から出力されたものであるため、すべての給与を含んでいます。

次の例では、"ウィンドウ" を調整して、従業員、部門、部門の給与総額すべてを一覧に表示する方法を示しています。 OVER 句には PARTITION BY が追加されています。

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

結果は次のようになります。

| EmpName| DeptName| SalaryByDep
|-------|--------|-------------------
| Noah| Engineering| 60000
| Sophia| Engineering| 60000
| Liam| Engineering| 60000
| Mason| Executive| 50000
| Emma| HR| 30000
| Jacob| HR| 30000
| Olivia| HR| 30000
| Ava| Marketing| 25000
| Ethan| Marketing| 25000

ここでも、入力行数と出力行数は同じです。 ただし、各行には、対応する部門の給与総額が示されています。




## レポート集計関数

ウィンドウ関数では、次の集計もサポートされています。

- COUNT
- SUM
- 最小
- 最大
- 平均
- STDEV
- VAR

構文は次のとおりです。

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

注:

- 既定では、集計関数は null 値を無視します (COUNT を除く)。
- 集計関数を OVER 句と共に指定する場合、OVER 句では ORDER BY 句を使用できません。

### SUM の使用

次の例では、部門別の給与総額を各入力行に追加します。

    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

出力内容は次のとおりです。

| EmpID| EmpName| DeptName| DeptID| Salary| TotalByDept
|-----|-------|--------|------|------|-----------
| 1| Noah| Engineering| 100| 10000| 60000
| 2| Sophia| Engineering| 100| 20000| 60000
| 3| Liam| Engineering| 100| 30000| 60000
| 7| Mason| Executive| 300| 50000| 50000
| 4| Emma| HR| 200| 10000| 30000
| 5| Jacob| HR| 200| 10000| 30000
| 6| Olivia| HR| 200| 10000| 30000
| 8| Ava| Marketing| 400| 15000| 25000
| 9| Ethan| Marketing| 400| 10000| 25000

### COUNT の使用

次の例では、各行にフィールドを追加し、各部門の従業員の合計数を示します。

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

結果は次のようになります。

| EmpID| EmpName| DeptName| DeptID| Salary| CountByDept
|-----|-------|--------|------|------|-----------
| 1| Noah| Engineering| 100| 10000| 3
| 2| Sophia| Engineering| 100| 20000| 3
| 3| Liam| Engineering| 100| 30000| 3
| 7| Mason| Executive| 300| 50000| 1
| 4| Emma| HR| 200| 10000| 3
| 5| Jacob| HR| 200| 10000| 3
| 6| Olivia| HR| 200| 10000| 3
| 8| Ava| Marketing| 400| 15000| 2
| 9| Ethan| Marketing| 400| 10000| 2


### MIN と MAX の使用

次の例では、各行にフィールドを追加し、各部門の最低給与額を示します。

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

結果は次のようになります。

| EmpID| EmpName| DeptName| DeptID| Salary| MinSalary
|-----|-------|--------|------|-------------|----------------
| 1| Noah| Engineering| 100| 10000| 10000
| 2| Sophia| Engineering| 100| 20000| 10000
| 3| Liam| Engineering| 100| 30000| 10000
| 7| Mason| Executive| 300| 50000| 50000
| 4| Emma| HR| 200| 10000| 10000
| 5| Jacob| HR| 200| 10000| 10000
| 6| Olivia| HR| 200| 10000| 10000
| 8| Ava| Marketing| 400| 15000| 10000
| 9| Ethan| Marketing| 400| 10000| 10000

MIN を MAX に置き換えて試してみてください。


## 順位付け関数

順位付け関数では、PARTITION BY 句と OVER 句で定義されたとおり、各パーティション内の行ごとに順位付け値 (long) を返します。 順位付けは、OVER 句の ORDER BY によって制御されます。

サポートされる順位付け関数は次のとおりです。

- RANK
- DENSE_RANK
- NTILE
- ROW_NUMBER

**構文:**

    [ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
        OVER (
            [PARTITION BY <identifier, > …[n]]
            [ORDER BY <identifier, > …[n] [ASC|DESC]] 
    ) AS <alias>

- ORDER BY 句は、順位付け関数では省略可能です。 指定した場合は、順位付けが決定されます。 ORDER BY を指定しなかった場合は、U-SQL がレコードを読み取る順番に基づいて値を割り当てます。 したがって、ORDER BY 句が指定されていない場合、行番号、順位、密度の高い順位は非確定的な値になります。
- NTILE には、正の整数に評価される式が必要です。 この数値は、各パーティションをいくつのグループに分割する必要があるかを示します。 この識別子は、NTILE 順位付け関数でのみ使用されます。

OVER 句の詳細については、次を参照してください。 [U SQL リファレンス]()します。

ROW_NUMBER、RANK、DENSE_RANK はすべて、ウィンドウ内の行に番号を割り当てます。 これらの関数を個別に説明することはしませんが、より直観的に理解できるように、同じ入力に対する応答を紹介します。

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;

OVER 句が同じであることに注意してください。 結果は次のようになります。

| クエリ| Latency:int| Vertical| RowNumber| Rank| DenseRank
|-----|-----------|--------|--------------|---------|--------------
| Banana| 300| Image| 1| 1| 1
| Cherry| 300| Image| 2| 1| 1
| Durian| 500| Image| 3| 3| 2
| Apple| 100| Web| 1| 1| 1
| Fig| 200| Web| 2| 2| 2
| Papaya| 200| Web| 3| 2| 2
| Fig| 300| Web| 4| 4| 3
| Cherry| 400| Web| 5| 5| 4
| Durian| 500| Web| 6| 6| 5

### ROW_NUMBER

各ウィンドウ (Vertical が Image または Web) 内で、Latency で並べ替えられ、行番号が 1 ずつ増加します。

![U-SQL window function ROW_NUMBER](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### RANK

RANK() では、ROW_NUMBER() とは異なり、ウィンドウに ORDER BY 句で指定された Latency の値が考慮されます。

Latency の最初の 2 つの値が同じであるため、RANK 列は (1,1,3) で始まります。 その次の値は、Latency 値が 500 に変わったため、3 になります。 
重要な点は、重複する値に同じ順位が指定された場合でも、RANK の数値は次の ROW_NUMBER 値に "スキップ" することです。 
このパターンは、Web バーティカルのシーケンス (2,2,4) で繰り返されていることがわかります。

![U-SQL ウィンドウ関数 RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### DENSE_RANK

DENSE_RANK は RANK と似ていますが、次の ROW_NUMBER に "スキップ" せずに、順番に次の番号に進む点が異なります。 サンプルでは (1,1,2) および (2,2,3) というシーケンスに注目してください。

![U-SQL window function DENSE_RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### 解説

- ORDER BY が指定されていない場合、順位付け関数は、順序付けなしで行セットに適用されます。 その結果、順位付け関数の適用に関して非決定的動作が発生します。
- 次の条件に当てはまる場合を除き、ROW_NUMBER を使用してクエリによって返される行は、実行するたびに同じように並べ替えられます。

    - パーティション分割された列の値が一意である。
    - ORDER BY 列の値が一意である。
    - パーティション列の値と ORDER BY 列の値の組み合わせが一意である。

### NTILE

NTILE により、順序付けされたパーティション内の行は、指定された数のグループに分散されます。 グループには 1 から始まる番号が付けられます。


次の例では、各パーティション (バーティカル) 内の行セットをクエリの待機時間の順番で 4 つのグループに分割し、行ごとにグループ番号を返します。

Image バーティカルには 3 行が含まれるため、3 グループになります。

Web バーティカルには 6 行が含まれます。追加の 2 行は最初の 2 グループに配分されます。 そのため、グループ 1 とグループ 2 には 2 行ありますが、グループ 3 とグループ 4 には 1 行しかありません。

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;

結果は次のようになります。

| クエリ| 待機時間| Vertical| Quartile
|-----|-----------|--------|-------------
| Banana| 300| Image| 1
| Cherry| 300| Image| 2
| Durian| 500| Image| 3
| Apple| 100| Web| 1
| Fig| 200| Web| 1
| Papaya| 200| Web| 2
| Fig| 300| Web| 2
| Cherry| 400| Web| 3
| Durian| 500| Web| 4

NTILE では、パラメーター ("numgroups") を使用します。 numgroups は、各パーティションが分割されるグループ数を指定する、正の int または long 定数式です。

- パーティション内の行数を numgroups で均等に割り切れる場合、グループのサイズは均一です。
- パーティション内の行数を numgroups で割り切れない場合、1 つのメンバーが異なる 2 つのサイズのグループが作成されます。 OVER 句で指定された順序では、大きいグループが小さいグループの前に位置します。

次に例を示します。

- 100 行が 4 グループに分割された場合: [ 25, 25, 25, 25 ]
- 102 行が 4 グループに分割された場合: [ 26, 26, 25, 25 ]


### RANK、DENSE_RANK、ROW_NUMBER によるパーティションごとの上位 N レコード

多くのユーザーは、グループごとに上位 n 行のみを選択したいと考えています。 これは、従来の GROUP BY では不可能です。

次の例は、「順位付け関数」セクションの冒頭で示しました。 この例では、各パーティションの上位 N レコードは示されません。

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

結果は次のようになります。

| クエリ| 待機時間| Vertical| Rank| DenseRank| RowNumber
|-----|-----------|--------|---------|--------------|--------------
| Banana| 300| Image| 1| 1| 1
| Cherry| 300| Image| 1| 1| 2
| Durian| 500| Image| 3| 2| 3
| Apple| 100| Web| 1| 1| 1
| Fig| 200| Web| 2| 2| 2
| Papaya| 200| Web| 2| 2| 3
| Fig| 300| Web| 4| 3| 4
| Cherry| 400| Web| 5| 4| 5
| Durian| 500| Web| 6| 5| 6

### DENSE RANK による上位 N

次の例では、各グループから上位 3 件のレコードが返されます。各ウィンドウ パーティションでは、行に連続した一連の順位番号が付けられます。

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

結果は次のようになります。

| クエリ| 待機時間| Vertical| DenseRank
|-----|-----------|--------|--------------
| Banana| 300| Image| 1
| Cherry| 300| Image| 1
| Durian| 500| Image| 2
| Apple| 100| Web| 1
| Fig| 200| Web| 2
| Papaya| 200| Web| 2
| Fig| 300| Web| 3

### RANK による上位 N

    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

結果は次のようになります。

| クエリ| 待機時間| Vertical| Rank
|-----|-----------|--------|---------
| Banana| 300| Image| 1
| Cherry| 300| Image| 1
| Durian| 500| Image| 3
| Apple| 100| Web| 1
| Fig| 200| Web| 2
| Papaya| 200| Web| 2


### ROW_NUMBER による上位 N

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

結果は次のようになります。

| クエリ| 待機時間| Vertical| RowNumber
|-----|-----------|--------|--------------
| Banana| 300| Image| 1
| Cherry| 300| Image| 2
| Durian| 500| Image| 3
| Apple| 100| Web| 1
| Fig| 200| Web| 2
| Papaya| 200| Web| 3

### グローバルに一意の行番号を割り当てる

多くの場合、グローバルに一意の番号を各行に割り当てると便利です。 これは、順位付け関数を使用すると簡単です (さらに、reducer を使用するよりも効率的です)。

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

## 分析関数

分析関数は、ウィンドウ内の値の分布を把握するために使用します。 分析関数の使用に関する最も一般的なシナリオは、百分位数の計算です。

**サポートされている分析ウィンドウ関数**

- CUME_DIST
- PERCENT_RANK
- PERCENTILE_CONT
- PERCENTILE_DISC

### CUME_DIST

CUME_DIST は、値のグループにおける指定された値の相対位置を算出します。 同じバーティカルで、現在のクエリの待機時間以下のクエリの割合が計算されます。 
行 R の場合、昇順での順序付けを想定すると、R の CUME_DIST は、R の値以下の値を含む行の数を、パーティションまたはクエリ結果セットで評価された行の数で割った値になります。 
CUME_DIST で返される数値の範囲は、0 より大きく 1 以下になります。

** 構文**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

次の例では、CUME_DIST 関数を使用して、バーティカル内のクエリごとに待機時間の百分位数を計算します。

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

結果は次のようになります。

| クエリ| 待機時間| Vertical| CumeDist
|-----|-----------|--------|---------------
| Durian| 500| Image| 1
| Banana| 300| Image| 0.666666666666667
| Cherry| 300| Image| 0.666666666666667
| Durian| 500| Web| 1
| Cherry| 400| Web| 0.833333333333333
| Fig| 300| Web| 0.666666666666667
| Fig| 200| Web| 0.5
| Papaya| 200| Web| 0.5
| Apple| 100| Web| 0.166666666666667

パーティション キーが "Web" であるパーティションには 6 行含まれています (4 行目以降)。

- 値が 500 以下の行は 6 行あるため、CUME_DIST は 6/6=1 となります。
- 値が 400 以下の行は 5 行あるため、CUME_DIST は 5/6=0.83 となります。
- 値が 300 以下の行は 4 行あるため、CUME_DIST は 4/6=0.66 となります。
- 値が 200 以下の行は 3 行あるため、CUME_DIST は 3/6=0.5 となります。 待機時間の値が同じ行が 2 行あります。
- 値が 100 以下の行は 1 行あるため、CUME_DIST は 1/6=0.16 となります。


**使用上の注意:**

- 同順位の値は、常に、同じ累積分布の値に評価されます。
- NULL 値は、有効な最小値として扱われます。
- CUME_DIST を計算するには、ORDER BY 句を指定する必要があります。
- CUME_DIST は PERCENT_RANK 関数に似ています。

注: SELECT ステートメントの後に OUTPUT がない場合は、ORDER BY 句を使用できません。 したがって、OUTPUT ステートメント内の ORDER BY 句により、結果行セットの表示順が決定します。


### PERCENT_RANK

PERCENT_RANK は、行グループ内の行の相対的な順位を計算します。 PERCENT_RANK を使用すると、行セットまたはパーティション内で値の相対的な位置を評価できます。 PERCENT_RANK によって返される値の範囲は、0 より大きく 1 以下になります。 CUME_DIST とは異なり、最初の行では、PERCENT_RANK は常に 0 になります。

** 構文**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**メモ**

- どのセットでも、最初の行では PERCENT_RANK は 0 になります。
- NULL 値は、有効な最小値として扱われます。
- PERCENT_RANK を計算するには、ORDER BY 句を指定する必要があります。
- CUME_DIST は PERCENT_RANK 関数に似ています。


次の例では、PERCENT_RANK 関数を使用して、バーティカル内のクエリごとに待機時間の百分位数を計算します。

結果セット内の行をバーティカルでパーティションに分割するには、PARTITION BY 句を指定します。 OVER 句内の ORDER BY 句により、各パーティション内の行に順序が付けられます。

PERCENT_RANK 関数で返された値は、バーティカル内のクエリの待機時間の順位をパーセンテージで表しています。


    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

結果は次のようになります。

| クエリ| Latency:int| Vertical| PercentRank
|-----|-----------|--------|------------------
| Banana| 300| Image| 0
| Cherry| 300| Image| 0
| Durian| 500| Image| 1
| Apple| 100| Web| 0
| Fig| 200| Web| 0.2
| Papaya| 200| Web| 0.2
| Fig| 300| Web| 0.6
| Cherry| 400| Web| 0.8
| Durian| 500| Web| 1

### PERCENTILE_CONT と PERCENTILE_DISC

これら 2 つの関数では、列値の連続型分布または離散型分布に基づいて百分位数を計算します。

**構文**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric_literal** - 計算する百分位数です。 値は 0.0 ～ 1.0 の範囲で指定してください。

グループ内で (ORDER BY <identifier> [ASC |DESC]) では、並べ替えるし、百分位数を計算する数値のリストを指定します。許可される列識別子は 1 つだけです。式は、数値型に評価される必要があります。その他のデータ型は許可されていません。既定の並べ替え順は昇順です。

経由で ([パーティション分割で <identifier,>.[n]) では、入力行セットを指定とパーセン タイル関数を適用するパーティション キーによってパーティションに分割します。詳細については、このドキュメントの順位付けに関するセクションを参照してください。
注: データ セット内の null はすべて無視されます。

**PERCENTILE_CONT** は、列値の連続型分布に基づいて百分位数を計算します。 結果には値が補間され、列内の特定の値と一致しない可能性があります。

**PERCENTILE_DISC** は、列値の離散型分布に基づいて百分位数を計算します。 結果は、列内の特定の値と等しくなります。 つまり、PERCENTILE_CONT とは異なり、PERCENTILE_DISC では、常に実際の値 (元の入力値) が返されます。

次の例では、この 2 つの関数の動作を確認できます。ここでは、各バーティカル内で待機時間の中央値 (百分位数 = 0.50) を検索します。

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 
    
        FROM @querylog;

結果は次のようになります。

| クエリ| Latency:int| Vertical| PercentileCont50| PercentilDisc50
|-----|-----------|--------|-------------------|----------------
| Banana| 300| Image| 300| 300
| Cherry| 300| Image| 300| 300
| Durian| 500| Image| 300| 300
| Apple| 100| Web| 250| 200
| Fig| 200| Web| 250| 200
| Papaya| 200| Web| 250| 200
| Fig| 300| Web| 250| 200
| Cherry| 400| Web| 250| 200
| Durian| 500| Web| 250| 200


PERCENTILE_CONT では、値を補間できるため、Web バーティカルに待機時間が 250 のクエリがない場合でも、Web の中央値は 250 になります。

PERCENTILE_DISC では値が補間されないため、Web の中央値は 200 になります。これは、入力行で見つかる実際の値です。











## 関連項目

- [Microsoft Azure Data Lake 分析の概要](data-lake-analytics-overview.md)
- [Azure ポータルを使用して Data Lake 分析を使ってみる](data-lake-analytics-get-started-portal.md)
- [Azure PowerShell を使用して Data Lake 分析を使ってみる](data-lake-analytics-get-started-powershell.md)
- [Visual Studio の Data Lake Tools を使用して U SQL スクリプトを開発します。](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure Data Lake 分析の対話型チュートリアルを使用します。](data-lake-analytics-use-interactive-tutorials.md)
- [Azure Data Lake 分析を使用して web サイト ログを分析します。](data-lake-analytics-analyze-weblogs.md)
- [Azure Data Lake 分析 U SQL 言語を使ってみる](data-lake-analytics-u-sql-get-started.md)
- [Azure ポータルを使用して Azure Data Lake 分析を管理します。](data-lake-analytics-use-portal.md)
- [Azure PowerShell を使用して Azure Data Lake 分析を管理します。](data-lake-analytics-use-powershell.md)
- [監視し、Azure ポータルを使用して Azure Data Lake 分析ジョブのトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)





