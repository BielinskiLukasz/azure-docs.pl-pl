<properties 
   pageTitle="複数 NIC を持つ VM の作成"
   description="複数の NIC を使用する VM を作成して構成する方法について説明します"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management,azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 複数 NIC を持つ VM の作成

Azure に仮想マシン (VM) を作成し、複数のネットワーク インターフェイス (NIC) を各 VM にアタッチできます。 複数 NIC は、アプリケーションの配布や WAN の最適化ソリューションなど、多くのネットワーク仮想アプライアンスに必要です。 また、複数 NIC には、フロントエンド NIC とバックエンド NIC 間でのトラフィックの分離、管理レベルのトラフィックとデータ レベルのトラフィックの分離など、多くのネットワーク トラフィック管理機能も用意されています。

![VM の複数の NIC](./media/virtual-networks-multiple-nics/IC757773.png)

上の図は、3 つの NIC を持つ VM を示しており、各 NIC は異なるサブネットに接続されています。

## 要件と制約

現時点では、複数 NIC には次の要件と制約があります。 

- 複数 NIC の VM は、Azure の仮想ネットワーク (Vnet) 上に作成する必要があります。 Vnet 以外の VM はサポートされません。 
- 単一のクラウド サービス (従来のデプロイ) またはリソース グループ (リソース マネージャー デプロイ) 内では、次の設定のみが許可されています。 
    - そのクラウド サービス内のすべての VM で複数 NIC を有効にする。 
    - そのクラウド サービス内のすべての VM それぞれに NIC を 1 つ用意する。 

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] 従来のデプロイ モデルです。 
 
- インターネットに接続された VIP (従来のデプロイ) は、"既定" の NIC でのみサポートされています。 既定の NIC の IP アドレスに対して VIP は 1 つしかありません。 
- 現時点では、複数 NIC の VM のインスタンスレベル パブリック IP (LPIP) アドレス (従来のデプロイ) はサポートされていません。 
- VM 内の NIC の順序はランダムになり、Azure インフラストラクチャの更新によって変更される場合もあります。 ただし、IP アドレスと、対応するイーサネットの MAC アドレスは変更されません。 たとえば、 **Eth1** IP アドレスが 10.1.0.100、MAC アドレスを 00-0d-3a-b0-39-0d に、Azure インフラストラクチャが更新されて再起動後に変更は **Eth2**, 、IP と MAC が、ペアは変わりません。 お客様が再起動を開始した場合、NIC の順序は変更されません。 
- 各 VM の NIC のアドレスはそれぞれサブネットに存在する必要があるため、1 つの VM 上の複数 NIC それぞれには、同じサブネット内のアドレスに割り当てることができます。 
- VM のサイズによって、VM に作成できる NIC の数が決まります。 次の表では、VM のサイズに対応する NIC の数を示します。 

|VM のサイズ (標準的な SKU)|NICの数 (VM ごとに許可される最大数)|
|---|---|
|Basic のすべてのサイズ|1|
|A0\極小|1|
|A1\小|1|
|A2\中|1|
|A3\大|2|
|A4\極大|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|8|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|8|
|D1_v2|1|
|D2_v2|2|
|D3_v2|4|
|D4_v2|8|
|D5_v2|8|
|D11_v2|2|
|D12_v2|4|
|D13_v2|8|
|D14_v2|8|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|8|
|その他のサイズすべて|1|

## ネットワーク セキュリティ グループ (NSG)
リソース マネージャー デプロイでは、複数 NIC が有効になっている VM 上の NIC など、VM 上の任意の NIC をネットワーク セキュリティ グループ (NSG) に関連付けることができます。 NIC にサブネット内のアドレスが割り当てられ、そのサブネットが NSG に関連付けられている場合、サブネットの NSG のルールがその NIC にも適用されます。 サブネットを NSG に関連付けるだけでなく、NIC を NSG に関連付けることもできます。 

そのサブネットが NSG に個別に関連付けられているサブネットが NSG、内の NIC と関連付けられている場合で、関連付けられている NSG のルールが適用されます **フロー順序** 、nic で受け渡されるトラフィックの方向に従って。 

- **着信トラフィック **は、送信先が対象の NIC であり、サブネット経由で最初に送信され、サブネットの NSG ルールをトリガーし、NIC へ渡される前に NIC の NSG ルールをトリガーします。 
- **送信トラフィック** 、NIC の NSG ルールをサブネットを通過し、サブネットの NSG ルールをトリガーする前に、NIC から最初に送信のフローは、対象の NIC でのソース。 

詳細について [ネットワーク セキュリティ グループ](virtual-networks-nsg) と Vm と Nic のサブネットへの関連付けに基づいてその適用方法.

