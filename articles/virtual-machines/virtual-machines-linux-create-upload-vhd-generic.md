<properties
    pageTitle="Azure 上での Linux VHD の作成とアップロード"
    description="Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。"
    services="virtual-machines"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="szarkos"/>

# <a id="nonendorsed"> </a>動作保証外のディストリビューションに関する情報 #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**重要な**: Azure プラットフォーム SLA がある場合にのみ、Linux OS を実行する仮想マシンに適用されますの [動作保証済みディストリビューション](virtual-machines-../linux-endorsed-distributions.md) を使用します。 Azure イメージ ギャラリーにあるすべての Linux ディストリビューションは、必須の構成による動作保証済みディストリビューションです。

- [Azure での動作保証済み Linux ディストリビューション](virtual-machines-../linux-endorsed-distributions.md)
- [マイクロソフト Azure での Linux のイメージのサポート](http://support2.microsoft.com/kb/2941892)

Azure 上で動作するすべてのディストリビューションは、プラットフォーム上で適切に実行できるように、いくつかの前提条件を満たす必要があります。  この記事は決して包括的なものではありません。前提条件はディストリビューションによって異なるためです。次に示している条件をすべて満たす場合でも、プラットフォーム上で適切に動作するように Linux システムを微調整する必要があります。

いずれかで開始することをお勧めする理由から、この、 [動作保証済みディストリビューションの Azure Linux](../linux-endorsed-distributions.md) 可能な場合です。 次の記事では、Azure でサポートされる以下のさまざまな動作保証済み Linux ディストリビューションを準備する方法について説明します。

- **[CentOS ベースのディストリビューション](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES と openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**

以降では、Azure 上で Linux ディストリビューションを実行するための一般的なガイダンスについて、重点的に説明します。


## <a id="linuxinstall"> </a>Linux のインストールに関する一般的な注記 ##

- VHDX 形式はサポートされていません、Azure でのみ **固定 VHD**します。  Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。  LVM または [RAID](virtual-machines-linux-configure-raid.md) 優先する場合にデータ ディスク上で使用される可能性があります。

- さらに大きいサイズの VM では NUMA はサポートされていません。2.6.37 以下のバージョンの Linux カーネルにバグがあるためです。 この問題は、主に、アップストリームの Red Hat 2.6.32 カーネルを使用したディストリビューションに影響します。 Azure Linux エージェント (waagent) を手動でインストールすると、Linux カーネルの GRUB 構成で NUMA が自動的に無効になります。

- OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。  このことに関する詳細については、次の手順を参照してください。

- すべての VHD のサイズは 1 MB の倍数であることが必要です。


### Hyper-V を使用しない Linux のインストール ###

Linux インストーラーは、Linux が HYPER-V 環境を実行していることを検出しない限り、初期 RAM ディスク (initrd または initramfs) に HYPER-V 用のドライバーを含まない場合があります。  別の仮想化システム (Virtualbox、KVM など) を使用して Linux イメージを準備する場合は、少なくとも `hv_vmbus` と `hv_storvsc` のカーネル モジュールを初期 RAM ディスクで確実に使用できるように initrd の再構築が必要になる場合があります。  これは少なくとも、アップ ストリームの Red Hat ディストリビューションに基づくシステムの既知の問題です。

initrd または initramfs イメージの再構築のためのメカニズムは、ディストリビューションによって異なる場合があります。 適切な手順については、使用しているディストリビューションのドキュメントまたはサポートを参照してください。  次は、`mkinitrd` ユーティリティを使用して initrd を再構築する方法の 1 つの例です。

まず、既存の initrd イメージをバックアップします。

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

次に、`hv_vmbus` と `hv_storvsc` のカーネル モジュールを使用して initrd を再構築します。

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### VHD のサイズ変更 ###

Azure の VHD イメージは、1 MB に整列された仮想サイズが必要です。  通常、HYPER-V を使用して作成された VHD は既に正しく整列されている必要があります。  VHD が正しく整列されていないかどうかは、作成しようとするときに、次のようなエラー メッセージが表示される、 *イメージ* 、VHD から。

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

この問題を解決するのには、HYPER-V マネージャー コンソールを使用して VM のサイズを変更することができますか [RESIZE-VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell コマンドレット。  Windows 環境で実行していない場合は、qemu-img を使用して変換し (必要な場合)、VHD のサイズを変更することをお勧めします。

> [AZURE.NOTE] Qemu img バージョンでは既知のバグがあり、> = 不適切な書式の VHD に 2.2.1 します。 この問題は、qemu-img の今後のリリースで修正される予定です。  現時点では、qemu-img のバージョン 2.2.0 以前を使用することをお勧めします。 参照: https://bugs.launchpad.net/qemu/+bug/1490611


 1. `qemu-img` や `vbox-manage` などのツールを使用して直接 VHD のサイズを変更すると、VHD が起動できなくなる可能性があります。  そのため、最初に VHD を RAW ディスク イメージに変換することをお勧めします。  既に VM イメージを RAW ディスク イメージとして作成している場合は (KVM などの一部のハイパーバイザーでは既定)、この手順を省略できます。

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. ディスク イメージの必要なサイズを計算して、仮想サイズが 1 MB に整列されていることを確認します。  次の bash シェル スクリプトは、これを行うのに役立ちます。  このスクリプトは "`qemu-img info`" を使用してディスク イメージの仮想サイズを決定し、次の 1 MB までサイズを計算します。

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. 上記のスクリプトのセットとして $rounded_size を使用して RAW ディスクのサイズを変更します。

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. 次に、RAW ディスク を固定サイズの VHD に変換します。

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## Linux カーネルの要件 ##

Hyper-V および Azure 用の Linux Integration Services (LIS) ドライバーは、アップストリームの Linux カーネルに直接提供されています。 最新の Linux カーネル バージョン (つまり 3.x) を含む多くのディストリビューションでこれらのドライバーが含まれています。含まれていない場合は、これらのドライバーのバックポートされたバージョンがカーネルと共に提供されます。  これらのドライバーは常に更新されているで新しい修正と機能、アップ ストリームのカーネルのため実行することが推奨可能であれば、 [動作保証済みディストリビューション](../linux-endorsed-distributions.md) これらの修正と更新プログラムに含まれています。

Red Hat Enterprise Linux バージョンの変形を実行するかどうかは **6.0-6.3**, 、HYPER-V の最新の LIS ドライバーをインストールする必要があります。 ドライバーが含まれる [この場所で](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)します。 Rhel **6.4 以上** (および派生版)、LIS ドライバーがカーネルに含まれているし、ため追加のインストール パッケージは必要ありません Azure でこれらのシステムを実行します。

カスタム カーネルが必要な場合は、最新のカーネル バージョンを使用して (つまり、勧め **3.8+**). 独自のカーネルを維持するディストリビューションまたはベンダーの場合は、アップストリームのカーネルの LIS ドライバーをカスタム カーネルに定期的にバックポートするために多少の手間がかかります。  比較的最新のカーネル バージョンを既に実行している場合でも、LIS ドライバーのアップストリームの修正を常に追跡し、必要に応じてパックポートすることを強くお勧めします。 LIS ドライバーのソース ファイルの場所は、 [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) Linux カーネルのソース ツリー内のファイル。

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

最低限、以下のパッチが適用されていない場合は、Azure 上で問題が発生することがわかっているため、これらのパッチをカーネルに含める必要があります。 この一覧は、すべてのディストリビューションに対して包括的でも完全でもありません。

- [ata_piix: defer disks to the Hyper-V drivers by default (既定ではディスクは Hyper-V ドライバーによって決定される)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: RESET パスの転送中パケットを正しく考慮する](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc: WRITE_SAME の使用を回避する](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc: RAID と仮想ホスト アダプター ドライバーの WRITE SAME を無効にする](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: NULL ポインターの逆参照の修正](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc: リング バッファーのエラーにより I/O の凍結が発生する可能性がある](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)


## Azure Linux エージェント ##

 [Azure Linux エージェント](virtual-machines-linux-agent-user-guide.md) Azure で Linux 仮想マシンを適切にプロビジョニングする (waagent) が必要です。 最新バージョン、ファイルの問題を取得したりでプル リクエストの提出、 [Linux Agent GitHub リポジトリ](https://github.com/Azure/WALinuxAgent)します。

- Linux エージェントは、Apache 2.0 ライセンス下でリリースされています。 数多くのディストリビューションでは、このエージェント用の RPM パッケージや deb パッケージを既に提供しているため、場合によっては、ほとんど労力をかけずにこのエージェントをインストールし、更新することができます。

- Azure Linux エージェントには、Python v2.6 以上が必要です。

- このエージェントでは、python-pyasn1 モジュールも必要です。 ほとんどのディストリビューションでは、インストール可能な個別のパッケージとして提供しています。

- Azure Linux エージェントは NetworkManager と互換性がない場合があります。 ディストリビューションによって提供される多くの RPM/Deb パッケージでは、NetworkManager が waagent パッケージに対する競合として構成されるため、Linux エージェント パッケージをインストールすると NetworkManager がアンインストールされます。


## Linux システムの一般的な要件 ##

- GRUB または GRUB2 でカーネルのブート行を変更して次のパラメーターを含めます。 これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

    そのほかにもすることが推奨 *削除* 存在している場合は、次のパラメーター。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。

    必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

- Azure Linux エージェントをインストールします。

    Azure Linux エージェントは、Azure で Linux イメージをプロビジョニングするために必要です。  多くのディストリビューションでは、このエージェントを RPM または Deb パッケージとして提供しています (パッケージは、通常 'WALinuxAgent' または 'walinuxagent' と呼ばれます)。  エージェントが次の手順に従って、手動でインストールするもできる、 [Linux エージェント ガイド](virtual-machines-linux-agent-user-guide.md)します。

- SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。

- OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは、 *一時* ディスクで空になる VM のプロビジョニングが解除されるとします。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- /etc/sudoers で、次の行を削除するかコメント アウトする必要があります (ある場合)。

        Defaults targetpw
        ALL    ALL=(ALL) ALL

- 最後の手順として、次のコマンドを実行して仮想マシンをプロビジョニング解除します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

- その後、仮想マシンをシャットダウンし、Azure に VHD をアップロードする必要があります。

