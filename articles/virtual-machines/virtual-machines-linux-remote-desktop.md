<properties
    pageTitle="リモート デスクトップを Linux VM にアタッチする | Microsoft Azure"
    description="リモート デスクトップをインストールして、Microsoft Azure Linux VM に接続するように構成する方法を説明します。"
    services="virtual-machines"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2015"
    ms.author="mingzhan"/>


#リモート デスクトップを使用して Microsoft Azure Linux VM に接続する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


##概要

RDP (リモート デスクトップ プロトコル) は、Windows の専用プロトコルです。 RDP を使用して Linux VM (仮想マシン) をリモート接続するにはどうすればよいでしょうか。

このガイドを読めば、その答えがわかります。 RDP は、Microsoft Azure Linux VM に xrdp をインストールして構成するのに役立ちます。また、これには Windows コンピューターからリモート デスクトップを使用して接続できます。 このガイドでは、例として Ubuntu または OpenSUSE を実行する Linux VM を使用します。

xrdp はオープン ソースの RDP サーバーで、これを使用すると、Windows コンピューターからリモート デスクトップを使用して Linux サーバーに接続できます。 このパフォーマンスは、VNC (Virtual Network Computing) よりも優れています。 VNC には "JPEG" 品質と動作が遅いという欠点があるのに対し、RDP は高速かつ明瞭です。


> [AZURE.NOTE] 既に、Linux を実行する Microsoft Azure VM が必要です。 作成し、Linux VM をセットアップを参照してください、 [Azure Linux VM チュートリアル](virtual-machines-linux-tutorial.md)します。


##リモート デスクトップ用のエンドポイントを作成する
このドキュメントでは、リモート デスクトップに既定のエンドポイント 3389 を使用します。 そのため、次のように、Linux VM に対してリモート デスクトップとしてエンドポイント 3389 を設定します。


![image](./media/virtual-machines-linux-remote-desktop/no1.png)


仮想マシンにエンドポイントを設定する方法がわからない場合は、次を参照してください。 [ガイダンス](virtual-machines-set-up-endpoints.md)します。


##Gnome デスクトップをインストールする

Putty を使用して Linux VM に接続し、`Gnome Desktop` をインストールします。

Ubuntu の場合は、次のコマンドを使用します。

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


OpenSUSE の場合は、次のコマンドを使用します。

    #sudo zypper install gnome-session

##xrdp をインストールする

Ubuntu の場合は、次のコマンドを使用します。

    #sudo apt-get install xrdp

OpenSUSE の場合は、次のコマンドを使用します。

> [AZURE.NOTE] OpenSUSE バージョンを使用しているに次のコマンドでは、次のバージョンのコマンドの例は、更新 `OpenSUSE 13.2`します。

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##xrdp を開始して起動時に xdrp サービスを設定する

OpenSUSE の場合は、次のコマンドを使用します。

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Ubuntu の場合は、インストール後の自動起動時に xrdp が開始され、有効になります。

##Ubuntu 12.04LTS 以降のバージョンの Ubuntu を使用している場合に xfce を使用する

現在の xrdp では Ubuntu 12.04LTS 以降のバージョンの Ubuntu から Gnome デスクトップへの接続がサポートされていないため、代わりに `xfce` のデスクトップを使用します。

`xfce` をインストールするには、次のコマンドを使用します。

    #sudo apt-get install xubuntu-desktop

`xfce` を有効にするには、次のコマンドを使用します。

    #echo xfce4-session >~/.xsession

構成ファイル `/etc/xrdp/startwm.sh` を編集するには、次のコマンドを使用します。

    #sudo vi /etc/xrdp/startwm.sh   

行 `/etc/X11/Xsession` の前に行 `xfce4-session` を追加します。

xrdp サービスを再起動するには、次のコマンドを使用します。

    #sudo service xrdp restart


##Windows コンピューターから Linux VM を接続する
Windows コンピューターでリモート デスクトップ クライアントを起動し、Linux VM の DNS 名を入力するか、Azure クラシック ポータルで VM の`Dashboard`に移動して [`Connect`] をクリックすると、Linux VM に接続され、次のようなログイン ウィンドウが表示されます。

![image](./media/virtual-machines-linux-remote-desktop/no2.png)

Linux VM の `user` と `password` を使用してログインすると、Microsoft Azure Linux VM からリモート デスクトップを今すぐ利用できるようになります。


##次へ
参照 xrdp を使用する詳細については、 [ここ](http://www.xrdp.org/)します。

