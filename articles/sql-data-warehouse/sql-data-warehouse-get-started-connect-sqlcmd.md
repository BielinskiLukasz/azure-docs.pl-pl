<properties
   pageTitle="作業開始: Azure SQL Data Warehouse への接続 | Microsoft Azure"
   description="SQL Data Warehouse に接続して、クエリを実行する"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="twounder"/>


# SQLCMD を使用した接続とクエリ

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)


このチュートリアルでは、sqlcmd.exe ユーティリティを使用して、Azure SQL Data Warehouse データベースへの接続とクエリを数分で実行する方法について説明します。 このチュートリアルでは次を行います。

+ 前提条件のソフトウェアをインストールする
+ AdventureWorksDW サンプル データベースを含んだデータベースに接続する
+ サンプル データベースに対してクエリを実行する

## 前提条件

+ [sqlcmd.exe](https://msdn.microsoft.com/library/azure/ms162773.aspx) - するには sqlcmd.exe のダウンロードについてを参照してください、 [Microsoft Command Line Utilities 11 for SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501)します。

## 完全修飾 Azure SQL サーバー名を取得します。

データベースに接続するには、サーバーの完全な名前を必要 (*** servername**. **.database.windows.net*) に接続するデータベースが格納されています。

1. 移動して、 [Azure ポータル](https://portal.azure.com)します。
2. 接続先のデータベースを探します。
3. サーバーの完全名を見つけます (これを次の手順で使用します)。

![][1]


## sqlcmd で SQL Data Warehouse に接続する

sqlcmd を使用するときに SQL Data Warehouse の特定のインスタンスに接続するには、コマンド プロンプトを開いて、**sqlcmd** の後に、SQL Data Warehouse データベースの接続文字列を入力する必要があります。 接続文字列では、次のパラメーターを指定します。

+ **ユーザー (・ U):** 形式でサーバー ユーザー `<`ユーザー`>`
+ **パスワード (-P):** ユーザーに関連付けられているパスワード。
+ **サーバー (-S):** 形式でサーバー `<`サーバー名`>`..database.windows.net を付けて
+ **データベース (-D):** データベース名。
+ **引用符で囲まれた ID の有効化 (-I):** SQL Data Warehouse インスタンスに接続するには、引用符で囲まれた ID を有効にする必要があります。

したがって、SQL Data Warehouse インスタンスに接続するには、次のように入力します。

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## サンプル クエリの実行

接続後、インスタンスに対してサポートされているすべての Transact-SQL ステートメントを発行できます。

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT COUNT(*) FROM dbo.FactInternetSales;
2> GO
3> QUIT
```

Sqlcmd に関する追加情報を参照してください、 [sqlcmd のドキュメント](https://msdn.microsoft.com/library/azure/ms162773.aspx)します。


## 次のステップ

これで接続して、クエリを実行できる、 [PowerBI で接続する][]します。



[connecting with powerbi]: ./sql-data-warehouse-integrate-power-bi.md 
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png 

