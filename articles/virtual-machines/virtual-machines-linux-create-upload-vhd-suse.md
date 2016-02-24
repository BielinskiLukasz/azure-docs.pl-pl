<properties
    pageTitle="Azure 上での SUSE Linux VHD の作成とアップロード"
    description="SUSE Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。"
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
    ms.date="11/13/2015"
    ms.author="szark"/>

# Azure 用の SLES または openSUSE 仮想マシンの準備

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

##前提条件##

この記事では、既に SUSE または openSUSE Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。 .vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。 手順については、次を参照してください。 [Hyper-v 役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)します。

 - [SUSE Studio](http://www.susestudio.com) 簡単に作成、管理できる Azure および HYPER-V 用の openSUSE イメージ。 これは、独自の SUSE イメージと openSUSE イメージをカスタマイズするアプローチとして推奨されます。 SUSE Studio ギャラリーにある次の公式イメージをダウンロードまたは SUSE Studio アカウントに複製できます。

  - [SUSE Studio ギャラリーの Azure 向け SLES 11 SP3](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)
  - [SUSE Studio ギャラリーの Azure 向け openSUSE 13.1](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)


- 独自の VHD を作成する代わりに、SUSE も公開によって影響を受けます (サブスクリプション持ち込み) イメージ用の SLES [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007)します。


**SLES/openSUSE のインストールに関する一般的な注記**

- VHDX 形式はサポートされていません、Azure でのみ **固定 VHD**します。  Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。  LVM または [RAID](virtual-machines-linux-configure-raid.md) 優先する場合にデータ ディスク上で使用される可能性があります。

- OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。  このことに関する詳細については、次の手順を参照してください。

- すべての VHD のサイズは 1 MB の倍数であることが必要です。


## SUSE Linux Enterprise Server 11 SP3 を準備します。 ##

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. クリックして **接続** を仮想マシンのウィンドウを開きます。

3. SUSE Linux Enterprise システムを登録すると、更新プログラムをダウンロードしてパッケージをインストールできるようになります。

4. 最新の更新ログラムでシステムを更新します。

        # sudo zypper update

5. SLES リポジトリから Azure Linux エージェントをインストールします。

        # sudo zypper install WALinuxAgent

6. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

7.  "/etc/sysconfig/network/dhcp" ファイルを編集して、次のように `DHCLIENT_SET_HOSTNAME` パラメーターを変更することをお勧めします。

        DHCLIENT_SET_HOSTNAME="no"

8.  "/etc/sudoers" で、次の行をコメント アウトするか削除する必要があります (ある場合)。

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。

10. OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは、 *一時* ディスクで空になる VM のプロビジョニングが解除されるとします。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. クリックして **アクションには、シャット ダウン]-> [** Hyper V マネージャーにします。 これで、Linux VHD を Azure にアップロードする準備が整いました。


----------

## openSUSE 13.1 以上の準備 ##

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. クリックして **接続** を仮想マシンのウィンドウを開く

3. シェルでコマンド "`zypper lr`" を実行します。 このコマンドによって次のような出力が返されます (バージョン番号は異なる場合があります)。

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    この場合、リポジトリは予期したとおりに構成されているため、調整は不要です。

    コマンドは、"No repositories defined..."が返された場合 その場合は、次のコマンドを実行してこれらのリポジトリを追加します。

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    "`zypper lr`" コマンドをもう一度実行してリポジトリが追加されたことを確認できます。 更新したリポジトリのいずれかが有効になっていない場合は、次のコマンドを使用して有効にします。

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. カーネルを最新のバージョンに更新します。

        # sudo zypper up kernel-default

    または、次のように、すべての最新のパッチでシステムを更新します。

        # sudo zypper update

5.  Azure Linux エージェントをインストールします。

        # sudo zypper install WALinuxAgent

6.  GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 また、カーネルのブート行に次のパラメーターがある場合は削除します。

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  "/etc/sysconfig/network/dhcp" ファイルを編集して、次のように `DHCLIENT_SET_HOSTNAME` パラメーターを変更することをお勧めします。

        DHCLIENT_SET_HOSTNAME="no"

8.  **重要:** "/etc/sudoers"でのコメント アウト、または存在する場合は、次の行を削除します。

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。

10. OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは、 *一時* ディスクで空になる VM のプロビジョニングが解除されるとします。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. 起動時に Azure Linux エージェントが実行されるようにします。

        # sudo systemctl enable waagent.service

13. クリックして **アクションには、シャット ダウン]-> [** Hyper V マネージャーにします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

