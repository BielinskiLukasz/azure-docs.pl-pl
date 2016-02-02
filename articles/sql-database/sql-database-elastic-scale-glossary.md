<properties 
    pageTitle="エラスティック データベース ツールの用語集 |Microsoft Azure" 
    description="Elastic Database ツールで使用される用語の説明" 
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
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh"/>


# Elastic Database ツールの用語集

に関する次の用語の定義、 [エラスティック データベース ツール](sql-database-elastic-scale-introduction.md), 、Azure SQL Database の機能です。 ツールを使用する管理 [のシャード マップ](sql-database-elastic-scale-shard-map-management.md), を含めると、 [クライアント ライブラリ](sql-database-elastic-database-client-library.md), 、 [分割/マージ ツール](sql-database-elastic-scale-overview-split-and-merge.md), 、[エラスティック プール](sql-database-elastic-pool.md), と [クエリ](sql-database-elastic-query-overview.md)します。

これらの用語を使用 [エラスティック データベース ツールを使用してシャードを追加する](sql-database-elastic-scale-add-a-shard.md) と [RecoveryManager クラスを使用してシャード マップに関する問題の解決](sql-database-elastic-database-recovery-manager.md)します。

![Elastic Scale の用語][1]

**データベース**: Azure SQL データベース

**データ依存ルーティング**: アプリケーションが特定のシャーディング キーを持つシャードに接続することを可能にする機能。 **マルチシャード クエリ**も参照。

**グローバル シャード マップ**: **シャード セット**内のシャーディング キーとそれに対応するシャードの間のマップ。 グローバル シャード マップは、**シャード マップ マネージャー**に格納されます。 **ローカル シャード マップ**も参照。

**リスト シャード マップ**: シャーディング キーが個別にマップされたシャード マップ。 **範囲シャード マップ**も参照。

**ローカル シャード マップ**: ローカル シャード マップは、シャードに格納され、シャードに存在するシャードレットのマッピングを含みます。

**マルチシャード クエリ**: 複数のシャードに対してクエリを発行する機能。結果セットは、UNION ALL セマンティクス ("ファンアウト クエリ" とも呼ばれます) を使用して返されます。 **データ依存ルーティング**も参照。

**範囲シャード マップ**: 連続値の複数の範囲に基づくシャード分散戦略を持つシャード マップ。

**参照テーブル**: シャード化されず、シャード間で複製されるテーブル。 たとえば、郵便番号を参照テーブルに格納できます。

**シャード**: シャード化されたデータ セットのデータを格納する Azure SQL データベース。

**シャードの弾力性**: **水平スケーリング**と**垂直スケーリング**の両方を実行する機能。

**シャード化テーブル**: シャード化されたテーブル。つまり、データがシャーディング キー値に基づいて複数のシャードに分散されたテーブル。

**シャーディング キー**: シャード間でデータを分散する方法を決定する値です。 値の型には、**int**、**bigint**、**varbinary**、**uniqueidentifier** のいずれかを使用できます。

**シャード セット**: シャード マップ マネージャーの同じシャード マップに属するシャードのコレクション。

**シャードレット**: シャード上のシャーディング キーの単一の値に関連付けられたデータのすべて。 シャードレットは、シャード化テーブルを再分散する際に使用できる最小データ移動単位です。

**シャード マップ**: シャーディング キーとそれに対応するシャードの間のマッピングのセット。

**シャード マップ マネージャー**: 1 つまたは複数のシャード セットのシャード マップ、シャードの場所、およびマッピングを含む管理オブジェクトおよびデータ ストア。

![マッピング][2]


## 動詞

**水平スケーリング**: シャード マップに対するシャードの追加や削除を行ってシャードのコレクションをスケール アウト (またはスケール イン) する操作 (下図参照)。

![水平および垂直方向のスケーリング][3]

**マージ**: 2 つのシャードから 1 つのシャードにシャードレットを移動し、それに応じてシャード マップを更新する操作。

**シャードレットの移動**: 1 つのシャードレットを異なるシャードに移動する操作。

**シャード**: 同じ構造を持つデータをシャーディング キーに基づいて複数のデータベースに水平方向に分割する操作。

**分割**: 1 つのシャードから別の (通常は新しい) シャードに複数のシャードレットを移動する操作。 シャーディング キーは分割ポイントとしてユーザーから提供されます。

**垂直スケーリング**: 個々のシャードのパフォーマンス レベルをスケール アップ (またはダウン) する操作。 たとえば、シャードを Standard から Premium に変更 (結果、コンピューティング リソースが増大)。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



[1]: ./media/sql-database-elastic-scale-glossary/glossary.png 
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png 
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png 

