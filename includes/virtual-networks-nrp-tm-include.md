## Traffic Manager プロファイル

Traffic Manager とその子エンドポイントのリソースは、Azure 内と Azure 外部のエンドポイントへ DNS のルーティングをできるようにします。 このようなトラフィック分散は、ポリシーのルーティング メソッドが適用されます。 Traffic Manager では、エンドポイントの正常性を監視できます。また、エンドポイントの正常性に基づいて、トラフィックが適切に転送できるようにします。 

| プロパティ | 説明 |
|---|---|
|**トラフィック ルーティング方法**| 指定できる値は *パフォーマンス*, 、*重み付け*, 、および *優先順位* | 
| **DNS の構成** | プロファイルの FQDN | 
| **プロトコル** | 監視プロトコル有効な値は *HTTP* と *HTTPS。*|
| **Port** | 監視ポート |  
| **パス** | 監視パス |
| **エンドポイント** |  エンドポイント リソースのコンテナー | 

### エンドポイント 

エンドポイントは、Traffic Manager プロファイルの子リソースです。 Traffic Manager プロファイル リソースに構成されたポリシーに基づいてユーザー トラフィックが配信されるサービスまたは Web エンドポイントを表します。 

| プロパティ | 説明 | 
|---|---| 
| **型** |  エンドポイントの種類を指定できる値は *Azure 終点*, 、*外部エンドポイント*, 、および  *入れ子になったエンドポイント* | 
| **ターゲット リソース ID** |  サービスまたは Web のエンドポイントのパブリック IP アドレスです。 Azure のエンドポイントまたは外部のエンドポイントを指定できます。 | 
| **重量** | トラフィック管理に使用されるエンドポイントの重み付けです。 | 
| **優先順位** | フェールオーバー アクションを定義するために使用されるエンドポイントの優先度です。 |

Json 形式での Traffic Manager のサンプル: 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## その他のリソース

読み取り [REST API のドキュメント Traffic Manager の](https://msdn.microsoft.com/library/azure/mt163664.aspx) の詳細。

