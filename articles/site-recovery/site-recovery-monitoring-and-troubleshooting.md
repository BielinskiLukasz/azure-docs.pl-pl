<properties
    pageTitle="仮想マシンおよび物理サーバーの保護の監視とトラブルシューティング | Microsoft Auzre" 
    description="Azure Site Recovery は、オンプレミスのサーバーに配置されている仮想マシンの Azure またはセカンダリ データセンターへのレプリケーション、フェールオーバー、および復旧を調整します。監視し、VMM または HYPER-V サイト保護のトラブルシューティングを行うには、この記事を使用します。" 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="12/14/2015" 
    ms.author="anbacker"/>


# 仮想マシンおよび物理サーバーの保護の監視とトラブルシューティング

この監視とトラブルシューティング ガイドでは、Azure Site Recovery のレプリケーション正常性を追跡する方法とトラブルシューティング手法について説明します。

## コンポーネントについて

### オンプレミスと Azure の間のレプリケーションのための VMware/物理サイトのデプロイ。

オンプレミスの VMware/物理マシン間に DR をセットアップするには、構成サーバー、マスター ターゲット、およびプロセス サーバーを構成する必要があります。 Azure Site Recovery では、ソース サーバーに対する保護を有効にしながら、モビリティ サービスがインストールされます。 ソース サーバーが Azure にフェールオーバーされたら、オンプレミスの障害をポストします。顧客はソース サーバーを保護しながらオンプレミスに戻して再構築できるように、Azure でプロセス サーバーをセットアップし、オンプレミスでマスター ターゲット サーバーをセットアップする必要があります。

