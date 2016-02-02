<properties
    pageTitle="SQL Database 監査の使用 | Microsoft Azure"
    description="SQL Database 監査の使用"
    services="sql-database"
    documentationCenter=""
    authors="jeffgoll"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/12/2015"
    ms.author="jeffreyg; ronitr"/>


# SQL Database 監査の使用

Azure SQL Database 監査は、データベース イベントを追跡し、監査したイベントを Azure ストレージ アカウントの監査ログに書き込みます 監査は通常、Basic、Standard、と Premium の各サービス層で使用できます。

監査と規制遵守の維持、データベースの利用状況を理解およびビジネス上の懸念がある可能性やセキュリティ違犯の疑いのある不一致や異常に関する洞察が役立ちます。

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。 Azure の詳細については、標準準拠をサポートするプログラムを参照してください、 [Azure のトラスト センター](http://azure.microsoft.com/support/trust-center/compliance)します。

+ [Azure SQL Database 監査の基本]
+ [データベースの監査を設定する]
+ [監査ログとレポートを分析する]

## <a id="subheading-1"></a>Azure SQL データベースの監査の基本

以下のセクションでは、Azure ポータルを使用した監査の構成について説明します。 必要な場合も [従来の Azure クラシック ポータルを使用して、データベースの監査を設定する]。

SQL Database 監査により、以下のことが可能になります。

- 選択したイベントの監査証跡の**保持**。 監査するデータベース活動のカテゴリを定義できます。
- データベース活動の**レポート**。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
- レポートの**分析**。 疑わしいイベント、異常な活動、および傾向を発見できます。

> [AZURE.NOTE] プレビューが開始された新しい**脅威の検出**機能を使用すると、セキュリティ上の脅威になる可能性がある異常なデータベース アクティビティのプロアクティブ アラートを受信できるようになります。 [脅威の検出] は [監査の構成] ブレードでオンにして構成することができます。 参照してください [脅威の検出の概要](sql-database-threat-detection-get-started.md) 詳細です。

次のイベント カテゴリの監査を構成できます。

収集された監査ログが次のように分類される**プレーンな SQL** および**パラメーター化された SQL**

- **データへのアクセス**
- **スキーマの変更 (DDL)**
- **データの変更 (DML)**
- **アカウント、ロール、アクセス許可 (DCL)**
- **ストアド プロシージャ**、**ログイン**、**トランザクション管理**。

各イベント カテゴリに対し、**成功**および**失敗**した操作の監査が個別に構成されます。

アクティビティとイベントの監査についてさらに詳細については、「、 [監査ログ形式のリファレンス (doc ファイルのダウンロード)](http://go.microsoft.com/fwlink/?LinkId=506733)します。

監査ログは、Azure ストレージ アカウントに格納されます。 古いログを削除するまでの監査ログのリテンション期間の定義できます。

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。 既定のサーバー監査ポリシーは、特定のオーバーライド データベース監査ポリシーが定義されていないサーバー上のすべてのデータベースに適用されます。

使用している場合は、チェックを監査を設定する前に、 [「ダウンレベル クライアント」](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)します。


## <a id="subheading-2"></a>データベースに対する監査を設定します。

1. 起動、 [Azure ポータル](https://portal.azure.com) https://portal.azure.com にします。 また、起動することも、 [従来の Azure クラシック ポータル](https://manage.windowsazure.com/) https://manage.windowsazure.com/でします。 詳しい手順は次のようになります。

2. 監査する SQL Database または SQL Server の [設定] ブレードに移動します。[設定] ブレードで、**[監査と脅威の検出]** を選択します。

    ![ナビゲーション ウィンドウ][1]

3. [監査の構成] ブレードで [監査] を [**オン**] にします。

4. **[容量の詳細]** を選択して [監査ログの容量] ブレードを開きます。 ログを保存する Azure ストレージ アカウントを選択し、リテンション期間を選択します。 **ヒント:** 監査レポートのテンプレートを最大限活用するには、すべての監査済みデータベースに同じストレージ アカウントを使用してください。

    ![ナビゲーション ウィンドウ][2]

5. 監査するイベントをカスタマイズするには、[**監査されたイベント**] をクリックします。 **[イベントによるログ]** ブレードで、すべてのイベントのログを記録するには、**[成功]** および **[失敗]** をクリックします。または、イベント カテゴリを個別に選択します。

6. このデータベースをサーバーの設定に従い監査する場合は、[**監査設定をサーバーから継承**] チェック ボックスをオンにします。 このオプションをオンにすると、このコンテキストでサーバーの監査設定を表示または変更するリンクが表示されます。

    ![ナビゲーション ウィンドウ][3]

7. 監査設定を構成したら [脅威の検出] を [**オン**] にして、セキュリティの警告を受信する電子メールを構成します。 参照してください、 [脅威検出作業の開始](sql-database-threat-detection-get-started.md) 詳細についてはページです。

8. **[保存]** をクリックします。



## <a id="subheading-3"></a>監査ログとレポートを分析します。

監査ログは、設定時に選択した Azure ストレージ アカウントで、**SQLDBAuditLogs** というプレフィックスを使用してストア テーブルのコレクションに集計されます。 などのツールを使用してログ ファイルを表示する [Azure ストレージ エクスプ ローラー](http://azurestorageexplorer.codeplex.com/)します。

事前構成されたレポート テンプレートが、 [ダウンロードできる Excel スプレッドシート](http://go.microsoft.com/fwlink/?LinkId=403540) ログ データをすばやく分析できます。 監査ログでテンプレートを使用する Excel 2013 またはそれ以降、および Power Query ダウンロードすることが必要 [ここ](http://www.microsoft.com/download/details.aspx?id=39379)します。

Power Query を使用すると、Azure ストレージ アカウントから直接 Excel テンプレートに、監査ログをインポートできます。 その後監査レコードを確認し、ログ データを使用しダッシュ ボードとレポートを作成できます。


![Navigation Pane][4]


## <a id="subheading-4"></a>従来の Azure クラシック ポータルを使用して、データベースに対する監査を設定します。

1. 起動、 [従来の Azure クラシック ポータル](https://manage.windowsazure.com/) https://manage.windowsazure.com/でします。

2. 監査する SQL Database/SQL Server をクリックしてから、[**監査とセキュリティ**] タブをクリックします。

3. [監査] を [**有効**] に設定します。

    ![ナビゲーション ウィンドウ][5]

4. 必要に応じて監査するイベントをカスタマイズするために [**イベントごとのログ記録**] を編集します。

    ![ナビゲーション ウィンドウ][6]

5. [**ストレージ アカウント**] を選択し、[**保持**] を設定します。

    ![ナビゲーション ウィンドウ][7]

6. **[保存]** をクリックします。




## <a id="subheading-5">運用環境での使用手法</a>

このセクションでは、上のスクリーン キャプチャーについて説明します。 いずれか [Azure ポータル](https://portal.azure.com) または [従来の Azure クラシック ポータル](https://manage.windowsazure.com/) 使用することがあります。


## <a id="subheading-6"></a>ストレージ キーの再生成

運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 キーを最新の情報に更新する場合は、監査ポリシーを再度保存する必要があります。 このプロセスは次のとおりです。


1. [監査の構成] ブレードで、**[ストレージ アクセス キー]** を *[プライマリ]* から *[セカンダリ]* に切り替え、**[保存]** をクリックします。

    ![][8]

2. ストレージ構成ブレードに移動し、*プライマリ アクセス キー*を**再生成**します。

3. [監査の構成] ブレードに戻り、**[ストレージ アクセス キー]** を *[セカンダリ]* から *[プライマリ]* に切り替え、**[保存]** をクリックします。

4. ストレージの UI に戻り、*セカンダリ アクセス キー*を**再生成** (次のキー更新サイクルの準備として) します。

## <a id="subheading-7"></a>オートメーション

Azure SQL Database で監査を構成する際、使用できる PowerShell コマンドレットがいくつかあります。

- [Get AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [削除 AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [削除 AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [セット AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [セット AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [使用する AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)







[azure sql database auditing basics]: #subheading-1 
[set up auditing for your database]: #subheading-2 
[analyze audit logs and reports]: #subheading-3 
[set up auditing for your database using the classic azure classic portal]: #subheading-4 
[practices for usage in production]: #subheading-5 
[storage key regeneration]: #subheading-6 
[automation]: #subheading-7 
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png 
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png 
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png 
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png 
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png 
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png 
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png 
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png 

