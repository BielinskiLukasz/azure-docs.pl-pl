<properties
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh" />

# 最新の エラスティック データベース クライアント ライブラリへのアップグレード

新しいバージョンの [Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md) で使用可能な [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) と Visual Studio の NuGetPackage マネージャーのインターフェイスです。 アップグレードにはバグ修正が含まれており、クライアント ライブラリの新機能をサポートします。

新しいライブラリを使用してアプリケーションを再構築し、新しい機能をサポートするために、Azure SQL Database に格納されている既存のシャード マップ マネージャーのメタデータを変更します。

これらの手順を実行することで、メタデータ オブジェクトの更新時に古いバージョンのクライアント ライブラリが環境内に存在しないようにします。つまり、古いバージョンのメタデータ オブジェクトはアップグレード後は作成されなくなります。   

## アップグレードの手順

**1.アプリケーションをアップグレードします。** Visual Studio で、最新のクライアント ライブラリのバージョンをダウンロードして、ライブラリを使用する開発プロジェクトのすべてに参照させます。その後、再構築してデプロイします。 

 * Visual Studio ソリューションで選択 **ツール** --> **NuGet パッケージ マネージャー** -->  **ソリューションの NuGet パッケージの管理**します。 
 * (Visual Studio 2013)左側のパネルの [ **更新**, 、し、[、 **更新** ] ボタンをクリックして、パッケージに **Azure SQL データベース Elastic Scale クライアント ライブラリ** ウィンドウに表示されます。
 * (Visual Studio 2015)フィルター ボックスを設定 **利用可能なアップグレード**します。 更新、およびをクリックするには、パッケージを選択して、 **更新** ] ボタンをクリックします。
    
 
 * 構築してデプロイします。 

**2.スクリプトをアップグレードします。** 使用している場合 **PowerShell** 、シャードを管理するためのスクリプト [新しいライブラリ バージョンをダウンロード](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) しスクリプトを実行するディレクトリにコピーします。 

**3.分割-結合サービスをアップグレードします。** エラスティック データベース分割/マージ ツールを使用してシャード化されたデータを再編成する場合 [のダウンロードし、ツールの最新バージョンを展開](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)します。 詳しいアップグレード手順については、サービス [ここ](sql-database-elastic-scale-overview-split-and-merge.md)します。 

**4.シャード マップ マネージャーのデータベースをアップグレードします。** Azure SQL Database でシャード マップをサポートするメタデータをアップグレードします。  これは、PowerShell か C# を使用して実行できます。 次に両方の方法について説明します。

***オプション 1: PowerShell を使用してメタデータをアップグレードします。***

1. NuGet の最新のコマンド ライン ユーティリティをダウンロード [ここ](http://nuget.org/nuget.exe) し、フォルダーに保存します。 

2. コマンド プロンプトを開き、同じフォルダーに移動して、コマンドを発行します。
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. ダウンロードした新しいクライアントの DLL バージョンを含むサブフォルダーに移動します。例:
`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. エラスティック データベース クライアントのアップグレード スクリプトレットをダウンロード、 [スクリプト センター](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9), 、DLL が含まれる同じフォルダーに保存します。

5. そのフォルダーのコマンド プロンプトから"PowerShell.\upgrade.ps1"を実行し、画面の指示に従います。
 
***オプション 2: C を使用してメタデータをアップグレードします。#***

または、ShardMapManager を開き、すべてのシャードを反復処理し、メソッドを呼び出して、メタデータのアップグレードを実行する Visual Studio アプリケーションを作成 [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) と [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) この例のようにします。 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

このメタデータのアップグレードの手法は問題なく何度も適用できます。 たとえば古いクライアント バージョンで、シャードをすでに更新した後に不注意にシャードを作成してしまった場合、すべてのシャードで再びアップグレードを実行し、最新のメタデータ バージョンがインフラストラクチャ全体に存在するようにできます。 

**注:**  クライアント ライブラリの新しいバージョンの発行までの Azure SQL DB、またはその逆に、古いバージョンのシャード マップ マネージャー メタデータで作業を続行します。   ただし、最新のクライアントで一部の新機能を活用するには、メタデータをアップグレードする必要があります。   メタデータのアップグレードはユーザー データやアプリケーション専用データに影響することはありません。シャード マップ マネージャーで作成し、使用したオブジェクトのみに影響します。  アプリケーションは以下に説明するアップグレード シーケンスを通じて引き続き操作できます。 

## エラスティック データベース クライアントのバージョン履歴 

**バージョン 1.0 - 2015 年 4 月**

* 一般公開リリース
* シャーディング キーとして Datetime 型のサポートが追加されました。

**バージョン 0.8 – 2015 年 3 月**

* 新しい ShardMap.OpenConnectionForKeyAsync メソッドのデータ依存型ルーティングに非同期サポートが追加されました。 
* パブリックの KeyType プロパティが ShardMap に追加されました。 
* データベースの復元サポートの改善とシャード用の障害復旧のシナリオが追加されました。 

**バージョン 0.7 – 2014 年 10 月** 

初期プレビュー バージョン 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 

