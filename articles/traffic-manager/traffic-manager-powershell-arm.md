<properties
   pageTitle="Azure リソース マネージャーによる Traffic Manager プレビューのサポート | Microsoft Azure "
   description="Azure リソース マネージャー (ARM) での Traffic Manager に対する Powershell の使用 (プレビュー)"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/19/2015"
   ms.author="joaoma" />



# Azure リソース マネージャーによる Azure Traffic Manager プレビューのサポート
Azure リソース マネージャー (ARM) は、Azure のサービスの新しい管理フレームワークです。  Azure リソース マネージャー ベースの API とツールを使用して、Azure Traffic Manager プロファイルを管理できるようになりました。 Azure リソース マネージャーについての詳細については、次を参照してください。 [を使用してリソース グループを Azure のリソースを管理](../azure-preview-portal-using-resource-groups.md)します。

>[AZURE.NOTE] ARM のトラフィック マネージャーのサポートは現在プレビューの REST API、Azure PowerShell、Azure CLI および .NET SDK を含むにです。



## リソース モデル

Azure Traffic Manager は、Traffic Manager プロファイルと呼ばれる設定のコレクションを使用して構成されます。 これには、DNS 設定、トラフィック ルーティング設定、エンドポイント監視設定、トラフィックのルーティング先となるサービス エンドポイントのリストが含まれます。

ARM では、各 Traffic Manager プロファイルは、Microsoft.Network リソース プロバイダーによって管理される、TrafficManagerProfiles 型の ARM リソースで表されます。  REST API レベルでの各プロファイルの URI は次のとおりです。

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Azure Traffic Manager サービス管理 API との比較

ARM を使用して Traffic Manager プロファイルを構成すると、下記のプレビューの制限事項を除き、Azure サービス管理 (ASM) API と同じ Traffic Manager の機能セットにアクセスできます。

ただし、機能はこれまでと同じですが、一部の用語が変更されました。

- Traffic Manager が特定の DNS 要求に応答するときに、トラフィックのルーティング先のエンドポイントを選択する方法を決定する "負荷分散方法" が、"トラフィック ルーティング方法" という名前に変更されました。

- トラフィック ルーティング方法の "ラウンド ロビン" が、"重み付け" という名前に変更されました。

- トラフィック ルーティング方法の "フェールオーバー" が、"優先度" という名前に変更されました。

## プレビューの制限事項
Azure リソース マネージャーによる Traffic Manager のサポートはプレビュー サービスであるため、現在は制限事項がいくつかあります。

- 既存の (ARM 以外の) Azure サービス管理 (ASM) API、ツール、"クラシック" ポータルを使用して作成された Traffic Manager プロファイルは ARM では使用できません。逆の場合も同様です。 プロファイルを削除して再作成する方法以外に、ASM から ARM API へのプロファイルの移行は現在サポートされていません。

- "入れ子になった" Traffic Manager エンドポイントは、ARM API では現在サポートされていません。

- Azure Traffic Manager は、Azure "プレビュー" ポータルではまだ使用できません。"クラシック" ポータルでのみ使用できます。

## Azure PowerShell の設定

各手順では、Microsoft Azure PowerShell を使用します。次の手順を使用して、Microsoft Azure PowerShell を構成する必要があります。

PowerShell ユーザーまたは Windows ユーザーでない場合は、Azure CLI を使用して類似の操作を実行できます。

### 手順 1.
Azure のダウンロード ページから入手できる、最新の Azure PowerShell をインストールします。

### 手順 2.
Azure アカウントにログインします。

    PS C:\> Login-AzureRmAccopunt

資格情報を使用して認証を行うように求めるメッセージが表示されます。

### 手順 3.
使用する Azure サブスクリプションを選択します。

    PS C:\> Select-AzureRmContext -SubscriptionName "MySubscription"

使用できるサブスクリプションの一覧を表示するには、"Get-AzureRmSubscription" コマンドレットを使用します。

### 手順 4.

Traffic Manager サービスは、Microsoft.Network リソース プロバイダーによって管理されています。  ARM で Traffic Manager を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。  この操作は、サブスクリプションごとに 1 回だけ実行します。

    PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### 手順 5.
リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    PS C:\> New-AzureRmResourceGroup -Name MyAzureResourceGroup -Location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 ただし、Traffic Manager プロファイル リソースはすべてグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure Traffic Manager には影響しません。

## Traffic Manager プロファイルの作成

Traffic Manager プロファイルを作成するには、New-AzureRmTrafficManagerProfile コマンドレットを使用します。

    PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

パラメーターは、次のとおりです。

- Name: Traffic Manager プロファイル リソースの ARM リソース名。  同じリソース グループ内のプロファイルには、一意の名前が必要です。  この名前は、DNS クエリに使用される DNS 名とは別のものです。

