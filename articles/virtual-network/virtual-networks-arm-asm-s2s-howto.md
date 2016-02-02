<properties 
   pageTitle="Azure で従来の VNet と ARM Vnet を接続する方法"
   description="従来の VNet と新しい VNet の間の VPN 接続を作成する方法を学習します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />


# 従来の VNet を新しい VNet に接続する

Azure には現在、Azure サービス マネージャー (従来型と呼ばれます) と Azure リソース マネージャー (ARM) という 2 つの管理モードがあります。 これまで Azure を使用してきているユーザーであれば、おそらく Azure VM および従来の VNet 上で実行されているインスタンス ロールを利用されていることでしょう。 そして、新しい VM とロール インスタンスが、ARM で作成された VNet 上で実行されていることも考えられます。

このような場合、新しいインフラストラクチャと従来のリソースが確実に通信できるようにする必要があります。 これを実行するには、2 つの VNet 間で VPN 接続を作成します。

この記事では、従来の VNet と ARM VNet の間にサイト間 (S2S) VPN 接続を作成する方法を学習します。
>[AZURE.NOTE] ここでは、従来の VNet および ARM VNet をすでに所有していること、そして従来の VNet の S2S VPN 接続設定について理解していることが前提になっています。 クラシック間の S2S VPN 接続に関する詳細なエンド ツー エンド ソリューションの ARM Vnet を参照してくださいと [ソリューション ガイド - S2S VPN を使用して、従来の VNet と ARM VNet を接続](../virtual-networks-arm-asm-s2s.md)します。

次に示すのは、従来の VNet と ARM VNet の間に Azure ゲートウェイを使用して S2S VPN 接続を作成するために、実行すべきタスクの概要です。

1- [VPN ゲートウェイ従来の VNet を作成する](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet)

2- [ARM VNet 用の VPN ゲートウェイの作成](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet)

