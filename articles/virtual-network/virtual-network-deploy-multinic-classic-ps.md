<properties 
   pageTitle="クラシック デプロイメント モデルで PowerShell を使用して複数 NIC VM をデプロイする | Microsoft Azure"
   description="クラシック デプロイメント モデルで PowerShell を使用して複数の NIC VM をデプロイする方法を説明します"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="telmos" />


# PowerShell を使用した複数 NIC VM (クラシック) のデプロイ

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [リソース マネージャー モデル](virtual-network-deploy-multinic-arm-ps.md)します。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

この時点では、単一の NIC を含む VM と複数の NIC を含む VM を同じクラウド サービス内で保持できないため、バックエンド サーバーを特定のクラウド サービスに実装したら、シナリオ内の他のすべてのコンポーネントは別のクラウド サービスに実装します。 以下の手順では、メイン リソースに *IaaSStory* という名前のクラウド サービスを使用し、バックエンド サーバーに *IaaSStory-BackEnd* を使用します。

## 前提条件

バックエンド サーバーをデプロイするには、このシナリオで必要なすべてのリソースを含むメイン クラウド サービスをデプロイする必要があります。 少なくとも、バックエンド用のサブネットを含む仮想ネットワークを作成する必要があります。 参照してください [PowerShell を使用して仮想ネットワークの作成](virtual-networks-create-vnet-classic-ps.md) を仮想ネットワークを展開する方法を参照してください。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## バックエンド VM のデプロイ

バックエンド VM は、以下にリストしたリソースの作成に依存します。

- **バックエンド サブネット**。 データベース サーバーは、トラフィックを分離するために、別のサブネットに含まれます。 次のスクリプトでは、このサブネットが *WTestVnet* という名前の Vnet に存在する必要があります。
- **データ ディスクのストレージ アカウント**。 パフォーマンスを高めるために、データベース サーバー上のデータ ディスクはソリッド ステート ドライブ (SSD) テクノロジーを使用します。これには、Premium Storage アカウントが必要です。 デプロイする Azure の場所が Premium Storage をサポートすることを確認してください。
- **可用性セット**。 メンテナンス中に少なくとも 1 つの VM が稼働し、実行されているようにするためには、すべてのデータベース サーバーを単一の可用性セットに追加します。

### 手順 1 - スクリプトの開始

完全に使用する PowerShell スクリプトをダウンロードする [ここ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/multinic.ps1)します。 以下の手順に従って、ご使用の環境で機能するようにスクリプトを変更します。

1. 既存のリソース グループの上に展開に基づく次の変数の値を変更 [の前提条件](#Prerequisites)します。

        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"

2. バックエンド デプロイメントに使用する値に基づいて、以下の変数の値を変更します。

        $backendCSName         = "IaaSStory-Backend"
        $prmStorageAccountName = "iaasstoryprmstorage"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $diskSize              = 127
        $vmNamePrefix          = "DB"
        $dataDiskSuffix        = "datadisk"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2


### 手順 2 - VM 用に必要なリソースの作成

新しいクラウド サービス、およびすべての VM に対してデータ ディスクのストレージ アカウントを作成する必要があります。 また、イメージと、VM のローカル管理者アカウントを指定する必要があります。 これらのリソースを作成するには、次の手順を実行します。

1. 新しいクラウド サービスを作成します。

        New-AzureService -ServiceName $backendCSName -Location $location

2. Premium Storage アカウントを作成します。

        New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
            -Location $location `
            -Type Premium_LRS

3. 上で作成したストレージ アカウントを、サブスクリプションの現在のストレージ アカウントとして設定します。

        $subscription = Get-AzureSubscription `
            | where {$_.IsCurrent -eq $true}  
        Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
            -CurrentStorageAccountName $prmStorageAccountName

4. VM のイメージを選択します。

        $image = Get-AzureVMImage `
            | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
            | sort PublishedDate -Descending `
            | select -ExpandProperty ImageName -First 1

5. ローカル管理者アカウントの資格情報を設定します。

        $cred = Get-Credential -Message "Enter username and password for local admin account"


### 手順 3 - VM の作成

ループを使用して必要な数の VM を作成し、必要な NIC と VM をループ内に作成します。 NIC と VM を作成するには、次の手順を実行します。

1. 開始、 `の` をバーチャル マシンと、必要に応じて何度でも 2 つの Nic を作成するためのコマンドを繰り返すループの値に基づいて、 `$numberOfVMs` 変数です。

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. 作成、 `VMConfig` イメージやサイズなど、VM に対する可用性セットを指定するオブジェクト。

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureVMConfig -Name $vmName `
                            -ImageName $image `
                            -InstanceSize $vmSize `
                            -AvailabilitySetName $avSetName  

3. Windows VM として VM をプロビジョニングします。

            Add-AzureProvisioningConfig -VM $vmConfig -Windows `
                -AdminUsername $cred.UserName `
                -Password $cred.Password

4. 既定の NIC 設定し、静的 IP アドレスを割り当てます。

            Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
            Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. 各 VM の 2 つめの NIC を追加します。

            Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
                -SubnetName $backendSubnetName `
                -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
                -VM $vmConfig 

6. 各 VM のデータ ディスクを作成します。

         $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
         Add-AzureDataDisk -CreateNew -VM $vmConfig `
             -DiskSizeInGB $diskSize `
             -DiskLabel $dataDisk1Name `
             -LUN 0       
    
         $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
         Add-AzureDataDisk -CreateNew -VM $vmConfig `
             -DiskSizeInGB $diskSize `
             -DiskLabel $dataDisk2Name `
             -LUN 1

7. 各 VM を作成し、ループを終了します。

            New-AzureVM -VM $vmConfig `
                -ServiceName $backendCSName `
                -Location $location `
                -VNetName $vnetName
        }


### 手順 4 - スクリプトの実行

ニーズに合わせてスクリプトをダウンロードおよび変更し、スクリプトを実行して複数の NIC を持つバックエンド データベース VM を作成しました。

1. スクリプトを保存し、それを **PowerShell** コマンド プロンプトまたは **PowerShell ISE** から実行します。 次のように、最初の出力が表示されます。

     OperationDescription    OperationId                          OperationStatus
     --------------------    -----------                          ---------------
     New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
     New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
    
     WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. 資格情報プロンプトに必要な情報を入力して、**[OK]** をクリックします。 次の出力が表示されます。

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded 