![オンプレミスと Azure の間のレプリケーションのための VMware/物理サイトのデプロイ](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### オンプレミス サイト間のレプリケーションのための VMM サイトのデプロイ

2 つのオンプレミス サイト間で DR を設定するとき、Azure Site Recovery プロバイダーをダウンロードし、VMM サーバーにインストールする必要があります。 Azure ポータルからトリガーされるすべての操作が保護の有効化やフェールオーバーの一環としてのプライマリ側の仮想マシンのシャットダウンなどのオンプレミス操作に変換されるように、プロバイダーはインターネット接続を必要とします。

![オンプレミス サイト間のレプリケーションのための VMM サイトのデプロイ](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### オンプレミスと Azure の間のレプリケーションのための VMM サイトのデプロイ

オンプレミスと Azure の間で DR を設定するとき、Azure Site Recovery プロバイダーをダウンロードして VMM サーバーにインストールし、Azure Recovery Services エージェントを各 Hyper-V ホストにインストールする必要があります。参照 [Azure への保護についてサイト](./site-recovery-understanding-site-to-azure-protection.md) の詳細。

![オンプレミスと Azure の間のレプリケーションのための VMM サイトのデプロイ](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### オンプレミスと Azure の間のレプリケーションのための Hyper-V サイトのデプロイ

これは VMM のデプロイの場合と同じです。プロバイダーとエージェントが Hyper-V ホスト自体にインストールされるという点のみが異なります。参照 [Azure への保護についてサイト](./site-recovery-understanding-site-to-azure-protection.md) の詳細。

## 構成、保護、復旧操作の監視

ASR のすべての操作が「ジョブ」タブの下で監査され、追跡されます。 構成、保護、復旧にエラーが発生した場合、[ジョブ] タブに移動し、障害がないかどうかを確認します。

![構成、保護、復旧操作の監視](media/site-recovery-monitoring-and-troubleshooting/image3.png)

[ジョブ] ビューの下で障害が見つかったら、そのジョブを選択し、そのジョブの [エラー詳細] をクリックします。

![構成、保護、復旧操作の監視](media/site-recovery-monitoring-and-troubleshooting/image4.png)

エラー詳細は問題の考えられる原因と推奨措置を特定するために役立ちます。

![構成、保護、復旧操作の監視](media/site-recovery-monitoring-and-troubleshooting/image5.png)

上の例では、別の操作が進行中になっているようです。その保護構成にエラーがあるためです。 推奨措置に従って問題を解決します。その後、[再起動] をクリックし、操作を再開します。

![構成、保護、復旧操作の監視](media/site-recovery-monitoring-and-troubleshooting/image6.png)

一部の操作では再起動オプションを利用できません。その場合、オブジェクトに戻り、操作をやり直してください。 進行中のジョブは [キャンセル] ボタンでいつでも取り消すことができます。

![構成、保護、復旧操作の監視](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## 仮想マシンのレプリケーション正常性の監視

ASR は Azure ポータルを利用し、それぞれの保護対象を集中/リモート管理します。保護対象アイテムに移動し、[VMM クラウド] または [保護グループ] を選択します。[VMM クラウド] タブは VMM 基準のデプロイ専用です。その他すべてのシナリオでは、[保護グループ] タブの下に保護対象エンティティがあります。

![仮想マシンのレプリケーション正常性の監視](media/site-recovery-monitoring-and-troubleshooting/image8.png)

次に、それぞれのクラウドまたは保護グループの下で保護対象エンティティを選択します。 保護対象エンティティを選択すると、下のウィンドウに許可される操作が表示されます。

![仮想マシンのレプリケーション正常性の監視](media/site-recovery-monitoring-and-troubleshooting/image9.png)

上の例では、仮想マシンの正常性が「重大」です。下の「エラー詳細」ボタンをクリックすると、エラーを確認できます。 "考えられる原因" に基づき、前述の "推奨" によって問題を解決します。

![仮想マシンのレプリケーション正常性の監視](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![仮想マシンのレプリケーション正常性の監視](media/site-recovery-monitoring-and-troubleshooting/image11.png)

注: アクティブな操作が進行中であるか、失敗した場合、先に述べたように [ジョブ] ビューに移動し、そのジョブ固有のエラーを確認します。

## オンプレミスの Hyper-V に関する問題のトラブルシューティング

内部設置型 HYPER-V manager コンソールに接続する仮想
マシンとレプリケーションの正常性を参照してください。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-monitoring-and-troubleshooting/image12.png)

ここで *レプリケーション ヘルス* が"重大"– として報告された * ビュー
レプリケーション ヘルス * 詳細を表示します。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-monitoring-and-troubleshooting/image13.png)

仮想マシンのレプリケーションが一時停止されている場合は、選択を右クリック *レプリケーション*]-> [*レプリケーションの再開*
![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-monitoring-and-troubleshooting/image19.png)

仮想マシンで新しい Hyper-V ホスト (クラスター内またはスタンドアロン マシン内に ASR を使用して構成したもの) を移行する場合、仮想マシンのレプリケーションが影響を受けることはありません。 新しい Hyper-V ホストが、すべての前提条件を満たし、ASR を使用して構成されていることを確認します。

### イベント ログ

| イベント ソース| 詳細|
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** (VMM サーバー)| さまざまな VMM 問題を解決するために役立つログ記録が利用できます。|
| **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Hyper-V ホスト)| これは、多くの Microsoft Azure Recovery Services エージェントに関する問題のトラブルシューティングの便利なログ記録を提供します。<br/> ![HYPER-V ホストのイベント ソース](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png)|
| **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** (Hyper-V ホスト)| これは、多くの Microsoft Azure Site Recovery サービスに関する問題のトラブルシューティングの便利なログ記録を提供します。<br/> ![HYPER-V ホストのイベント ソース](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png)|
| **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V ホスト)| これにより、多くの HYPER-V 仮想マシンの管理に関する問題のトラブルシューティングのログ記録を便利です。<br/> ![HYPER-V ホストのイベント ソース](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png)|


### Hyper-V レプリケーションのログ記録のオプション

HYPER-V レプリカに関連するすべてのイベントはの下にあるハイパー-V の VMMS\\Admin ログに記録 **アプリケーションとサービス Logs\\Microsoft\\Windows**します。 また、Hyper-V-VMMS の分析ログを有効にすることができます。 このログを有効にするには、最初にイベント ビューアーに 分析ログとデバッグ ログを表示する必要があります。 イベント ビューアーを開き、**[表示] メニュー**で、**[分析ログとデバッグ ログを表示する]** をクリックします。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-monitoring-and-troubleshooting/image14.png)

分析ログは Hyper-V-VMMS の下に表示されます。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-monitoring-and-troubleshooting/image15.png)

**[操作]** ウィンドウで **[ログを有効にする]** をクリックします。 一度有効になっていること
表示されます **パフォーマンス モニター** にあるイベント トレース セッションとして
**データ コレクター セット**。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-monitoring-and-troubleshooting/image16.png)

収集された情報を表示するには、最初にログを無効にしてトレース セッションを停止し、イベント ビューアーでログを保存し、再び開きます。あるいは、必要に応じて他のツールで変換します。



## Microsoft サポートを得る

### ログ コレクション

VMM サイト保護について、[ASR Log Collection サポートの使用方法を参照してください。
Diagnostics Platform (SDP)
Tool] (http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)
必要なログを収集します。

HYPER-V サイト保護、ダウンロード、
[ツール](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)
(& a)、ログを収集して HYPER-V で実行します。