3- [ゲートウェイ間の接続を作成する](#Step-3:-Create-a-connection-between-the-gateways)

## 手順 1: 従来の VNet 用の VPN ゲートウェイを作成する

従来の VNet 用の VPN ゲートウェイを作成するには、以下の手順に従います。

1. Https://manage.windowsazure.com から従来のポータルを開き、必要に応じて、資格情報を入力します。
2. 画面の左下で、**[新規]**ボタン、**[ネットワーク サービス]**、**[仮想ネットワーク]**、**[ローカル ネットワークの追加]**の順にクリックします。
3. **[ローカル ネットワークの詳細を指定する]**ウィンドウで、接続先の ARM VNet の名前を入力してから、ウィンドウの右下にある矢印ボタンをクリックします。
3. アドレス空間の **[開始 IP]** テキスト ボックスに、接続先となる ARM VNet のネットワーク プレフィックスを入力します。
4. **[CIDR (アドレス数)]**ドロップダウン ボックスの一覧から、接続先の ARM VNet で使用される CIDR ブロックのネットワーク部分に使用するビット数を選択します。
5. **[VPN デバイス IP アドレス (オプション)]**で、任意の有効なパブリック IP アドレスを入力します。 この IP アドレスは後で変更します。 次に、画面の右下にあるチェック マーク ボタンをクリックします。 次の図は、このページの設定例です。

    ![ローカル ネットワークの設定](..\virtual-network\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. **[ネットワーク]**ページで、**[仮想ネットワーク]**、従来の VNet 、**[構成]**の順にクリックします。
6. **[サイト間接続]**で、**[ローカル ネットワークに接続する]**チェック ボックスをオンにします。
7. **[ローカル ネットワーク]**ドロップダウン ボックスで、使用可能なネットワークの一覧から、手順 4 で作成したローカル ネットワークを選択して**[保存]**をクリックします。
8. 設定を保存したら **[ダッシュ ボード]** をクリックして、ページの下部で**[ゲートウェイの作成]**、**[動的ルーティング]**、**[はい]**の順にクリックします。
9. ゲートウェイが作成されるまで待ってから、そのパブリック IP アドレスをコピーします。 このアドレスは、ARM VNet でゲートウェイを設定するときに必要になります。

## 手順 2: ARM VNet 用の VPN ゲートウェイを作成する

ARM VNet 用の VPN ゲートウェイを作成するには、以下の手順に従います。

1. PowerShell コンソールで、次のコマンドを実行して ARM モードに切り替えます。

        Switch-AzureMode AzureResourceManager

2. 次のコマンドを実行して、ローカル ネットワークを作成します。 ローカル ネットワークには、接続先となる従来の VNet の CIDR ブロックと、上記の手順 1 で作成したゲートウェイのパブリック IP アドレスを使用する必要があります。

        New-AzureLocalNetworkGateway -Name VNetClassicNetwork `
            -Location "East US" -AddressPrefix "10.0.0.0/20" `
            -GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. 次のコマンドを実行して、ゲートウェイのパブリック IP アドレスを作成します。

        $ipaddress = New-AzurePublicIpAddress -Name gatewaypubIP`
            -ResourceGroupName RG1 -Location "East US" `
            -AllocationMethod Dynamic

4. 次のコマンドを実行して、ゲートウェイに使用されているサブネットを取得します。

        $subnet = Get-AzureVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

    >[AZURE.IMPORTANT] ゲートウェイ サブネットが既に存在しており、GatewaySubnet という名前が付けられている必要があります。

5. 次のコマンドを実行して、ゲートウェイの IP 構成オブジェクトを作成します。 ゲートウェイ サブネットの ID が使用されていることに注意してください。 そのサブネットは、VNet に存在している必要があります。。

        $ipconfig = New-AzureVirtualNetworkGatewayIpConfig `
            -Name ipconfig -PrivateIpAddress 10.1.2.4 `
            -SubnetId $subnet.id -PublicIpAddressId $ipaddress.id

    >[AZURE.IMPORTANT] *SubnetId* パラメーターと *PublicIpAddressId* パラメーターはそれぞれ、サブネットの id プロパティと IP アドレス オブジェクトの  id プロパティを渡さなければなりません。 単純な文字列を使用することはできません。

5. 次のコマンドを実行して、ARM VNet ゲートウェイを作成します。

        New-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
            -Location "East US" -GatewayType Vpn -IpConfigurations $ipconfig `
            -EnableBgp $false -VpnType RouteBased

6. VPN ゲートウェイが作成されたら、次のコマンドを実行してパブリック IP アドレスを取得します。 IP アドレスをコピーしてください。従来の VNet 用のローカル ネットワークを構成するときに必要になります。

        Get-AzurePublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1


## 手順 3: ゲートウェイ間の接続を作成する

1. Https://manage.windowsazure.com から従来のポータルを開き、必要に応じて、資格情報を入力します。
2. 従来のポータルで下にスクロールして、**[ネットワーク]**、**[ローカル ネットワーク]**、接続先となる ARM VNet、**[編集]** ボタンの順にクリックします。
3. **[VPN デバイス IP アドレス (オプション)]**で、上記の手順 2 で取得した ARM VNet ゲートウェイの IP アドレスを入力してから、右下にある右矢印をクリックし、チェック マーク ボタンをクリックします。
4. PowerShell コンソールで、次のコマンドを実行して Azure サービス マネージャー モードに切り替えます。

        Switch-AzureMode AzureServiceManager

5. 次のコマンドを実行して、共有キーを設定します。 VNet の名前を、使用されている VNet の名前に必ず変更してください。

        Set-AzureVNetGatewayKey -VNetName VNetClassic `
            -LocalNetworkSiteName VNetARM -SharedKey abc123

6. PowerShell コンソールで、次のコマンドを実行して Azure リソース マネージャー モードに切り替えます。

        Switch-AzureMode AzureResourceManager

7. 次のコマンドを実行して、VPN 接続を作成します。

     $vnet01gateway = Get-AzureLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
     $vnet02gateway = Get-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
    
     New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
         -ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
         -LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
         -RoutingWeight 10 -SharedKey 'abc123'


## 次のステップ

- 詳細について [ARM のネットワーク リソース プロバイダー (NRP)](../resource-groups-networking.md)します。
- 作成、 [S2S VPN を使用して、ARM VNet に従来の VNet を接続するエンド ツー エンド ソリューション](../virtual-networks-arm-asm-s2s.md)します。




