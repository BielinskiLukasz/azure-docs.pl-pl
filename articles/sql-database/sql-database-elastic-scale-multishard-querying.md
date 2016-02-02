<properties 
    pageTitle="マルチシャード クエリ |Microsoft Azure" 
    description="Elastic Database クライアント ライブラリを使用して複数のシャードを対象にクエリを実行します。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="torsteng;sidneyh"/>


# マルチシャード クエリ実行

## 概要

[エラスティック データベース ツール](sql-database-elastic-scale-introduction.md), 、シャーディング データベース ソリューションを作成することができます。 **マルチシャード クエリ実行**は、複数のシャードにまたがるクエリが実行が必要となるデータ収集/レポート作成などのタスクに使用されます (対照的に [データ依存型ルーティング](sql-database-elastic-scale-data-dependent-routing.md), 、単一のシャード上のすべての操作を実行します)。

## 概要

使用してシャードを管理する、 [Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)します。 ライブラリと呼ばれる名前空間に含まれる **Microsoft.Azure.SqlDatabase.ElasticScale.Query** 、1 つのクエリと結果を使用して複数のシャードを照会する機能を提供します。 この名前空間により、シャードのコレクションに対するクエリ抽象化が提供されます。 また、多数のシャードを照会したときのエラーを処理するための代替実行ポリシー (具体的には、部分的な結果) も提供されます。

マルチシャード クエリ実行へのメインのエントリ ポイントは、**MultiShardConnection** クラスです。 データ依存ルーティングと、API がの使い慣れた操作を次のように、* *[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)* * クラスとメソッド。 **SqlClient** ライブラリの場合、最初に **SqlConnection** を作成し、接続の **SqlCommand** を作成します。次に、**Execute** メソッドのいずれかを使用してコマンドを実行します。 最後に、**SqlDataReader** で、コマンドの実行から返された結果セットを反復処理します。 複数シャード クエリ API を操作するには、次の手順に従います。

1. **MultiShardConnection** を作成する。
2. **MultiShardConnection** の **MultiShardCommand** を作成する。
3. コマンドを実行する。
4. **MultiShardDataReader** を介して結果を使用する。

主な相違点は、マルチシャード接続の構築です。 ここで **SqlConnection** が 1 つのデータベースに、 **MultiShardConnection** は、 *** シャード * のコレクション** 入力として。 シャードのコレクションは、シャード マップから設定できます。 次に、**UNION ALL** セマンティクスを使用してシャードのコレクションに対するクエリを実行して、単一の全体的な結果を生成します。 必要に応じて、コマンドの **ExecutionOptions** プロパティを使用して、行の元になっているシャードの名前を出力に追加できます。

## 例

次のコードは、*myShardMap* という名前の **ShardMap** を使用してマルチシャード クエリを実行する方法を示しています。

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 
    
            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

ここで、**myShardMap.GetShards()** の呼び出しに注目してください。 このメソッドは、シャード マップからすべてのシャードを取得し、すべての関連データベースを対象にクエリを実行する簡単な方法を提供します。 マルチシャード クエリのためのシャードのコレクションは、**myShardMap.GetShards()** 呼び出しから返されたコレクションに対して LINQ クエリを実行することによって、さらに絞り込むことができます。 部分的な結果のポリシーとの組み合わせにおいて、マルチシャード クエリ実行の現在の機能は、数十から最大で数百のシャードで適切に動作するように設計されています。

現在のマルチシャード クエリ実行には、クエリの対象となるシャードとシャードレットの検証が欠けているという制限があります。 データ依存ルーティングでは特定のシャードがシャード マップの一部であることが照会時に確認されますが、マルチシャード クエリ実行ではこのようなチェックは実行されません。 そのため、シャード マップから削除されたデータベースに対してマルチシャード クエリが実行される可能性があります。

## マルチシャード クエリと分割/マージ操作

マルチシャード クエリでは、照会されたデータベース上のシャードレットが進行中の分割/マージ操作に参加しているかどうかは確認されません。 (を参照してください [Elastic Database 分割/マージ ツールを使用したスケーリング](sql-database-elastic-scale-overview-split-and-merge.md).) そのため、同じマルチシャード クエリ内で複数のデータベースに対して同じシャードレットの行が表示される不整合が発生する可能性があります。 これらの制限を考慮したうえで、マルチシャード クエリを実行するときは、進行中の分割/マージ操作とシャード マップへの変更をドレインすることを検討してください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]





