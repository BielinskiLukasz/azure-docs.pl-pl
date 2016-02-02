<properties
    pageTitle ="仮想マシンをバックアップから復元 |Microsoft Azure]
    description =「Azure の仮想マシンを復元する方法について説明します」
    サービス =「バックアップ」
    documentationCenter=""
    authors ="trinadhk"
    manager ="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload=「記憶域のバックアップ回復」
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/29/2015"
    ms.author="trinadhk"です。"jimpark"/>

# Azure での仮想マシンの復元

復元操作を使用して、Azure バックアップ資格情報コンテナーに格納されているバックアップから新しい VM に仮想マシンを復元できます。

## ワークフローの復元

### 1.復元する項目を選択する

1. **[保護された項目]** タブに移動し、新しい VM に復元する仮想マシンを選択します。

    ![保護された項目](./media/backup-azure-restore-vms/protected-items.png)

    **[保護された項目]** ページの **[回復ポイント]** 列には、仮想マシンの回復ポイント数が表示されます。 **[最新の回復ポイント]** 列には、仮想マシンを復元できる最新のバックアップ時刻が表示されます。

2. **[復元]** をクリックすると **[復元するアイテム]** ウィザードが開きます。

    ![項目を復元する](./media/backup-azure-restore-vms/restore-item.png)

### 2.回復ポイントを選択する

1. **[回復ポイントの選択]** 画面で、最新の回復ポイントまたは以前の特定の時点から復元できます。 ウィザードが開いたときに選択されている既定のオプションは、*[最新の復旧ポイント]* です。

    ![回復ポイントを選択する](./media/backup-azure-restore-vms/select-recovery-point.png)

2. 以前の特定の時点を選択するには、ドロップダウン リストで **[日付の選択]** オプションを選択し、**カレンダー アイコン**をクリックしてカレンダー コントロールから日付を選択します。 コントロール内の回復ポイントが設定されているすべての日付は、淡い灰色の網掛けで表示され、選択可能です。

    ![日付を選択する](./media/backup-azure-restore-vms/select-date.png)

    カレンダー コントロールの日付をクリックすると、その日付の使用可能な回復ポイントが下の回復ポイント テーブルに表示されます。 **[時間]** 列は、スナップショットが作成された日時を示します。  **型** 列が表示されます、 [整合性](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) 、回復ポイントのです。 テーブル ヘッダーのかっこ内は、その日に使用可能な回復ポイントの数を示します。

    ![回復ポイント](./media/backup-azure-restore-vms/recovery-points.png)

3. **[回復ポイント]** テーブルから回復ポイントを選択し、次へ進む矢印をクリックして次の画面に移動します。

### 3.コピー先の場所を指定する

