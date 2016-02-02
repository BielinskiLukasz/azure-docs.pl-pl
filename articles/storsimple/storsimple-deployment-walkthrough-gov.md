<properties 
   pageTitle="Government Portal での StorSimple デバイスのデプロイ | Microsoft Azure"
   description="Azure Government Portal で StorSimple Update 1 のデバイスとサービスをデプロイするための手順とベスト プラクティスを説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/09/2015"
   ms.author="v-sharos" />


# Government Portal でのオンプレミスの StorSimple デバイスのデプロイ

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## 概要

Microsoft Azure StorSimple デバイスのデプロイへようこそ。 デプロイに関するこれらのチュートリアルは、Azure Government Portal で Update 1 ソフトウェアを実行している StorSimple 8000 シリーズに適用されます。 このチュートリアルのシリーズでは、StorSimple デバイスを構成する方法について説明します。また、構成チェック リスト、構成の前提条件、詳細な構成手順についても紹介します。

これらのチュートリアルの情報は、ユーザーが安全上の注意を確認していること、および StorSimple デバイスのパッケージを展開してラックに配置し、配線していることを想定しています。 これらのタスクを実行する場合は、確認作業の開始、 [安全に関する注意事項](storsimple-safety.md)します。 また、デバイス モデルに応じて以下のいずれかの手順に従い、デバイスの開梱、ラック取り付け、およびケーブル接続を行ってください。