- ResourceGroupName: プロファイル リソースが属する ARM リソース グループの名前。

- TrafficRoutingMethod: 受信 DNS クエリへの応答で返されるエンドポイントを特定するために使用する、トラフィック ルーティング方法を指定します。 指定できる値は、"Performance"、"Weighted"、または "Priority" です。

- RelativeDnsName: この Traffic Manager プロファイルで提供される相対 DNS 名を指定します。  Azure Traffic Manager が使用する DNS ドメイン名とこの値を組み合わせて、プロファイルの完全修飾ドメイン名 (FQDN) が形成されます。  たとえば、値 "contoso" を指定すると、Traffic Manager プロファイルの完全修飾ドメイン名は "contoso.trafficmanager.net" になります。

- TTL: DNS の TTL (Time-to-Live) を秒単位で指定します。  このパラメーターにより、ローカル DNS リゾルバーと DNS クライアントに、この Traffic Manager プロファイルで提供される DNS 応答をキャッシュする時間を通知します。

- MonitorProtocol: エンドポイントの正常性の監視に使用するプロトコルを指定します。 指定できる値は、"HTTP" と "HTTPS" です。

- MonitorPort: エンドポイントの正常性の監視に使用する TCP ポートを指定します。

- MonitorPath: エンドポイントの正常性のプローブに使用する、エンドポイントのドメイン名の相対パスを指定します。

