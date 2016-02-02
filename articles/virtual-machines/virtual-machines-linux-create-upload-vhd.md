<properties
    pageTitle="Linux VHD の作成とアップロード | Microsoft Azure"
    description="Linux オペレーティング システムを格納したクラシック デプロイ モデルを使用して Azure 仮想ハード ディスク (VHD) を作成してアップロードします。"
    services="virtual-machines"
    documentationCenter=""
    authors="dsk-2015"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="dkshir"/>


# Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


ここでは、仮想ハードディスク (VHD) を作成およびアップロードし、それをイメージとして活用して Azuere 内で仮想マシンを作成する方法を示します。 そのイメージに基づいて複数の仮想マシンを作成できるよう、オペレーティング システムを準備する方法についても説明します。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Azure の仮想マシンでは、仮想マシンの作成時に選択したイメージに基づいてオペレーティング システムが実行されます。 イメージはストレージ アカウント内に VHD 形式 (.vid ファイル) で保存されます。 詳細については、「 [Azure 内のディスク](virtual-machines-disks-vhds.md) と [Azure のイメージ](virtual-machines-images.md)します。

仮想マシンを作成するときに、実行するアプリケーションに合わせてオペレーティング システムの一部の設定をカスタマイズすることができます。 手順については、次を参照してください。 [カスタム仮想マシンを作成する方法](virtual-machines-create-custom.md)します。

**重要**: Azure プラットフォームの SLA は、構成の動作保証済みディストリビューションのいずれかを使用する場合にのみ、Linux OS を実行する仮想マシンでサポートされているバージョン ' によって指定された詳細の [Linux Azure-Endorsed ディストリビューション](virtual-machines-../linux-endorsed-distributions.md)します。 Azure イメージ ギャラリーにあるすべての Linux ディストリビューションは、必須の構成による動作保証済みディストリビューションです。


## 前提条件

この記事では、次の項目があることを前提としています。

- **管理証明書** - VHD をアップロードするサブスクリプションの管理証明書を作成し、その証明書を .cer ファイルにエクスポートした。 証明書の作成の詳細については、次を参照してください。 [Azure 用の証明書の概要](../cloud-services/cloud-services-certs-create.md)します。

- **.Vhd ファイルにインストールされている Linux オペレーティング システム**  のバーチャル ハード_ディスクにサポートされている Linux オペレーティング システムをインストールします。 .vhd ファイルを作成するツールは複数あります。たとえば、Hyper-V などの仮想化ソリューションにより、.vhd ファイルを作成し、オペレーティング システムをインストールできます。 手順については、次を参照してください。 [Hyper-v 役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)します。

    **重要**: 新しい VHDX 形式は、Azure ではサポートされていません。 Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

    動作保証済みディストリビューションの一覧は、次を参照してください。 [Linux Azure-Endorsed ディストリビューション](../linux-endorsed-distributions.md)します。 全般の Linux ディストリビューションの一覧を表示するには、次を参照してください。 [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-vhd-generic.md)します。

- **Azure コマンド ライン インターフェイス** -Linux オペレーティング システム イメージを作成するを使用している場合は使用する、 [Azure コマンド ライン インターフェイス](../virtual-machines-command-line-tools.md) VHD をアップロードします。

