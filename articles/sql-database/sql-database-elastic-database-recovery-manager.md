<properties 
    pageTitle="Recovery Manager を使用したシャード マップに関する問題の解決 | Microsoft Azure" 
    description="RecoveryManager クラスを使用したシャード マップに関する問題の解決" 
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
    ms.date="11/09/2015" 
    ms.author="ddove"/>

# RecoveryManager クラスを使用したシャード マップに関する問題の解決

 [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) クラスは、ADO.Net アプリケーションを簡単に検出し、グローバル シャード マップ (GSM) とシャード化データベース環境でローカルのシャード マップ (LSM) の間の不整合を修正機能を提供します。 

GSM と LSM はシャード化環境内の各データベースのマッピングを追跡します。 場合によっては、GSM と LSM の間で断絶が発生します。 その場合は、検出して、中断を修復する RecoveryManager クラスを使用します。

RecoveryManager クラスの一部である、 [Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library)します。 


![シャード マップ][1]


用語の定義を参照してください。 [エラスティック データベース ツールの用語集](sql-database-elastic-scale-glossary.md)します。 理解する方法、 **ShardMapManager** シャーディング ソリューションでデータを管理、表示に使用されます [シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)します。


## Recovery Manager を使用する理由

シャード化データベース環境には多数のデータベース サーバーがあります。 サーバーにはそれぞれ多数のデータベースが含まれており、マルチテナント ソリューションのユーザーごとに 1 つずつデータベースがあります。 各データベースは、呼び出しを適切なサーバーとデータベースに正確にルーティングできるようにマップされている必要があります。 データベースは、シャーディング キーに従って追跡されます。各サーバーにはキー値の範囲が割り当てられます。 たとえば、あるシャーディング キーは、"D" ～ "F" の顧客名を表します。 すべてのサーバーとそのキー範囲のマッピングは、すべてグローバル シャード マップに保持されます。 各サーバーには、シャードに属しているデータベースのマップも保持されます。これをローカル シャード マップと呼びます。 LSM は、キャッシュされたデータの検証に使用されます  (アプリがシャードに接続すると、すばやく取得できるようにマッピングがアプリにキャッシュされます。 LSM はこのマッピングを検証します)。 

Elastic Database クライアント ツール ライブラリなどのツールを使用して、シャード間でデータを移動できます。 移動中に中断が発生した場合は、GSM と LSM が同期しなくなる可能性があります。 他にも次のような理由があります。

1. 範囲が使用されていないと思われるシャードの削除またはシャードの名前変更によって発生する不整合。 シャードの削除の結果、 **シャード マッピングの孤立**します。 データベースの名前を変更した場合も、同様に孤立したシャード マッピングが発生します。 このような場合は、シャードの場所を更新すれば解決されます。 
2. geo フェールオーバー イベントの発生。 続行するには、シャード マップ内のすべてのシャードのサーバー名、データベース名、シャード マッピングの詳細を更新する必要があります。 geo フェールオーバーが発生した場合に備えて、フェールオーバー ワークフロー内でこのような復旧ロジックを自動化しておく必要があります。 
3. シャード化データベースまたは ShardMapManager データベースが以前の時点に復元された。 
 
復旧操作を自動化すると、geo 対応データベースを円滑に管理でき、ユーザーによる手動操作もなくすことができます。 また、データが誤って削除された場合の回復シナリオにも役立ちます。 

Azure SQL Database の Elastic Database ツール、geo レプリケーション、および復元の詳細については、次のページを参照してください。 

* [Azure SQL Database の Elastic Database 機能](sql-database-elastic-scale-introduction.md) 
* [Azure SQL Database のビジネス継続性](sql-database-business-continuity.md) 
* [Elastic Database ツールの概要](sql-database-elastic-scale-get-started.md)  
* [ShardMap 管理](sql-database-elastic-scale-shard-map-management.md)

## RecoveryManager からの ShardMapManager の取得 

最初の手順は、RecoveryManager インスタンスの作成です。  [GetRecoveryManager メソッド](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) 、現在の回復マネージャーを返します [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) インスタンス。 シャード マップ内の不整合を解決するには、最初に、特定のシャード マップの RecoveryManager を取得する必要があります。 

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 