Vmware/physical シナリオでは、[Azure Site Recovery Log Collection を参照してください。
VMware と物理サイトの
保護」(http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx)
必要なログを収集します。

ツールはローカルのログを収集します。これは **%LocalAppData%\ElevatedDiagnostics** にある無作為に名前が付けられたサブフォルダーにあります。

![Hyper-V サイト保護で表示される手順のサンプル。](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### サポート チケットを開く

ASR のサポート チケットを生成するにアクセスを使用して Azure をサポートします
この url は URL <http://aka.ms/getazuresupport>

## KB 記事

-   [フェイル オーバーまたは Azure に移行されている保護された仮想マシンのドライブ文字を保持する方法](http://support.microsoft.com/kb/3031135)
-   [オンプレミスと Azure 保護ネットワーク帯域幅の使用を管理する方法](https://support.microsoft.com/kb/3056159)
-   [ASR:「クラスター リソースが見つかりません」エラーをバーチャル マシンの保護を有効にしようとすると](http://support.microsoft.com/kb/3010979)
-   [理解し、HYPER-V レプリカの番組ガイドのトラブルシューティング](http://www.microsoft.com/en-in/download/details.aspx?id=29016)

## ASR の一般的なエラーとその解決策

遭遇する可能性のある一般的なエラーとその解決策を次に示します。 それぞれのエラーについては、個別の WIKI ページに記載されています。

### 全般

-   <span style="color:green;">新しい</span> [ジョブのエラーが発生した「操作が進行中です」。 エラー 505、514、532] (http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">新しい</span> [ジョブの「サーバーがインターネットに接続されていない」エラーで失敗します。 Error 25018] (http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### セットアップ

-   [内部エラーのため、VMM サーバーを登録することはできません。 エラーの詳細については、Site Recovery ポータルのジョブ ビューを参照してください。 サーバーを登録するには、もう一度セットアップを実行します。](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [HYPER-V Recovery Manager コンテナーには、接続を確立できません。 プロキシ設定を確認または後でもう一度実行してください。](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### 構成

-   [保護グループを作成できません: サーバーの一覧を取得中にエラーが発生しました。](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [HYPER-V ホスト クラスターには、少なくとも 1 つの静的なネットワーク アダプターが含まれています。 または、DHCP を使用する接続されたアダプターが構成されていません。](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM には、操作が完了するアクセス許可がありません。](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [保護を構成するときに、サブスクリプション内のストレージ アカウントを選択することはできません。](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### 保護

- <span style="color:green;">新しい</span> [保護の有効化「仮想マシンの保護を構成できませんでした」エラーで失敗します。 Error 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">新しい</span> [保護の有効化"保護は、仮想マシンの enabled ことができませんでした". エラーで失敗します。 Error 70094] (http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">新しい</span> [ライブ マイグレーション エラー 23848 - 仮想マシンは、Live の種類を使用して移動になります。 これを損なうことに、仮想マシンの回復保護状態です。](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
- [保護エージェントがホスト コンピューターにインストールされていないために失敗を有効にします。](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [コンピューティング リソースが少ないため、レプリカ仮想マシンに適したホストが見つからない](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [論理ネットワークに接続されていないため、レプリカ仮想マシンに適したホストが見つからない](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [接続できないレプリカのホスト コンピューターに接続を確立できません](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### 復旧

- VMM が、ホストの操作を完了できません。
    -   [仮想マシンの選択した回復ポイントにフェールオーバーする。 一般的なアクセス拒否エラー。](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [HYPER-V の仮想マシンの選択した回復ポイントにフェールオーバーできませんでした。 操作が中止された最新の復旧ポイントを実行してください。 (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   サーバーとの接続を確立できない (0x00002EFD)
        -   [Hyper-v で仮想マシンのレプリケーションの反転を有効にできませんでした。](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Hyper-v で仮想マシンのレプリケーションを有効にできませんでした。](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [仮想マシンに対するフェールオーバーをコミットできませんでした。](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [復旧計画には、計画フェールオーバーの準備ができていない仮想マシンが含まれています。](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [仮想マシンの計画されたフェールオーバーの準備ができていません。](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [仮想マシンが実行されていないと、オフ電源が入っていません。](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [仮想マシンとコミットのフェールオーバーに失敗しました [帯域外操作が発生しました](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   テスト フェールオーバー
    -   [テスト フェールオーバーが進行中であるために、フェールオーバーを開始できませんでした。](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)

### 構成サーバー、プロセス サーバー、マスター ターゲット

構成サーバー (CS)、プロセス サーバー (PS)、マスター ターゲット (MT)
-   [PS/CS が VM としてホスト ESXi ホストが死の紫色の画面で失敗します。](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### リモート デスクトップのフェールオーバー後のトラブルシューティング

-   多くのお客様が、Azure でフェールオーバー後の VM への接続について、問題に直面しています。 [トラブルシューティングのドキュメントを RDP を使用して VM に](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)





