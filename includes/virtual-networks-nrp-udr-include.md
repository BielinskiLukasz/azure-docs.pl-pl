## ルート テーブル

ルート テーブル リソースには、Azure インフラストラクチャ内でトラフィックがどのようにフローするかを定義するために使用されるルートが含まれています。 ファイアウォールや侵入検知システム (IDS) など、ユーザー定義ルート (UDR) を使用して所定のサブネットからのすべてのトラフィックを仮想アプライアンスに送信できます。 サブネットにルート テーブルを関連付けることができます。

ルート テーブルには次のプロパティが含まれています。

| プロパティ| 説明| サンプルの値|
|---|---|---|
| **routes**| ルート テーブル内のユーザー定義ルートのコレクション| を参照してください [ユーザー定義のルート](#User-defined-routes)|
| **サブネット**| ルート テーブルが適用されるサブネットのコレクション| を参照してください [サブネット](#Subnets)|


### ユーザー定義のルート

宛先アドレスに基づいてトラフィックの送信先を指定する UDR を作成できます。 ルートは、ネットワーク パケットの宛先アドレスに基づく既定のゲートウェイ定義として考えることができます。

UDR には、次のプロパティが含まれています。

| プロパティ| 説明| サンプルの値|
|---|---|---|
| **addressPrefix**| アドレスのプレフィックス、または宛先の完全な IP アドレス| 192.168.1.0/24, 192.168.1.101|
| **nextHopType**| トラフィックの送信先となるデバイスの種類| VirtualAppliance、VPN Gateway、インターネット|
| **nextHopIpAddress**| 次のホップ先の IP アドレス| 192.168.1.4|


JSON 形式のサンプル ルート テーブル

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### その他のリソース

- 詳細 [Udr](virtual-networks-udr-overview.md)します。
- 読み取り、 [REST API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/mt502549.aspx) をルート テーブルです。
- 読み取り、 [REST API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/mt502539.aspx) ユーザーのルート (Udr) を定義します。




