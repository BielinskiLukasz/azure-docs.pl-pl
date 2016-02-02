<properties
    pageTitle="ディスクを VM にアタッチする | Microsoft Azure"
    description="従来のデプロイ モデルで作成された Windows 仮想マシンにデータ ディスクを接続し、初期化します。"
    services="virtual-machines, storage"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2015"
    ms.author="cynthn"/>


# クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [リソース マネージャー モデル](virtual-machines-attach-disk-preview.md)します。

データ ディスクを追加する必要がある場合は、空のディスクまたはデータを含む既存のディスクを仮想マシンにアタッチできます。 どちらの場合も、ディスクとは、実際には、Azure ストレージ アカウント内に存在する必要がある .vhd ファイルです。 新しいディスクの場合は、ディスクをアタッチした後で、Windows VM で使用できるように初期化する必要もあります。

仮想マシンのデータを格納するには、1 つ以上の個別のディスクを使用することをお勧めします。 Azure の仮想マシンを作成する場合、それは C ドライブにマップされているオペレーティング システムのディスクと一時ディスク ドライブにマップ **データを格納する一時ディスクを使用しないでください**します。 名前が示すように、一時ディスクは、一時的なストレージのみを提供します。 Azure Storage に配置されていないため、冗長性やバックアップは提供しません。

## ビデオ チュートリアル

以下には、このチュートリアルのステップが記載されています。

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>方法: Windows Server で新しいデータ ディスクの初期化

1. 仮想マシンへの接続 手順については、次を参照してください。 [[ログオン] の Windows Server を実行する仮想マシンにログオンする方法][logon]します。

2. 仮想マシンにログオンした後、**サーバー マネージャー**を開きます。 左側のウィンドウで、**[ファイル サービスと記憶域サービス]** を選択します。

    ![サーバー マネージャーを開く](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. メニューを展開し、**[ディスク]** を選択します。

4. **[ディスク]** セクションにディスクが一覧表示されます。 普通、disk 0、disk 1、disk 2 といった名前です。 Disk 0 はオペレーティング システム ディスク、disk 1 は、一時的なディスク (データ ストレージとしては使用できません)、および disk 2 は、仮想マシンに接続したばかりのデータ ディスクです。 新しいデータ ディスクのパーティションは **[不明]** と表示されます。 ディスクを右クリックして **初期化**します。

5.  ディスクの初期化時にすべてのデータが消去されることが通知されます。 **[はい]** をクリックして警告を了解し、ディスクを初期化します。 完了すると、パーティションは **[GPT]** と表示されます。 ディスクをもう一度右クリックして **新しいボリューム**します。

6.  既定の値を使用してウィザードを完了します。 ウィザードが終了すると、**[ボリューム]** セクションに新しいボリュームが表示されます。 ディスクがオンラインになり、データを格納できるようになります。

    ![ボリュームの初期化に成功](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] 仮想マシンのサイズによって、アタッチできるディスクの数が決まります。 詳細については、「 [仮想マシンのサイズ](virtual-machines-size-specs.md)します。

## その他のリソース

[Windows 仮想マシンからディスクを切断する方法](storage-windows-detach-disk.md)

[ディスクとバーチャル マシンの Vhd について](virtual-machines-disks-vhds.md)


[logon]: virtual-machines-log-on-windows-server.md 

