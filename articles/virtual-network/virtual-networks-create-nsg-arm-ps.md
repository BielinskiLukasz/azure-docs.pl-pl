<properties 
   pageTitle="PowerShell を使用して ARM モードで NSG を作成する方法 | Microsoft Azure"
   description="PowerShell を使用して ARM で NSG を作成してデプロイする方法について"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="telmos" />

# PowerShell で NSG を作成する方法

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] この記事では、リソース マネージャーの配置モデルについて説明します。 こともできます [クラシック展開モデルに Nsg を作成](virtual-networks-create-nsg-classic-ps.md)します。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下の PowerShell のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに表示されているように、コマンドを実行する場合は、最初にビルド、テスト環境を展開して [このテンプレート](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), をクリックして **Deploy to Azure**, 、必要に応じて、既定のパラメーター値を置き換えるし、ポータルの指示に従います。

## フロントエンドのサブネットの NSG を作成する方法
名前を名前付き NSG を作成する *NSG フロント エンド* 上記のシナリオに基づいて、以下の手順です。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Azure PowerShell を初めて使用する場合は、次を参照してください。 [インストールおよび Azure PowerShell の構成方法](powershell-install-configure.md) Azure にサインインし、サブスクリプションを選択最後までの指示に従います。

3. インターネットからポート 3389 へのアクセスを許可するセキュリティ規則を作成します。

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
            -SourceAddressPrefix Internet -SourcePortRange * `
            -DestinationAddressPrefix * -DestinationPortRange 3389

4. インターネットからポート 80 へのアクセスを許可するセキュリティ規則を作成します。 

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
            -SourceAddressPrefix Internet -SourcePortRange * `
            -DestinationAddressPrefix * -DestinationPortRange 80

5. という名前の新しい NSG を上記で作成した規則を追加 **NSG フロント エンド**します。

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
            -SecurityRules $rule1,$rule2

6. NSG に作成された規則を確認します。

        $nsg

    出力にはセキュリティ規則のみが表示されます。

        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
                                   "Description": "Allow RDP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "3389",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 100,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "web-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
                                   "Description": "Allow HTTP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "80",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 101,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

6. 上記で作成したに NSG を関連付ける、 *フロント エンド* サブネットです。

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

    のみを示す出力、 *フロント エンド* サブネットの設定の値に注意してください、 **NetworkSecurityGroup** プロパティ。

        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                },
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }

>[AZURE.WARNING] 上記のコマンドの出力は、PowerShell を実行しているコンピューター上にのみ存在する仮想ネットワークの構成オブジェクトのコンテンツを表示します。 実行する必要があります、 **セット AzureRmVirtualNetwork** コマンドレットを Azure にこれらの設定を保存します。

7. 新しい VNet 設定を Azure に保存します。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    出力には NSG 部分のみが表示されます。

        "NetworkSecurityGroup": {
          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
        }

## バックエンドのサブネットの NSG を作成する方法
名前を名前付き NSG を作成する *NSG バックエンド* 上記のシナリオに基づいて、以下の手順です。

1. フロント エンドのサブネットからポート 1433 (SQL Server で使用される既定のポート) へのアクセスを許可するセキュリティ規則を作成します。

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
            -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
            -DestinationAddressPrefix * -DestinationPortRange 1433

4. インターネットへのアクセスをブロックするセキュリティ規則を作成します。 

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
            -Access Deny -Protocol * -Direction Outbound -Priority 200 `
            -SourceAddressPrefix * -SourcePortRange * `
            -DestinationAddressPrefix Internet -DestinationPortRange *

5. という名前の新しい NSG を上記で作成した規則を追加 **NSG バックエンド**します。

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
            -SecurityRules $rule1,$rule2

6. 上記で作成したに NSG を関連付ける、 *バックエンド* サブネットです。

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
            -AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

    のみを示す出力、 *バックエンド* サブネットの設定の値に注意してください、 **NetworkSecurityGroup** プロパティ。

        Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [...],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

7. 新しい VNet 設定を Azure に保存します。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
