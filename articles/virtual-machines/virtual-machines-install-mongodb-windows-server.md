<properties
    pageTitle="Windows VM への MongoDB のインストール | Microsoft Azure"
    description="Windows Server を実行するクラシック デプロイ モデルで作成された Azure VM に MongoDB をインストールする方法について説明します。"
    services="virtual-machines"
    documentationCenter=""
    authors="dsk-2015"
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
    ms.author="dkshir"/>


# Windows VM への MongoDB のインストール

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


[MongoDB ][mongodb] は一般的なオープン ソースの高パフォーマンスの NoSQL データベースです。 使用して、 [Azure クラシック ポータル ][azureportal], 、従来のデプロイ モデルを使用して、イメージ ギャラリーから Windows Server を実行する仮想マシンを作成できます。 この仮想マシンには、MongoDB データベースをインストールして構成することができます。


## Windows Server を実行する仮想マシンの作成

仮想マシンを作成するには、以下の手順を実行します。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]
> [AZURE.NOTE] 仮想マシンの作成中に MongoDB のエンドポイントを追加して、**Mongo** という名前を付けることができます。また、プロトコルとして **TCP** を指定し、パブリック ポートとプライベート ポートの両方を **27017** に設定します。

## データ ディスクの接続

仮想マシン用のストレージを用意するため、データ ディスクを接続し、Windows で使用できるように初期化します。 使用するデータがある場合は既存のディスクを接続するか、空のディスクを接続できます。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

ディスクの初期化の手順については、次を参照してください。"する方法: Windows Server で新しいデータ ディスクの初期化"で [Windows 仮想マシンにデータ ディスクをアタッチする方法](storage-windows-attach-disk.md)します。

## 仮想マシンへの MongoDB のインストールと実行

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## 概要

このチュートリアルでは、Windows Server を実行する仮想マシンを作成する方法、リモートから仮想マシンに接続する方法、データ ディスクを接続する方法について学習しました。 Windows ベースの仮想マシンに MongoDB をインストールして構成する方法も学習しました。 高度なトピックに従って MongoDB を Windows ベースの仮想マシンに今すぐアクセスすることができます、 [MongoDB のドキュメント ][mongodocs]します。


[mongodocs]: http://docs.mongodb.org/manual/ 
[mongodb]: http://www.mongodb.org/ 
[azureportal]: http://manage.windowsazure.com 

