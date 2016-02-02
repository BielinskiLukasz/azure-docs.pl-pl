## PowerShell を使用して ARM テンプレートをデプロイする

PowerShell を使用してダウンロードした ARM テンプレートをデプロイするには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、次を参照してください。 [インストールおよび Azure PowerShell の構成方法](powershell-install-configure.md) Azure にサインインし、サブスクリプションを選択最後までの指示に従います。

3. 必要に応じて **New-AzureRmResourceGroup** コマンドレットを実行して、新しいリソース グループを作成します。 次のコマンドを実行すると、*TestRG* というリソース グループが *Central US* Azure リージョンに作成されます。 リソース グループの詳細については、次を参照してください。 [Azure リソース マネージャーの概要](resource-group-overview.md)します。

        New-AzureRmResourceGroup -Name TestRG -Location centralus

    上記のコマンドで想定される出力を次に示します。

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

4. **New-AzureRmResourceGroupDeployment** コマンドレットを実行し、上記でダウンロードおよび変更したテンプレート ファイルとパラメーター ファイルを使用して、新しい VNet をデプロイします。

     New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
         -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

 上記のコマンドで想定される出力を次に示します。

     DeploymentName    : TestVNetDeployment
     ResourceGroupName : TestRG
     ProvisioningState : Succeeded
     Timestamp         : 8/14/2015 9:40:00 PM
     Mode              : Incremental
     TemplateLink      :
     Parameters        :
                         Name             Type                       Value
                         ===============  =========================  ==========
                         location         String                     Central US
                         vnetName         String                     TestVNet
                         addressPrefix    String                     192.168.0.0/16
                         subnet1Prefix    String                     192.168.1.0/24
                         subnet1Name      String                     FrontEnd
                         subnet2Prefix    String                     192.168.2.0/24
                         subnet2Name      String                     BackEnd
    
     Outputs           :

5. 次に示すように、**Get-AzureRmVirtualNetwork** コマンドレットを実行して新しい VNet のプロパティを表示します。

        Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet

    上記のコマンドで想定される出力を次に示します。

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]





