<properties 
   pageTitle="インターネットに接続するクラシック デプロイ モデルのロード バランサー (クラウド サービス用) の作成の開始 | Microsoft Azure"
   description="インターネットに接続するクラシック デプロイ モデルのロード バランサー (クラウド サービス用) を作成する方法について説明します"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/30/2015"
   ms.author="joaoma" />


# インターネットに接続するロード バランサー (クラウド サービス用) の作成の開始

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] この記事では、従来のデプロイ モデルについて説明します。 こともできます [インターネットへの Azure リソース マネージャーを使用してロード バランサーを作成する方法について](load-balancer-get-started-internet-arm-cli.md)します。

クラウド サービスはロード バランサーで自動的に構成され、サービス モデルを使用してカスタマイズできます。

## サービス定義ファイルを使用してロード バランサーを作成します。

Azure SDK for .NET 2.5 を使用してクラウド サービスを更新できます。 クラウド サービスのエンドポイントの設定が行われた、 [サービス定義](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef ファイルです。

次の例は、クラウド デプロイメントの servicedefinition.csdef ファイルを構成する方法を示しています。

クラウド デプロイメントによって生成された .csdef ファイルのスニペット内を確認すると、外部エンドポイントがポート 10000、10001、10002 の HTTP ポートを使用するように構成されていることがわかります。


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />
    
        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />
    
        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>

## クラウド サービスのロード バランサーの正常性状態を確認する

次に、正常性プローブの例を示します。

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

ロード バランサーには、エンドポイントの情報およびサービスの正常性クエリに使用できる vm}:80/probe.aspx の http://{DIP の形式で URL を作成するプローブの情報が集約されます。

サービスは同じ IP アドレスからの定期的なプローブを検出します。 これは、仮想マシンを実行しているノードのホストから送信される正常性プローブ要求です。
サービスは、サービスが正常であると仮定するロード バランサーの状態コード HTTP 200 で応答する必要があります。 その他の HTTP 状態コード (503 など) は、仮想マシンをローテーションから直接除外します。

プローブの定義はプローブの頻度も制御します。 上記の場合、ロード バランサーは、エンドポイントを 5 秒ごとに調査しています。 10 秒以内 (2 つのプローブの間隔) に肯定応答を受信しなかった場合、プローブは停止していると見なされ、仮想マシンはローテーションから除外されます。 同様に、サービスがローテーションから除外され、肯定応答を受信した場合、サービスはすぐにローテーションに戻されます。 サービスが正常な状態と異常な状態の間で変動する場合、ロード バランサーは多数のプローブが正常な状態になるまで、サービスをローテーションに戻すことを遅らせることができます。

サービス定義スキーマを確認して、 [正常性プローブ](https://msdn.microsoft.com/library/azure/jj151530.aspx) の詳細。

## 次のステップ

[内部ロード バランサーの構成の開始します。](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードを構成します。](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定を構成します。](load-balancer-tcp-idle-timeout.md)






