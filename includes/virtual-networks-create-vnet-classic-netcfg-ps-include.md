## PowerShell のネットワーク構成ファイルを使用して VNet を作成する方法

Azure では、xml ファイルを使用して、サブスクリプションに使用可能なすべての Vnet を定義します。 このファイルをダウンロードし、既存の Vnet が変更または削除されるようにファイルを編集することで、新しい VNet を作成します。 このドキュメントでは、ネットワーク構成 (または netcgf) ファイルをダウンロードする方法と、そのファイルを編集して新しい VNet を作成する方法を説明します。 チェック、 [Azure 仮想ネットワーク構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx) をネットワーク構成ファイルの詳細を参照してください。

PowerShell で取得した netcfg ファイルを使用して VNet を作成するには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、次を参照してください。 [インストールおよび Azure PowerShell の構成方法](powershell-install-configure.md) Azure にサインインし、サブスクリプションを選択最後までの指示に従います。
2. Azure PowerShell コンソールから、**Get AzureVnetConfig** コマンドレットを使用して次のコマンドを実行することで、ネットワーク構成ファイルをダウンロードします。

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    予想される出力:

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. 任意の XML またはテキスト エディター アプリケーションを使用して上記の手順 2 で保存したファイルを開き、検索、* *<VirtualNetworkSites>*作成済みのネットワークがある場合は、各ネットワークが自分と表示されます **。*作成済みのネットワークがある場合は、各ネットワークが自分と表示されます **<VirtualNetworkSite>* * 要素。
4. このシナリオで説明した仮想ネットワークを作成するには、次の XML を弱追加、* *<VirtualNetworkSites>* * 要素。

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  ネットワーク構成ファイルを保存します。
10. Azure PowerShell コンソールから、**Set-AzureVnetConfig** コマンドレットを使用して次のコマンドを実行することで、ネットワーク構成ファイルをアップロードします。 コマンド下に表示される出力を参照してください。**OperationStatus** の下に **Succeeded** と表示されます。 表示されていない場合は、エラーがないか xml ファイルを確認します。

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    上記のコマンドで想定される出力を次に示します。

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 

11. Azure PowerShell コンソールから、**Get-AzureVnetSite** コマンドレットを使用して次のコマンドを実行することで、ネットワーク構成ファイルが追加されたことを確認します。

        Get-AzureVNetSite -VNetName TestVNet

    上記のコマンドで想定される出力を次に示します。

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded





