<properties
    pageTitle="ディスクと VHD について |Microsoft Azure"
    description="Azure での仮想マシン用のディスクと VHD の基本について説明します。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="cynthn"/>

# Azure 仮想マシン用のディスクと VHD について

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Azure の仮想マシンでは、その他のコンピューターとまったく同じように、オペレーティング システム、アプリケーション、およびデータを格納するための場所としてディスクを使用します。 Azure のすべての仮想マシンには、オペレーティング システム ディスクと一時ディスクの少なくとも 2 つのディスクがあります。 オペレーティング システム ディスクはイメージから作成されます。オペレーティング システム ディスクとイメージの両方は実際に仮想ハード ディスク (VHD) であり、Azure のストレージ アカウントに格納されます。 仮想マシンでは 1 つ以上のデータ ディスクも保持することができ、これらも VHD として格納されます。

## オペレーティング システム ディスク

どの仮想マシンにも 1 つのオペレーティング システム ディスクが取り付けられています。 これは SATA ドライブとして登録され、C ドライブのラベルが付けられます。 このディスクの最大容量は 1023 ギガバイトです。 Azure がオペレーティング システム ディスクを作成する際に、高い耐久性を保つためにディスクのコピーを 3 つ作成します。 さらに、geo レプリケーション用に仮想マシンを構成する場合、400 マイル以上離れた別のサイトにも VHD を複製します。

##一時ディスク

一時ディスクが自動的に作成されます。 Windows 仮想マシンでは、このディスクは D ドライブのラベルが付けられます。 Linux 仮想マシンでは、ディスクは通常 /dev/sdb であり、Azure Linux エージェントにより書式設定され /mnt/resource にマウントされます。

>[AZURE.WARNING] 一時ディスクにデータを格納しないでください。 これはアプリケーションとプロセスに一時的なストレージを提供し、ページ ファイルやスワップ ファイルなどのデータのみを格納するためのものです。 Windows 仮想マシンのためにディスクを再マップするには、「 [Windows 一時ディスクのドライブ文字を変更](virtual-machines-windows-change-drive-letter.md)します。

## データ ディスク

データ ディスクは仮想マシンに取り付けられる VHD であり、ユーザーが保存しておく必要があるアプリケーションなどのデータを格納するためのものです。 データ ディスクは SCSI ドライブとして登録され、ユーザーが選択した文字のラベルが付けられます。  各ディスクの最大容量は 1023 ギガバイトです。 仮想マシンのサイズにより、そこに取り付けできるデータ ディスクの数と、ディスクをホストするために使用できるストレージの種類が決まります。

    For more details about virtual machines capacities, see [Sizes for virtual machines](virtual-machines-size-specs.md).

ユーザーがイメージから仮想マシンを作成するときに、Azure はオペレーティング システム ディスクを作成します。 ユーザーがデータ ディスクを含むイメージを使用する場合、Azure は仮想マシンの作成時にデータ ディスクも作成します。 (Azure、パートナー、またはユーザーが提供するイメージを使用することができます。)それ以外の場合は、仮想マシンを作成した後にデータ ディスクを追加してください。

仮想マシンにディスクを「取り付ける」ことで、ユーザーは仮想マシンにデータ ディスクをいつでも追加できます。 ユーザーがアップロードした VHD やストレージ アカウントにコピーした VHD を使用できます。または Azure が作成する VHD も使用できます。 データ ディスクを取り付けると、ストレージ アカウントからの VHD ファイルが仮想マシンに関連付けられます。VHD で「リース」を設定しておけば、仮想マシンに取り付けている間にストレージから削除することはできなくなります。

## VHD について

Azure で使用される VHD は .vhd ファイルです。Azure では Standard または Premium Storage アカウントでページ BLOB としてこれらを格納します。 (Premium Storage は特定の地域で使用できます。)ページ blob についての詳細については、「 [ブロック blob とページ blob について](https://msdn.microsoft.com/library/ee691964.aspx)います。 Premium storage についての詳細については、「 [Premium storage: Azure の仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage-premium-storage-preview-portal.md)します。

Azure 以外では、仮想ハード ディスクには VHD フォーマットまたは VHDX フォーマットを使用でき、 固定、動的拡張、差分にも対応しています。 Azure は VHD フォーマットの固定ディスクをサポートしています。 固定フォーマットの場合、ファイル内で論理ディスクがリニアにレイアウトされるため、ディスク オフセット X は BLOB オフセット X に格納されます。BLOB 末尾の小さなフッターに、VHD のプロパティが記述されます。 固定フォーマットの場合、ほとんどのディスクに大きな未使用の範囲が含まれるため、容量が無駄になることがよくあります。 しかし、Azure では .vhd ファイルをスパース フォーマットで格納するため、固定ディスクのメリットと動的ディスクのメリットを同時に享受できます。 詳細については、次を参照してください。 [バーチャル ハード_ディスクの概要](https://technet.microsoft.com/library/dd979539.aspx)します。

ディスクやイメージを作成する際にソースとして使用する場合、Azure でのすべての .vhd ファイルは読み出し専用です。 ユーザーがディスクやイメージを作成するときに Azure は .vhd ファイルのコピーを作成します。 これらのコピーは、ユーザーが VHD をどのように使用するかに応じて、読み出し専用または読み書き可能にすることができます。

 ユーザーがイメージから仮想マシンを作成するときに、Azure はソースの .vhd ファイルのコピーである仮想マシンのディスクを作成します。 Azure では、誤って削除されるのを防ぐために、イメージ、オペレーティング システム ディスク、またはデータ ディスクを作成する際に使用されるソースの .vhd ファイルにリースを設定します。

ディスクやイメージを削除してリースを解除しなければ、ソースの .vhd ファイルを削除できません。 仮想マシンがオペレーティング システム ディスクとして使用中の .vhd ファイルを削除する場合、仮想マシンを削除し、関連付けられたすべてのディスクを削除することで、ユーザーは仮想マシン、オペレーティング システム ディスク、およびソースの .vhd ファイルをすべて一度に削除することができます。 ただし、データ ディスクのソースである .vhd ファイルを削除するには、いくつかの手順を決められた順序で行う必要があります。つまり、仮想マシンからディスクを取り外し、ディスクを削除し、続いて .vhd ファイルを削除します。

>[AZURE.WARNING] 記憶域からソース .vhd ファイルを削除してストレージ アカウントを削除するか、マイクロソフトは、そのデータを回復できません。

## 次のステップ

Linux 仮想マシン

-  [ディスクを取り付けて、使用する準備を整える](virtual-machines-linux-how-to-attach-disk.md)
-  [Linux 仮想マシンをキャプチャする](virtual-machines-linux-capture-image.md)
-  [ディスクを取り外す](virtual-machines-linux-how-to-detach-disk.md)

Windows 仮想マシン

-  [ディスクを取り付けて、使用する準備を整える](storage-windows-attach-disk.md)
- [Windows 一時ディスクのドライブ文字を変更する](virtual-machines-windows-change-drive-letter.md)
-  [Windows 仮想マシンをキャプチャする](virtual-machines-capture-image-windows-server.md)
-  [ディスクを取り外す](storage-windows-detach-disk.md)

