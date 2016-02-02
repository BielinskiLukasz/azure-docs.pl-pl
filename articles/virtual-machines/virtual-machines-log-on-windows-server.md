<properties
    pageTitle="VM へのログオン | Microsoft Azure"
    description="Azure クラシック ポータルを使用して、クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにログオンします。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="cynthn"/>



# Azure クラシック ポータルを使用して Windows 仮想マシンにログオンする

Azure クラシック ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始し、Windows VM にログオンします。

Linux VM に接続する場合は、 参照してください [Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


## ビデオ チュートリアル

以下は、このチュートリアルのステップのビデオです。 Azure で Windows VM に接続するために使用する、エンドポイント、パブリック ポート、およびプライベート ポートについても説明します。

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## 仮想マシンへの接続

1. Azure クラシック ポータルにサインインします。

2. **[Virtual Machines]** をクリックし、仮想マシンを選択します。

3. ページの下部にあるコマンド バーで、**[接続]** をクリックします。

    ![仮想マシンへのログオン](./media/virtual-machines-log-on-windows-server/connectwindows.png)

> [AZURE.TIP] [接続] ボタンを使用できない場合は、この記事の最後にあるトラブルシューティングのヒントを参照してください。

## 仮想マシンへのログオン

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## トラブルシューティングのヒント

試すことができるいくつかの操作を次に示します。

-   **[接続]** ボタンが非アクティブである場合、またはリモート デスクトップ接続に関するその他の問題が発生している場合は、構成をリセットします。 仮想マシンのダッシュボードで、**[概要]** の **[リモート構成のリセット]** をクリックします。
-   パスワードに問題がある場合は、パスワードをリセットしてください。 仮想マシンのダッシュボードで、**[概要]** の **[パスワードのリセット]** をクリックします。

これらのヒントが動作しない、またはられないを参照してください [Windows ベースの Azure 仮想マシンへのリモート デスクトップのトラブルシューティングを行う接続](virtual-machines-troubleshoot-remote-desktop-connections.md)します。 この記事では、一般的な問題の診断と解決の手順について説明します。