1. **[Select restore instance]** 画面で、仮想マシンを復元する場所の詳細を指定します。

  - 仮想マシン名を指定する: 特定のクラウド サービスでは、仮想マシンの名前を一意にする必要があります。 既存の仮想マシンを同じ名前に置き換える場合は、まず既存の仮想マシンとデータ ディスクを削除し、次に Azure Backup からデータを復元します。
  - 仮想マシンのクラウド サービスを選択する: これは仮想マシンを作成するために必須です。 既存のクラウド サービスを使用するか、新しいクラウド サービスを作成するかを選択できます。

        どのようなクラウド サービス名の選択は、グローバル一意識別する必要があります。 通常、クラウド サービス名が [cloudservice] の形式で公開された URL に関連付けを取得します。 cloudapp.net します。 Azure では、名前は既に使用されている場合は、新しいクラウド サービスを作成することはできません。 選択を作成する場合は、新しいクラウド サービスの作成、関連するクラウド サービスに適用するのに十分な一意する必要があります、VM 名の選択の場合 – 仮想マシンと同じ名前が付けられます。

        私たちは、クラウド サービスおよび復元インスタンスの詳細でアフィニティ グループに関連付けられていない仮想ネットワークにのみ表示されます。 [Learn More](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

2. 仮想マシンのストレージ アカウントを選択する: これは仮想マシンを作成するために必須です。 Azure Backup 資格情報コンテナーと同じリージョン内の既存のストレージ アカウントから選択できます。 ゾーン冗長または Premium Storage タイプのストレージ アカウントはサポートされません。

    サポートされている構成のストレージ アカウントがない場合は、復元操作を開始する前に、サポートされている構成のストレージ アカウントを作成してください。

    ![仮想ネットワークを作成する](./media/backup-azure-restore-vms/restore-sa.png)

3. 仮想ネットワークを選択する: 仮想マシンの作成時に、仮想マシンの仮想ネットワーク (VNET) を選択する必要があります。 復元 UI は、使用できるこのサブスクリプション内の VNET をすべて表示します。 復元された仮想マシンの VNET を選択する必要はありません。VNET が適用されない場合でも、インターネット経由で復元された仮想マシンに接続できます。

    選択したクラウド サービスが仮想ネットワークに関連付けられている場合は、仮想ネットワークを変更できません。

    ![仮想ネットワークを作成する](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. サブネットを選択する: VNET にサブネットがある場合、既定では最初のサブネットが選択されます。 ドロップダウン リストのオプションから、任意のサブネットを選択します。 サブネットの詳細については、Networks] 拡張機能を参照してください、 [ポータルのホーム ページ](https://manage.windowsazure.com/), に進み、 **仮想ネットワーク** サブネットの詳細を表示する構成にドリル ダウンし、仮想ネットワークを選択します。

    ![サブネットを選択する](./media/backup-azure-restore-vms/select-subnet.png)

5. ウィザードの **[送信]** アイコンをクリックして詳細情報を送信し、復元ジョブを作成します。

## 復元操作を追跡する

復元ウィザードにすべての情報を入力して送信すると、Azure Backup は復元操作を追跡するジョブの作成を試みます。

![復元ジョブの作成](./media/backup-azure-restore-vms/create-restore-job.png)

ジョブの作成に成功すると、ジョブが作成されたことを示すトースト通知が表示されます。 **[ジョブの表示]** ボタンをクリックすると、**[ジョブ]** タブに詳細情報が表示されます。

![作成された復元ジョブ](./media/backup-azure-restore-vms/restore-job-created.png)

復元操作が完了すると、**[ジョブ]** タブに完了マークが付けられます。

![復元ジョブの完了](./media/backup-azure-restore-vms/restore-job-complete.png)

仮想マシンを復元した後は、元の VM 上の既存の拡張機能を再インストールする必要があり、 [エンドポイントを変更](virtual-machines-set-up-endpoints) Azure ポータルでバーチャル マシンのです。

## ドメイン コントローラー の VM の復元

ドメイン コントローラー (DC) の仮想マシンのバックアップは、Azure Backup でサポートされているシナリオです。 ただし、この復元プロセスではいくつか注意が必要です。 復元操作は、シングル DC 構成でのドメイン コントローラーの VM と、マルチ DC 構成でのドメイン コントローラーの VM とで大きく異なります。

### シングル DC

VM は、(他の VM と同様に) Azure ポータルから復元するか、または PowerShell を使用して復元することができます。

### マルチ DC

マルチ DC 環境の場合、ドメイン コントローラーによってデータの同期方法が異なります。 *適切な対策を講じずに*以前のバックアップ ポイントを復元すると、USN ロールバック プロセスによってマルチ DC 環境でエラーが生じるおそれがあります。 このような VM を復元する適切な方法は、DSRM モードでの起動です。

ここで、Azure に DSRM モードがないことが問題となります。 したがって、このような VM を復元する場合に Azure ポータルは使用できません。 サポートされている復元メカニズムは、PowerShell を使用したディスクベースの復元のみです。
>[AZURE.WARNING] マルチ DC 環境のドメイン コントローラーの VM を復元する場合、Azure ポータルは使用しないでください。 PowerShell ベースの復元のみがサポートされています。

詳細について、 [USN ロールバックの問題](https://technet.microsoft.com/library/dd363553) および修正するための提案戦略です。

## 特別なネットワーク構成を持つ VM の復元

Azure Backup は、次のように特殊なネットワーク構成の仮想マシンのバックアップをサポートしています。

- ロード バランサー (内部および外部) の対象になっている VM
- 予約済み IP が複数ある VM
- NIC が複数ある VM

このような構成の場合、復元時に次の点を考慮する必要があります。
>[AZURE.TIP] 復元後に特殊なネットワーク構成の VM を再作成するには、PowerShell ベースの復元フローを使用してください。 

### UI からの復元:

UI から復元する場合は、**常に新しいクラウド サービスを選択してください**。 ポータルの復元フローでは、必須のパラメーターのみが使用されるので、UI を使用して VM を復元した場合、元の特殊なネットワーク構成は失われます。 つまり、復元される VM は、ロード バランサー、複数の NIC、複数の予約済み IP などの構成がない、通常の VM になります。

### PowerShell からの復元:

PowerShell には仮想マシンを作成する機能はなく、バックアップから VM ディスクを復元する機能しかありません。 そのため、前述したような特殊なネットワーク構成が必要な仮想マシンを復元するときに役立ちます。

ディスクの復元後に仮想マシンを完全に再作成するには、次の手順を実行します。

1. ディスクをを使用してバックアップ コンテナーから復元 [Azure バックアップ PowerShell](../backup-azure-vms-automation.md#restore-an-azure-vm)

2. PowerShell コマンドレットを使用して、ロード バランサー、複数の NIC、複数の予約済み IP に必要な VM 構成を作成し、その構成を使用して、目的の構成の VM を作成します。
    - クラウド サービス内でVM の作成 [内部ロード バランサー ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
    - [インターネットに接続するロード バランサー] に接続する仮想マシンの作成 (https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted)
    - VM を作成する [複数 Nic](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics)
    - VM を作成する [複数の ip アドレスを予約する](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)


## 次のステップ

- [エラーのトラブルシューティング](backup-azure-vms-troubleshoot.md#restore)
- [仮想マシンを管理します。](backup-azure-manage-vms.md)





