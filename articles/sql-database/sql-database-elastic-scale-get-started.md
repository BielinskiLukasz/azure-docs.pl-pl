<properties 
    pageTitle="Elastic Database ツールの概要" 
    description="実行が容易なサンプル アプリケーションを含む、Azure SQL Database の Elastic Database ツール機能の基本説明です。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="ddove" 
    editor="sidneyh"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh"/>

# Elastic Database ツールの概要

このドキュメントでは、サンプル アプリを実行して開発作業について説明します。 サンプルでは単純なシャーディング アプリケーションを作成し、Elastic Database ツールの主な機能について詳しく見て行きます。 サンプルの機能、 [elastic database クライアント ライブラリ](sql-database-elastic-database-client-library.md)

## 前提条件

1. C# と Visual Studio 2012 以上が必要です。 無償評価版をダウンロード [Visual Studio のダウンロード](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)します。
2. Nuget 2.7 以上。 最新のバージョンを入手するには、「 [NuGet のインストール](http://docs.nuget.org/docs/start-here/installing-nuget)

## サンプル アプリケーションのダウンロードと実行

 **Azure SQL Elastic Database-Getting Started** サンプル アプリケーションは、Azure SQL エラスティック データベース ツールを使用してシャード化されたアプリケーションの開発エクスペリエンスの最も重要な側面を示しています。 主な使用事例に焦点を当てます [シャード マップの管理](sql-database-elastic-scale-shard-map-management.md), 、[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md) と [マルチシャード クエリ実行](sql-database-elastic-scale-multishard-querying.md)します。 サンプルをダウンロードして実行するには、次の手順を実行します。 

1. Visual Studio を開き、選択 **ファイル]、[新しいプロジェクト]-> [**します。
2. ダイアログ ボックスで [ **オンライン**します。

    ![New Project>Online][2]
3. クリックし、 **Visual c#** [ **サンプル**します。

    ![Visual C] をクリックします。#][3]
4. 検索ボックスに入力 **elastic db** サンプルを検索します。タイトル **Elastic DB Tools for Azure SQL - Getting Started** が表示されます。

    ![Search Box][1]
 
5. サンプルを選択して、名前とキーを押して、新しいプロジェクトの場所を選択 **OK** プロジェクトを作成します。
6. 開いている、 **app.config** サンプル プロジェクトのソリューション内のファイルし、Azure SQL データベース サーバー名とログイン情報 (ユーザー名とパスワード) を追加するファイルの指示に従います。
7. アプリケーションをビルドし、実行します。 確認を求められたら、ソリューションの NuGet パッケージの復元を Visual Studio に許可してください。 これで Elastic Database クライアント ライブラリの最新版が NuGet からダウンロードされます。
8. クライアント ライブラリの機能をより深く知るために、さまざまなオプションを試してみてください。 アプリケーションで実行されたステップはコンソールに出力されますので、動作していたコードをじっくりと検討することができます。

    ![進捗状況][4]

これで、最初のシャーディング アプリケーションを Elastic Database ツールを使用して正しく Azure SQL Database にビルドし、実行できました。 Azure DB Server に Visual Studio または SQL Server Management Studio を接続して、サンプルで作成したシャードの内容を簡単に確認してください。 新しいサンプル シャード データベースと、シャード マップ マネージャー データベースがサンプルで作成されていることがわかります。

**注**   SQL Server Management Studio があるない場合は、次を参照してください。 [Azure SQL Database の管理 SQL Server Management Studio を使用して](sql-database-manage-azure-ssms.md), 、、このツールの入手に関する手順を参照します。  

### コード サンプルの主要部

1. **シャードとシャード マップを管理する**: コードは、シャード、範囲、およびマッピング ファイルで使用する方法を示しています。 **ShardMapManagerSample.cs**します。 詳細については、このトピックを見つけることができます: [シャード マップの管理](http://go.microsoft.com/?linkid=9862595)します。  
2. **データ依存ルーティング**: でトランザクションの適切なシャードへのルーティングを示す **DataDependentRoutingSample.cs**します。 詳細については、次を参照してください。 [データ依存ルーティング](http://go.microsoft.com/?linkid=9862596)します。 
3. **複数のシャードにまたがるクエリ実行**: ファイルに示さはシャード間でクエリを実行する **MultiShardQuerySample.cs**します。 詳細については、次を参照してください。 [マルチシャード クエリ実行](http://go.microsoft.com/?linkid=9862597)します。
4. **空のシャードを追加する**: 新しい空のシャードの反復追加は内のコードで実行
ファイル **AddNewShardsSample.cs**します。 このトピックの詳細に記載されて: [シャード マップの管理](http://go.microsoft.com/?linkid=9862595)します。

### 他の Elastic Scale の操作

1. **既存のシャードの分割**: を介してシャードを分割する機能が提供される、 **分割/マージ ツール**します。 ここで、このツールに関する詳細情報を記載することができます: [分割/マージ ツールの概要](sql-database-elastic-scale-overview-split-and-merge.md)します。
2. **既存のシャードのマージ**: シャードのマージを使用してを実行しても、 **分割/マージ ツール**します。 詳細についてを参照してください: [分割/マージ ツールの概要](sql-database-elastic-scale-overview-split-and-merge)します。   


## コスト

Elastic Database ツールは無料です。 Elastic Database ツールでは、Azure の利用料以外の追加料金は発生しません。 

たとえば、サンプル アプリケーションは新しいデータベースを作成します。 その場合のコストは、選択した Azure SQL DB データベースのエディションと、アプリケーションによる Azure の使用状況に応じて異なります。

料金情報については、次を参照してください。 [SQL Database の料金詳細](http://azure.microsoft.com/pricing/details/sql-database/)します。

## 次のステップ
Elastic Database ツールの詳細については、以下のページを参照してください。

* [Elastic Database ツールのドキュメント マップ](sql-database-elastic-scale-documentation-map.md) 
-    コード サンプル: 
    -    [Azure SQL で使用する Elastic DB - 概要](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Azure DB で使用する Elastic DB - Entity Framework との統合](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [スクリプト センター「Azure SQL Database Elastic Scale: Shard Elasticity (Azure SQL データベース Elastic Scale: シャードの弾力性)」](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    ブログ: [Elastic Scale の発表](http://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Channel 9: [Elastic Scale の概要に関するビデオ](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    ディスカッション フォーラム: [Azure SQL Database フォーラム](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 