## 従来のデプロイに複数 NIC の VM を構成する方法

3 つの NIC (既定の NIC と追加の 2 つの NIC) を含む複数 NIC の VM を作成するには、次の手順が役立ちます。 この構成手順によって、次のサービス構成ファイルの一部に従って構成される VM が作成されます。

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


この例の PowerShell コマンドを実行するには、次の前提条件を満たしておく必要があります。

- Azure サブスクリプション。
- 構成済みの仮想ネットワーク。 参照してください [仮想ネットワークの概要](virtual-networks-overview.md) Vnet の詳細についてです。
- Azure PowerShell の最新バージョンをダウンロードしてインストールしていること。 参照してください [をインストールして、Azure PowerShell を構成する方法](../install-configure-powershell)します。

複数の NIC を使用する VM を作成するには、次の手順に従います。

1. Azure VM イメージ ギャラリーから VM イメージを選択します。 イメージは頻繁に変更され、リージョンごとに利用できることに注意してください。 次の例で指定されたイメージは変更されたり、リージョンによって提供されていない可能性があるため、必ず必要なイメージを指定してください。 
        
        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. VM 構成を作成します。 

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. 既定の管理者ログインを作成します。 

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. VM 構成に NIC を追加します。 

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. 既定の NIC のサブネットと IP アドレスを指定します。 

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm 
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. 仮想ネットワーク内に VM を作成します。 

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] ここで指定する VNet は必要があります (前述した「前提条件) に既に存在します。 次の例は、という名前の仮想ネットワークを指定 **Multinic-vnet**します。 

## 他のサブネットへのセカンダリ NIC アクセス

Azure の現在のモデルでは、仮想マシンのすべての NIC にはデフォルト ゲートウェイが設定されます。 これにより、NIC はサブネットの外部の IP アドレスと通信できます。 Linux などの弱いホスト ルーティング モデルを使用するオペレーティング システムでは、受信トラフィックと送信トラフィックが異なる NIC を使用している場合、インターネット接続が中断します。

この問題を解決するため、2015 年 7 月の最初の週に、セカンダリ NIC からデフォルト ゲートウェイを削除する更新プログラムがプラットフォームに展開されます。 仮想マシンを再起動するまで、これによる既存の仮想マシンへの影響はありません。 再起動後に新しい設定が有効になり、セカンダリ NIC でのトラフィック フローは同じサブネット内に制限されるようになります。 ユーザーは、セカンダリ NIC がユーザーのサブネットの外部と通信できるようにする場合は、以下に示すように、ルーティング テーブルにエントリを追加してゲートウェイを構成する必要があります。

### Windows VM の構成

次のような 2 つの NIC を備えた Windows VM があるものとします。

- プライマリ NIC の IP アドレス: 192.168.1.4
- セカンダリ NIC の IP アドレス: 192.168.2.5

この VM の IPv4 ルーティング テーブルは、次のようになります。

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

既定のルート (0.0.0.0) はプライマリ NIC だけが使用できることに注意してください。 次に示すように、セカンダリ NIC のサブネットの外部にあるリソースにはアクセスできません。

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
     
    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

セカンダリ NIC で既定のルートを追加するには、次の手順に従います。

1. コマンド プロンプトから次のコマンドを実行して、セカンダリ NIC のインデックス番号を識別します。

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. インデックスが 27 (この例の場合) である表の 2 番目のエントリに注意してください。
3. コマンド プロンプトから実行、 **ルートを追加** コマンドを次のようにします。 この例では、セカンダリ NIC のデフォルト ゲートウェイとして 192.168.2.1 を指定します。

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. 接続をテストするには、コマンド プロンプトに戻り、次の例で示すように、セカンダリ NIC から別のサブネットに対して ping を試みます。

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
         
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. 次に示すように、ルート テーブルで新しく追加されたルートを確認することもできます。

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### Linux VM を構成します。

Linux VM の場合、既定の動作では弱いホスト ルーティングが使用されるので、セカンダリ NIC を同じサブネット内のトラフィック フローだけに制限することをお勧めします。 ただし、特定のシナリオでサブネット外部への接続が必要な場合は、送信と受信のトラフィックが同じ NIC を使用するようにポリシー ベースのルーティングを有効にする必要があります。

## 次のステップ

- 展開 [リソース マネージャーの展開で 2 層アプリケーションのシナリオで MultiNIC Vm](virtual-network-deploy-multinic-arm-template.md)します。
- 展開 [クラシック展開の 2 層アプリケーションのシナリオで MultiNIC Vm](virtual-network-deploy-multinic-classic-ps.md)します。