このコマンドレットは、Azure Traffic Manager 内に Traffic Manager プロファイルを作成し、対応するプロファイル オブジェクトを返します。  この時点で、プロファイルにエンドポイントがありません-を参照してください [Traffic Manager エンドポイントの追加](#adding-traffic-manager-endpoints) Traffic Manager プロファイルにエンドポイントを追加する方法の詳細についてです。

## Traffic Manager プロファイルの取得

既存の Traffic Manager プロファイル オブジェクトを取得するには、Get-AzureRmTrafficManagerProfle コマンドレットを使用します。

    PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

このコマンドレットは、Traffic Manager プロファイル オブジェクトを返します。

## Traffic Manager プロファイルを更新します。 [](#update-traffic-manager-profile)

Traffic Manager プロファイルを変更して、エンドポイントを追加または削除したり、プロファイル設定を変更したりするときは、次の 3 段階のプロセスに従います。

1.  Get-AzureRmTrafficManagerProfile を使用してプロファイルを取得します (または、New-AzureRmTrafficManagerProfile から返されるプロファイルを使用します)。

2.  エンドポイントの追加、エンドポイントの削除、エンドポイントのパラメーターの変更、またはプロファイルのパラメーターの変更を行って、プロファイルを変更します。  これらの変更はオフライン操作です。つまり、プロファイルを表すローカル オブジェクトだけが変更されます。

3.  Set-AzureRmTrafficManagerProfile コマンドレットを使用して、変更をコミットします。  これにより、Azure Traffic Manager 内の既存のプロファイルが、提供されたプロファイルに置き換えられます。

すべてのプロファイル プロパティは変更できますが、例外として、プロファイルの作成後にプロファイルの RelativeDnsName を変更することはできません (この値を変更するには、プロファイルを削除して再作成します)。

たとえば、プロファイルの TTL を変更するには、次のように入力します。

    PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
    PS C:\> $profile.Ttl = 300
    PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

## Traffic Manager エンドポイントを追加します。 [](#adding-traffic-manager-endpoints)
Traffic Manager エンドポイントの次の 3 つの種類があります。
1. Azure エンドポイント: Azure でホストされるサービスを表します。
2. 外部エンドポイント: Azure の外部でホストされるサービスを表します。
3. 入れ子になったエンドポイント: Traffic Manager プロファイルの入れ子構造の階層を作成するために使用され、より複雑なアプリケーションの高度なトラフィック ルーティング構成を可能にします。  これらは ARM API ではまだサポートされていません。

すべての 3 つのケースでは、2 つの方法でエンドポイントを追加できます。
1. プロセスを使用して、3 つの手順のようなで説明されている [Traffic Manager プロファイルを更新して](#update-traffic-manager-profile): Get AzureRmTrafficManagerProfile を使用して、そのプロファイル オブジェクトを取得、追加 AzureRmTrafficManagerEndpointConfig を使用して、エンドポイントを追加するオフラインになった更新、Azure トラフィック マネージャーにセット AzureRmTrafficManagerProfile を使用して変更をアップロードします。  この方法の利点は、1 回の更新でエンドポイントの変更を複数行うことができる点です。
2. New-AzureRmTrafficManagerEndpoint コマンドレットを使用します。  これにより、1 回の操作で既存の Traffic Manager プロファイルにエンドポイントが追加されます。

### Azure エンドポイントの追加
Azure エンドポイントは、Azure でホストされる他のサービスを参照します。  現時点では、Azure のエンドポイントの 3 種類がサポートされています。
1. Azure Web Apps
2. 'クラシック' のクラウド サービス (これは、PaaS サービスまたは IaaS 仮想マシンのいずれかを含めることができます)
3. ARM Microsoft.Network/publicIpAddress リソース (ロード バランサーまたは仮想マシン NIC に接続できます)。  publicIpAddress には、Traffic Manager で使用するために DNS 名を割り当てておく必要があることに注意してください。

各 case で。
 - サービスは、追加 AzureRmTrafficManagerEndpointConfig または新規 AzureRmTrafficManagerEndpoint の 'targetResourceId' パラメーターを使用して指定します。
 - 'Target' と 'EndpointLocation' を指定しないでください、上記で指定した TargetResourceId によって暗黙的に指定します。
 - 指定して、"Weight"は省略できます。  Weight が使用されるのは、プロファイルがトラフィック ルーティング方法として "Weighted" を使用するように構成されている場合のみです。それ以外の場合は、無視されます。  指定する場合は、1 ～ 1,000 の値にする必要があります。  既定値は '1' です。
 - 指定して、'Priority' はオプションです。  Priority が使用されるのは、プロファイルがトラフィック ルーティング方法として "Priority" を使用するように構成されている場合のみです。それ以外の場合は、無視されます。  有効な値は 1 ～ 1,000 です (値が小さくなるほど、優先度が高くなります)。  1 つのエンドポイントに指定した場合は、すべてのエンドポイントに指定する必要があります。  省略した場合は、1、2、3 などから始まる既定値が、エンドポイントが指定される順に適用されます。

#### 例 1: Add-AzureRmTrafficManagerEndpointConfig を使用して Web アプリ エンドポイントを追加する
この例では、新しい Traffic Manager プロファイルを作成し、Add-AzureRmTrafficManagerEndpointConfig コマンドレットを使用して 2 つの Web アプリ エンドポイントを追加した後、Set-AzureRmTrafficManagerProfile を使用して、更新したプロファイルを Azure Traffic Manager にコミットします。

    PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName myrg -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
    PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
    PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
    PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
    PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### 例 2: New-AzureRmTrafficManagerEndpoint を使用して "クラシック" クラウド サービス エンドポイントを追加する
この例では、"クラシック" クラウド サービス エンドポイントが Traffic Manager プロファイルに追加されます。  この場合は、プロファイル オブジェクトを渡すのではなく、プロファイル名とリソース グループ名を使用してプロファイルを指定していることに注意してください (どちらの方法もサポートされています)。

    PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### 例 3: New-AzureRmTrafficManagerEndpoint を使用して publicIpAddress エンドポイントを追加する
この例では、ARM パブリック IP アドレス リソースが Traffic Manager プロファイルに追加されます。  パブリック IP アドレスでは DNS 名が構成されている必要があります。また、パブリック IP アドレスは、VM の NIC、またはロード バランサーにバインドすることができます。

    PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
    PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### 外部エンドポイントの追加
Traffic Manager は、外部エンドポイントを使用して、Azure の外部でホストされているサービスにトラフィックを送信します。  Azure エンドポイントと同様に、外部エンドポイントを追加するには、Add-AzureRmTrafficManagerEndpointConfig の後に Set-AzureRmTrafficManagerProfile を使用するか、New-AzureRMTrafficManagerEndpoint を使用することができます。

外部エンドポイントを指定する場合
 - 'Target' パラメーターを使用してエンドポイントのドメイン名を指定する必要があります。
 - 「パフォーマンス」トラフィック ルーティング方法を使用する場合は、'EndpointLocation' が必要なそれ以外の場合は省略可能です。  値がある必要があります、 [有効な Azure リージョン名](http://azure.microsoft.com/regions/)します。
 - "Weight"、「優先度」はオプションですが、Azure のエンドポイントです。

#### 例 1: Add-AzureRmTrafficManagerEndpointConfig と Set-AzureRmTrafficManagerProfile を使用して外部エンドポイントを追加する
この例では、新しい Traffic Manager プロファイルを作成し、2 つの外部エンドポイントを追加して、変更をコミットします。

    PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName myrg -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
    PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
    PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### 例 2: New-AzureRmTrafficManagerEndpoint を使用して外部エンドポイントを追加する
この例では、プロファイル名とリソース グループ名を使用して指定した既存のプロファイルに、外部エンドポイントを追加します。

    PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

## Traffic Manager エンドポイントの更新
既存の Traffic Manager エンドポイントを更新する 2 つの方法があります。
1. Get-AzureRmTrafficManagerProfile を使用して Traffic Manager プロファイルを取得し、プロファイル内でエンドポイントのプロパティを更新した後、Set-AzureRmTrafficManagerProfile を使用して変更をコミットします。  この方法の利点は、1 回の操作で複数のエンドポイントを更新できることです。
2. Get-AzureRmTrafficManagerEndpoint を使用して Traffic Manager エンドポイントを取得し、エンドポイントのプロパティを更新した後、Set-AzureRmTrafficManagerEndpoint を使用して変更をコミットします。  この方法は、プロファイル内の Endpoints 配列にインデックスを作成する必要がないため、より単純です。

#### 例 1: Get-AzureRmTrafficManagerProfile と Set-AzureRmTrafficManagerProfile を使用してエンドポイントを更新する
この例では、既存のプロファイル内の 2 つのエンドポイントの優先度を変更します。

    PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName myrg
    PS C:\> $profile.Endpoints[0].Priority = 2
    PS C:\> $profile.Endpoints[1].Priority = 1
    PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### 例 2: Get-AzureRmTrafficManagerEndpoint と Set-AzureRmTrafficManagerEndpoint を使用してエンドポイントを更新する
この例では、既存のプロファイル内の 1 つのエンドポイントの重み付けを変更します。

    PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName myrg -Type ExternalEndpoints
    PS C:\> $endpoint.Weight = 20
    PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## エンドポイントとプロファイルの有効化と無効化
Traffic Manager を使用すると、プロファイル全体だけでなく、個々のエンドポイントを有効にしたり無効にしたりすることができます。
これらの変更は、エンドポイントまたはプロファイルのリソースを取得、更新、設定することで実行できます。  このような一般的な操作を合理化するために、専用のコマンドレットを使用した操作もサポートされています。

#### 例 1: Traffic Manager プロファイルを有効または無効にする
Traffic Manager プロファイルを有効にするには、Enable-AzureRmTrafficManagerProfile を使用します。  プロファイルを指定するには、プロファイル オブジェクトを使用する (パイプラインまたは "-TrafficManagerProfile" パラメーターを使用して渡す) か、この例と同様に、プロファイル名とリソース グループ名を使用することができます。

    PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

同様に、Traffic Manager プロファイルを無効にするには、次のように入力します。 

    PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Disable-AzureRmTrafficManagerProfile コマンドレットでは、確認のメッセージが表示されますが、"-Force" パラメーターを使用して、表示されないように設定できます。

#### 例 2: Traffic Manager エンドポイントを有効または無効にする
Traffic Manager エンドポイントを有効にするには、Enable-AzureRmTrafficManagerEndpoint を使用します。  エンドポイントを指定するには、TrafficManagerEndpoint オブジェクトを使用する (パイプラインまたは "-TrafficManagerEndpoint" パラメーターを使用して渡す) か、エンドポイント名、エンドポイントの種類、プロファイル名、リソース グループ名を使用することができます。

    PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyResourceGroup

同様に、Traffic Manager エンドポイントを無効にするには、次のように入力します。 

    PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyResourceGroup -Force

Disable-AzureRmTrafficManagerProfile と同様に、Disable-AzureRmTrafficManagerEndpoint コマンドレットでも確認のメッセージが表示されますが、"-Force" パラメーターを使用して、表示されないように設定できます。

## Traffic Manager エンドポイントの削除
Traffic Manager エンドポイントを削除する 1 つの方法では、(Get-AzureRmTrafficManagerProfile を使用して) プロファイル オブジェクトを取得し、ローカルのプロファイル オブジェクト内でエンドポイントのリストを更新して、(Set-AzureRmTrafficManagerProfile を使用して) 変更をコミットします。  この方法を使用すると、エンドポイントに対する複数の変更をまとめてコミットできます。

また、個々のエンドポイントを削除するには、Remove-AzureRmTrafficManagerEndpoint コマンドレットを使用する方法もあります。

    PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyResourceGroup
    
このコマンドレットでは、"-Force" パラメーターを使用して非表示に指定しない限り、確認のメッセージが表示されます。

## Traffic Manager プロファイルの削除
Traffic Manager プロファイルを削除するには、プロファイル名とリソース グループ名を指定して、Remove-AzureRmTrafficManagerProfile コマンドレットを使用します。

    PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

このコマンドレットでは、確認のメッセージが表示されます。  オプションの "-Force" スイッチを使用すると、このメッセージが表示されないようにすることができます。
プロファイル オブジェクトを使用して、削除するプロファイルを指定することもできます。

    PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
    PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

また、このシーケンスをパイプすることもできます。

    PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]

## 次のステップ

[Traffic Manager の監視](traffic-manager-monitoring.md)

[Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)
 

