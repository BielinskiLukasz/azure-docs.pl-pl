<properties 
   pageTitle="CLI を使用してクラシック モードで静的プライベート IP を設定する方法 | Microsoft Azure"
   description="静的プライベート IP (DIP) とそれらを CLI を使用してクラシック モードで管理する方法を理解します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />


# Azure CLI での静的プライベート IP アドレス (クラシック) の設定方法

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] この記事では、従来のデプロイ モデルについて説明します。 こともできます [リソース マネージャーの配置モデルの静的なプライベート IP アドレスを管理](virtual-networks-static-private-ip-arm-cli.md)します。

次のサンプル Azure CLI コマンドでは、単純な環境が既に作成されていると想定します。 このドキュメントに表示されているように、コマンドを実行する場合は、最初に説明されているテスト環境にビルド [vnet を作成する](virtual-networks-create-vnet-classic-cli.md)します。

## VM 作成時に静的プライベート IP アドレスを指定する方法

上記のシナリオに基づいて、*TestService* という名前の新しいクラウド サービスで *DNS01* という名前の VM を作成するには、以下の手順に従います。

1. Azure CLI を初めて使用する場合は、次を参照してください。 [のインストールと Azure CLI の構成](xplat-cli-install.md) Azure アカウントとサブスクリプションを選択する時点までの指示に従います。
1. **azure service create** コマンドを実行して、クラウド サービスを作成します。

        azure service create TestService --location uscentral

    予想される出力:

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK

2. **azure create vm** コマンドを実行して、VM を作成します。 静的プライベート IP アドレスの値を確認します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    予想される出力:

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l (または --location)**。 VM が作成される Azure リージョンです。 ここでは、*centralus* です。
    - **-n (または --vm-name)**。 作成する VM の名前です。
    - **-w (または --virtual-network-name)**。 VM の作成先となる VNet の名前です。
    - **-S (または --static-ip)**。 VM の静的プライベート IP アドレスです。
    - **TestService**。 VM の作成先となるクラウド サービスの名前です。
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**。 VM の作成に使用されるイメージです。
    - **adminuser**。 Windows VM のローカル管理者です。
    - **AdminP@ssw0rd**します。 Windows VM のローカル管理者のパスワードです。

## VM 用の静的プライベート IP アドレス情報を取得する方法

上記のスクリプトで作成された VM の静的プライベート IP アドレス情報を表示するには、次の Azure CLI コマンドを実行し、*Network StaticIP* の値を確認します。

    azure vm static-ip show DNS01

予想される出力:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## VM から静的プライベート IP アドレスを削除する方法

上記のスクリプトで VM に追加された静的プライベート IP アドレスを削除するには、次の Azure CLI コマンドを実行します。

    azure vm static-ip remove DNS01

予想される出力:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## 既存の VM に静的プライベート IP を追加する方法

上記のスクリプトを使用して作成した VM に静的プライベート IP アドレスを追加するには、次のコマンドを実行します。

    azure vm static-ip set DNS01 192.168.1.101

予想される出力:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## 次のステップ

- について学習 [予約済みパブリック IP](../virtual-networks-reserved-public-ip) アドレス。
- について学習 [インスタンス レベル パブリック IP (ILPIP)](../virtual-networks-instance-level-public-ip) アドレス。
- 参照してください、 [予約済み IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)します。





