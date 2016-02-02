<properties
    pageTitle="System Center 2012 R2 DPM を使用して Exchange サーバーを Azure Backup にバックアップする | Microsoft Azure"
    description="System Center 2012 R2 DPM を使用して Exchange サーバーを Azure Backup にバックアップする方法について説明する"
    services="backup"
    documentationCenter=""
    authors="Jim-Parker"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="jimpark;delhan"/>



# System Center 2012 R2 DPM を使用して Exchange サーバーを Azure Backup にバックアップする

この記事では、Azure Backup を Microsoft Exchange server をバックアップする System Center 2012 R2 Data Protection Manager (DPM) サーバーを構成する方法について説明します。

## 更新プログラム

Azure Backup に DPM サーバーを正常に登録するには、System Center 2012 R2 DPM 用の最新の更新プログラムのロールアップと、Azure Backup エージェントの最新バージョンをインストールする必要があります。 最新の更新プログラムのロールアップを取得、 [Microsoft カタログ](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)します。
>[AZURE.NOTE] この記事の例では、Azure Backup エージェントのバージョン 2.0.8719.0 と、System Center 2012 R2 DPM に対する更新プログラムのロールアップ 6 がインストールされています。

## 前提条件

続行する前に、以下のことを確認すべて、 [の前提条件](backup-azure-dpm-introduction.md#prerequisites) Microsoft Azure Backup を使用して保護するワークロードが満たされています。 該当する前提条件を以下に示します。

- Azure サイトに対するバックアップ コンテナーが作成済みである。
- エージェントとコンテナーの資格情報が DPM サーバーにダウンロード済みである。
- DPM サーバーにエージェントがインストールされている。
- コンテナーの資格情報を使用して DPM サーバーを登録済みである。

## DPM 保護エージェント

Exchange サーバーに DPM 保護エージェントをインストールするには、次の手順に従います。

1. ファイアウォールが正しく構成されていることを確認します。 参照してください [エージェントに対するファイアウォールの例外を構成する](https://technet.microsoft.com/library/Hh758204.aspx)します。

2. DPM 管理者コンソールで **[管理]、[エージェント]、[インストール]** の順にクリックし、Exchange サーバーにエージェントをインストールします。参照してください [DPM 保護エージェントをインストール](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) の詳細な手順を実行します。

## Exchange サーバーの保護グループを作成する

1. DPM 管理者コンソールで **[保護]** をクリックし、次にツール リボンの **[新規]** をクリックして **[新しい保護グループの作成]** ウィザードを開きます。

2. ウィザードの **[ようこそ]** 画面で **[次へ]** をクリックします。

3. **[保護グループの種類の選択]** 画面で、**[サーバー]** を選択し、**[次へ]** をクリックします。

4. 保護する Exchange サーバー データベースを選択し、**[次へ]** をクリックします。
    >[AZURE.NOTE] Exchange 2013 を保護する場合、 [Exchange 2013 の前提条件](https://technet.microsoft.com/library/dn751029.aspx)します。

    次の例では、Exchange 2010 データベースが選択されています。

    ![グループ メンバーの選択](./media/backup-azure-backup-exchange-server/select-group-members.png)

5. データ保護方法を選択します。

    保護グループに名前を付けて、次のオプションの両方を選択します。

    - ディスクを使用した短期的な保護を利用する
    - オンライン保護を利用する

6. **[次へ]** をクリックします。

7. Exchange Server データベースの整合性を確認する場合は、**[Eseutil を実行してデータの整合性をチェックする]** オプションを選択します。

    このオプションを選択すると、Exchange サーバー上で **eseutil** コマンドを実行したときに I/O トラフィックが生成されるのを防ぐために、DPM サーバー上でバックアップの整合性チェックが実行されます。
    >[AZURE.NOTE] このオプションを使用するには、DPM サーバー上の C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin ディレクトリに Ese.dll ファイルと Eseutil.exe ファイルをコピーする必要があります。 それ以外の場合、次のエラーがトリガーされます。  
    ![eseutil エラー](./media/backup-azure-backup-exchange-server/eseutil-error.png)

8. **[次へ]** をクリックします。

9. **[コピー バックアップ]** 用のデータベースを選択し、**[次へ]** をクリックします。
    >[AZURE.NOTE] データベースの少なくとも 1 つの DAG コピーに対して “完全バックアップ” が選択されていない場合、ログは切り捨てられません。

10. **[短期的なバックアップ]** の目標を構成し、**[次へ]** をクリックします。

11. 使用可能なディスク領域を確認し、**[次へ]** をクリックします。

12. DPM サーバーで初期レプリケーションを作成する時刻を選択し、**[次へ]** をクリックします。

13. 整合性チェック オプションを選択し、**[次へ]** をクリックします。

14. Azure にバックアップするデータベースを選択し、**[次へ]** をクリックします。 次に例を示します。

    ![オンライン保護データの指定](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)

15. **Azure Backup** のスケジュールを定義し、**[次へ]** をクリックします。 次に例を示します。

    ![オンライン バックアップ スケジュールの指定](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)
    >[AZURE.NOTE] オンライン回復ポイントは高速完全回復ポイントに基づいています。 そのため、オンライン回復ポイントは、高速完全回復ポイントに指定した時刻より後にスケジュールする必要があります。

16. **Azure Backup** の保持ポリシーを構成し、**[次へ]** をクリックします。

17. オンライン レプリケーション オプションを選択し、**[次へ]** をクリックします。

    データベースの規模が大きい場合は、ネットワーク経由で最初のバックアップを作成するのに時間がかかる可能性があります。 この問題を回避するには、オフライン バックアップを作成します。

    ![オンライン保持ポリシーの指定](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)

18. 設定を確認し、**[グループの作成]** をクリックします。

19. **[閉じる]** をクリックします。

## Exchange データベースを回復する

1. Exchange データベースを回復するには、DPM 管理者コンソールで **[回復]** をクリックします。

2. 回復する Exchange データベースを特定します。

3. *[回復時刻]* ドロップダウン リストからオンライン回復ポイントを選択します。

4. **[回復]** をクリックして、**回復ウィザード**を開始します。

オンライン回復ポイントでは、5 種類の回復があります。

- **元の Exchange Server の場所に回復する:** データは元の Exchange サーバーに回復します。
- **Exchange Server 上の別のデータベースに回復する:** データは別の Exchange サーバー上の別のデータベースに回復します。
- **回復用データベースに回復する:** データは Exchange 回復データベース (RDB) に回復します。
- **ネットワーク フォルダーにコピーする:** データはネットワーク フォルダーに回復します。
- **テープにコピーする:** テープ ライブラリまたはスタンドアロンのテープ ドライブが DPM サーバーに接続され、そこで構成されている場合、回復ポイントは空きテープにコピーされます。

    ![オンライン レプリケーションの選択](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## 次のステップ

- [Azure Backup の FAQ](backup-azure-backup-faq.md)





