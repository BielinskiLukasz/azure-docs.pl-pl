<properties
    pageTitle="Azure 上で Windows を実行する仮想マシンの作成"
    description="Azure クラシック ポータルで Windows 仮想マシンを作成します。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2015"
    ms.author="cynthn"/>


# Windows を実行する仮想マシンを Azure クラシック ポータルで作成する

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [リソース マネージャーのデプロイ モデル](virtual-machines-windows-tutorial.md)します。

このチュートリアルでは、Azure クラシック ポータルで Azure 仮想マシン (VM) を簡単に作成する方法を示します。 例として Windows Server イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。 イメージの選択肢は、サブスクリプションによって異なります。 たとえば、デスクトップ イメージは MSDN サブスクリプション会員のみが使用できますです。

使用して Vm を作成することもできます。 [、独自のイメージ](virtual-machines-create-upload-vhd-windows-server.md)します。 この方法やその他の方法の詳細については、次を参照してください。 [Windows 仮想マシンを作成するさまざまな方法](virtual-machines-windows-choices-create-vm.md)します。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## ビデオ チュートリアル

以下は、このチュートリアルのガイドです。

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>仮想マシンを作成する方法

このセクションでは、Azure 旧ポータルの **[ギャラリーから]** オプションを使用して仮想マシンを作成する方法について説明します。 このオプションは、**[簡易作成]** オプションよりも多数の構成の選択肢があります。 たとえば、仮想マシンを仮想ネットワークに参加させる場合、**[ギャラリーから]** オプションを使用する必要があります。
> [AZURE.NOTE] さらに、より機能が豊富でカスタマイズも可能な Azure ポータルを使用すれば、仮想マシンの作成、強化された監視機能や診断機能の使用、Premium Storage の使用など、さまざまな操作を試すことができます。 この 2 つのポータルにある利用可能な仮想マシン構成オプションには、性質上、重複するものもありますが、まったく同じではありません。 たとえば、Azure ポータルを利用して、Premium Storage を使用する仮想マシンを構成します。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## 次のステップ

- 仮想マシンにログオンします。 手順については、次を参照してください。 [Windows Server を実行する仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)します。

- データを格納するディスクを接続します。 空のディスクと、データが含まれているディスクのどちらも接続できます。 手順については、次を参照してください。、 [データ ディスクのチュートリアルをアタッチ](storage-windows-attach-disk.md)します。





