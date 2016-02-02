<properties 
   pageTitle="StorSimple 仮想デバイス Update 2 | Microsoft Azure"
   description="Microsoft Azure 仮想ネットワークで StorSimple 仮想デバイスを作成、デプロイ、管理する方法を説明します。StorSimple Update 2 に適用されます。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/14/2015"
   ms.author="alkohli" />


# Azure での StorSimple 仮想デバイスのデプロイと管理 (Update 2)

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-virtual-device-u2.md)
- [Update 1](../articles/storsimple/storsimple-virtual-device-u1.md)
- [GA Release](../articles/storsimple/storsimple-virtual-device.md)


## 概要

StorSimple 仮想デバイスは、Microsoft Azure StorSimple ソリューションに付属する追加的な機能です。 StorSimple 仮想デバイスは Microsoft Azure の仮想ネットワーク内の仮想マシン上で動作します。この仮想マシンを使用して、ホストからデータをバックアップしたり、複製したりできます。



#### 仮想デバイス モデルの比較

StorSimple 仮想デバイスは、2 つのモデルで利用可能です。1 つは Standard 8010 で、もう 1 つは Premium 8020 です (Update 2 で導入)。 2 つのモデルの比較を、次の表に示します。


| デバイスのモデル| 8010<sup>1, 2</sup>| 8020<sup>3</sup>|
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **最大容量**| 30 TB| 64 TB|
| **Azure VM**| Standard_A3 (4 コア、7 GB メモリ)| Standard_DS3 (4 コア、14 GB メモリ)|
| **バージョン互換性**| Update 2 より前または Update 2 以降を実行しているバージョン| Update 2 以降を実行しているバージョン|
| **利用可能なリージョン**| すべての Azure リージョン| Premium Storage をサポートしている azure リージョン<br></br>現在 Premium Storage をサポートしているリージョンの一覧は、次を参照してください [地域別の Azure サービス](https://azure.microsoft.com/regions/#services)。|
| **ストレージの種類**| Azure Standard Storage を使用して<br></br> 学習方法 [Standard Storage アカウントを作成する]()| Azure Premium Storage を使用して<br></br>学習方法 [Premium Storage アカウントを作成する](storage-premium-storage-preview-portal.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)|
| **ワークロードのガイダンス**| バックアップからファイルを項目レベルで取得| クラウドの開発とテストのシナリオ、低待機時間、高パフォーマンス ワークロード <br></br>災害復旧のためのセカンダリ デバイス|

<sup>1</sup> *1100年呼ばれていた*, 、<sup>2</sup> *A Azure フォーム ファクターの略*, 、<sup>3</sup> *AP Azure Premium フォーム ファクターの略します*。


この記事では、Azure で StorSimple 仮想デバイスをデプロイするための段階的なプロセスについて説明します。 この記事を読むと、次のことができます。

- 仮想デバイスと物理デバイスの違いを理解する。

- 仮想デバイスを作成および構成する。

- 仮想デバイスに接続する。

- 仮想デバイスでの作業方法を学習する。

このチュートリアルは、Update 2 以降を実行するすべての StorSimple 仮想デバイスに適用されます。

## 仮想デバイスと物理デバイスの違い

StorSimple 仮想デバイスは、Microsoft Azure 仮想マシン内の単一のノード上で動作する、StorSimple のソフトウェア専用バージョンです。 仮想デバイスは、物理デバイスが使用できない障害復旧のシナリオに対応します。また、バックアップからの項目レベルの取得、オンプレミスの障害復旧、クラウドの開発とテストのシナリオに適しています。

#### 物理デバイスとの違い

次の表は、StorSimple 仮想デバイスと StorSimple 物理デバイスの主な違いを示します。

| | 物理デバイス| 仮想デバイス|
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **場所**| データ センター内に存在します。| Azure で実行されます。|
| **ネットワーク インターフェイス**| 6 つのネットワーク インターフェイス (DATA 0 から DATA 5) があります。| ネットワーク インターフェイスは DATA 0 ただ 1 つです。|
| **登録**| 構成手順中に登録します。| 登録は別の作業です。|
| **サービス データ暗号化キー**| 物理デバイスで再生成し、新しいキーで仮想デバイスを更新します。| 仮想デバイスから再生成することはできません。|


## 仮想デバイスの前提条件

以下の各セクションでは、StorSimple 仮想デバイスの構成の前提条件について説明します。 仮想デバイスを展開する前に確認、 [仮想デバイスを使用してセキュリティの考慮事項](storsimple-security.md#storsimple-virtual-device-security)します。

#### Azure の要件

仮想デバイスをプロビジョニングする前に、Azure 環境で次の準備作業が必要となります。

- 仮想デバイスに [Azure の仮想ネットワークの構成](../virtual-network/virtual-networks-create-vnet.md)します。 Premium Storage を使用している場合は、Premium Storage をサポートする Azure リージョンに仮想ネットワークを作成する必要があります。 詳細について [現在 Premium Storage をサポートしているリージョン](https://azure.microsoft.com/regions/#services)します。
- 独自の DNS サーバー名を指定する代わりに、Azure に用意されている既定の DNS サーバーを使用することをお勧めします。 DNS サーバー名が有効でない場合または DNS サーバーが IP アドレスを正しく解決できない場合、仮想デバイスの作成は失敗します。
- ポイント対サイトおよびサイト間を必要に応じて選ぶことができますが、必須ではありません。 必要に応じてこれらのオプションを構成することで、より高度なシナリオを実現することができます。
- 作成することができます [Azure の仮想マシン](../virtual-machines/virtual-machines-about.md) (ホスト サーバー) で仮想ネットワークが仮想デバイスによって公開されているボリュームを使用できます。 これらのサーバーは次の要件を満たしている必要があります。
    - Windows または Linux の VM が実行され、iSCSI イニシエーター ソフトウェアがインストールされていること。
    - 仮想デバイスと同じ仮想ネットワークで実行されていること。
    - 仮想デバイスの内部 IP アドレスで仮想デバイスの iSCSI ターゲットに接続できること。

- iSCSI とクラウド トラフィックに対するサポートを同じ仮想ネットワークに対して構成済みであることを確認してください。


#### StorSimple の要件

仮想デバイスを作成する前に、Azure StorSimple サービスに対して次の更新作業を行います。


- 追加 [アクセス制御レコード](storsimple-manage-acrs.md) 仮想デバイスのホスト サーバーとなる Vm 用にします。

- 使用して、 [ストレージ アカウント](storsimple-manage-storage-accounts.md#add-a-storage-account) 仮想デバイスと同じリージョンにします。 ストレージ アカウントが異なるリージョンに存在すると、十分なパフォーマンスが得られない可能性があります。 仮想デバイスでは、Standard Storage アカウントまたは Premium Storage アカウントを使用できます。 作成する方法の詳細については、 [Standard Storage アカウント]() または [Premium Storage アカウント](storage-premium-storage-preview-portal.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- 仮想デバイスの作成には、データに使用するストレージ アカウントとは異なるストレージ アカウントを使用します。 同じストレージ アカウントを使用すると、十分なパフォーマンスが得られない可能性があります。

作業を開始する前に、次の情報を確認してください。

- Azure ポータル アカウントとアクセス資格情報。

- 物理デバイスから取得したサービス データ暗号化キーのコピー。


## 仮想デバイスの作成と構成

これらの手順を実行する前に満たしていることを確認します、 [仮想デバイスの前提条件](#prerequisites-for-the-virtual-device)します。

仮想ネットワークを作成して StorSimple Manager サービスを構成し、そのサービスに物理 StorSimple デバイスを登録した後、次の手順に従って StorSimple 仮想デバイスを作成および構成することができます。

### 手順 1. 仮想デバイスの作成

StorSimple 仮想デバイスを作成するには、次の手順を実行します。

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]


### 手順 2. 仮想デバイスの構成と登録

この手順を開始する前に、サービス データ暗号化キーのコピーがあることを確認してください。 サービス データ暗号化キーは、最初の StorSimple デバイスの構成時に作成され、安全な場所に保存するように指示されます。 サービス データ暗号化キーのコピーがない場合は、Microsoft サポートに支援を依頼する必要があります。

StorSimple 仮想デバイスを構成して登録するには、次の手順を実行します。
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### 手順 3. (オプション) デバイスの構成設定の変更

次のセクションでは、CHAP を使用する場合や、StorSimple Snapshot Manager のパスワードまたはデバイス管理者パスワードを変更する場合に StorSimple 仮想デバイスに必要なデバイス構成設定について説明します。

#### CHAP イニシエーターの構成

このパラメーターには、ボリュームへのアクセスを試みるイニシエーター (サーバー) に対して仮想デバイス (ターゲット) が要求する資格情報が含まれます。 この認証中、イニシエーターは自己の身元をデバイスに証明するために CHAP ユーザー名と CHAP パスワードを提供します。 詳細な手順を参照してください [デバイスの CHAP を構成する](storsimple-configure-chap.md#unidirectional-or-one-way-authentication)します。

#### CHAP ターゲットの構成

このパラメーターには、CHAP 対応のイニシエーターが相互認証 (双方向認証) を要求するときに仮想デバイスによって使用される資格情報が含まれます。 この認証プロセス中、仮想デバイスは、リバース CHAP ユーザー名とリバース CHAP パスワードを使用してその身元をイニシエーターに対して証明します。 CHAP ターゲットの設定はグローバル設定であることに注意してください。 これらの設定が適用されると、ストレージの仮想デバイスに接続されているすべてのボリュームに CHAP 認証が使用されます。 詳細な手順を参照してください [デバイスの CHAP を構成する](storsimple-configure-chap.md#bidirectional-or-mutual-authentication)します。

#### StorSimple Snapshot Manager のパスワードの構成

StorSimple Snapshot Manager ソフトウェアは Windows ホスト上に常駐し、管理者が、ローカル スナップショットとクラウド スナップショットの形式で StorSimple デバイスのバックアップを管理することを可能にします。
>[AZURE.NOTE] 仮想デバイスの場合、Windows ホストは Azure 仮想マシンです。

StorSimple Snapshot Manager でデバイスを構成するとき、ストレージ デバイスを認証するためのパスワードと StorSimple デバイスの IP アドレスを入力するように求められます。 詳細な手順を参照してください [StorSimple Snapshot Manager の構成のパスワード](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)します。

#### デバイス管理者のパスワードの変更

Windows PowerShell インターフェイスを使用して仮想デバイスにアクセスする際、デバイス管理者のパスワードの入力が求められます。 データのセキュリティ上、仮想デバイスの使用前にこのパスワードを変更することが必須となっています。 詳細な手順を参照してください [デバイス管理者のパスワードを構成する](storsimple-change-passwords.md#change-the-device-administrator-password)します。

## 仮想デバイスへのリモート接続

Windows PowerShell インターフェイス経由での仮想デバイスへのリモート アクセスは既定では有効になっていません。 リモート管理は、まず仮想デバイスで有効にして、次に、仮想デバイスへのアクセスに使用するクライアントで有効にする必要があります。

リモートで接続するための 2 段階のプロセスについては、以下で詳しく説明します。

### 手順 1. リモート管理の構成

StorSimple 仮想デバイスのリモート管理を構成するには、次の手順を実行します。

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### 手順 2. 仮想デバイスへのリモート アクセス

StorSimple デバイスの構成ページでリモート管理を有効にしたら、Windows PowerShell リモート処理を使って、同じ仮想ネットワーク内の別の仮想マシンから仮想デバイスに接続することができます。たとえば、iSCSI に接続するために構成して使用しているホスト VM から接続することができます。 ほとんどのデプロイでは、仮想デバイスへのアクセスに使用できるホスト VM にアクセスするパブリック エンドポイントは既に開かれています。
>[AZURE.WARNING] **セキュリティ強化のため、エンドポイントに接続する場合は HTTPS を使用して、PowerShell リモート セッション完了後にエンドポイントを削除することを強くお勧めします。**

手順を実行する必要があります [、StorSimple デバイスにリモートで接続する](storsimple-remote-connect.md) 仮想デバイスのリモート処理を設定します。

## 仮想デバイスへの直接接続

仮想デバイスに直接接続することもできます。 仮想ネットワーク外または Microsoft Azure 環境外の別のコンピューターから直接仮想デバイスに接続する場合は、次の手順に従って追加のエンドポイントを作成する必要があります。

仮想デバイス上にパブリック エンドポイントを作成するには、次の手順を実行します。

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

この方法では仮想ネットワーク上のパブリック エンドポイントの数が最小限になるため、同じ仮想ネットワーク内にある別の仮想マシンから接続することをお勧めします。 この方法を使用する際は、リモート デスクトップ セッションを介して仮想マシンに接続してから、ローカル ネットワーク上の他の Windows クライアントのように仮装マシンを構成します。 ポートは既にわかっているため、パブリック ポート番号を付加する必要はありません。

## StorSimple 仮想デバイスの作業

StorSimple 仮想デバイスの作成と構成が終了したので、操作を開始する準備ができました。 物理 StorSimple デバイスの場合と同じように、ボリューム コンテナー、ボリューム、およびバックアップ ポリシーを仮想デバイスで使用できます。唯一の違いは、デバイス リストから仮想デバイスを選択する必要があることです。 参照してください [StorSimple Manager サービスを使用して、仮想デバイスを管理する](storsimple-manager-service-administration.md) 仮想デバイスのさまざまな管理タスクの手順です。

以降のセクションでは、仮想デバイスでの作業時に遭遇するいくつかの違いについて説明します。

### StorSimple 仮想デバイスのメンテナンス

仮想デバイスはソフトウェア専用デバイスであるため、物理的なデバイスと比べて、メンテナンスは最小限で済みます。 次のオプションがあります。

- **[ソフトウェアの更新]** – ソフトウェアが最後に更新された日時と更新状態メッセージが表示されます。 新しい更新プログラムがないかどうかを確認する場合は、ページの下部にある **[更新プログラムのスキャン]** ボタンを使って、手動スキャンを実行できます。 更新プログラムが利用できる場合は、**[更新プログラムのインストール]** をクリックしてインストールします。 仮想デバイスには単一のインターフェイスしかないため、更新プログラムの適用時、サービスの中断はわずかで済みます。 仮想デバイスがシャットダウンして (必要に応じて) 再起動し、リリースされた更新プログラムが適用されます。 詳細な手順を参照してください [デバイスを更新する](storsimple-update-device.md#install-regular-updates-via-the-management-portal)します。
- **[サポート パッケージ]** – ご利用の仮想デバイスに関する問題を Microsoft サポートがトラブルシューティングできるようにするため、サポート パッケージを作成してアップロードできます。 詳細な手順を参照してください [を作成および管理サポート パッケージ](storsimple-create-manage-support-package.md)します。

### 仮想デバイスのストレージ アカウント

ストレージ アカウントは、StorSimple Manager サービス、仮想デバイス、および物理的なデバイスで使用するために作成されます。 ストレージ アカウントを作成する場合、すべてのシステム コンポーネントでリージョンが一致するように、表示名に地域識別子を使用することをお勧めします。 仮想デバイスの場合、パフォーマンスの問題を防ぐには、すべてのコンポーネントが同じリージョンに属していることが重要です。

詳細な手順を参照してください [ストレージ アカウントの追加](storsimple-manage-storage-accounts.md#add-a-storage-account)します。

### StorSimple 仮想デバイスの非アクティブ化

仮想デバイスを非アクティブ化すると、プロビジョニング時に作成された VM とリソースが削除されます。 仮想デバイスを非アクティブにすると、以前の状態に復元することはできません。 仮想デバイスを非アクティブ化する前に、それに依存しているクライアントとホストを必ず停止または削除してください。

仮想デバイスを非アクティブにすると、次のアクションが発生します。

- 仮想デバイスが削除されます。

- 仮想デバイス用に作成された OS ディスクとデータ ディスクが削除されます。

- プロビジョニング中に作成されたホストされるサービスと仮想ネットワークは保持されます。 それらを使用していない場合は、手動で削除する必要があります。

- 仮想デバイス用に作成されたクラウド スナップショットは保持されます。

詳細な手順を参照してください [、デバイスを非アクティブ化](storsimple-deactivate-and-delete-device.md#deactivate-a-device)します。

仮想デバイスは、StorSimple Manager サービス ページで非アクティブとして表示されたらすぐに、**[デバイス]** ページのデバイス一覧から削除できます。


### 仮想デバイスの開始、停止、および再起動

StorSimple 物理デバイスとは異なり、StorSimple 仮想デバイスには電源をオンまたはオフにするボタンはありません。 ただし、仮想デバイスを停止して再起動する必要がある場合があります。 たとえば、一部の更新プログラムでは、更新プロセスを完了するために VM の再起動が必要な場合があります。 仮想デバイスを開始、停止、再起動する最も簡単な方法は、Virtual Machines 管理コンソールを使用することです。

管理コンソールを表示すると、仮想デバイスは作成後、既定で開始されるため、状態は **[実行中]** になります。 仮想マシンはいつでも開始、停止、および再起動できます。

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### 工場出荷時の既定値へのリセット

仮想デバイスを始めからやり直したい場合は、非アクティブにしてから削除し、新しく作成し直します。 物理デバイスをリセットするときと同様に、新しい仮想デバイスには更新プログラムがインストールされていないため、使用する前に更新プログラムを必ず確認してください。


## 仮想デバイスへのフェールオーバー

障害復旧 (DR) は、StorSimple 仮想デバイスの設計目的であった主要シナリオの 1 つです。 このシナリオでは、物理 StorSimple デバイスまたはデータセンター全体が使用できなくなる可能性があります。 さいわいなことに、仮想デバイスを使って、別の場所に運用を復元することができます。 DR 中に、ソース デバイスのボリューム コンテナーの所有権が変更され、それらのコンテナーは仮想デバイスに転送されます。 DR の前提条件として、仮想デバイスが作成され、構成されていることと、ボリューム コンテナー内のすべてのボリュームがオフラインになっていること、さらに、ボリューム コンテナーにクラウド スナップショットが関連付けられていることが挙げられます。
>[AZURE.NOTE] 
>
> -Dr をセカンダリ デバイスと仮想デバイスを使用する場合は、8010 は 30 TB の標準的なストレージがあり 8020 は 64 TB Premium Storage のことに留意してください。 より容量が多い 8020 仮想デバイスは、DR シナリオにより適しています。
> にフェールオーバーすることはできませんか、更新前の 1 ソフトウェアを実行するデバイスへの更新プログラム 2 を実行しているデバイスから複製します。 ただし、Update 2 を実行しているデバイスを Update 1 (1.1 または 1.2) を実行するデバイスにフェールオーバーすることはできます。

詳細な手順を参照してください [仮想デバイスへのフェールオーバー](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device)します。


## 仮想デバイスのシャットダウンまたは削除

以前構成して使っていた StorSimple 仮想デバイスのコンピューティング料金の発生を止めるには、仮想デバイスをシャットダウンします。 仮想デバイスをシャットダウンしても、ストレージ内のオペレーティング システムやデータ ディスクは削除されません。 この操作によって、サブスクリプションの料金は発生しなくなりますが、OS とデータ ディスクのストレージの料金は継続して発生します。

仮想デバイスを削除またはシャットダウンすると、StorSimple Manager サービスの [デバイス] ページには **[オフライン]** として表示されます。 仮想デバイスによって作成されたバックアップも一緒に削除する場合は、デバイスを非アクティブにするか、削除するかを選択できます。 詳細については、次を参照してください。 [デバイスを非アクティブ化](storsimple-deactivate-and-delete-device.md#deactivate-a-device)します。

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## 次のステップ

- 学習方法 [StorSimple Manager サービスを使用して、仮想デバイスを管理する](storsimple-manager-service-administration.md)します。

- 理解する方法 [バックアップ セットから StorSimple ボリュームの復元](storsimple-restore-from-backup-set.md)します。






