<properties
    pageTitle="Site Recovery を利用し、オンプレミス Hyper-V 仮想マシンと Azure (VMM なし) の間で複製する | Microsoft Azure"
    description="Azure Site Recovery は、オンプレミスの Hyper-V サーバーに配置された仮想マシンの Azure へのレプリケーション、フェールオーバー、および復旧を調整します。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="12/10/2015"
    ms.author="raynew"/>



# Azure Site Recovery を利用し、オンプレミス Hyper-V 仮想マシンと Azure (VMM なし) の間で複製する

Azure Site Recovery は、レプリケーション、フェールオーバー、および仮想マシンとさまざまな展開シナリオでの物理サーバーの回復を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。 [詳細](site-recovery-overview.md) Site Recovery に関するします。

## 概要

この記事では、Windows Server 2012 R2 を実行する Hyper-V ホストが System Center Virtual Machine Manager (VMM) クラウドで管理されていないときに、Site Recovery をデプロイし、Hyper-V 仮想マシンを複製する方法について説明します。

この記事は、デプロイの前提条件をまとめたものです。レプリケーションの設定を構成して仮想マシンの保護を有効にするのに役立ちます。 すべてが正しく動作していることを確認するために、最後にフェールオーバーをテストします。 問題が生じた場合に質問を投稿、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)します。


## 開始する前に

開始する前に、すべての準備が整っていることを確認してください。

### Azure の前提条件