この例では、RecoveryManager が ShardMapManager から初期化されています。 ShardMap が含まれた ShardMapManager も既に初期化されています。 

このアプリケーション コードではシャード マップを直接操作しないため、ファクトリ メソッドで使用される資格情報 (上記の例では smmConnectionString) は、接続文字列が参照する GSM データベースに対する読み取り/書き込みアクセス許可を持つ資格情報である必要があります。 これらの資格情報は、通常、データ依存ルーティングの接続で使用される資格情報とは異なります。 詳細については、次を参照してください。 [エラスティック データベース クライアントの資格情報を使用して](sql-database-elastic-scale-manage-credentials.md)します。

## シャードを削除した後の ShardMap からのシャードの削除

 [DetachShard メソッド](https://msdn.microsoft.com/library/azure/dn842083.aspx) シャード マップから特定のシャードの割り当てを解除し、シャードに関連付けられているマッピングを削除します。  

* Location パラメーターはシャードの場所、具体的には、デタッチするシャードのサーバー名とデータベース名です。 
* shardMapName パラメーターは、シャード マップの名前です。 これは、複数のシャード マップが同じシャード マップ マネージャーによって管理されている場合のみ必須です。 省略可能。 

**重要な**: 更新されたマッピングが空の範囲が特定される場合のみ、この手法を使用します。 上記の方法では、移動される範囲のデータはチェックされないため、コード内にチェックを含めることが最善です。

次の例は、RecoveryManager を使用してシャードをシャード マップから削除するにはシャード マップには、シャードの削除の前に、GSM にシャードの場所が反映されます。 シャードが削除されたため、これは意図的な操作であり、シャーディング キー範囲が使用されなくなったと見なされます。 意図した操作でない場合は、ポイントインタイム リストアを実行して、以前の時点からシャードを復元できます  (その場合、以下のセクションで、シャードの不整合の検出について確認してください)。データベースの削除は意図的な操作であることを前提とするため、最終的なクリーンアップ管理操作は、シャード マップ マネージャーでシャードのエントリを削除することです。 これによって、アプリケーションが誤って予期しない範囲に情報を書き込むことがなくなります。
    
    rm.DetachShard(s.Location, customerMap); 

## マッピングの相違点を検出するには 

 [DetectMappingDifferences メソッド](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) を選択し、シャード マップ (ローカルまたはグローバル) のいずれかの情報源として返し、に両方のシャード マップ (GSM と LSM) へのマッピングを調整します。

    rm.DetectMappingDifferences(location, shardMapName);

*  *場所* パラメーターは、サーバー名とデータベース名、シャードの具体的には、シャードの場所。 
*  *ShardMapName* パラメーターは、シャード マップ名。 これは、複数のシャード マップが同じシャード マップ マネージャーによって管理されている場合のみ必須です。 省略可能。 

## マッピングの相違点を解決するには

 [ResolveMappingDifferences メソッド](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) の情報源として、シャード マップ (ローカルまたはグローバル) のいずれかを選択し、両方のシャード マップ (GSM と LSM) にへのマッピングを調整します。

    ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution);
   
*  *RecoveryToken* パラメーターは、GSM と特定のシャードの LSM 間のマッピングの相違を列挙します。 

*  [MappingDifferenceResolution 列挙](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) シャード マッピングの違いを解決するための方法を示すために使用します。 **MappingDifferenceResolution.KeepShardMapping** の LSM には、正確なマッピングが含まれています。 であるため、シャードのマッピングを使用する必要がありますことをお勧めします。 これは通常、フェールオーバーが発生した場合に当てはまります。そのような状況では、新しいサーバーにシャードが存在します。 最初に (RecoveryManager.DetachShard メソッドを使用して) GSM からシャードを削除する必要があるため、マッピングは GSM には存在しなくなります。 そのため、LSM を使用して、シャード マッピングを再確立する必要があります。

## シャードを復元した後の ShardMap へのシャードのアタッチ 

 [AttachShard メソッド](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) 特定のシャードをシャード マップにアタッチします。 その後、シャード マップの不整合が検出され、復元された時点のシャードと一致するようにマッピングが更新されます。 ポイントインタイム リストアでは既定で新しいデータベースにタイムスタンプが付加されるため、(シャードの復元の実行前に) 元のデータベース名を反映するようにデータベース名が変更されていることを前提とします。 

    rm.AttachShard(location, shardMapName) 

