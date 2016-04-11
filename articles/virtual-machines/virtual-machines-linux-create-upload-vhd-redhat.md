<properties 
    pageTitle="Azure で使用するための Red Hat Enterprise Linux VHD の作成とアップロード" 
    description="Red Hat Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。" 
    services="virtual-machines" 
    documentationCenter="" 
    authors="SuperScottz" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="virtual-machines" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/23/2015" 
    ms.author="mingzhan"/>


# Azure 用の Red Hat ベースの仮想マシンの準備
この記事では、Red Hat Enterprise Linux (RHEL) の仮想マシンを Azure で使用できるように準備する方法について説明します。  この記事で取り上げる RHEL のバージョンは 6.7、7.1、および 7.2 です。準備対象のハイパーバイザーは、Hyper-V、KVM、および VMWare です。  Red Hat のクラウドへのアクセスのプログラムに参加するための資格要件の詳細については、次を参照してください。 [Red Hat のクラウドへのアクセスの web サイト](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) と [Azure で実行されている RHEL](https://access.redhat.com/articles/1989673)します。 




##Hyper-V マネージャーからのイメージの準備 
###前提条件
このセクションでは、Red Hat の Web サイトから取得した ISO ファイルの RHEL イメージが仮想ハード ディスク (VHD) に既にインストールされていることを前提としています。 HYPER-V マネージャーを使用して、オペレーティング システム イメージをインストールする方法の詳細については、次を参照してください。 [Hyper-v 役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)します。 

**RHEL のインストールに関する注記**

- 新しい VHDX 形式は、Azure ではサポートされていません。 Hyper-V マネージャーまたは convert-vhd PowerShell コマンドレットを使用して、ディスクを VHD 形式に変換できます。

- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、LVM または RAID をデータ ディスク上で使用できます。

- OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。 このことに関する詳細については、次の手順を参照してください。

- すべての VHD のサイズは 1 MB の倍数であることが必要です。

- qemu-img を使用してディスク イメージを VHD 形式に変換する場合、qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は、qemu-img の今後のリリースで修正される予定です。  現時点では、qemu-img のバージョン 2.2.0 以前を使用することをお勧めします。


###RHEL 6.7

1.  Hyper-V マネージャーで仮想マシンを選択します。

2.  クリックして **接続** を仮想マシンのコンソール ウィンドウを開きます。

3.  次のコマンドを実行して NetworkManager をアンインストールします。

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

7.  次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

8.  RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.  WALinuxAgent パッケージ `WALinuxAgent-<version>` が Fedora EPEL 6 リポジトリにプッシュされました。 次のコマンドを実行して epel リポジトリを有効にします。

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

10. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 これにより、RHEL 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。

    上記のほかに、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。

    必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

11. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 通常これが既定です。 /etc/ssh/sshd_config を変更して、次の行を含めます。

        ClientAliveInterval 180

12. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    **注:** WALinuxAgent パッケージをインストールすることは、NetworkManager networkmanager-gnome パッケージが既に削除されていない場合の手順に従ってで手順 2. および削除します。

13. OS ディスクにスワップ領域を作成しないでください。
Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # sudo subscription-manager unregister

15. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. クリックして **アクションには、シャット ダウン]-> [** Hyper V マネージャーにします。 これで、Linux VHD を Azure にアップロードする準備が整いました。
 

###RHEL 7.1/7.2

1.  Hyper-V マネージャーで仮想マシンを選択します。

2.  [接続] をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

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

5.  次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

6.  RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行う `/etc/default/grub` テキスト エディターで編集、 **GRUB_CMDLINE_LINUX** 例については、パラメーター。

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 上記のほかに、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。
    必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

8.  上記のとおりに `/etc/default/grub` の編集を終了したら、次のコマンドを実行して GRUB 構成をリビルドします。

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.  SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 通常これが既定です。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

        ClientAliveInterval 180

10. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Fedora EPEL 6 リポジトリにプッシュされました。 次のコマンドを実行して epel リポジトリを有効にします。

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

11. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service 

12. OS ディスクにスワップ領域を作成しないでください。 Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux Agent のインストール後に (前の手順を参照)、`/etc/waagent.conf` にある次のパラメーターを正確に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. サブスクリプションを登録解除する場合は、次のコマンドを実行します。

        # sudo subscription-manager unregister

14. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。
        
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. クリックして **アクションには、シャット ダウン]-> [** Hyper V マネージャーにします。 これで、Linux VHD を Azure にアップロードする準備が整いました。
 


##KVM からのイメージの準備 
###RHEL 6.7

1.  Red Hat の Web サイトからは、RHEL 6.7 の KVM イメージをダウンロードします。

2.  ルート パスワードの設定

    暗号化されたパスワードを生成し、コマンドの出力をコピーします。

        # openssl passwd -1 changeme
    guestfish でルート パスワードを設定します。
       
        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit
    Root ユーザーからの 2 番目のフィールドを変更する"!!" 暗号化されたパスワードです。

3.  Kvm qcow2 イメージから仮想マシンを作成、ディスクの種類を設定 **qcow2**, 、仮想ネットワーク インターフェイス デバイスのモデルを設定 **virtio**します。 仮想マシンを起動し、ルートとしてログインします。

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

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # chkconfig network on

8.  RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # subscription-manager register –auto-attach --username=XXX --password=XXX

9.  GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 これにより、RHEL 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。

    上記のほかに、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。
    必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

10. cloud-init をアンインストールします。

        # yum remove cloud-init

11. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。
 
        # chkconfig sshd on

    /etc/ssh/sshd_config を変更して、次の行を含めます。

        PasswordAuthentication yes
        ClientAliveInterval 180

    sshd を起動します。

        # service sshd restart

12. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Fedora EPEL 6 リポジトリにプッシュされました。 次のコマンドを実行して epel リポジトリを有効にします。

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

13. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # yum install WALinuxAgent
        # chkconfig waagent on

14. Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux エージェントをインストールした後 (前の手順を参照)、次のパラメーターを変更 **/etc/waagent.conf** 適切にします。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。
        
        # subscription-manager unregister

16. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. KVM で VM をシャット ダウンします。

18. qcow2 イメージを vhd 形式に変換します。
    まず、イメージを未加工の形式に変換します。
         
         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    未加工のイメージのサイズが 1 MB になっていることを確認します。そうでない場合は、1 MB になるようにサイズの端数を切り上げます。
         # MB = $((1024*1024))
        # サイズ = $(qemu img 情報 -f raw - json の出力"rhel 6.7.raw"| \
                 gawk ' と一致 ($0/「仮想サイズ」: ([0-9] +)、/、val) {val[1]}') を印刷します。
        # rounded_size$ = ((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    未フォーマット ディスクを固定サイズの vhd に変換します。

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


