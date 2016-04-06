<properties 
   pageTitle="PowerShell を使用してクラシック モードで静的プライベート IP を設定する方法 | Microsoft Azure"
   description="静的プライベート IP (DIP) とそれらをクラシック モードおよび PowerShell で管理する方法を理解します。"
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

# PowerShell での静的プライベート IP アドレス (クラシック) の設定方法

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] この記事では、従来のデプロイ モデルについて説明します。 こともできます [リソース マネージャーの配置モデルの静的なプライベート IP アドレスを管理](virtual-networks-static-private-ip-arm-ps)します。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下のサンプル PowerShell コマンドでは、単純な環境が既に作成されていると想定します。 このドキュメントに表示されているように、コマンドを実行する場合は、最初に説明されているテスト環境にビルド [vnet を作成する](virtual-networks-create-vnet-classic-netcfg-ps.md)です。

## 特定の IP アドレスが使用可能であるかを確認する方法
場合を確認する IP アドレス *192.168.1.101* という vnet で使用できるは *TestVnet*, 次の PowerShell コマンドを実行しの値を確認して、 *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

予想される出力:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## VM 作成時に静的プライベート IP アドレスを指定する方法
次の PowerShell スクリプトという名前の新しいクラウド サービスを作成する *TestService*, 、Azure からのイメージを取得し、という名前の VM を作成 *DNS01* 、新しいクラウド サービスではという名前のサブネットに存在する VM を設定、取得したイメージを使用して *フロント エンド*, 、設定と *192.168.1.7 から* VM の静的なプライベート IP アドレスとして。

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames FrontEnd `
  	| Set-AzureStaticVNetIP -IPAddress 192.168.1.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVNet

予想される出力:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## VM 用の静的プライベート IP アドレス情報を取得する方法
前述のスクリプトで作成した VM の静的なプライベート IP アドレス情報を表示する次の PowerShell コマンドを実行しの値を確認します *IpAddress*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

予想される出力:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## VM から静的プライベート IP アドレスを削除する方法
上記のスクリプトで VM に追加された静的プライベート IP アドレスを削除するには、次の PowerShell コマンドを実行します。
    
    Get-AzureVM -ServiceName TestService -Name DNS01 `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

予想される出力:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## 既存の VM に静的プライベート IP アドレスを追加する方法
上記のスクリプトを使用して作成した VM に静的プライベート IP アドレスを追加するには、次のコマンドを実行します。

    Get-AzureVM -ServiceName TestService -Name DNS01 `
  	| Set-AzureStaticVNetIP -IPAddress 192.168.1.7 `
  	| Update-AzureVM

予想される出力:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## 次のステップ

- について学習 [予約済みパブリック IP](../virtual-networks-reserved-public-ip) アドレス。
- について学習 [インスタンス レベル パブリック IP (ILPIP)](../virtual-networks-instance-level-public-ip) アドレス。
- 参照してください、 [予約済み IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)します。

