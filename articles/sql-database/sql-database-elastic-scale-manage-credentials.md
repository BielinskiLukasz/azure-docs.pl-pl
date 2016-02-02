<properties 
    pageTitle="エラスティック データベース クライアント ライブラリの資格情報を管理する | Microsoft Azure" 
    description="エラスティック データベース アプリの適切な資格情報のレベルを設定する方法 (管理者から読み取り専用)" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/11/2015" 
    ms.author="ddove;sidneyh"/>


# Elastic Database クライアント ライブラリへのアクセスに使用する資格情報

[Elastic Database クライアント ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) 3 つの異なる種類の資格情報を使用します。 資格情報が使用されるアクセスを [シャード マップ マネージャー](sql-database-elastic-scale-shard-map-management.md)します。 によっては、その必要性には、最下位レベルのアクセスを許可の資格情報を使用します。

* **管理の資格情報**: シャード マップ マネージャーを作成または操作します。 (、を参照してください [用語集](sql-database-elastic-scale-glossary.md).)
* **アクセス資格情報**: 既存のシャード マップ マネージャーにアクセスして、シャードに関する情報を取得します。
* **接続の資格情報**: シャードに接続します。

関連項目 [Azure SQL Database におけるデータベースとログインを管理する](sql-database-manage-logins.md)します。

## 管理の資格情報

管理資格情報は、作成に使用する、 [* * * * ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) シャード マップを操作するアプリケーションのオブジェクト。 (たとえば、を参照してください [エラスティック データベース ツールを使用してシャードを追加する](sql-database-elastic-scale-add-a-shard.md).) エラスティック スケール クライアント ライブラリのユーザーは、SQL ユーザーと SQL ログインを作成したうえで、グローバル シャード マップ データベースとすべてのシャード データベースに対する読み取り/書き込みアクセス許可が各ユーザーに付与されていることを確認する必要があります。 これらの資格情報は、シャード マップに変更を加えるときにグローバル シャード マップとローカル シャード マップを維持するために使用されます。 たとえば、管理資格情報を使用してシャード マップ マネージャー オブジェクトを作成する (を使用して [* * * * GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx):

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

変数 **smmAdminConnectionString** は、管理資格情報を格納する接続文字列です。 ユーザー ID とパスワードにより、シャード マップ データベースと個々のシャードの両方に対する読み取り/書き込みアクセスが提供されます。 管理接続文字列には、グローバル シャード マップ データベースを識別するためのサーバー名とデータベース名も含まれます。 この処理に使用する一般的な接続文字列を次に示します。

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

値は "username@server" の形式では使用しないでください。代わりに "username" のみを使用します。 これは、シャード マップ マネージャー データベースと個々のシャードの両方で資格情報を有効にする必要があり、それらが別々のサーバー上に存在する場合があるためです。

## 資格情報にアクセスする

シャード マップの管理用ではないアプリケーションでシャード マップ マネージャーを作成するときは、グローバル シャード マップに対する読み取り専用アクセス許可が与えられる資格情報を使用します。 これらの資格情報の下でグローバル シャード マップから取得される情報のため [データ依存型ルーティング](sql-database-elastic-scale-data-dependent-routing.md) と、クライアント上のシャード マップ キャッシュを設定します。 資格情報は、上に示すように、**GetSqlShardMapManager** と同じ呼び出しパターンを通じて提供されます。

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

ここでは、**管理者以外**のユーザーのアクセス用に使用されるさまざまな資格情報を反映するために **smmReadOnlyConnectionString** を使用しています。これらの資格情報では、グローバル シャード マップに対する書き込みアクセス許可は提供されません。

## 接続の資格情報

使用する場合に、追加の資格情報が必要な [* * * * OpenConnectionForKey](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) シャーディング キーに関連付けられているシャードにアクセスするメソッドです。 これらの資格情報は、シャード上に存在するローカル シャード マップ テーブルへの読み取り専用アクセス許可を提供する必要があります。 これは、シャード上でのデータ依存ルーティングのための接続検証を行う場合に必要になります。 次のコード スニペットは、データ依存ルーティングのコンテキストでデータ アクセスを許可します。

    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

この例の **smmUserConnectionString** は、ユーザー資格情報の接続文字列を保持します。 Azure SQL DB に対するユーザー資格情報の一般的な接続文字列を次に示します。

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

管理の資格情報と同じように、値は "username@server" の形式では使用しないでください。 代わりに "username" のみを使用します。 接続文字列にはサーバー名とデータベース名が含まれていないことにも注意してください。 これは、**OpenConnectionForKey** の呼び出しによって接続がキーに基づいた正しいシャードに自動的に転送されるためです。 そのため、データベース名とサーバー名を指定しないでください。

## 関連項目

[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)

[エラスティック データベース プールにユーザーを追加する方法](sql-database-elastic-jobs-add-logins-to-dbs.md)

[エラスティック データベース ジョブの概要](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]