- **Azure Powershell ツール** - `Add-azurevhd` コマンドレットを使用して、VHD をアップロードすることもできます。 参照してください [Azure のダウンロード](http://azure.microsoft.com/downloads/) Azure Powershell コマンドレットをダウンロードします。 詳細については、次を参照してください。 [Add-azurevhd](https://msdn.microsoft.com/library/azure/dn495173.aspx)します。

<a id="prepimage"> </a>
## 手順 1. アップロードするイメージを準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (を参照してください [動作保証済みディストリビューション](../linux-endorsed-distributions.md))します。 次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。

- * *[CentOS ベースのディストリビューション](virtual-machines-linux-create-upload-vhd-centos.md)* *
- **[Debian Linux](virtual-machines-linux-create-upload-vhd-debian.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- * *[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)* *
- **[SLES & openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- * *[その他 - 動作保証外のディストリビューション](virtual-machines-linux-create-upload-vhd-generic.md)* *

参照して、* *[Linux のインストールに関する注記](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)* * Azure の Linux イメージの準備に関するヒントを表示します。

上のガイドに説明されている次の手順を行うと、Azure にアップロードする VHD ファイルの準備が整います。

<a id="connect"> </a>
## 手順 2. Azure への接続を準備する

.vhd ファイルをアップロードする前に、コンピューターと Azure のサブスクリプションの間にセキュリティで保護された接続を確立する必要があります。


### Azure CLI を使用する場合

最新の Azure CLI は既定でリソース マネージャーのデプロイ モデルになっているので、必ず次のコマンドを使用してクラシック デプロイ モデルにしてください。

        azure config mode asm  

次に、以下のログイン方法のいずれかを使用して Azure サブスクリプションに接続します。

ログインに Azure AD のメソッドを使用します。

1. Azure CLI ウィンドウを開きます。

2. 次のコマンドを入力します。

    `azure ログイン`

    メッセージが表示されたら、ユーザー名とパスワードを入力します。

**または**、PublishSettings ファイルを使用します。

1. Azure CLI ウィンドウを開きます。

2. 次のコマンドを入力します。

    `azure account download`

    このコマンドにより、ブラウザー ウィンドウが開き、Azure サブスクリプションの情報と証明書を含む .publishsettings ファイルが自動的にダウンロードされます。

3. .publishsettings ファイルを保存します。

4. 次のコマンドを入力します。

    `azure アカウントのインポート < ファイルへのパス >`

    ここで `< PathToFile >` .publishsettings ファイルへの完全なパスです。

    詳細については、「 [Azure CLI から Azure への接続](../xplat-cli-connect.md)します。


### Azure PowerShell を使用する場合

ログインに Azure AD のメソッドを使用します。

1. Azure PowerShell ウィンドウを開きます。

2. 次のコマンドを入力します。

    `Add-AzureAccount`

    メッセージが表示されたらは、組織のユーザー ID とパスワードを入力します。

**または**、代わりに PublishSettings ファイルを使用します。

1. Azure PowerShell ウィンドウを開きます。

2. 次のコマンドを入力します。

    `Get-AzurePublishSettingsFile`

    このコマンドにより、ブラウザー ウィンドウが開き、Azure サブスクリプションの情報と証明書を含む .publishsettings ファイルが自動的にダウンロードされます。

3. .publishsettings ファイルを保存します。

4. 次のコマンドを入力します。

    `Import-AzurePublishSettingsFile &lt;PathToFile&gt;`

    ここで `< PathToFile >` .publishsettings ファイルへの完全なパスです。

    詳細については、を参照してください [をインストールして、Azure PowerShell を構成する方法](powershell-install-configure.md)。

> [AZURE.NOTE] Azure サブスクリプションへのログインには、Azure CLI や Azure PowerShell のいずれかから、新しい Azure Active Directory メソッドを使用することをお勧めします。

<a id="upload"> </a>
## 手順 3. Azure にイメージをアップロードする

### Azure CLI を使用する場合

Azure CLI を使用して、イメージをアップロードします。 次のコマンドを使用してイメージをアップロードできます。

        azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>

### PowerShell を使用する場合

VHD ファイルをアップロードするストレージ アカウントが必要になります。 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成することができます。 ストレージの作成にアカウントを参照してください [ストレージ アカウントの作成](../storage-create-storage-account.md)

.vhd ファイルをアップロードするときは、BLOB ストレージ内であればどこにでも .vhd ファイルを置くことができます。 以下のコマンドの例では、**BlobStorageURL** は使用する予定のストレージ アカウントの URL であり、**YourImagesFolder** は BLOB ストレージ内でイメージを格納するコンテナーです。 **VHDName** に表示されるラベルは、 [Azure クラシック ポータル](http://manage.windowsazure.com) バーチャル ハード_ディスクを識別するためにします。 **PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。

前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

        Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

詳細については、次を参照してください。 [Add-azurevhd](https://msdn.microsoft.com/library/azure/dn495173.aspx)します。
> [AZURE.NOTE] [Azure Powershell 1.0 のプレビュー バージョン](https://azure.microsoft.com/blog/azps-1-0-pre/) 、クラシック モードとリソース マネージャーの配置モデル用のコマンドレットの処理方法が大幅に変更します。 この記事ではまだ Preview バージョンを使用していません。 



[step 1: prepare the image to be uploaded]: #prepimage 
[step 2: prepare the connection to azure]: #connect 
[step 3: upload the image to azure]: #upload 