###RHEL 7.1/7.2

1.  Red Hat の Web サイトから RHEL 7.1 (または 7.2) の KVM イメージをダウンロードします。次の例では RHEL 7.1 を使用します。

2.  ルート パスワードの設定

    暗号化されたパスワードを生成し、コマンドの出力をコピーします。

        # openssl passwd -1 changeme

    guestfish でルート パスワードを設定します。

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Root ユーザーからの 2 番目のフィールドを変更する"!!" 暗号化されたパスワード

3.  Kvm qcow2 イメージから仮想マシンを作成、ディスクの種類を設定 **qcow2**, 、仮想ネットワーク インターフェイス デバイスのモデルを設定 **virtio**します。 仮想マシンを起動し、ルートとしてログインします。

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

6.  次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # chkconfig network on

7.  RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # subscription-manager register –auto-attach --username=XXX --password=XXX

8.  GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行う `/etc/default/grub` テキスト エディターで編集、 **GRUB_CMDLINE_LINUX** 例については、パラメーター。

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 上記のほかに、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。
    必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

9.  上記のとおりに `/etc/default/grub` の編集を終了したら、次のコマンドを実行して GRUB 構成をリビルドします。

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. cloud-init をアンインストールします。

        # yum remove cloud-init

11. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 

        # systemctl enable sshd

    /etc/ssh/sshd_config を変更して、次の行を含めます。

        PasswordAuthentication yes
        ClientAliveInterval 180

    sshd を起動します。

        systemctl restart sshd  

12. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Fedora EPEL 6 リポジトリにプッシュされました。 次のコマンドを実行して epel リポジトリを有効にします。

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

13. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # yum install WALinuxAgent

    waagent サービスを有効にします。

        # systemctl enable waagent.service

14. OS ディスクにスワップ領域を作成しないでください。 Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux Agent のインストール後に (前の手順を参照)、`/etc/waagent.conf` にある次のパラメーターを正確に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # subscription-manager unregister

16. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. KVM で仮想マシンをシャットダウンします。

18. qcow2 イメージを vhd 形式に変換します。

    まず、イメージを未加工の形式に変換します。

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    未加工のイメージのサイズが 1 MB になっていることを確認します。そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    未フォーマット ディスクを固定サイズの vhd に変換します。

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##VMWare からのイメージの準備
###前提条件
このセクションでは、VMWare に RHEL の仮想マシンが既にインストールされていると仮定します。 VMWare のオペレーティング システムをインストールする方法の詳細については、「 [VMWare のゲスト オペレーティング システムのインストール ガイド](http://partnerweb.vmware.com/GOSIG/home.html)します。
 
- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、LVM または RAID をデータ ディスク上で使用できます。

- OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。 このことに関する詳細については、次の手順を参照してください。

- バーチャル ハード_ディスクを作成するときに選択 **ストアの仮想ディスク ファイルを 1 つとして**します。

###RHEL 6.7
1.  次のコマンドを実行して NetworkManager をアンインストールします。

         # sudo rpm -e --nodeps NetworkManager

    **注:** このコマンドは失敗してエラー メッセージ、パッケージが既にインストールされていない場合。 これは予期されることです。

2.  という名前のファイルを作成する **ネットワーク** /etc/etc/sysconfig/ディレクトリに、次のテキストが含まれています。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.  という名前のファイルを作成 **ifcfg-eth0** /etc/sysconfig/network-scripts/ディレクトリに、次のテキストが含まれています。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.  udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.  次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

6.  RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  WALinuxAgent パッケージ `WALinuxAgent-<version>` が Fedora EPEL 6 リポジトリにプッシュされました。 次のコマンドを実行して epel リポジトリを有効にします。

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

8.  GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 これにより、RHEL 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。
    上記のほかに、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。
    必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

9.  SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 通常これが既定です。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

        ClientAliveInterval 180

10. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

11. OS ディスクにスワップ領域を作成しないでください。
    
    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux Agent のインストール後に (前の手順を参照)、`/etc/waagent.conf` にある次のパラメーターを正確に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # sudo subscription-manager unregister

13. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision 
        # export HISTSIZE=0
        # logout

14. VM をシャット ダウンし、VMDK ファイルを VHD ファイルに変換します。

    まず、イメージを未加工の形式に変換します。

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    未加工のイメージのサイズが 1 MB になっていることを確認します。そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    未フォーマット ディスクを固定サイズの vhd に変換します。

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

###RHEL 7.1/7.2

1.  という名前のファイルを作成する **ネットワーク** /etc/etc/sysconfig/ディレクトリに、次のテキストが含まれています。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.  という名前のファイルを作成 **ifcfg-eth0** /etc/sysconfig/network-scripts/ディレクトリに、次のテキストが含まれています。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.  次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

4.  RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.  GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行う `/etc/default/grub` テキスト エディターで編集、 **GRUB_CMDLINE_LINUX** 例については、パラメーター。

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 上記のほかに、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。
    必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

6.  上記のとおりに `/etc/default/grub` の編集を終了したら、次のコマンドを実行して GRUB 構成をリビルドします。

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.  Hyper-V モジュールを initramfs に追加します。 

    `/etc/dracut.conf` を編集して、コンテンツを追加します。

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Initramfs を再構築します。

        # dracut –f -v

8.  SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 通常これが既定です。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

        ClientAliveInterval 180

9.  WALinuxAgent パッケージ `WALinuxAgent-<version>` が Fedora EPEL 6 リポジトリにプッシュされました。 次のコマンドを実行して epel リポジトリを有効にします。


        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

10. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11. OS ディスクにスワップ領域を作成しないでください。 Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux Agent のインストール後に (前の手順を参照)、`/etc/waagent.conf` にある次のパラメーターを正確に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. サブスクリプションを登録解除する場合は、次のコマンドを実行します。

        # sudo subscription-manager unregister

13. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. VM をシャットダウンし、VMDK ファイルを VHD 形式に変換します。

    まず、イメージを未加工の形式に変換します。

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    未加工のイメージのサイズが 1 MB になっていることを確認します。そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    未フォーマット ディスクを固定サイズの vhd に変換します。

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##kickstart ファイルを使用して ISO から自動的に準備する
###RHEL 7.1/7.2

1.  以下のコンテンツを含む kickstart ファイルを作成し、ファイルを保存します。 今すぐインストールに関する詳細についてを参照してください、 [完備インストール ガイド](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)します。


        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        # Primary Fedora repo
        repo --name="epel7" --baseurl="http://dl.fedoraproject.org/pub/epel/7/x86_64/"

        %packages
        @base
        @console-internet
        chrony
        sudo
        python-pyasn1
        parted
        ntfsprogs
        WALinuxAgent
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

 

2.  インストール システムから到達可能な場所に kickstart ファイルを置きます。 
 
3.  Hyper-V マネージャーで新しい VM を作成します。  **仮想ハード_ディスクの接続** ] ページで、[ **バーチャル ハード_ディスクを後で接続**, 、新しいバーチャル マシン ウィザードを完了します。

4.  VM 設定を開きます。

    a.  新しいバーチャル ハード ディスクを VM に接続を選択してください **VHD 形式** と **固定サイズ**します。
    
    b.  インストール ISO を DVD ドライブに接続します。

    c.  CD から起動するように BIOS を設定します。

5.  インストール ガイドが表示されたら、仮想マシンを起動キーを押して **タブ** ブート オプションを構成します。

6.  Enter `inst.ks=<the location of the Kickstart file>` ブート オプションとそのキーを押しての最後に **Enter**します。

7.  インストールが完了するのを待ちます。完了すると、VM は自動的にシャット ダウンします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

##既知の問題
HYPER-V と Azure で RHEL 7.1 を使用する場合に既知の問題があります。

###問題: ディスク I/O のフリーズ 

この問題は、Hyper-V および Azure のRHEL 7.1 で、ストレージ ディスク I/O のアクティビティが頻繁な場合に発生する可能性があります。   

再現率:

この問題の発生は断続的です。Hyper-V および Azure で頻繁に行われるディスク I/O 操作中に非常によく発生します。   

    
[AZURE.NOTE] この既知の問題は既に Red Hat によってアドレス指定されています。 関連付けられている修正プログラムをインストールするには、次のコマンドを実行します。

    # sudo yum update


## 次のステップ
Red Hat Enterprise Linux .vhd を使用して、Azure で新しい Azure の仮想マシンを作成する準備ができました。 Red Hat Enterprise Linux の実行を承認されているハイパーバイザーの詳細については、次を参照してください。 [Red Hat の web サイト](https://access.redhat.com/certified-hypervisors)します。


