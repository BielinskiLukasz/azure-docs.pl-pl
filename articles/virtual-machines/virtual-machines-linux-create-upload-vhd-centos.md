<properties
    pageTitle="Azure 上での CentOS ベースの Linux VHD の作成とアップロード"
    description="CentOS ベースの Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。"
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
    ms.date="10/22/2015"
    ms.author="szarkos"/>

# Azure 用の CentOS ベースの仮想マシンの準備


- [Azure 用の CentOS 6.x 仮想マシンの準備](#centos6)
- [Azure 用の CentOS 7.0 以上の仮想マシンの準備](#centos7)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

##前提条件##

この記事では、既に CentOS (または同様な派生版) Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。 .vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。 手順については、次を参照してください。 [Hyper-v 役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)します。


**CentOS のインストールに関する注記**

- VHDX 形式はサポートされていません、Azure でのみ **固定 VHD**します。  Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。  LVM または [RAID](virtual-machines-linux-configure-raid.md) 優先する場合にデータ ディスク上で使用される可能性があります。

- さらに大きいサイズの VM では NUMA はサポートされていません。2.6.37 以下のバージョンの Linux カーネルにバグがあるためです。 この問題は、主に、アップストリームの Red Hat 2.6.32 カーネルを使用したディストリビューションに影響します。 Azure Linux エージェント (waagent) を手動でインストールすると、Linux カーネルの GRUB 構成で NUMA が自動的に無効になります。 このことに関する詳細については、次の手順を参照してください。

- OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。  このことに関する詳細については、次の手順を参照してください。

- すべての VHD のサイズは 1 MB の倍数であることが必要です。


## <a id="centos6"></a>CentOS 6.x ##

1. Hyper-V マネージャーで仮想マシンを選択します。

2. クリックして **接続** を仮想マシンのコンソール ウィンドウを開きます。

3. 次のコマンドを実行して NetworkManager をアンインストールします。

        # sudo rpm -e --nodeps NetworkManager

    **注:** このコマンドは失敗してエラー メッセージ、パッケージが既にインストールされていない場合。 これは予期されることです。

4.  という名前のファイルを作成する **ネットワーク** で、 `/etc/sysconfig/` 、次のテキストを格納するディレクトリ。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  という名前のファイルを作成する **ifcfg-eth0** で、 `/etc/sysconfig/network-scripts/` 、次のテキストを格納するディレクトリ。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/


7. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on


8. **CentOS 6.3 のみ**: Linux Integration Services (LIS) 用のドライバーをインストールします。

    **重要: この手順は CentOS 6.3 以下にのみ有効です。**  CentOS 6.4 以上で、Linux Integration Services は *、標準のカーネル内で既に使用可能な*です。

    - インストールの指示に従って、 [LIS のダウンロード ページ](https://www.microsoft.com/en-us/download/details.aspx?id=46842) し、イメージに RPM をインストールします。  


9. 次のコマンドを実行して python-pyasn1 パッケージをインストールします。

        # sudo yum install python-pyasn1

10. Azure データセンター内にホストされている OpenLogic のミラーを使用する場合は、/etc/yum.repos.d/CentOS-Base.repo ファイルを次のリポジトリに置き換えます。  これも追加されますが、 **[openlogic]** を Azure Linux エージェント用のパッケージを含むリポジトリ。

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **注:** このガイドの残りの部分を使用するには、少なくとも [openlogic] リポジトリは、以下の Azure Linux エージェントのインストールに使用される、前提とします。


11. /etc/yum.conf ファイルに次の行を追加します。

        http_caching=packages

     **CentOS 6.3 のみで** 、次の行を追加します。

        exclude=kernel*

12. "/etc/yum/pluginconf.d/fastestmirror.conf" ファイルの `[main]` で次のように入力して、yum モジュール "fastestmirror" を無効にします。

        set enabled=0

13. 次のコマンドを実行して、現在の yum メタデータをクリアします。

        # yum clean all

14. **CentOS 6.3 のみ**, 、次のコマンドを使用してカーネルを更新します。

        # sudo yum --disableexcludes=all install kernel

15. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 これにより、CentOS 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。

    そのほかにもすることが推奨 *削除* 、次のパラメーター。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。

    必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。


16. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。

17. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent

    手順 2. で説明したように NetworkManager パッケージおよび NetworkManager-gnome パッケージがまだ削除されていない場合、WALinuxAgent パッケージをインストールすると、これらのパッケージが削除されることに注意してください。

18. OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは、 *一時* ディスクで空になる VM のプロビジョニングが解除されるとします。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. クリックして **アクションには、シャット ダウン]-> [** Hyper V マネージャーにします。 これで、Linux VHD を Azure にアップロードする準備が整いました。


----------


## <a id="centos7"></a>CentOS 7.0 以上 ##

**CentOS 7 (および同様な派生版) への変更**

Azure 用の CentOS 7 仮想マシンを準備する手順は、CentOS 6 の場合とほとんど同じですが、次のように、重要な違いがいくつかあります。

 - NetworkManager パッケージが、Azure Linux エージェントと競合しなくなりました。 このパッケージは既定でインストールされます。このパッケージを削除しないことをお勧めします。
 - GRUB2 が、既定のブートローダーとして使用されるようになったため、カーネル パラメーターの編集手順が変更されました (以下を参照)。
 - XFS が既定のファイル システムになりました。 必要に応じて、引き続き ext4 ファイル システムを使用できます。


**構成の手順**

1. Hyper-V マネージャーで仮想マシンを選択します。

2. クリックして **接続** を仮想マシンのコンソール ウィンドウを開きます。

3.  という名前のファイルを作成する **ネットワーク** で、 `/etc/sysconfig/` 、次のテキストを格納するディレクトリ。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  という名前のファイルを作成する **ifcfg-eth0** で、 `/etc/sysconfig/network-scripts/` 、次のテキストを格納するディレクトリ。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.  udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

7. 次のコマンドを実行して python-pyasn1 パッケージをインストールします。

        # sudo yum install python-pyasn1

8. Azure データセンター内にホストされている OpenLogic のミラーを使用する場合は、/etc/yum.repos.d/CentOS-Base.repo ファイルを次のリポジトリに置き換えます。  これも追加されますが、 **[openlogic]** を Azure Linux エージェント用のパッケージを含むリポジトリ。

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **注:** このガイドの残りの部分を使用するには、少なくとも [openlogic] リポジトリは、以下の Azure Linux エージェントのインストールに使用される、前提とします。

9.  次のコマンドを実行して、現在の yum メタデータをクリアし、更新をインストールします。

        # sudo yum clean all
        # sudo yum -y update

10. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/etc/default/grub" を開き、次のように、`GRUB_CMDLINE_LINUX` パラメーターを編集します。

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 そのほかにもすることが推奨 *削除* 、次のパラメーター。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。

    必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

11. 上記のとおりに "/etc/default/grub" の編集を終了したら、次のコマンドを実行して GRUB 構成をリビルドします。

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。

13. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent

14. OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは、 *一時* ディスクで空になる VM のプロビジョニングが解除されるとします。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. クリックして **アクションには、シャット ダウン]-> [** Hyper V マネージャーにします。 これで、Linux VHD を Azure にアップロードする準備が整いました。