- [開梱、ラック取り付け、および、8100 をケーブル接続](storsimple-8100-hardware-installation.md)
- [開梱、ラック取り付け、および、8600 のケーブル接続](storsimple-8600-hardware-installation.md)

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 開始する前に、構成チェック リストを確認することをお勧めします。 デプロイと構成のプロセスは、完了するまでに時間がかかることがあります。
> [AZURE.NOTE] Microsoft Azure の Web サイトで発行されている StorSimple のデプロイに関する情報は、StorSimple 8000 シリーズ デバイスのみに適用されます。 7000 シリーズ デバイスの完全な情報を参照してください: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)します。 7000 シリーズのデプロイについて、次を参照してください。、 [StorSimple システム クイック スタート ガイド](http://onlinehelp.storsimple.com/111_Appliance/)します。

## デプロイの手順

StorSimple デバイスを構成し、StorSimple Manager サービスに接続するには、次の必須手順を実行します。 必須手順に加えて、デプロイ中にオプションの手順が必要になる場合があります。 デプロイの詳細な手順では、どの時点でこれらの省略可能な手順を実行するかを示しています。


| 手順| 説明|
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **前提条件**| これらの前提条件は、今回のデプロイの準備として完了する必要があります。|
| デプロイの構成チェック リスト| このチェック リストを使用して、デプロイ前およびデプロイ中に情報を収集し、記録します。|
| デプロイの前提条件| これらの検証、環境を展開できるようします。|
| | |
| **デプロイの手順**| 運用環境に StorSimple デバイスをデプロイするには、次の手順を実行します。|
| 手順 1. 新しいサービスを作成する。| クラウドの管理と StorSimple デバイスの記憶域を設定します。既に他の StorSimple デバイス用のサービスがある場合は、この手順をスキップします。|
| 手順 2. サービス登録キーを取得する。| このキーを使用して、StorSimple デバイスを管理サービスに登録し、接続します。|
| 手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し、登録する。| デバイスをネットワークに接続し、管理サービスを使用してセットアップを実行する Azure に登録します。|
| 手順 4: デバイスの最小セットアップを完了する</br>省略可能: StorSimple デバイスを更新します。| 管理サービスを使用して、デバイスのセットアップを完了し、ストレージを提供できるようにします。|
| 手順 5. ボリューム コンテナーを作成する。| ボリュームをプロビジョニングするためのコンテナーを作成します。ボリューム コンテナーではストレージ アカウント、帯域幅、およびそれに含まれているすべてのボリュームの暗号化の設定です。|
| 手順 6. ボリュームを作成する。| サーバーの StorSimple デバイスでストレージ ボリュームをプロビジョニングします。|
| 手順 7: にマウント、初期化、およびボリュームをフォーマットします。</br>省略可能: MPIO を構成します。| デバイスによって提供される iSCSI ストレージにサーバーを接続します。必要に応じて、サーバーがリンク、ネットワーク、およびインターフェイスの障害を許容できるように MPIO を構成します。|
| 手順 8. バックアップを取得する。| データを保護するためのバックアップ ポリシーを設定します。|
| | |
| **その他の手順**| ソリューションのデプロイ中に、これらの手順を参照する必要が生じる場合があります。|
| サービスの新しいストレージ アカウントを構成する。| |
| PuTTY を使用してデバイスのシリアル コンソールに接続する。| |
| 更新プログラムをスキャンして適用する。| |
| Windows Server ホストの IQN を取得する。| |
| 手動バックアップを作成する。|
| MPIO を構成する。|


## デプロイの構成チェック リスト

次のデプロイの構成チェック リストでは、StorSimple デバイスでソフトウェアを構成する前および構成中に収集する必要のある情報について説明します。 事前にこれらの情報の一部を準備することで、環境内に StorSimple デバイスをデプロイするプロセスを効率化できます。 また、デバイスのデプロイ中も、このチェック リストを使用して、構成の詳細情報を書き留めることができます。

| 段階| パラメーター| 詳細| 値|
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **デバイスのケーブルを接続する**| シリアル アクセス| デバイスの初期構成| はい/いいえ|
| | | | |
| **デバイスを構成して登録する**| DATA 0 のネットワーク設定| Data 0 の IP アドレス:</br>サブネット マスク:</br>ゲートウェイ:</br>プライマリ DNS サーバー:</br>プライマリ NTP サーバー:</br>Web プロキシ サーバーの IP または FQDN (省略可能):</br>Web プロキシのポート。| |
| | デバイス管理者のパスワード| パスワードは、小文字、大文字、数字、および特殊文字を含む 8 ～ 15 文字にする必要があります。| |
| | StorSimple Snapshot Manager のパスワード| パスワードは、小文字、大文字、数字、および特殊文字を含む 14 ～ 15 文字にする必要があります。| |
| | サービス登録キー| このキーは、Azure ポータルから生成されます。| |
| | サービス データ暗号化キー| このキーは、デバイスが StorSimple 用 Windows PowerShell を使用して管理サービスに登録されると作成されます。このキーをコピーし、安全な場所に保存しておきます。| |
| | | | |
| **デバイスの最小セットアップを完了する**| デバイスの表示名| デバイスのわかりやすい名前です。| |
| | タイム ゾーン| デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。| |
| | セカンダリ DNS サーバー| この構成は、必須です。| |
| | ネットワーク インターフェイス: DATA 0 のコントローラーの固定 IP| これらの ip アドレスからは、インターネットにルーティング可能である必要があります。</br>コント ローラー 0 の固定の IP アドレス:</br>コント ローラー 1 の固定の IP アドレス。|
| | | | |
| **追加のネットワーク インターフェイス設定**| ネットワーク インターフェイス: Data 1</br>iSCSI を有効にすると、ゲートウェイを構成しなかった場合です。| 目的: クラウド/iSCSI/未使用</br>IP アドレス:</br>サブネット マスク:</br>ゲートウェイ。|
| | ネットワーク インターフェイス: データ 2</br>iSCSI を有効にすると、ゲートウェイを構成しなかった場合です。| 目的: クラウド/iSCSI/未使用</br>IP アドレス:</br>サブネット マスク:</br>ゲートウェイ。|
| | ネットワーク インターフェイス: Data 3</br>iSCSI を有効にすると、ゲートウェイを構成しなかった場合です。| 目的: クラウド/iSCSI/未使用</br>IP アドレス:</br>サブネット マスク:</br>ゲートウェイ。|
| | ネットワーク インターフェイス: Data 4</br>iSCSI を有効にすると、ゲートウェイを構成しなかった場合です。| 目的: クラウド/iSCSI/未使用</br>IP アドレス:</br>サブネット マスク:</br>ゲートウェイ。|
| | ネットワーク インターフェイス: データは 5</br>iSCSI を有効にすると、ゲートウェイを構成しなかった場合です。| 目的: クラウド/iSCSI/未使用</br>IP アドレス:</br>サブネット マスク:</br>ゲートウェイ。|
| | | | |
| **ボリューム コンテナーを作成する**| ボリューム コンテナー名:| コンテナーの名前| |
| | Azure ストレージ アカウント:| このボリューム コンテナーに関連付けるストレージ アカウント名とアクセス キー| |
| | クラウド ストレージ暗号化キー:| 各コンテナーに格納するための暗号化キー| |
| | | | |
| **ボリュームを作成する**| 各ボリュームの詳細| ボリューム名:| |
| | | サイズ:| |
| | | 使用法の種類:| |
| | | ACR 名:| |
| | | 既定のバックアップ ポリシー:| |
| | | | |
| **ボリュームをマウント、初期化、フォーマットする**| ストレージに接続している各ホスト サーバーの詳細| Windows Server 名:| |
| | | Windows Server の IQN:| |
| | | Windows Server のボリューム名:| |
| | | NTFS マウント ポイント/ドライブ文字:| |


## デプロイの前提条件

ここでは、StorSimple Manager サービスと StorSimple デバイスの構成の前提条件について説明します。

### StorSimple Manager サービスの場合

開始する前に次の点を確認します。

- アクセスの資格情報を持つ Microsoft アカウントがある。

- アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがある。

- StorSimple Manager サービスの Microsoft Azure サブスクリプションが有効である。 サブスクリプションを購入する必要があります、 [Enterprise Agreement](http://azure.microsoft.com/pricing/enterprise-agreement/)します。

- PuTTY などのターミナル エミュレーション ソフトウェアにアクセスできる。

### データセンターのデバイスの場合

デバイスを構成する前に次の点を確認します。

- 以下のページの説明に従って、デバイスが完全に開梱され、ラックに取り付けられ、電源、ネットワーク、およびシリアル アクセスのケーブルが完全に接続されている。

    -  [開梱、ラック取り付け、および 8100 デバイスのケーブル配線](storsimple-8100-hardware-installation.md)
    -  [開梱、ラック取り付け、および 8600 デバイス向けケーブル配線](storsimple-8600-hardware-installation.md)


### データセンターのネットワークの場合

開始する前に次の点を確認します。

- 」の説明に従って、iSCSI とクラウド トラフィックを許可するように、データ センターのファイアウォールでポートが開かれた [StorSimple デバイスのネットワーク要件](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)します。

## デプロイの手順

StorSimple デバイスをデータセンター内にデプロイするには、次の詳細な手順を実行します。

## 手順 1. 新しいサービスを作成する

StorSimple Manager サービスでは、複数の StorSimple デバイスを管理できます。 StorSimple Manager サービスの新しいインスタンスを作成するには、次の手順を実行します。

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]
> [AZURE.IMPORTANT] サービスでストレージ アカウントの自動作成を有効にしていない場合は、サービスの作成が完了してから、1 つ以上のストレージ アカウントを作成する必要があります。 このストレージ アカウントは、ボリューム コンテナーを作成するときに使用します。 
>
> * ストレージ アカウントを自動的に作成していない場合に進みます [、サービスの新しいストレージ アカウントを構成する](#configure-a-new-storage-account-for-the-service) 詳細な手順についてです。 
> * ストレージ アカウントの自動作成を有効にした場合に進みます [手順 2: サービス登録キーの取得](#step-2-get-the-service-registration-key)します。

## 手順 2. サービス登録キーを取得する

StorSimple Manager サービスが稼働したら、サービス登録キーを取得する必要があります。 このキーを使用して StorSimple デバイスを登録し、サービスに接続します。

Government Portal で、次の手順を実行します。

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## 手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する

次の手順の説明に従い、StorSimple 用 Windows PowerShell を使用して StorSimple デバイスの初期セットアップを完了します。 この手順を完了するには、ターミナル エミュレーション ソフトウェアを使用する必要があります。 詳細については、次を参照してください。 [デバイスのシリアル コンソールに接続するための PuTTY を使用して](#use-putty-to-connect-to-the-device-serial-console)します。

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

## 手順 4. デバイスの最小セットアップを完了する

StorSimple デバイスの最小構成を完了するには、次の手順を実行する必要があります。

- セカンダリ DNS サーバーをセットアップします。
- 1 つ以上のネットワーク インターフェイスで iSCSI を有効にします。
- 両方のコントローラーに固定の IP アドレスを割り当てます。

デバイスの最小セットアップを完了するには、Government Portal で次の手順を実行します。

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## 手順 5. ボリューム コンテナーを作成する

ボリューム コンテナーでは、そこに含まれるすべてのボリュームのストレージ アカウント、帯域幅、暗号化が設定されています。 StorSimple デバイス上のボリュームのプロビジョニングを開始する前に、ボリューム コンテナーを作成する必要があります。

ボリューム コンテナーを作成するには、Government Portal で次の手順を実行します。

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## 手順 6. ボリュームを作成する

ボリューム コンテナーを作成したら、サーバーの StorSimple デバイスでストレージ ボリュームをプロビジョニングできます。 ボリュームを作成するには、Government Portal で次の手順を実行します。
> [AZURE.IMPORTANT] Azure StorSimple は、仮想プロビジョニングされたボリュームのみを作成できます。 完全にプロビジョニングされたボリュームまたは部分的にプロビジョニングされたボリュームを Azure StorSimple システム上に作成することはできません。 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## 手順 7. ボリュームをマウント、初期化、フォーマットする

Windows Server ホスト上で次の手順を実行します。
> [AZURE.IMPORTANT]


> - StorSimple ソリューションの高可用性を実現するため、iSCSI を構成する前に、ホスト サーバーで MPIO を構成することをお勧めします (省略可能)。 ホスト サーバーに MPIO を構成すると、サーバーはリンク、ネットワーク、またはインターフェイスの障害を許容できるようになります。
>
> - ISCSI の MPIO のインストールと構成手順については Windows Server ホストを参照してください [StorSimple デバイスの MPIO の構成](storsimple-configure-mpio-windows-server.md)します。 このページには、StorSimple ボリュームのマウント、初期化、フォーマットを実行する手順も記載されています。
>
> - ISCSI の MPIO のインストールと構成手順については、Linux ホスト、を参照してください [MPIO StorSimple Linux ホストの構成](storsimple-configure-mpio-linux.md)

MPIO を構成しない場合は、次の手順に従い、Windows Server ホストに StorSimple ボリュームをマウント、初期化、フォーマットします。

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## 手順 8. バックアップを取得する

バックアップにより、特定の時点のボリュームを保護し、復元時間を最小限に抑えながら回復性を向上させることができます。 StorSimple デバイスでは、ローカル スナップショットとクラウド スナップショットという 2 種類のバックアップを実行できます。 どちらの種類のバックアップも、**[スケジュール設定]** または **[手動]** で実行できます。

スケジュールされたバックアップを作成するには、Government Portal で次の手順を実行します。

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

手動バックアップはいつでも実行できます。 移動する手順については、 [手動バックアップの作成](#create-a-manual-backup)します。

## サービスの新しいストレージ アカウントを構成する

これは省略可能な手順で、サービスでストレージ アカウントの自動作成を有効にしていない場合のみ実行する必要があります。 StorSimple ボリューム コンテナーを作成するには、Microsoft Azure ストレージ アカウントが必要です。

別のリージョンで Azure ストレージ アカウントを作成する必要がある場合は、次を参照してください。 [Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md) 詳しい説明を参照します。

Government Portal の **[StorSimple Manager サービス]** ページで次の手順を実行します。

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## PuTTY を使用してデバイスのシリアル コンソールに接続する

StorSimple 用 Windows PowerShell に接続するには、PuTTY などのターミナル エミュレーション ソフトウェアを使用する必要があります。 シリアル コンソールから直接デバイスにアクセスするか、またはリモート コンピューターから telnet セッションを開いて PuTTY を使用できます。

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## 更新プログラムをスキャンして適用する

デバイスの更新には、数時間かかることがあります。 次の手順を実行して、更新プログラムをスキャンしてデバイスに適用します。



#### デバイスを更新するには

1.  デバイスの **[クイック スタート]** ページで、**[デバイス]** をクリックします。 物理デバイスを選択し、**[メンテナンス]**、**[更新プログラムのスキャン]** を順にクリックします。
2.  利用可能な更新プログラムをスキャンするジョブが作成されます。 更新プログラムが利用できる場合、**[更新プログラムのスキャン]** が **[更新プログラムのインストール]** に変わります。 **[更新プログラムのインストール]** をクリックします。
3.  更新ジョブが作成されます。 **[ジョブ]** に移動して、更新の状態を監視します。
    > [AZURE.NOTE] 更新ジョブが開始されるとすぐ、状態は 50% として表示されます。 更新ジョブの完了後、状態が 100% に変わります。 更新プログラムのプロセスでは、リアルタイムの状態は表示されません。

4.  デバイスが正常に更新された後、DATA 2 および DATA 3 のネットワーク インターフェイスを無効にしていた場合は有効にします。

## Windows Server ホストの IQN を取得する

Windows Server® 2012 を実行する Windows ホストの ISCSI 修飾名 (IQN) を取得するには、次の手順を実行します。

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## 手動バックアップの作成

StorSimple デバイスの 1 つのボリュームに対し、オンデマンドの手動バックアップを作成するには、Government Portal で次の手順を実行します。

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup-gov.md)]

## MPIO を構成する

マルチパス I/O (MPIO) はオプションの機能であり、Windows サーバーに既定ではインストールされていません。 サーバー マネージャーを使用して、機能としてインストールする必要があります。 MPIO のインストール手順についてを参照してください [StorSimple デバイスの MPIO の構成](storsimple-configure-mpio-windows-server.md)します。

Linux ホストに接続されている StorSimple デバイスの MPIO のインストール手順を参照してください [Linux ホストに MPIO を構成](storsimple-configure-mpio-linux.md)します。
> [AZURE.NOTE] StorSimple 仮想デバイスでは、MPIO がサポートされていません。 

## 次のステップ

構成、 [仮想デバイス](storsimple-virtual-device.md)します。

使用して、 [StorSimple Manager サービス](https://msdn.microsoft.com/library/azure/dn772396.aspx) StorSimple デバイスを管理します。





