<properties
    pageTitle="データ ディスクを接続する |Microsoft Azure"
    description="Azure ポータルでリソース マネージャーデプロイ モデルを使用して、新規または既存のデータ ディスクを VM に接続する方法。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2015"
    ms.author="cynthn"/>


# Azure ポータルでデータ ディスクを接続する方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [従来のデプロイ モデル](storage-windows-attach-disk.md)します。

この記事では、Azure ポータルを使用して新しいディスクと既存のディスクを仮想マシンに接続する方法について説明します。 接続する前に、次のヒントを確認してください。

- 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](virtual-machines-size-specs.md)します。
- Premium Storage を使用するには、DS シリーズまたは GS シリーズの仮想マシンが必要です。 これらの仮想マシンでは、Premium および Standard のストレージ アカウントのディスクを使用できます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage-preview-portal.md)します。
- 仮想マシンに接続されているディスクは、実際には Azure ストレージ アカウントの .vhd ファイルです。 詳細については、「 [ディスクとバーチャル マシンの Vhd について](virtual-machines-disks-vhds.md)します。
- 新しいディスクの場合、接続時に Azure で自動的に作成されるので、最初に作成する必要はありません。
- 既存のディスクの場合、Azure ストレージ アカウントで .vhd ファイルを使用できる必要があります。 別の仮想マシンに接続されていない場合、既存のファイルを使用します。または、.vhd ファイルをストレージ アカウントにアップロードします。

## 仮想マシンの検索

1. Azure ポータルにサインインします。

2. ハブ メニューの **[参照]** をクリックします。

3. 検索ブレードを下へスクロールし、**[仮想マシン]** をクリックします。

    ![仮想マシンのサイズ](./media/virtual-machines-attach-disk-preview/search-blade-preview-portal.png)

4.  一覧から仮想マシンを選択します。

5. 右側にある **[要点]** で **[すべての設定]** をクリックし、**[ディスク]** をクリックします。

    ![ディスク設定を開く](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

次の手順に従って、新しいディスクまたは既存のディスクを接続します。

## オプション 1: 新しいディスクを接続する

1.  **[ディスク]** ブレードの **[新しいディスクの接続]** をクリックします。

2.  既定の設定を確認し、必要に応じて更新して、**[OK]** をクリックします。

    ![ディスク設定を確認する](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.  Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]** に新しいディスクが表示されます。

## オプション 2: 既存のディスクを接続する

1.  **[ディスク]** ブレードの **[既存のディスクの接続]** をクリックします。

2.  **[既存のディスクの接続]** の **[VHD ファイル]** をクリックします。

    ![既存のディスクを接続する](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.  **[ストレージ アカウント]** で、アカウントと、.vhd ファイルが格納されているコンテナーを選択します。

    ![VHD の場所を検索する](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.  .vhd ファイルを選択します。

5.  **[既存のディスクの接続]** の **[VHD ファイル]** に、選択したファイルが表示されます。 **[OK]** をクリックします。

6.  Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]** にそのディスクが表示されます。

## 次のステップ

ディスクを追加した後は、仮想マシンのオペレーティング システムで使用できるように準備する必要があります。

- Linux では、次を参照してください。"する方法: Linux での新しいデータ ディスクの初期化"この [記事](virtual-machines-linux-how-to-attach-disk.md)します。
- Windows では、次を参照してください。"する方法: Windows Server で新しいデータ ディスクの初期化"この [記事](storage-windows-attach-disk.md)します。

## その他のリソース

[Azure ストレージ アカウント] について




[about azure storage accounts]: ../storage-whatis-account/ 