- 必要があります、 [Microsoft Azure](http://azure.microsoft.com/) アカウントです。 まず、 [無料評価版](pricing/free-trial/)します。
- レプリケートしたデータを格納するには Azure ストレージ アカウントが必要になります。 アカウントでは geo レプリケーションを有効にする必要があります。 アカウントは Azure Site Recovery コンテナーと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。 [の詳細については、Azure ストレージ](../storage/storage-introduction.md)します。
- プライマリ サイトからフェールオーバーするとき、Azure 仮想マシンがネットワークに接続されるように、Azure 仮想ネットワークが必要になります。

### Hyper-V の前提条件

- ソースのオンプレミス サイトには、Hyper-V の役割がインストールされ、Windows Server 2012 R2 を実行している少なくとも 1 つのサーバーが必要になります。 このサーバーの要件:
- 1 つまたは複数の仮想マシンが含まれています。
- 直接、またはプロキシを経由して、インターネットに接続します。
- サポート技術情報で説明されている修正プログラムを実行している [2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977")します。

### 仮想マシンの前提条件

保護する仮想マシンに準拠する必要があります [仮想マシンの前提条件](site-recovery-best-practices.md#virtual-machines)します。

### プロバイダーとエージェントの前提条件

Azure Site Recovery のデプロイの一部として、各 Hyper-V サーバーで Azure Site Recovery プロバイダーと Azure Recovery Services エージェントをインストールします。 以下の点に注意してください。

- 常に最新バージョンのプロバイダーとエージェントを実行することが推奨されます。 これらは Site Recovery ポータルで入手できます。
- コンテナー内のすべての Hyper-V サーバーで、プロバイダーとエージェントが同じバージョンである必要があります。
- サーバーで実行されているプロバイダーは、インターネットで Site Recovery に接続します。 このことは、プロキシを使用せずに、現在 Hyper-V サーバーで構成されているプロキシ設定を使用して、またはプロバイダーのインストール中に構成するカスタム プロキシ設定を使用して行えます。 使用するプロキシ サーバーがこれらの URL にアクセスし、Azure に接続できることを確認する必要があります。
    - *.hypervrecoverymanager.windowsazure.com
    - *.accesscontrol.windows.net
    - *.backup.windowsazure.com
    - *.blob.core.windows.net
    - *.store.core.windows.net

- さらに記載されている IP アドレスを許可する [Azure Datacenter の IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=41653) と HTTPS (443) プロトコルです。 使用を計画している Azure リージョンの IP の範囲と米国西部の IP の範囲をホワイトリストに登録する必要があります。


次の図は、Site Recovery でオーケストレーションやレプリケーションに使用される、さまざまな通信チャネルと通信ポートを示しています。

![B2A Topology](./media/site-recovery-hyper-v-site-to-azure/B2ATopology.png)


## ステップ 1: コンテナーの作成

1. サインイン、 [管理ポータル](https://portal.azure.com)します。

2. **[Data Services]**、**[Recovery Services]** の順に展開し、**[Site Recovery コンテナー]** をクリックします。

3. **[新規作成]**、**[簡易作成]** の順にクリックします。

4. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。

5. **[リージョン]** ボックスで、コンテナーのリージョンを選択します。 確認するには、サポートされているリージョンで利用可能な地域を参照してください。 [Azure Site Recovery の料金詳細](pricing/details/site-recovery/)します。

6. **[コンテナーの作成]** をクリックします。

    ![新しいコンテナー](./media/site-recovery-hyper-v-site-to-azure/SR_HvVault.png)

ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。 メイン [Recovery Services] ページで、コンテナーは **[アクティブ]** と表示されています。


## ステップ 2: HYPER-V サイトを作成します。

1. [Recovery Services] ページで、コンテナーをクリックして [クイック スタート] ページを開きます。 [クイック スタート] は、アイコンを使っていつでも開くことができます。

    ![クイック スタート](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_QuickStartIcon.png)

2. ドロップダウン リストで、**[オンプレミスの Hyper-V サイトと Microsoft Azure 間]** を選択します。

    ![Hyper-V のサイトのシナリオ](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectScenario.png)

3. **[Hyper-V サイトの作成]** で、**[Hyper-V サイトの作成]** をクリックします。 サイトの名前を指定して、保存します。

    ![Hyper-V サイト](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateSite2.png)


## ステップ 3: プロバイダーとエージェントのインストール

保護する VM のある各 Hyper-V サーバーにプロバイダーとエージェントをインストールします。

Hyper-V クラスターをインストールする場合は、フェールオーバー クラスター内の各ノードでステップ 5 ～ 11 を実行します。 すべてのノードが登録されて、保護が有効になると、クラスター内のノード間で移行する場合でも仮想マシンは保護されます。

1. **[Hyper-V サーバーの準備]** で、**[登録キーのダウンロード]** ファイルをクリックします。
2. **[登録キーのダウンロード]** ページで、サイトの横にある **[ダウンロード]** をクリックします。 Hyper-V サーバーが簡単にアクセスできる安全な場所に、キーをダウンロードします。 キーは生成後 5 日間有効です。

    ![登録キー](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_DownloadKey2.png)

4. **[プロバイダーのダウンロード]** をクリックして最新のバージョンを取得します。
5. コンテナーに登録する各 Hyper-V サーバーで、ファイルを実行します。 このファイルにより、2 つのコンポーネントがインストールされます。
    - **Azure Site Recovery プロバイダー** - Hyper-V サーバーと Azure Site Recovery ポータルとの間で、通信と調整を処理します。
    - **Azure Recovery Services エージェント** - ソースの Hyper-V サーバーで稼働する仮想マシンと Azure ストレージの間のデータ転送を処理します。
6. **[Microsoft Update]** で、アップデートの内容を設定できます。 この設定を有効にすることで、Microsoft Update のポリシーに従って、プロバイダーとエージェントのアップデートがインストールされます。

    ![Microsoft 更新プログラム](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider1.png)

7. **[インストール]** で、プロバイダーとエージェントを Hyper-V サーバーにインストールする場所を指定します。

    ![インストール場所](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider2.png)

8. インストール後は、セットアップを続行し、サーバーをコンテナーに登録します。

    ![インストールの完了](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider3.png)

9. **[インターネット接続]** ページで、プロバイダーが Azure Site Recovery に接続する方法を指定します。 **[既定のシステム プロキシ設定を使用]** を選択して、サーバー上に構成されている既定のインターネット接続設定を使用します。 値を指定しない場合は、既定の設定が使用されます。

    ![インターネット設定](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider4.png)

9. **[コンテナーの設定]** ページで **[参照]** をクリックし、キー ファイルを選択します。 Azure Site Recovery のサブスクリプション、コンテナー名、Hyper-V サーバーが属している Hyper-V サイトを指定します。

    ![サーバー登録](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectKey.png)

11. サーバーのコンテナーへの登録が開始されます。

    ![サーバー登録](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider6.png)

11. 登録が完了すると、Azure Site Recovery により Hyper-V サーバーからメタデータが取得され、コンテナーの **[サーバー]** ページにある **[Hyper-V サイト]** タブにサーバーが表示されます。

    ![サーバー登録](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider7.png)


### コマンドラインからプロバイダーをインストールする

別の方法としては、コマンドラインから Azure Site Recovery プロバイダーをインストールできます。 Windows 2012 R2 Server Core を実行しているコンピューターにプロバイダーをインストールする場合、この方法を使用する必要があります。 コマンド ラインから次のように実行します。

1. プロバイダーのインストール ファイルと登録キーをフォルダーにダウンロードします。 たとえば、C:\ASR です。
2. 管理者としてコマンド プロンプトを開き、次のように入力します。

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

3. 次のコマンドを実行し、プロバイダーをインストールします。

        C:\ASR> setupdr.exe /i

4. 次のコマンドを実行し、登録を完了します。

        CD C:\Program Files\Microsoft Azure Site Recovery Provider
        C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         


パラメーターは次のとおりです。

- **/Credentials**: ダウンロードした登録キーの場所を指定します。
- **/FriendlyName**: Hyper-V ホスト サーバーを識別する名前を指定します。 この名前はポータルに表示されます。
- **/EncryptionEnabled**: 省略可能です。 Azure でレプリカ仮想マシンを暗号化するかどうかを指定します (保存時の暗号化)。
- **/proxyAddress**; **/proxyport**; **/proxyUsername**; **/proxyPassword**: 省略可能です。 カスタム プロキシを使用する場合、あるいは既存のプロキシで認証が必要な場合、プロキシ パラメーターを指定します。


## ステップ 4: Azure のストレージ アカウントを作成する

1. **リソースの準備** 選択 **ストレージ アカウントの作成**  お持ちでない場合は、Azure ストレージ アカウントを作成します。 アカウントでは geo レプリケーションを有効にする必要があります。 アカウントは Azure Site Recovery コンテナーと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。

    ![ストレージ アカウントの作成](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateResources1.png)

## ステップ 5: 保護グループの作成と構成

保護グループは、同じ保護設定を使用して保護する仮想マシンの論理グループです。 保護設定を保護グループに適用すると、これらの設定はグループに追加するすべての仮想マシンに適用されます。

1. **[保護グループの作成と構成]** で、**[保護グループの作成]** をクリックします。 前提条件が満たされていない場合は、メッセージが発行され、**[詳細の表示]** をクリックすると、詳細情報が表示されます。

2. **[保護グループ]** タブで、保護グループを追加します。 名前、ソースの Hyper-V サイト、ターゲットの **Azure**、Azure Site Recovery のサブスクリプション名、Azure ストレージ アカウントを指定します。

    ![保護グループ](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroupCreate3.png)

2. **[レプリケーション設定]** で **[コピーの頻度]** を設定し、ソースとターゲット間でデータの差分を同期する頻度を指定します。 30 秒、5 分間、または 15 分に設定できます。
3. **[保持する復旧ポイントの保持]** で、何時間分の復旧履歴を格納するかを指定します。
4. **[アプリケーションの整合性スナップショットの頻度]** で、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットの取得時にアプリケーションの整合性を保証するスナップショットを取得するかどうかを指定できます。 既定ではこれらは取得されません。 この値は、構成する追加の復旧ポイント数よりも少ない値にしてください。 これは、仮想マシンが、Windows オペレーティング システムを実行している場合にのみサポートされます。
5. **[初期レプリケーションの開始時刻]** に、保護グループ内の仮想マシンの初期レプリケーションを Azure に送信する時刻を指定します。

    ![保護グループ](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroup4.png)


## ステップ 6: 仮想マシンの保護の有効化

仮想マシンを保護グループに追加し、保護を有効にします。
>[AZURE.NOTE] 静的 IP アドレスを持ち、Linux を実行している VM は保護できません。 

1. **マシン** 保護グループのタブをクリックして** 仮想マシンの保護を有効にする保護グループへ追加** します。
2. **[仮想マシンの保護の有効化]** ページで、保護する仮想マシンを選択します。

    ![仮想マシンの保護の有効化](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_AddVM3.png)

    保護の有効化ジョブが開始されます。 ジョブの進捗状況は **[ジョブ]** タブで追跡できます。 保護の最終処理のジョブが実行されると、仮想マシンは、フェールオーバーを実行できる状態になります。
3. 保護の設定後、次のことを行えます。

 - **[保護された項目]** > **[保護グループ]** > *保護グループ名* > **[Virtual Machines]** の順に移動し、仮想マシンを表示します。**プロパティ** タブでマシンの詳細をドリルダウンできます。
 - **[保護された項目]** > **[保護グループ]** > *保護グループ名* > **[Virtual Machines]** *仮想マシン名* > **[構成]** の順に移動し、仮想マシンのフェールオーバーのプロパティを構成します。 利用できる構成は、次のとおりです。
     - **名前**: Azure の仮想マシンの名前。
     - **サイズ**: フェールオーバーする仮想マシンのターゲット サイズ。

     ![仮想マシンのプロパティの構成](./media/site-recovery-hyper-v-site-to-azure/VMProperties.png)
 - *[保護された項目]* > **[保護グループ]** > *保護グループ名* > **[Virtual Machines]** *仮想マシン名* > **[構成]** の順に移動し、次に示す追加の仮想マシンの設定を構成します。

   - **ネットワーク アダプター**: ネットワーク アダプターの数は、ターゲット仮想マシンに指定したサイズによって異なります。 確認 [仮想マシン サイズの仕様](../virtual-machines/virtual-machines-size-specs.md#size-tables) の仮想マシンのサイズでサポートされている nic の数。

     When you modify the size for a virtual machine and save the settings, the number of network adapter will change when you open **Configure** page the next time. The number of network adapters of target virtual machines is minimum of the number of network adapters on source virtual machine and maximum number of network adapters supported by the size of the virtual machine chosen. It is explained below:
    
    
     - If the number of network adapters on the source machine is less than or equal to the number of adapters allowed for the target machine size, then the target will have the same number of adapters as the source.
     - If the number of adapters for the source virtual machine exceeds the number allowed for the target size then the target size maximum will be used.
     - For example if a source machine has two network adapters and the target machine size supports four, the target machine will have two adapters. If the source machine has two adapters but the supported target size only supports one then the target machine will have only one adapter.     

   - **Azure ネットワーク**: 仮想マシンのフェールオーバー先のネットワークを指定します。 仮想マシンに複数のネットワーク アダプターがある場合は、すべてのアダプターが、同じ Azure ネットワークに接続する必要があります。
   - **サブネット**: 仮想マシン上の各ネットワーク アダプターに対して、フェールオーバー後にマシンが接続する Azure ネットワーク内のサブネットを選択します。
   - **ターゲット IP アドレス**: 静的 IP アドレスを使用するようソース仮想マシンのネットワーク アダプターが構成されている場合、フェールオーバー後にターゲット仮想マシンの IP アドレスが同じになるよう、ターゲット仮想マシンの IP アドレスを指定できます。 IP アドレスを指定しない場合、フェールオーバー時に、使用可能なアドレスが割り当てられます。 使用中のアドレスを指定すると、フェールオーバーは失敗します。

   ![仮想マシンのプロパティの構成](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_VMMultipleNic.png)




## ステップ 7: 復旧計画の作成

デプロイをテストするために、単一の仮想マシンに対して、または 1 つ以上の仮想マシンを含む復旧計画に対してテスト フェールオーバーを実行できます。 [詳細](site-recovery-create-recovery-plans.md) に関する復旧計画を作成します。

## ステップ 8: デプロイのテスト

Azure へのテスト フェールオーバーを実行する方法は 2 つあります。

- **Azure ネットワークを使用しないテスト フェールオーバー** — この種のテスト フェールオーバーでは、仮想マシンが Azure で正しく動作することを確認します。 フェールオーバー後、仮想マシンはどの Azure ネットワークにも接続しません。
- **Azure ネットワークを使用するテスト フェールオーバー** — この種のフェールオーバーでは、レプリケーション環境全体が正しく動作することと、フェールオーバーされた仮想マシンが指定したターゲットの Azure ネットワークに接続されることを確認します。 テスト フェールオーバーでは、テストの仮想マシンのサブネットはレプリカの仮想マシンのサブネットに基づいて決定されることに注意してください。 これは、レプリカの仮想マシンのサブネットが、ソースの仮想マシンのサブネットに基づいている通常のレプリケーションとは異なります。

Azure ネットワークを指定せずにテスト フェールオーバーを実行する場合、何も用意する必要がありません。

ターゲット Azure ネットワークを指定してテスト フェールオーバーを実行するには、Azure 運用ネットワークから独立した新しい Azure ネットワークを作成する必要があります (Azure で新しいネットワークを作成する場合の既定の動作)。 読み取り [テスト フェールオーバーを実行](site-recovery-failover.md#run-a-test-failover) 詳細です。


レプリケーションとネットワークのデプロイを完全にテストするには、複製された仮想マシンが予想どおり機能するようにインフラストラクチャを設定する必要があります。 次のように DNS とドメイン コント ローラーとして仮想マシンを設定して、テスト フェールオーバーを実行して、テスト用ネットワークで作成する [Site Recovery を使用して Azure にレプリケートする方法の 1 つです。 [に関する詳細は、慎重](site-recovery-active-directory.md#considerations-for-test-failover) Active Directory のフェールオーバーに関する考慮事項をテストします。

次のようにテスト フェールオーバーを実行します。

1. **[復旧計画]** タブで、計画を選択し、**[テスト フェールオーバー]** をクリックします。
2. **[テスト フェールオーバーの確認]** ページで、**[なし]** または特定の Azure ネットワークを選択します。 **[なし]** を選択した場合、テスト フェールオーバーでは、仮想マシンが Azure に正しくレプリケートされたかどうかは確認されますが、レプリケーションのネットワーク構成は確認されないことに注意してください。

    ![テスト フェールオーバー](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_TestFailoverNoNetwork.png)

3. **[ジョブ]** タブで、フェールオーバーの進行状況を追跡できます。 仮想マシンのテスト レプリカも Azure ポータルで確認できます。 オンプレミスのネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。
4. フェールオーバーが **[テストの完了]** フェーズに達したら、**[テストの完了]** をクリックして、テスト フェールオーバーを終了します。 **[ジョブ]** タブまでドリルダウンし、フェールオーバーの進行状況と状態を追跡して、必要な操作を実行します。
5. フェールオーバー後に、Azure ポータルで仮想マシンのテスト レプリカを表示することができます。 オンプレミスのネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。

    1. 仮想マシンが正常に起動することを確認します。
    2. フェールオーバー後に Azure でリモート デスクトップを使用して仮想マシンに接続する場合は、テストのフェールオーバーを実行する前に、仮想マシンのリモート デスクトップ接続を有効にします。 また、仮想マシンに RDP エンドポイントを追加する必要もあります。 利用できる、 [Azure Automation Runbook](site-recovery-runbook-automation.md) を行うには。
    3. フェールオーバー後、リモート デスクトップでパブリック IP アドレスを使用して Azure の仮想マシンに接続する場合、パブリック アドレスを使用して仮想マシンに接続することを妨げるドメイン ポリシーがないことを確認します。

6. テストが完了したら、以下の手順を実行します。

    - **[テスト フェールオーバーが完了しました]** をクリックします。 テスト環境をクリーンアップして、自動的に電源をオフにし、テスト仮想マシンを削除します。
    - **[メモ]** をクリックして、テスト フェールオーバーに関連する監察結果をすべて記録し、保存します。
7. フェールオーバーが **[テストの完了]** フェーズに達したら、次の手順に従って検証を行います。
    1. Azure ポータルで、レプリカ仮想マシンを表示します。 仮想マシンが正常に起動することを確認します。
    2. オンプレミスのネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。
    3. **[テストの完了]** をクリックして終了します。
    4. **[メモ]** をクリックして、テスト フェールオーバーに関連する監察結果をすべて記録し、保存します。
    5.  **[テスト フェールオーバーが完了しました]** をクリックします。 テスト環境をクリーンアップして、自動的に電源をオフにし、テスト仮想マシンを削除します。

## 次のステップ

稼働している、デプロイを設定した後 [詳細](site-recovery-failover.md) のフェールオーバーについてです。





