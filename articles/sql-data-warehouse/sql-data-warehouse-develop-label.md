<properties
   pageTitle="SQL Data Warehouse | Microsoft Azure でラベルを使用して、クエリをインストルメント化"
   description="ソリューション開発のための Azure SQL Data Warehouse でのラベルを使用してクエリのインストルメント化に関するヒント。"
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

# SQL Data Warehouse で、ラベルを使用してクエリをインストルメント化
SQL Data Warehouse は、クエリ ラベルと呼ばれる概念をサポートします。 難解な領域に入る前に、1 つの例を説明しましょう。

    ```
    SELECT *
    FROM sys.tables
    OPTION (LABEL = 'My Query Label')
    ;
    ```

この最後の行は、クエリに、 'My Query Label' という文字列をタグ付けします。 これは、ラベルが DMV を介してクエリできるので特に便利です。 これにより、問題のあるクエリを追跡して、ETL 実行によって進行状況を識別するためのメカニズムが提供されます。 

良好な命名規則は、実際にはここで役立ちます。 たとえば、'PROJECT : PROCEDURE : STATEMENT : COMMENT' は、ソース管理内のすべてのコード間でのクエリを一意に識別する上で役立ちます。

ラベルで検索するために、動的管理ビューを使用する以下のクエリを使用することができます。

    ```
    SELECT  *
    FROM    sys.dm_pdw_exec_requests r
    WHERE   r.[label] = 'My Query Label'
    ;
    ``` 

> [AZURE.NOTE] クエリを実行するときに、角かっこまたは二重引用符で囲んで、語句のラベルをラップすることが重要です。 ラベルは予約語ですが、区切られていない場合、エラーが発生します。


## 次のステップ
他の開発のヒントを参照してください。 [開発の概要][]します。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->