*  *場所* パラメーターは、サーバー名とが関連付けられているシャードのデータベースの名前。 

*  *ShardMapName* パラメーターは、シャード マップ名。 これは、複数のシャード マップが同じシャード マップ マネージャーによって管理されている場合のみ必須です。 省略可能。 

この例では、以前の時点から最近復元されたシャード マップにシャードを追加します。 シャード (厳密には LSM 内のシャードのマッピング) が復元されたため、GSM 内のシャード エントリと不整合が生じている可能性があります。 このコード例では、外部でシャードが復元され、データベースの元の名前に変更されています。 LSM 内のマッピングは復元されたため、信頼されたマッピングであると見なします。 

    rm.AttachShard(s.Location, customerMap); 
    var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 

## シャードの geo フェールオーバー (復元) の後のシャードの場所の更新

geo フェールオーバーが発生した場合は、セカンダリ データベースが書き込みアクセス許可を与えられて、新しいプライマリ データベースになります。 サーバーの名前が、場合によっては (構成による) データベースの名前も、元のプライマリと異なっている今年があります。 そのため、GSM と LSM のシャードのマッピング エントリを修正する必要があります。 同様に、データベースを別の名前、別の場所、または以前の時点に復元した場合も、シャード マップで不整合が生じている可能性があります。 適切なデータベースへのオープンな接続の分配は、シャード マップ マネージャーが処理します。 分配は、シャード マップ内のデータと、アプリケーションによる要求の対象であるシャーディング キーに基づいて行われます。 geo フェールオーバー後は、この情報を、回復されたデータベースの正確なサーバー名、データベース名、およびシャード マッピングを使用して更新する必要があります。 

## ベスト プラクティス

geo フェールオーバーと復旧は一般的に、アプリケーションのクラウド管理者が Azure SQL Database のビジネス継続性機能の 1 つを意図的に使用して管理する操作です。 ビジネス継続性の計画には、ビジネス運営を中断なく確実に続行できるプロセス、手順、手段が必要です。 実行する復旧アクションに基づいて GSM と LSM が最新の状態に維持されるように、RecoveryManager クラスの一部として提供されているメソッドをこのワークフロー内で使用してください。 フェールオーバー後に GSM と LSM に正確な情報が反映されていることを正しく確認するには、次の 5 つの基本的な手順を実行します。 これらの手順を実行するアプリケーション コードを、既存のツールやワークフローに統合できます。 

1. Recovery Manager を ShardMapManager から取得します。 
2. シャード マップから使用しなくなったシャードをデタッチします。
3. 新しいシャードの場所を含むシャード マップに、新しいシャードをアタッチします。
4. GSM と LSM のマッピングの不整合が検出されます。 
5. GSM と LSM の相違点を、LSM を信頼して解決します。 

この例では、次のステップを実行します。
1. フェールオーバーの前に、シャードの場所が反映されているシャード マップからシャードを削除します。
2. 新しいシャード場所が ("Configuration.SecondaryServer" パラメーターは、サーバー名は新しく、データベース名は同じです) が反映されているシャード マップにシャードをアタッチします。
3. 各シェードの GSM と LSM のマッピングの相違点を検出して、回復トークンを取得します。 
4. 各シャードの LSM のマッピングを信頼して、不整合を解決します。 

    var シャード = smm します。GetShards() です。 
    foreach (シャード シャード内) 
    { 
     場合 (s.Location.Server Configuration.PrimaryServer = =) 
         { 
          ShardLocation slNew = 新しい ShardLocation (Configuration.SecondaryServer、s.Location.Database) です。 
        
          rm.DetachShard(s.Location); 
        
          rm.AttachShard(slNew); 
        
          var gs = rm.DetectMappingDifferences(slNew); 
    
          foreach (RecoveryToken g in gs) 
            { 
               rm.ResolveMappingDifferences(g,                      MappingDifferenceResolution.KeepShardMapping); 
            } 
        } 
    } 



[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
 
